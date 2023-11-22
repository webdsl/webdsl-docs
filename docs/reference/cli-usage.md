# CLI Usage

Running an application using the command-line interface
----

The quickest way to get an application running is to execute:

    webdsl run appname

This will generate an application.ini file with default settings, then compile the application, and start a Tomcat instance on port 8080 with the application deployed.

If there is already an application.ini file with settings that have to be used, execute:

    webdsl run

This will also build and run, using the settings in the existing application.ini file.

To create just the war file instead, use:

    webdsl war

Building .war file and deploying to external Tomcat
----

The installation of WebDSL will result in a *webdsl* script and a directory with templates being added to your install location. The script is used to invoke the compilation and deployment of WebDSL applications. 

In your console, go to the location of the main .app file and invoke the webdsl script with 

    webdsl build

The script uses an application.ini file for configuration. If an application.ini file is not in the current directory, the script will offer an interactive way to generate it. If the application.ini is available it will be used to configure the application with e.g. database connection settings. The compilation begins by creating a .servletapp directory to which the WebDSL template, the application files, and the static resources are copied. Then the actual WebDSL compiler, webdslc, is invoked. This will either produce an error and halt, or it will produce the source code of a java web application. Upon a successful run of the webdsl compiler, the script will compile the java code, and build a war file. This war file can be copied manually to the tomcat /webapps dir, or it can be uploaded through the web deploy interface of tomcat. If the tomcat path is set in application.ini, then

    webdsl deploy

will copy the war file to the /webapps directory. 

If you have updated webdsl and need to copy the new WebDSL template in .servletapp use

    webdsl cleanall

to remove the .servletapp directory (or simply delete it with rm) and then do a build.

The script commands can be combined, e.g. 

    webdsl cleanall build deploy

to clean the generated directory and its contents, regenerate, and deploy.

Example Application
----

1 create a hello.app file

hello.app:

    application test

    page root(){
      "Hello world"
    }

create or generate application.ini:

    backend=servlet
    tomcatpath=**path to your tomcat directory e.g. /Apps/tomcat/**
    appname=hello
    dbserver=localhost
    dbuser=**mysql user account, e.g. root**
    dbpassword=**password**
    dbname=webdsldb
    dbmode=create-drop
    smtphost=localhost
    smtpport=25
    smtpuser=
    smtppass=
 
2 create the database

mysql -u root -p

create database webdsldb;

exit

3 start tomcat in another shell:

catalina.sh run (stop with cmd/ctrl+c)

or in the background

catalina.sh start (stop with catalina.sh stop)

4 compile and deploy WebDSL app

webdsl cleanall deploy

5 open browser and go to http://localhost:8080/hello
