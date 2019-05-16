# Setup Server
npm install --production <br/>
git clone https://github.com/node-red/node-red.git <br/>
cd node-red <br/>
npm install <br/>
sudo npm install -g grunt-cli <br/>
grunt build <br/>
npm start <br/>
//===== upgrade <br/>
bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)  <br/>
cd ~/.node-red <br/>
npm rebuild <br/>
cd ~/.node-red <br/>
npm ls --depth=0 <br/>
cd ~/.node-red <br/>
npm outdated <br/>
npm update <br/>
//====== autostart on boot <br/>
sudo systemctl enable nodered.service <br/>
//====== adding nodes to preloaded Raspbian version <br/>
sudo apt-get install npm <br/>
sudo npm install -g npm <br/>
hash -r <br/>
cd ~/.node-red <br/>
npm install node-red-{example node name} <br/>
# Setup IP static
sudo nano /etc/dhcpcd.conf
```
interface eth0
 
static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=8.8.8.8 208.67.222.222

interface wlan0

static ip_address=192.168.0.200/24
static routers=192.168.0.1
static domain_name_servers=8.8.8.8 208.67.222.222
```
# Setup MQTT broker
wget http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key <br/>
sudo apt-key add mosquitto-repo.gpg.key <br/>
cd /etc/apt/sources.list.d/ <br/>
sudo wget http://repo.mosquitto.org/debian/mosquitto-stretch.list <br/>
sudo -I <br/>
apt-get update <br/>
apt-get install mosquitto <br/>
# Setup apache2 + php + mysql
sudo apt-get install -t stretch apache2 <br/>
sudo apt-get install -t stretch php7.0 <br/>
sudo apt-get install -t stretch php7.0-mysql <br/>
sudo nano /var/www/html/info.php <br/>
```
	<?php
	phpinfo();
	?>
```	
Ctr X + Y <br/>
sudo int 6 <br/>
sudo apt-get install -t stretch mysql-server <br/>
sudo apt-get install -t stretch mysql-client <br/>
sudo apt-get update <br/>
Sudo apt-get install -t stretch phpmyadmin <br/>
//Choose apche2 <br/>
Yes (space) <br/>
//password <br/>
sudo mysql_secure_installation <br/>
question1: no <br/>
question2: no  <br/>
question3: no <br/>
question4: no <br/>
question5: yes <br/>
mysql -u root -p <br/>
GRANT ALL PRIVILEGES on *.* to 'root'@'localhost' IDENTIFIED BY 'NDTpy030397'; <br/>
FLUSH PRIVILEGES; <br/>
\q <br/>
service mysql stop <br/>
service mysql start <br/>
# Config SSL/TLS
Put code in /etc/mosquitto/mosquitto.conf <br/>
```
	port 8883 
	pid_file /var/run/mosquitto.pid 
	persistence true 
	persistence_location /var/lib/mosquitto/
	log_dest file /var/log/mosquitto/mosquitto.log
	include_dir /etc/mosquitto/conf.
	cafile /etc/mosquitto/ca_certificates/ca.crt 
	keyfile /etc/mosquitto/certs/client.key 
	certfile /etc/mosquitto/certs/client.crt  
	require_certificate true  
	use_identity_as_username true
	tls_version tlsv1 
```
# Generate Key and CA
openssl verify -CAfile ca.crt server.crt <br/>
openssl genrsa -out ca.key 2048 <br/>
openssl req -new -x509 -days 1826 -key ca.key -out ca.crt <br/>
openssl genrsa -out server.key 2048 <br/>
openssl req -new -out server.csr -key server.key <br/>
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3600 <br/>
//=== generate sig and encrypt to base64 <br/>
openssl dgst -sha256 -sign key_sign -out signature transaction <br/>
openssl base64 -in signature -out signatureBase64 <br/>
//=== decrypt base64 to defaul signature and verify <br/>
openssl base64 -d -in signatureBase64 -out signatureDgst <br/>
openssl dgst -sha256 -verify ../public -signature sigBase64 transaction <br/>
