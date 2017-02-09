# Jenkins

# Step 1: Install Java

Before you can install Jenkins, you need to setup a Java virtual machine on your system. Here, let's install the latest OpenJDK Runtime Environment 1.8.0 using YUM:

sudo yum -y install java

After the installation, you can confirm it by running the following command:

java -version

or

- For 64 Bit:
cd /opt/ 
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-linux-x64.tar.gz"
tar xzf jdk-7u79-linux-x64.tar.gz
- For 32 Bit:
cd /opt/
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/7u79-b15/jdk-7u79-linux-i586.tar.gz"
tar xzf jdk-7u79-linux-i586.tar.gz

Now you should to install Java manually:

cd /opt/jdk1.7.0_79/
alternatives --install /usr/bin/java java /opt/jdk1.7.0_79/bin/java 2
alternatives --config java
alternatives --install /usr/bin/jar jar /opt/jdk1.7.0_79/bin/jar 2
alternatives --install /usr/bin/javac javac /opt/jdk1.7.0_79/bin/javac 2
alternatives --set jar /opt/jdk1.7.0_79/bin/jar
alternatives --set javac /opt/jdk1.7.0_79/bin/javac

After the installation, you can confirm it by running the following command:

  java -version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

Now you should to export variables:

  vim ~/.bashrc

export JAVA_HOME=/opt/jdk1.7.0_79
export JRE_HOME=/opt/jdk1.7.0_79/jre
export PATH=$PATH:/opt/jdk1.7.0_79/bin:/opt/jdk1.7.0_79/jre/bin

Restart bashrc:

  . ~/.bashrc

Step 2: Install Jenkins

Now we can install Jenkins:

The following command download Jenkins from the Red Hat repo:

  sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo

Then, import the verification key using the package manager RPM:

  sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key

Finally, install Jenkins by running:

  sudo yum install jenkins

Now, we are able to start Jenkins as a service:

  sudo systemctl start jenkins.service
  
or

  sudo service start jenkins

Jenkins is running, we can confirm this by visiting the web interface, at http://ip-of-your-machine:8080



Step 3: Install Maven

Download newest version of Maven from: https://maven.apache.org/download.cgi

  tar -xvzf apache-maven-***.tar.gz

  mv apache-maven-***/ /usr/local/

  ln -s /usr/local/apache-maven-***/ /usr/local/maven

  vim ~/.bashrc

- export M2_HOME=/usr/local/maven
  export PATH=${M2_HOME}/bin:${PATH}
  export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.25/jre

Restart bashrc:

  . ~/.bashrc

  chmod +x /usr/local/maven/bin/mvn

Check if Maven works:

  mvn --version


Step 4: Configure Java & Maven & GitLab

Open Jenkins:

 - your-jenkins-ip:8080

Go to:

# Manage Jenkins - Manage Plugins - Available:
	Add "Maven Integration Plugin"
	Add "GitLab Plugin"

# Manage Jenkins - Global Tool Configuration. 
	Click on JDK, then enter NAME = "Java 1.*" and JAVA_HOME = "/opt/jdk1.*"
	Click on Maven, then enter NAME = "Maven ***" and MAVEN_HOME = /usr/local/maven"

# Manage Jenkins - Configure System - GitLab:
	Enter 'Connection Name' = "GitLab"
	Enter 'GitLab Host URL' = "http://your-gitlab-ip
	Create 'Credentials'

STEP - 5 (Create New Node)

# Manage Jenkins - Manage Nodes - New Node:
	Enter 'Node Name'
	Point 'Permanent Agent'
	Enter '# of executors'(how many jobs can be run at the same time)
	Choose 'Launch metod' = "Launch slave agents via SSH" - 'Add'
	
# How to setup a Jenkins slave with ssh private key


			Slave Machine
 [Slave] Create Account for Autobuild
slave:~$ sudo adduser jenkins

1) Create private key and public key
slave:~$ ssh-keygen -t rsa -C "The access key for the slave Jenkins"
(note: Enter passphrase)
Output:
private key: id_rsa         # copy to master 
public key: id_rsa.pub    # copy to slave (.ssh/authorized_keys)

2) Setup Authorized key
	slave:~$ cat id_rsa.pub > ~/.ssh/authorized_keys


	Jenkins Master Machine
3) Setup the Credential key
[Manage Jenkins] -> [Manage Credential] -> [Add Credentials]: 
Select: SSH Username with private key
	
	Info
	# you are on the slave
(a) copy the private key
    slave:~$ cd .ssh;cat id_rsa    

# you are on the Jenkins master
(b) paste to the jenkins master board
      Private key:
      Enter directly: <private key ascii>

