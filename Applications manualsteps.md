sudo apt-get update -y
sudo apt-get install openjdk-8-jdk -y
java -version

Install Tomcat 7
----------------

### First, create a user and group for Tomcat with the following command:
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat

### Next, download the Tomcat 7 with the following command:
wget https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.109/bin/apache-tomcat-7.0.109.tar.gz

### Next, create a directory for Tomcat and extract the downloaded file to /opt/tomcat directory:
sudo mkdir /opt/tomcat
sudo tar -xvzf apache-tomcat-7.0.109.tar.gz -C /opt/tomcat/ --strip-components=1

### Next, navigate to the /opt/tomcat directory and set proper permission and ownership:
-----------------------------------------------------------------------------------------
sudo cd /opt/tomcat
sudo chgrp -R tomcat /opt/tomcat
sudo chmod -R g+r conf
sudo chmod g+x conf
sudo chown -R tomcat webapps/ work/ temp/ logs/



Create a Systemd Service File for Tomcat
----------------------------------------
### Next, you will need to create a systemd service file to manage the Tomcat service. You can create it with the following command:

sudo nano /etc/systemd/system/tomcat.service

Add the following lines:
**************************************
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target
[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment=’CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC’
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target
***************************************

### Save and close the file then reload the systemd daemon to apply the changes:
sudo systemctl daemon-reload

### Next, start the Tomcat service with the following command:
sudo systemctl start tomcat
sudo systemctl status tomcat

$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$
Install OpenMRS
----------------
### First, create a directory for OpenMRS and set proper ownership with the following command:
sudo mkdir /var/lib/OpenMRS
sudo chown -R tomcat:tomcat /var/lib/OpenMRS

### Next, download the latest version of OpenMRS using the following command:
wget https://sourceforge.net/projects/openmrs/files/releases/OpenMRS_Platform_2.5.0/openmrs.war

### Once the download is completed, copy the downloaded file to the Tomcat webapps directory:
sudo cp openmrs.war /opt/tomcat/webapps/

### Next, change the ownership of the openmrs.war file to tomcat:
sudo chown -R tomcat:tomcat /opt/tomcat/webapps/openmrs.war


### Access OpenMRS Installation Wizard
http://your-server-ip:8080/openmrs


-$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$
######  Mysql Server Installation:

sudo apt update
sudo apt install mysql-server
sudo mysql
# In the mysql shell try to execute following commands
CREATE USER 'nop'@'localhost' IDENTIFIED BY 'nop12345';
GRANT ALL PRIVILEGES ON *.* TO 'nop'@'localhost';
FLUSH PRIVILEGES;
exit

To verify the nop execute
mysql -u nop -p -----enter password and you should be allowed in sql shell


======================================================================================================================
-------------------------------------
###### Nop commerce installations:
-----------------------
It reqiure .Net (Ubuntu 20.04)(https://learn.microsoft.com/en-us/dotnet/core/install/linux-debian#dependencies)

### install Dot net :
---
-->wget https://packages.microsoft.com/config/debian/10/packages-microsoft-prod.deb -O packages-microsoft-prod.deb

sudo dpkg -i packages-microsoft-prod.deb

rm packages-microsoft-prod.
-->sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-7.0
  dotnet --helpdotnet --help
---
### To install Nop

---
sudo apt install unzip -y
 sudo mkdir /usr/share/nopCommerce
 cd /usr/share/nopCommerce
 
 Download and unpack nopCommerce:
 ---------------------------------

sudo wget https://github.com/nopSolutions/nopCommerce/releases/download/release-4.60.3/nopCommerce_4.60.3_NoSource_linux_x64.zip

sudo apt-get install unzip

sudo unzip nopCommerce_4.60.3_NoSource_linux_x64.zip

Create couple directories to run nopCommerce:
---------------------------------------

sudo mkdir bin
sudo mkdir logs
sudo chmod 777 -R /usr/share/nopCommerce/App_Data/
dotnet Nop.Web.dll --urls "http://0.0.0.0:5000"

sudo /usr/bin/dotnet Nop.Web.
sudo /usr/bin/dotnet Nop.Web.dll --urls "http://0.0.0.0:5000"

-----------------------------------------------------------
Now Create a user
sudo adduser nop
Giving full permissions to user '/usr/share/nopCommerce'
* Change the file permissions:

cd ..
sudo chgrp -R nop /usr/share/nopCommerce/
sudo chown -R nop /usr/share/nopCommerce/

* create a file in '/etc/systemd/system/nopCommerce.service' with following content
* ------------------------
[Unit]
Description=Example nopCommerce app running on Xubuntu

[Service]
WorkingDirectory=/usr/share/nopCommerce
ExecStart=/usr/bin/dotnet /usr/share/nopCommerce/Nop.Web.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=nopCommerce-example
User=nop
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=ASPNETCORE_URLS=http://0.0.0.0:5000
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target


------------------------------------------
sudo systemctl daemon-reload
sudo systemctl enable nopCommerce.service
sudo systemctl start nopCommerce.service
Check the nopCommerce service status:

sudo systemctl status nopCommerce.service

* now access application http:// ip-address:5000
  ================================================
  ============================================
  * allow remote access mysql from any ip
  * how to create a mysql database with remote access to all databases
============================================
* Enabling mysql connections from any where

Enabling mysql connections from anywhere Refer Here
Step 1:
The first thing we must do is configure MySQL for remote connections. To do this, log into your MySQL database server and open the configuration file with the command:
---
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

In that file, look for the line:

bind-address = 127.0.0.1

Change that line to:

bind-address = 0.0.0.0

Save and close the file. Restart the MySQL service with:

sudo systemctl restart mysql
---
* Step 2: launch mysql shell 

sudo mysql
CREATE USER 'nop'@'%' IDENTIFIED BY 'nop12345';
GRANT ALL PRIVILEGES ON *.* to 'nop'@'%';
FLUSH PRIVILEGES;
exit

&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&7
### Now to connect to mysql server from other servers followind command used

mysql --hosts=34.216.73.109 -u nop -p


@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$4
#### GAME OF LIFE #######################################
----Game of life requires Java-8 & tomcat-9----
sudo apt udate 
sudo apt install openjdk-8-jdk maven -y
 sudo apt install tomcat9 -y
git clone https://github.com/wakaleo/game-of-life.git
# Now update the java-17 wit java-8 by supply environment variables.
ls -al /usr/bin/java
whereis java
ls -al /etc/alternatives/java
![pre]
sudo vi ~/.bashrc(at end of page insert below command)
export PATH="/usr/lib/jvm/java-11-openjdk-amd64/bin:$PATH"
mvn package
source ~/.bashrc
cd gameoflife
mvn package
$ Now check in browser as"http://<external IP>:8080/gameoflife

&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
##### SPRING PET CLINIC  #############################
 This application reqires Java-17

 sudo apt update 
 
 sudo apt install openjdk-17-jdk maven -y
 git clone https://github.com/spring-projects/spring-petclinic.git
 cd spring-petclinic
 mvn package -Dmaven.test.failure.ignore=true
 cd target/
 ls
 java -jar spring-petclinic-3.1.0-SNAPSHOT.jar(To run the Applicaton[double click])


 @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

 ### 

















