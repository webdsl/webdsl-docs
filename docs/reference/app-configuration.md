# App Configuration

In the _application.ini_ file compile-, database- and deployment information is stored. Executing the _webdsl_ command in a certain directory will look for a _application.ini_ file to obtain compilation information. If no such file was found, it will start a simple wizard to create one. 

Example _application.ini_:

    backend=servlet
    tomcatpath=/opt/tomcat
    appname=hello
    dbserver=localhost
    dbuser=webdsluser
    dbpassword=webdslpassword
    dbname=webdsldb
    dbmode=update
    smtphost=localhost
    smtpport=25
    smtpuser=
    smtppass=

Required Configuration
----

**backend** The back-end target platform of the application. Currently, the **servlet** back-end is only up-to-date.

**appname** The name of the application to compile. The compiler will look for a APPNAME.app file to compile. This name will also become the servlet name and show up as part of the URL. By renaming the generated APPNAME.war file to ROOT.war and then deploying it, the application name will not be in the URL.

**tomcatpath** This field should contain the root directory of the Tomcat installation. For example /opt/tomcat. It is used when executing 'webdsl deploy'.

Database Configuration MySQL
----

**dbmode** This field indicates if the application should try to create tables in a database, or try to sync it with the existing schema to avoid loss of data. Valid values are **create-drop**, **update**, and **false**. Update can lead to unpredictable results if data model is changed too much, if data needs to be properly migrated, use [[page(Acoda)|Acoda]] instead. For production deployment use 'export DBMODE=false'.

**dbserver** Location of the Mysql server, which will be used in the connection URL, e.g. 'localhost'.

**dbuser** User to be used for connecting to the MySQL database.
 
**dbpassword** Password for the specified user.

**dbname** Database name, note that the database needs to exist when the application is run. The 'webdsl' script will try to create the database in the wizard, but manually creating it via command-line or MySQL Administrator is also possible.

Database Configuration H2 Database Engine in file
----

**db** Set `db=h2` to enable H2 Database Engine instead of the default MySQL.

**dbfile** H2 database file, an empty file will be populated with tables automatically, when using 'create-drop' or 'update' db modes.

**dbmode** Same as for MySQL.

Database Configuration H2 Database Engine in memory
----

**db** Set `db=h2mem` to enable in-memory H2 Database Engine instead of the default MySQL.

**dbmode** Same as for MySQL, although effectively the tables are always dropped after a restart with in-memory database

Database Configuration through JNDI
----

**db** Set `db=jndi` to retrieve a JDBC resource from the application server, rather than providing the configuration in the web application.

**dbjndipath** JNDI path to the JDBC resource. On Apache Tomcat this is typically prefixed by 'java:comp/env'. An example may be: 'java:comp/env/jdbc/mydatabase'

**dbmode** Same as for MySQL.

Apart from settings in the application.ini, also a Context XML file must be provided for Apache Tomcat. An example may be:

    <Context>
        <Resource name="jdbc/mydatabase"
            auth="Container"
            type="javax.sql.DataSource"
            driverClassName="com.mysql.jdbc.Driver"
            maxActivate="100" maxIdle="30" maxWait="10000"
            username="root" password="dbpassword"
            url="jdbc:mysql://localhost:3306/mydatabase?useServerPrepStmts=false&amp;characterEncoding=UTF-8&amp;useUnicode=true&amp;autoReconnect=true" />
    </Context>

This XML file must be stored in: $TOMCAT_BASE/conf/Catalina/localhost/&lt;appname&gt;.xml

Email Configuration
----

**smtphost** SMTP host for sending email, e.g. smtp.gmail.com

**smtpport** SMTP port for sending email, e.g. 465

**smtpuser** SMTP username

**smtppass** SMTP password

**smtpprotocol** `smtpprotocol=smtps` [smtp/smtps] Use smtp or smtps as protocol.

**smtpauthenticate** `smtpauthenticate=true` [true/false] Authenticate with a username and password.

Search Configuration
----

**indexdir** set the index directory, default is /var/indexes.

**searchstats** Enable/disable search statistics, which can be displayed using template showSearchStats(). Default is false.

Optional Configuration
----

**rootapp** `rootapp=true` will deploy the application as root application, it will not have the application name prefix in the URL.

**wikitext-hardwraps** `wikitext-hardwraps=true` will enable so-called _hard wraps_ in markdown. This way, each newline which isn't followed by 2 white spaces is also rendered as new line. Default is _false_. See http://yellowgrass.org/issue/WebDSL/818

**appurlforrenderwithoutrequest** (as of WebDSL 1.3.0) Sets the URL to be used when links to pages are to be rendered outside a request. Normally, WebDSL will construct links using the request URL as a base. In case pages or templates with links are to be rendered outside a request (e.g. using a background task), WebDSL will use this property value as the base url.

**sessiontimeout** Sets the session timeout, specified in minutes.

**javacmem** `javacmem=3G` set javac max memory for compilation of generated Java classes

**debug** `debug=true` will show queries and Java exception stacktraces in the log.

**verbose** `verbose=2` will show more info during compilation, mainly for developers.

**fastpp** `fastpp=true` will make the compiler write Java code faster (writing files stage), however, it also becomes less readable. (only for C-based back-end of the WebDSL compiler)

Deploy with Tomcat Manager
----

For the `webdsl tomcatdeploy` and `webdsl tomcatundeploy` commands to work, a user has to be configured in Tomcat (tomcat/conf/tomcat-users.xml). For example:

    <tomcat-users>
      <role rolename="manager"/>
      <user username="tomcat" password="tomcat" roles="manager"/>
    </tomcat-users>

The tomcat manager URL and username and password can be set in the application.ini file (defaults are listed as examples):

**tomcatmanager** `tomcatmanager=http:\\localhost:8080\manager` URL to Tomcat manager

**tomcatuser** `tomcatuser=tomcat` manager user declared in tomcat/conf/tomcat-users.xml

**tomcatpassword** `tomcatpassword=tomcat` password for that user