# FTP
Configuring FTP (with TLS/SSL, LFTP and Filezilla)

#Step 1 — Installing vsftpd

sudo apt-get update

sudo apt-get install vsftpd

#Error: unable to lock directory /var/lib/apt/lists/

#Solution: sudo rm /var/lib/apt/lists/lock

sudo rm /var/cache/apt/archives/lock

sudo rm /var/lib/dpkg/lock

sudo cp /etc/vsftpd.conf / etc/vsftpd.conf.orig

#Step 2 — Opening the Firewall

sudo ufw status

#Error: Status disables

#Solution: sudo ufw enable

#We'll need to open ports 20 and 21 for FTP, port 990 for later when we enable TLS, and ports 40000-50000 for the range of passive 
ports

sudo ufw allow 20/tcp

sudo ufw allow 21/tcp

sudo ufw allow 990/tcp

sudo ufw allow 40000:50000/tcp

sudo ufw status

#Step 3 — Preparing the User Directory

sudo adduser sammy

sudo mkdir /home/sammy/ftp

sudo chown nobody:nogroup /home/sammy/ftp

sudo chmod a-w /home/sammy/ftp

sudo ls -la /home/sammy/ftp

#we'll create the directory where files can be uploaded and assign ownership to the user:

sudo mkdir /home/sammy/ftp/files

sudo chown sammy:sammy /home/sammy/ftp/files

sudo ls -la /home/sammy/ftp

#we'll add a test.txt file to use when we test later on

echo "vsftpd test file" | sudo tee /home/sammy/ftp/files/test.txt

#Step 4 — Configuring FTP Access

sudo nano /etc/vsftpd.conf

#we'll need to changes some things in the file

write_enable=YES

chroot_local_user=YES

#We’ll add

user_sub_token=$USER

local_root=/home/$USER/ftp

pasv_min_port=40000

pasv_max_port=50000

userlist_enable=YES

userlist_file=/etc/vsftpd.userlist

userlist_deny=NO

#Exit file

#we’ll create and add our user to the file. We'll use the -a flag to append to file

echo "sammy" | sudo tee -a /etc/vsftpd.userlist

cat /etc/vsftpd.userlist

sudo systemctl restart vsftpd

#Step 5 — Testing FTP Access

ftp -p 203.0.113.0 (ip address)

ifconfig 

#command ‘ifconfig’ not found

#Solution: sudo apt install net-tools

#ip address: 10.0.2.15

ftp -p 10.0.2.15

ftp> cd files

ftp>get test.txt

ftp>put test.txt upload.txt

ftp>bye

#Securing Transactiong(Enabling TTL/SSL)

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem

sudo nano /etc/vsftpd.conf

#Editing /etc/vsftpd.conf( Remove the hashtag)

#rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem

#rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key

#Add to the documents

rsa_cert_file=/etc/ssl/private/vsftpd.pem

rsa_private_key_file=/etc/ssl/private/vsftpd.pem

ssl_enable=YES

allow_anon_ssl=NO

force_local_data_ssl=YES

force_local_logins_ssl=YES

ssl_tlsv1=YES

ssl_sslv2=NO

ssl_sslv3=NO

require_ssl_reuse=NO

ssl_ciphers=HIGH

sudo systemctl restart vsftpd

#to identify what port is being used

sudo lsof -i -P -n |grep LISTEN


#Filezilla

Sudo apt install filezilla

#activate by

filezilla


#LFTP

Sudo apt install lftp

lftp -d ftp://10.0.2.15 -u username,password -p port

ftp> cd files

ftp>get test.txt

ftp>put test.txt upload.txt

ftp>bye


#DISK PARTITION

Sudo parted /dev/sda1 mkpart primary

File system type? Ext 2

Start? 1

End? 1024

Repeat
