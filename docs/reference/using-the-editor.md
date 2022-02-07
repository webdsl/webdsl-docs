# Using the Editor

This is the WebDSL language reference. For more background information on the ideas, architecture, and design decisions behind WebDSL, see the [Background](../background/) section.

## Download and Installation

See [Install WebDSL](../howtos/install/).

## New Project Wizard

See [Hello World! in WebDSL](../tutorials/hello-world/).

The plugin includes a new project wizard which will help you get started using WebDSL:

- Right-click in the package explorer and select 'New WebDSL Project' (mostly empty project, shows ‘hello world’) or ‘Example WebDSL Project’ (a small example project)
- Enter project name
- Select either MySQL or H2 database and enter the required database info (H2 is recommended for first-time users because it doesn’t require extra installation steps).
- Check “overwrite database when deployed”, “WTP Tomcat” and click Finish (these settings can be easily changed later on by using the ‘Convert to a WebDSL Project’)
- The WebDSL project is created, execute the first build (ctrl+alt+b or cmd+alt+b), the application is deployed on an internal Tomcat and the server is started.
- Go to `http://localhost:8080/{projectname}` to see the result.
- Make changes to the app and build the project (ctrl+alt+b or cmd+alt+b), it is automatically deployed.
- use clean-project.xml to clean the project’s generated files before committing to version control
- alternatively, switch to deploy to external tomcat setting. Specify the location of tomcat (without …/webapps/), then start tomcat using bin/catalina.sh run (Mac/Linux) or bin/catalina.bat run (Windows).

## Setting up projects from version control or repairing generated build files

Use the ‘Convert to a WebDSL Project’ wizard to regenerate the project build files (this will overwrite the old files, including application.ini).

## Troubleshooting

If you encounter issues when running the plugin, here are a few things that you should check or try:

- Do you have a Java JDK 6 installed?
- Have you tried installing the plugin in a clean Eclipse classic distribution?
- “Transaction not successfully started” error in log -> check db settings in application.ini, see App Configuration
- “Dispatchservlet class not found” -> rebuild the project and check whether automatic project build of eclipse is enabled
- Currently, renaming the project in eclipse is not fully supported, check .settings/*.* files and application.ini for project references if you want to rename.
- “Project Facet Java version 6.0 not supported” error, set Eclipse -> Preferences -> Installed JREs to Java 6 or 1.6
- Tomcat hangs and shows the following error “java.lang.OutOfMemoryError: PermGen space”. You can recover from this by killing the Tomcat process (unfortunately it is just listed as ‘java’) and start it again. To prevent this error or at least postpone it, right click on your project -> Run As -> Run Configurations… -> click on tomcat instance in tree pane on the left-> click on ‘Arguments’ tab -> add “-XX:MaxPermSize=512m” to ‘VM arguments’. Similarly, if Tomcat gives a HeapSpaceError, add “-Xmx1024m” to these options (adjust downwards for low-memory systems).
- Report issues here: http://yellowgrass.org/project/WebDSL. You can also subscribe to the mailing https://mailman.st.ewi.tudelft.nl/listinfo/webdsl and report your issue, or go to the #webdsl channel on irc.freenode.net


