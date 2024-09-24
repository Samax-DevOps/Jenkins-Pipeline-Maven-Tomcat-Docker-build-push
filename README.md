# 1. Tomcat-project

## Install Apache-TOMCAT
```bash
sudo apt update 
sudo apt install openjdk-11-jdk
java -version
```
- get latest tar.gz from [tomcat](https://tomcat.apache.org/download)
```bash
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.28/bin/apache-tomcat-10.1.28.tar.gz        
```


```cmd 
sudo mkdir -p /opt/tomcat
sudo tar xzvf apache-tomcat-*tar.gz -C /opt/tomcat --strip-components=1
```

## Create A Dedicated User
```bash
sudo groupadd tomcat
sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
```

## User Permissions
```bash
sudo chown -R tomcat: /opt/tomcat
sudo sh -c 'chmod +x /opt/tomcat/bin/*.sh'
```

## Create A Systemd Service File For TOMCAT
### * Check Java version
```bash
sudo update-alternatives --config java
```
> /usr/lib/jvm/java-11-openjdk-amd64/bin/java* 

### * Tomcat.service file
```bash
sudo nano /etc/systemd/system/tomcat.service
```



```nano
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
```


```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat
```

## Add Roles and Admin Username and Password 
> sudo nano /opt/tomcat/conf/tomcat-users.xml
```nano
<role rolename="admin-gui,manager-gui,manager-script,manager-jmx,manager-status,admin-gui"/>
<user username="admin" password="admin" roles="admin-gui,manager-gui,manager-script"/>
```

## Enable TOMCAT and Host Manager Remote Access
- **Deployment error so we have to commit these two lines**

> sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
```nano
<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
```


>sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
```nano
<!-- <Valve className="org.apache.catalina.valves.RemoteAddrValve"
allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
```
- **Comment this line** 

>sudo systemctl restart tomcat

## Change TOMCAT Port
```bash
sudo su 
cd /opt/tomcat/conf
sudo nano server.xml
```
---


# 2. JENKINS
- **Java install**
```bash
sudo apt update
sudo apt install openjdk-17-jre  (For Jenkins)
java -version
```
- **Jenkins install**
```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
/usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
```
```bash
sudo apt update
sudo apt-get install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
 - **Firewall Enable**
```bash 
firewall-cmd —zone=public --permanent —add-port=8080/tcp firewall-cmd --reload
```
 *Skip this step*

## Change Jenkins Port  
```bash
sudo nano /etc/sysconfig/jenkins
```
 > look for port 8080 and change to 8081
 
>cat /etc/sysconfig/jenkins |grep JENKINS_PO
```bash
systemctl restart jenkins
systemctl start jenkins
systemctl status jenkins
```
## Password from Jenkins
```bash
Cat /var/lib/jenkins/secrets/initialAdminPassword  
```
>copy the password in this admin and create account
---

# 3. MAVEN
```bash
sudo apt install maven
mvn --version
```

---


### Global Tool Configuration

- JDK| JDK-18= /usr/lib/jvm/java-11-openjdk-amd64
- GIT| /bin/git (which git)
- MVN| 3.9.4

### Creating Job
**Source code management** 
- GIT| Address for the repo ( from Git)
- Credential ( your git hub credentials)
- Branch| */master

### BUILD
Invoke Top-level Maven Targets
Maven Version | mvm-394
Goal | Clean install 
#### Post build Action
- WAR/EAR file | **/*.war
- Context path | / 
- Container | Tomcat 9.x Remote
- Credentials | deployer | deployer 
- Tomcat URL | URL IP with Port 8080






-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
NhAAAAAwEAAQAAAYEA3Q2jkuptdIJ/U2ch+u5IF4M501Azr36yavmXia3mLyGjgmyJ3v3H
uPXFCqe/sQKXCz/314HVR6z2G0re6iYnMV/UEfxI9YmunQJ3mQPOOkLjx+RjKDMYnRWeE9
UorulYAUfVL9GqjT9UtAh0Sbj12od1UghRs/jGyhczATuWCkctExEuQX3l72qVe8c2ioPm
cpi5L5W44jWtJdFRAOtge96/IKGGIqe6aRCje3L5erbopB0REToU9RiF5fBPebAusJ3gK7
/ToY4tQYAZ3NTQRhb9itMew+bpBsRpXBRUETLYft1g2WzHr1nB+IFxiUHS5zQFoKeoRnRk
74ub8fSXC17Svn72WJyVZRRLriLg0P4y7fE5yzZzEf56bxu8qcMGWD07S4hpVWr0ZuosNF
YSQZjRfB8ORIq5tU6EclgKFGuoPHqB2vFId/qF3DU3pf3sK/+Igwyusv87TvSPo2MEMO6j
bbeHP/sinGSEPoRTm0nhm59lI3RTzXKvAxbLc753AAAFkCdqqnEnaqpxAAAAB3NzaC1yc2
EAAAGBAN0No5LqbXSCf1NnIfruSBeDOdNQM69+smr5l4mt5i8ho4Jsid79x7j1xQqnv7EC
lws/99eB1Ues9htK3uomJzFf1BH8SPWJrp0Cd5kDzjpC48fkYygzGJ0VnhPVKK7pWAFH1S
/Rqo0/VLQIdEm49dqHdVIIUbP4xsoXMwE7lgpHLRMRLkF95e9qlXvHNoqD5nKYuS+VuOI1
rSXRUQDrYHvevyChhiKnumkQo3ty+Xq26KQdERE6FPUYheXwT3mwLrCd4Cu/06GOLUGAGd
zU0EYW/YrTHsPm6QbEaVwUVBEy2H7dYNlsx69ZwfiBcYlB0uc0BaCnqEZ0ZO+Lm/H0lwte
0r5+9liclWUUS64i4ND+Mu3xOcs2cxH+em8bvKnDBlg9O0uIaVVq9GbqLDRWEkGY0XwfDk
SKubVOhHJYChRrqDx6gdrxSHf6hdw1N6X97Cv/iIMMrrL/O070j6NjBDDuo223hz/7Ipxk
hD6EU5tJ4ZufZSN0U81yrwMWy3O+dwAAAAMBAAEAAAGAF4GQyFmnZAFQetT3txBJmD57Eq
5voJTPcjKyW5CjbpWcJo1WJ+FCsLdpyZVG/BKzbx3RRBhpTqLk1MgkZi/CcSyoh3UVaQ7I
A6g0gY/3CRj90c7GgIrWbjXTRnafSeJEWnaCBAC+qyB16GMRwpkpg6Bt6Fg8H/Ava2QGJ3
ZnoRYqg+GK0qoJnmjuFmh7s0UNZ1g4MQgxzdQ5YlEc1QUykFmCZ3zln5/IYc3EJKd4QgVb
PcBdhUBw5BNEgcwJF36Vddgwjvw7dJfQu9UaEr7PA68rHDeJdc+bBvysf8RH0JUeV27Xjr
YCjqJmS3FEHb1B+Yix5dLSO+/ecEtfXcJdOYXkwuFuxqFmDxCdfDyvWIBv2B30hY688BAG
JBbV7ZmbwRMqwr65pcjEV523l/oK/4lXz80BdK6k4eXFvozQRwprYyvXtPtZkycv63Fi9V
oGpSXq33EYrcC48CZpSUT+r5w+6NPlErYEP5SRTs/LAhYI8S9nZ0U5CObSUMIks4lBAAAA
wQCGpAfK1yRFBP0XgU3pNE6oHDXYuGuP29jnv/ZWKavqMtwvNMfdCOejeD4uQaapj9Bxpb
yO1bmMbAYQfchkmLjRR7v4tRZ8xndmn+QUy2tE3DzHxw4Hj/eVgtOkdIf2qi+pmZAXvG3H
bvQL2FuMslFXR30tjpyR1G7nL+mMkVp4s6gOYPgOPaB7FUAbETRbzCuhxdkAJ5Z+5eE3jY
iLnjVVc+lkjckxcB6Bpl9XPoJ7sRmw7w/yWYThtnLcThE2zjIAAADBAPAcFACUHjF1Jifi
1542LfeKyOyDGXxcExjOqkCtGIobNh9n0xBDDXJjiVlOqrrUqcFOEwbisRPk2eJUWyGRHN
kERwsGN8nFwysn6Ne2tSVdMQl4FHcT8VFPOBZkGhuJ4MQGnycfbMrc6GwsTMLQizwUcA5b
Ccw4wxBjDJVakQPMAU0WWZ/shr0yIz7DyC6wUkbmxMFIOfRIuZ2RSANLCMkVwRYBPVYcfL
DOUAt7/pdoVC0LJPbvkxaRmesbH+4eVwAAAMEA6661YxZ10iGTJLbK3UdTa6vN7rMkH5K9
S8DLeGKn+MluHeHPQyxAlnwCaHsMfaJmImJG2YNuCo2xUpOm5Kz7nXdMTmi0RSTkgLjT8o
STzgUhweCHY8+LBFmpRXgxzXWr7V6xYLWsQixnI7KyrQHo+FqQetmIwtAypdnP8JXtAM1o
NVMUjCmB1J2WWahNKn4ghN6Tu3NE7DY0qorc9hPxZElYSwvFy6Jec1N2Tortr8Ikzhyfb/
ni+5PJLgG22QzhAAAAFnNhbWF4QHNhbWF4LVZpcnR1YWxCb3gBAgME
-----END OPENSSH PRIVATE KEY-----



