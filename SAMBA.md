# SAMBA 伺服器的應用
組員
-------------
B1342067 廖柏翰

B1342065 林義和

SAMBA的由來
------------
* 安德魯·垂鳩（Andrew Tridgell）於1992年在澳洲國立大學（ANU）開發了第一版的Samba Unix軟體。
* 近年來，windows 與Linux作業系統各自擁有其支持者與市場，因而在一班公司行號或學校裡，可能同時有windows 及Linux　作業系統的主機。

* Windows和 Windows 之間可以藉由網路芳鄰共享資源，
* Linux 和Linux可以透NFS共享目錄
* 而SAMBA伺服器則可以讓Windows 與Linux達成共享。


* 為使Windows 主機間的資源能夠共享，IBM於1980年代發展SMB（Server Message Block伺服器訊息區塊）通訊協定，又稱CIFS（Common Internet File System網路檔案分享系統）
* 在此之後便有熱心人士在Linux 上實現SMB通訊協定，最後完成的軟體及稱為SAMBA。

SMB(CIFS)
-----------
* 伺服器訊息區塊（SMB）又稱網路檔案分享系統（CIFS），為一種應用層網路傳輸協定
* 由微軟開發，主要功能是使網路上的機器能夠共享電腦檔案、印表機、序列埠和通訊等資源
* 與功能類似的網路檔案系統（NFS）相比，NFS的訊息格式是固定長度，而CIFS的訊息格式大多數是可變長度，增加了協定的複雜性
* CIFS訊息一般使用NetBIOS或TCP協定傳送，分別使用不同的埠139或445，目前傾向於使用445埠。
* CIFS的訊息包括一個標頭（32位元組）和訊息體（1個或多個，可變長）。 

SMB實現(客戶端-伺服器端結構)
---------------
* SMB使用對等的通訊方式，一個客戶端向一個伺服器提出請求，伺服器相應地回答。
* SMB協定中的一部份專門用來處理對檔案系統的存取，使得客戶端可以存取一個檔案伺服器。SMB也有行程間通訊的部份。
* SMB協定尤其適用於局部子網路，但是也可以被用來通過全球資訊網來連結不同的子網路。Microsoft Windows的檔案和印表機分享主要使用這個功能。
* SMB伺服器向網路上的客戶端提供檔案系統和其它資源。客戶端電腦也可能有其自己的、不共享的硬碟，但是可能也想使用伺服器上分享的檔案系統和印表機。

daemon
-------------
* 在一個多工的電腦作業系統中，守護進程（daemon）是一種在後台執行的電腦程序。此類程序會被以進程的形式初始化。
* samba 所使用的daemon是smbd與nmbd。

smbd與nmbd
-------------------
Samba提供了基於CIFS的四個服務：文件和打印服務、授權與被授權、名字解析、瀏覽服務。
* 「文件和打印服務」、「授權與被授權」 由smbd提供
* 「名字解析」、「瀏覽服務」則由nmbd提供
實作
----------
* 安裝samba server
```
$ sudo apt-get install samba
```
* 安裝完後Samba會自動啟動，若沒有啟動，可輸入如下指令
```
$ sudo systemctl start nmbd
$ sudo systemctl start smbd
```
* 設定不須登入/操作shell的帳號
```
$sudo adduser smbuser --shell /bin/false
```
* 設定密碼
```
$sudo smbpasswd -a smbuser
```
* 移除使用者(可選)
```
$smbpasswd -x smbuser
$userdel -r smbuser
```
* 調整 Samba 的設定檔/etc/samba/smb.conf，在檔案的尾巴加入下面這幾行後存檔離開。
```
[share]
 path = /home/smbuser # 這個是要分享的資料夾路徑
 available = yes
 valid users = smbuser # 這個是可以使用這個分享資料夾的使用者，要具有 path 所指定的路徑的權限才行
 read only = no
 browseable = yes
 public = yes
 writable = yes
```
* 重啟 Samba Server
```
$sudo service smbd restart
$sudo service nmbd restart
```
* Linux 連結 Samba Server
```
$sudo apt-get install smbclient cifs-utils
$sudo mkdir -p /mnt/share
$sudo mount -t cifs  //{Samba Server IP}/share /mnt/share -o username=smbuser,password=123456789
$df
$sudo umount /mnt/share
```

