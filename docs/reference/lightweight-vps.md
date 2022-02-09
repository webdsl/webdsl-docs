# Lightweight VPS

WebDSL applications can be deployed on a light-weight server or VPS. Whether performance is acceptable depends on many factors such as complexity of the application, number of users, capacity of the server. Currently, the ram usage is usually the limiting factor. The JVM halts or gets stuck when the max heap space limit is crossed (-Xmx setting). 512mb ram, typically the lowest VPS option, can run a simple WebDSL application, but getting 1gb or 2gb is recommended.

In the rest of this section is a walkthrough of the minimal steps required for installation of a WebDSL application on an Ubuntu Server (this was for a VPS with 1gb ram).

Update packages library (run all apt-get commands as root or with sudo):

    apt-get update

Install MySQL:

    apt-get install mysql-server

Enter a password for the mysql root account.

Install Java, Tomcat, and other requirements for running the WebDSL compiler:

    apt-get install ant unzip openjdk-7-jdk tomcat7

Get WebDSL compiler:

    wget http://hydra.nixos.org/job/webdsl/trunk/buildJavaZip/latest/download/1/webdsl-java.zip
    unzip webdsl-java.zip
    chmod +x webdsl/bin/webdsl
    export PATH=$PATH:/[path]/webdsl/bin/

Add the export PATH line to your ~/.bashrc file to make the 'webdsl' command work the next time you log in as well.

Install mail SMTP server:

    apt-get install postfix

Choose the internet configuration, test locally with 'sendmail' command. If something is wrong in the configuration, change it with:

    sudo dpkg-reconfigure postfix
    /etc/init.d/postfix reload

Configure WebDSL application, create application.ini:

    appname=myapp
    backend=servlet
    tomcatpath=/var/lib/tomcat7/
    httpport=8080
    httpsport=8443
    dbmode=update
    indexdir=/var/indexes/
    dbserver=localhost
    dbname=mydb
    dbuser=myuser
    dbpassword=mypass
    smtphost=localhost
    smtpport=25
    smtpprotocol=smtp
    smtpauthenticate=false
    rootapp=true

If using a gmail account to send mail instead of local SMTP server, use:

    smtphost=smtp.gmail.com
    smtpport=465
    smtpuser=blabla
    smtppass=thepass

Create database and mysql user:

    mysql -u root -p
    create database mydb;
    grant all privileges on mydb.* to myuser@'localhost' identified by 'mypass';
    flush privileges;
    quit

Open up the indexes directory (can be placed anywhere):

    mkdir /var/indexes
    chown -R tomcat7 /var/indexes

Compile application (in this application.ini myapp.app is the main file) and deploy:

    webdsl build deploy

Check what's going on in Tomcat using:

    tail -f /var/lib/tomcat7/logs/catalina.out

Set Tomcat's heap higher:

    nano /etc/default/tomcat7

Change

    JAVA_OPTS="-Djava.awt.headless=true -Xmx128m -XX:+UseConcMarkSweepGC"

to

    JAVA_OPTS="-Djava.awt.headless=true -Xmx768m -XX:+UseConcMarkSweepGC"

Restart Tomcat:

    /etc/init.d/tomcat7 restart

Check Tomcat's current JVM arguments:
  
    ps aux | grep tomcat

Tomcat will run on port 8080 instead of 80, a quick fix to get it to work on port 80 is the following:

    iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080