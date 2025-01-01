
# Linux 伺服器

## 1. SSH 伺服器設定

### 安裝 SSH 伺服器
1. 在Ubuntu上安裝 OpenSSH 伺服器：
   ```
   sudo apt install openssh-server
   ```
   (如果已經安裝過，則跳過這一步)

### 查詢 SSH 伺服器狀態
2. 查看 SSH 伺服器是否運行：
   ```
   systemctl status sshd
   ```
3. 啟動 SSH 伺服器：
   ```
   sudo systemctl start sshd
   ```
4. 停止 SSH 伺服器：
   ```
   sudo systemctl stop sshd
   ```
5. 設定開機自啟動：
   ```
   sudo systemctl enable sshd
   ```
6. 關閉開機自啟動：
   ```
   sudo systemctl disable sshd
   ```

### SSH 無密碼登入設定
7. 生成 SSH 密鑰：
   ```
   ssh-keygen
   ```
8. 複製公鑰到遠端伺服器：
   ```
   ssh-copy-id user@server_ip
   ```
9. 登入遠端伺服器：
   ```
   ssh user@server_ip
   ```

---

## 2. NFS 伺服器設定

### 安裝 NFS 伺服器
1. 安裝 NFS 伺服器：
   ```
   sudo apt install -y nfs-kernel-server
   ```

### 設定共享資料夾
2. 創建共享資料夾：
   ```
   sudo mkdir /nfs-server
   ```
3. 編輯 `/etc/exports` 文件來配置共享資料夾，並加入以下行：
   ```
   /nfs-server *(rw,sync,no_subtree_check,all_squash)
   ```
4. 重啟 NFS 伺服器：
   ```
   sudo systemctl restart nfs-kernel-server
   ```

### 客戶端掛載 NFS
5. 安裝 NFS 客戶端：
   ```
   sudo apt install nfs-common -y
   ```
6. 挂載共享資料夾：
   ```
   sudo mount -t nfs server_ip:/nfs-server /nfs-client
   ```

---

## 3. MariaDB 資料庫設定

### 安裝 MariaDB
1. 安裝 MariaDB：
   ```
   sudo apt install mariadb-server
   ```

### 初始化 MariaDB
2. 初始化 MariaDB 並設置 root 密碼：
   ```
   sudo mysql_secure_installation
   ```

### 設定遠端訪問
3. 編輯 `/etc/mysql/mariadb.conf.d/50-server.cnf`，將 `bind-address` 修改為 `0.0.0.0`，允許遠端訪問：
   ```
   bind-address = 0.0.0.0
   ```
4. 重啟 MariaDB：
   ```
   sudo systemctl restart mariadb
   ```

### 資料庫操作
5. 使用 MariaDB 客戶端登入：
   ```
   sudo mysql -u root -p
   ```

6. 創建資料庫並授權：
   ```
   CREATE DATABASE example_db;
   GRANT ALL PRIVILEGES ON example_db.* TO 'root'@'%' IDENTIFIED BY 'password';
   FLUSH PRIVILEGES;
   ```

---

## 4. Apache 網頁伺服器設定

### 安裝 Apache
1. 安裝 Apache2 伺服器：
   ```
   sudo apt install apache2
   ```

### 啟動 Apache
2. 啟動 Apache 伺服器：
   ```
   sudo systemctl start apache2
   ```

### 配置虛擬主機
3. 創建虛擬主機配置：
   ```
   sudo vim /etc/apache2/sites-available/mywebsite.conf
   ```
   配置內容如下：
   ```
   <VirtualHost *:80>
       ServerAdmin webmaster@mywebsite.com
       ServerName mywebsite.com
       DocumentRoot /var/www/html/mywebsite
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

4. 啟用虛擬主機並重啟 Apache：
   ```
   sudo a2ensite mywebsite.conf
   sudo systemctl restart apache2
   ```

---

## 5. PHP 安裝與配置

### 安裝 PHP
1. 安裝 PHP 和 Apache 模組：
   ```
   sudo apt install php libapache2-mod-php
   ```

### 測試 PHP
2. 在 `/var/www/html` 下創建 `info.php` 檔案：
   ```php
   <?php
   phpinfo();
   ?>
   ```

3. 在瀏覽器中訪問 `http://localhost/info.php` 來檢查 PHP 是否運行。

---

## 6. 使用者與群組管理

### 新增使用者
1. 創建使用者：
   ```
   sudo adduser username
   ```

### 刪除使用者
2. 刪除使用者：
   ```
   sudo userdel username
   ```

### 新增群組
3. 創建群組：
   ```
   sudo groupadd groupname
   ```

---

## 7. Samba 伺服器設定 (共享資料夾)

### 安裝 Samba
1. 安裝 Samba：
   ```
   sudo apt install samba
   ```

### 設定共享資料夾
2. 配置 Samba 共享資料夾：
   編輯 `/etc/samba/smb.conf`，加入以下配置：
   ```
   [public]
   path = /path/to/share
   browseable = yes
   guest ok = yes
   writable = yes
   ```

3. 重啟 Samba 伺服器：
   ```
   sudo systemctl restart smbd
   ```

### 設定 Samba 密碼
4. 設定 Samba 密碼：
   ```
   sudo smbpasswd -a username
   ```

---

## 8. 磁碟管理與存儲空間

### 新增硬碟
1. 檢查新增硬碟：
   ```
   lsblk
   ```

2. 使用 `fdisk` 進行分區：
   ```
   sudo fdisk /dev/sdb
   ```

3. 格式化新硬碟：
   ```
   sudo mkfs.ext4 /dev/sdb1
   ```

4. 創建掛載點並掛載：
   ```
   sudo mkdir /mydisk
   sudo mount /dev/sdb1 /mydisk
   ```

5. 查詢磁碟空間：
   ```
   df -h
   ```

---

## 9. 網路設定

### 設定靜態路由與封包轉發
1. 設定靜態路由：
   ```
   sudo ip route add 192.168.20.0/24 via 12.1.1.2
   ```

2. 啟用路由功能：
   ```
   sudo sysctl -w net.ipv4.ip_forward=1
   ```

### 安裝 DHCP 伺服器
1. 安裝 DHCP 伺服器：
   ```
   sudo apt install isc-dhcp-server
   ```

2. 配置 DHCP 伺服器，編輯 `/etc/dhcp/dhcpd.conf`，加入以下內容：
   ```
   subnet 192.168.10.0 netmask 255.255.255.0 {
       range 192.168.10.1 192.168.10.100;
       option routers 192.168.10.254;
   }
   ```

3. 重啟 DHCP 伺服器：
   ```
   sudo systemctl restart isc-dhcp-server
   ```

---

## 10. Shell Script 編程

### 基本指令與條件判斷
1. 輸出特殊字符：
   ```
   echo -e "Hello
World"
   ```

2. 簡單的條件判斷：
   ```bash
   if [ -e "/path/to/file" ]; then
       echo "File exists"
   else
       echo "File does not exist"
   fi
   ```

3. 使用變數：
   ```bash
   name="Alice"
   echo "Hello, $name!"
   ```

### 迴圈操作
1. 使用 `for` 迴圈：
   ```bash
   for i in {1..5}; do
       echo "Iteration $i"
   done
   ```

2. 使用 `while` 迴圈：
   ```bash
   count=1
   while [ $count -le 5 ]; do
       echo "Count is $count"
       ((count++))
   done
   ```

---

## 11. 排程管理 (cron & at)

### 使用 Cron 設定排程任務
1. 編輯 `cron` 排程：
   ```
   crontab -e
   ```
   每分鐘執行指令：
   ```
   * * * * * /path/to/command
   ```

2. 查看排程任務：
   ```
   crontab -l
   ```

3. 刪除排程任務：
   ```
   crontab -r
   ```

### 使用 At 設定單次任務
1. 使用 `at` 指令設置一次性任務：
   ```
   echo "/path/to/command" | at 15:00
   ```

---

## 12. 打包與解壓縮

### 使用 Tar 打包檔案
1. 打包並壓縮：
   ```
   tar -cvzf archive.tar.gz /path/to/files
   ```

2. 解壓縮：
   ```
   tar -xvzf archive.tar.gz
   ```

