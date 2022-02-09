# HTTPS Encryption

note: this section is outdated, it is recommended to configure HTTPS on the Nginx or Apache Httpd in front of tomcat, using HSTS policy to force all traffic over HTTPS

## Tomcat Configuration

Using https requires some extra configuration when deploying to an external tomcat server, the tomcat instance used in the plugin and command-line test and run commands is already configured (note: this uses a dummy configuration which should not be used in production deployment of the app). Follow these steps to configure Tomcat 6:

Run this command and follow the instructions (note down the password):

    %JAVA_HOME%\bin\keytool -genkey -alias tomcat -keyalg RSA

Then, in tomcat/conf/server.xml add (use the password entered in the keytool):

    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
      maxThreads="150" scheme="https" secure="true"
      keystoreFile="${user.home}/.keystore" keystorePass="--password--"
      clientAuth="false" sslProtocol="TLS" />	

Read more about this topic here:
[http://tomcat.apache.org/tomcat-6.0-doc/ssl-howto.html](http://tomcat.apache.org/tomcat-6.0-doc/ssl-howto.html)
