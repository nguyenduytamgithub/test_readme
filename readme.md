//================ Setup Server
npm install --production
git clone https://github.com/node-red/node-red.git
cd node-red
npm install
sudo npm install -g grunt-cli
grunt build
npm start
//upgrade
bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)
cd ~/.node-red
npm rebuild
cd ~/.node-red
npm ls --depth=0
cd ~/.node-red
npm outdated
npm update
// autostart on boot
sudo systemctl enable nodered.service
// Adding nodes to preloaded Raspbian version
sudo apt-get install npm
sudo npm install -g npm
hash -r
cd ~/.node-red
npm install node-red-{example node name}
//================ Setup IP static
sudo nano /etc/dhcpcd.conf

interface eth0
 
static ip_address=192.168.0.10/24
static routers=192.168.0.1
static domain_name_servers=8.8.8.8 208.67.222.222

interface wlan0

static ip_address=192.168.0.200/24
static routers=192.168.0.1
static domain_name_servers=8.8.8.8 208.67.222.222
//================ Setup MQTT broker
wget http://repo.mosquitto.org/debian/mosquitto-repo.gpg.key
sudo apt-key add mosquitto-repo.gpg.key
cd /etc/apt/sources.list.d/
sudo wget http://repo.mosquitto.org/debian/mosquitto-stretch.list
sudo -I // khoi sudo
apt-get update
apt-get install mosquitto
//================ Setup apache2 + php + mysql
sudo apt-get install -t stretch apache2
sudo apt-get install -t stretch php7.0
sudo apt-get install -t stretch php7.0-mysql
sudo nano /var/www/html/info.php
	<?php
	phpinfo();
	?>
Ctr X + Y
//View in localhost and localhost/info.php
sudo int 6 
sudo apt-get install -t stretch mysql-server
sudo apt-get install -t stretch mysql-client
sudo apt-get update
Sudo apt-get install -t stretch phpmyadmin
//Choose apche2
Yes (space)
//password
sudo mysql_secure_installation
question1: no
question2: no 
question3: no
question4: no
question5: yes
mysql -u root -p
GRANT ALL PRIVILEGES on *.* to 'root'@'localhost' IDENTIFIED BY 'NDTpy030397';
FLUSH PRIVILEGES;
\q
service mysql stop 
service mysql start
//======================= Config SSL
Put code in /etc/mosquitto/mosquitto.conf
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
//======================== Generate Key and CA
openssl verify -CAfile ca.crt server.crt
openssl genrsa -out ca.key 2048
openssl req -new -x509 -days 1826 -key ca.key -out ca.crt
openssl genrsa -out server.key 2048
openssl req -new -out server.csr -key server.key
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3600
#generate sig and encrypt to base64
openssl dgst -sha256 -sign key_sign -out signature transaction 
openssl base64 -in signature -out signatureBase64
#decrypt base64 to defaul signature and verify
openssl base64 -d -in signatureBase64 -out signatureDgst
openssl dgst -sha256 -verify ../public -signature sigBase64 transaction
