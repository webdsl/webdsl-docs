# Production Server

Tomcat
----

We use the following settings for Tomcat on our production server (NixOS/Linux):

    -Xms350m 
    -Xss8m 
    -Xmx8G 
    -Djava.security.egd=file:/dev/./urandom 
    -XX:MaxPermSize=512M 
    -XX:PermSize=512M 
    -XX:-UseGCOverheadLimit 
    -XX:+UseCompressedOops
    -XX:+HeapDumpOnOutOfMemoryError 
    -XX:HeapDumpPath=/var/tomcat/logs/heapdump.hprof

Details
----

    -Xmx8G 

The most important setting, maximum heap space, value depends on size/number of applications, but the default setting is usually too low. If this setting is too high for your JVM, it won't start at all.

    -XX:MaxPermSize=512M 

This allows redeploying the application without running into permgenspace errors too quickly.

    -Djava.security.egd=file:/dev/./urandom 

The default implementation for random can be too slow (java.util.UUID.randomUUID is used for entity identifiers, including RequestLogEntry) see [http://stackoverflow.com/questions/137212/how-to-solve-performance-problem-with-java-securerandom](http://stackoverflow.com/questions/137212/how-to-solve-performance-problem-with-java-securerandom)

Monitoring and Debugging
----

Use `jvisualvm` to inspect the Tomcat process, this allows you to look at the heap and running threads and create dumps for later inspection. A heap dump can also be created using:

    jmap -F -dump:format=b,file=<filename> <process id>

The Eclipse Memory Analyzer can be used to inspect this file, get it from [http://www.eclipse.org/mat/](http://www.eclipse.org/mat/)

If the tomcat process becomes unresponsive try

    kill -3 <process id>

to generate a thread dump in the catalina.out log.

MySQL
----

See the [MySQL](../mysql) page.

Lightweight VPS
----

See the [Lightweight VPS](../lightweight-vps) page, it also contains a step-by-step installation guide.
