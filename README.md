Install LAMP (Linux, Apache, MySql & PHP) on AWS EC2 with Ubuntu 16.04
===
## Outline
- [架設 AWS EC2 Instance](#First)
- [安裝 Apache+MySQL+PHP](#Second)

<h2 id="First">架設 AWS EC2 Instance</h2>

### Create an Account
到 AWS(Aamzon Web Services) [官網](https://aws.amazon.com/) 註冊帳號。
![](https://i.imgur.com/AnMNokO.png)

[](https://aws.amazon.com/)

### Create a EC2 Instance
點選左上角的 Services，並選擇 EC2，進入 [EC2 Management Console](https://console.aws.amazon.com/ec2/)。
![](https://i.imgur.com/MY13ITZ.png)

右上角選擇機房位址，一般來說台灣會推薦選擇連線速度最佳的日本機房。
![](https://i.imgur.com/A18lZOq.png)

點選 Launch Instance
![](https://i.imgur.com/Jjj3mUW.png)

### Choosing the OS and Hardware
#### Step1
選擇要裝的映像檔，這裡選擇的是 _Ubuntu Server 16.04 LTS (HVM), SSD Volume Type_，可以依據不同的需求選擇想要的 ISO 檔。
![](https://i.imgur.com/4jujEoe.png)

#### Step2
接下來選擇硬體規格，這裡選擇免費的 t2.micro。
![](https://i.imgur.com/HcCTpcW.png)

#### Step3
![](https://i.imgur.com/9ez6vK2.png)

#### Step4
![](https://i.imgur.com/AzEoyNz.png)

#### Step5
為主機取名。
![](https://i.imgur.com/AsWD3Nj.png)

#### Step6
![](https://i.imgur.com/2SOpUMc.png)
![](https://i.imgur.com/nrZkLQz.png)

#### Step7
確認 Step1-Step6 上述所有資訊都無誤後，點選 _Launch_。
![](https://i.imgur.com/dkb3ye1.png)

初次建立主機，選擇 _Create a new key pair_，取一個名稱後將 key 下載下來，稍後我們會使用這把 key 由 SSH 連線到主機。
![](https://i.imgur.com/WKpzW19.png)

看到這個畫面表示已經成功的建立 Instance，點選右下角的 _View Instances_ 可以轉跳回 EC2 Management Console。
![](https://i.imgur.com/6Gq4VFj.png)

點選剛建立好的主機，下方可以看到相關資訊。
記下 Public IP，下一步我們將使用 SSH 遠端連線至主機。
![](https://i.imgur.com/jgCMnqW.png)

使用 Windows 的人需要先安裝 putty 和 puttygen。
（可以在[這裡](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載安裝檔）
![](https://i.imgur.com/eNRjxnd.png)

開啟 puttygen 點選 _Load_ 將剛剛儲存的 NewKey.pem 載入，並且點選 _Save private key_ 將其轉換成 NewKey.ppk。
![](https://i.imgur.com/ZCNW1Fe.png)

![](https://i.imgur.com/zWOZlZq.png)

### Connecting to Instance
打開 PuTTY，將 NewKey.ppk 載入。
![](https://i.imgur.com/Lhn0BiJ.png)

回到 _Session_，使用預設帳號 ubuntu 遠端登入主機<Public IP>。
![](https://i.imgur.com/ymLR4y5.png)

成功登入主機。
![](https://i.imgur.com/F54QVW5.png)

<h2 id="Second">安裝 Apache+MySQL+PHP</h2>

在登入主機後，首先更新 package 列表。
```
sudo apt-get update
```

### Installing Apache
下載 Apache。
```
sudo apt-get install -y apache2
```
接下來，打開瀏覽器並輸入 public IP，確認 Apache 是否成功安裝。
```
http://[YOUR_EXTERNAL_IP_ADDRESS]
```
看到 Apache 2 的預設歡迎頁面，就表示 Apache 已正確地安裝好了。
![](https://i.imgur.com/lf77puc.png)

### Installing MySql
```
sudo apt-get install -y mysql-server
```
設定使用者 root 的密碼。
![](https://i.imgur.com/m3QnJFi.png)

進行 MySQL 的安全性設定（更改 MySQL root 密碼及移除不必要的權限以加強安全性）。
```
sudo mysql_secure_installation
```
![](https://i.imgur.com/1oznN7U.png)
No

![](https://i.imgur.com/y81GuST.png)
No

![](https://i.imgur.com/d5KdZa8.png)
Yes

![](https://i.imgur.com/CJo7pr8.png)
No

![](https://i.imgur.com/GoUnS4J.png)
Yes

![](https://i.imgur.com/h2fGmP7.png)
Yes

使用指令登入 database 以確認 MySQL 成功安裝。
```
sudo mysql -u root -p
```
![](https://i.imgur.com/5aPs9VS.png)

### Installing PHP
安裝 PHP 及其常用模組。
```
sudo apt-get install php libapache2-mod-php php-mysql php-curl php-gd php-json php-zip php-mbstring
```

由於 Apache 已在執行中，更新了的 PHP 並不會立刻生效，所以需要重啟 Apache 伺服器。
```
sudo service apache2 restart
```

### Installing phpMyAdmin
```
sudo apt install -y phpmyadmin
```
![](https://i.imgur.com/0ypjR3w.png)
![](https://i.imgur.com/pquKiLi.png)

設定使用者 phpmyadmin 的 passwd。
![](https://i.imgur.com/UIPxAk5.png)

建立/var/www/html 下的軟連結
```
sudo ln -s /usr/share/phpmyadmin /var/www/html
```
接下來就可以在瀏覽器上直接連接到 phpMyAdmin 了。
```
http://[YOUR_EXTERNAL_IP_ADDRESS]/phpmyadmin
```
![](https://i.imgur.com/uQst4fL.png)

### Connecting Instance with FTP Client
![](https://i.imgur.com/R2FYIdx.png)

- 協定選擇 SFTP
- Host 填入 Public IP
- 使用金鑰檔案登入
- 使用者預設為 ubuntu
- 最後載入 NewKey.ppk

![](https://i.imgur.com/ytZJUyK.png)

Apache 的檔案存放目錄預設路徑為「/var/www/html」。
但此時由於權限問題，無法上傳檔案，所以我們需要調整存取權限。
```
sudo chown -R ubuntu /var/www/html #更改檔案的擁有者
sudo chmod -R 755 /var/www/html    #更改存取權限
```

## Reference
- [[ AWS ] 將 AWS EC2 的 .pem 檔轉換為 .ppk 檔教學](https://oranwind.org/-aws-jiang-aws-ec2-de-pem-dang-zhuan-huan-wei-ppk-dang-jiao-xue/)
- [How to Install LAMP (Linux, Apache, MySql & PHP) on AWS EC2 with Ubuntu 18.04](https://usefulangle.com/post/96/aws-ec2-install-linux-apache-mysql-php-phpmyadmin-lamp-stack-ubuntu-18-04)