# tomcat-project

***********************Install Apache-TOMCAT************************************ 
sudo apt update 
sudo apt install openjdk-11-jdk
java -version
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.28/bin/apache-tomcat-10.1.28.tar.gz         (get latest tar.gz from https://tomcat.apache.org/download)

sudo mkdir -p /opt/tomcat
sudo tar xzvf apache-tomcat-*tar.gz -C /opt/tomcat --strip-components=1

*******************Create A Dedicated User****************************************
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat

*****************User Permissions***************************************
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh'


************Create A Systemd Service File For TOMCAT*********************
**************Check Java version************
sudo update-alternatives --config java
*****+ /usr/lib/jvm/java-11-openjdk-amd64/bin/java 

sudo nano /etc/systemd/system/tomcat.service
//////---tomcat.service file---//////




[Unit]
Description=Tomcat Web Servlet Container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

# Restart settings
RestartSec=10
Restart=always

# Environment variables
Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom"

Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

# Use catalina.sh for starting and stopping Tomcat
ExecStart=/opt/tomcat/bin/catalina.sh start
ExecStop=/opt/tomcat/bin/catalina.sh stop

[Install]
WantedBy=multi-user.target




sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat


**************Add Roles and Admin Username and Password**************************** 
sudo nano /opt/tomcat/conf/tomcat-users.xml
<role rolename="admin-gui,manager-gui,manager-script,manager-jmx,manager-status,admin-gui"/>
<user username="admin" password="admin" roles="admin-gui,manager-gui,manager-script"/>

**************Enable TOMCAT and Host Manager Remote Access****************************
*************deployment error so we have to commit these two lines*****
sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Comment this line 

sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml

<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
Comment this line 

sudo systemctl restart tomcat

***************Change TOMCAT Port to 8000*****************************
sudo su 
cd /opt/tomcat/conf
sudo nano server.xml





****************Install JENKINS***************************************
sudo apt update
sudo apt install openjdk-17-jre  (For Jenkins)
java -version

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

***************Not Required*************************************** 
firewall-cmd —zone=public --permanent —add-port=8080/tcp firewall-cmd --reload
***************Skip this step*************************************

*****************Change jenkins port to 8081************** 
sudo nano /etc/sysconfig/jenkins  ( look for port 8080 and change to 8081)
cat /etc/sysconfig/jenkins |grep JENKINS_PO

systemctl restart jenkins
systemctl start jenkins
systemctl status jenkins

******************************Password from Jenkins*********************
Cat /var/lib/jenkins/secrets/initialAdminPassword  ( copy the password in this admin and create account)


*******************Install Maven**************************************
sudo apt install maven
mvn --version





****************************Global Tool Configuration********
JDK| JDK-18= /usr/lib/jvm/java-11-openjdk-amd64
GIT| /bin/git (which git)
MVN| 3.9.4

****************************Creating Job************
Source code management 
GIT| Address for the repo ( from Git) 
Credential ( your git hub credentials)
Branch| */master
**********************BUILD************
Invoke Top-level Maven Targets
Maven Version | mvm-394
Goal | Clean install 
************************Post build Actio****************
WAR/EAR file | **/*.war
Context path | / 
Container | Tomcat 9.x Remote
Credentials | deployer | deployer 
Tomcat URL | URL IP with Port 8080











-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----



