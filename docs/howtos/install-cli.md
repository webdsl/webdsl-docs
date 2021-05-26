# Install and Use the WebDSL CLI

Download the WebDSL CLI for your platform:

[:fontawesome-solid-download: WebDSL CLI](https://buildfarm.metaborg.org/job/webdsl-compiler/lastSuccessfulBuild/artifact/webdsl.zip){ .md-button .md-button--primary }


## Installation
An installation of [Java 8][1] or newer and [Apache Ant][2] are required.

1.  Extract the zip file.
2.  Add the `webdsl/bin` directory to your `$PATH`.


## Usage
Go to the directory of your application and execute:

```bash
webdsl run appname.app
```

!!! warning ""
    This will override any existing `application.ini` file.

This generates an `application.ini` file configured for testing with an [H2 in-memory database][3].

If there is already an `application.ini` file with configuration (see Application Configuration options), use instead:

```bash
webdsl run
```

Pressing ++ctrl+c++ stops the application.

??? tip "Faster compilation on command-line"
    You can avoid JVM startup overhead by keeping the WebDSL compiler process running.

    This requires having the [Nailgun][4] client installed. For example, on {{ os.macos }} you can install `nailgun` using `brew install nailgun`.

    Add to the `application.ini` configuration file of your project the following line to use the compile server for all the commands like `webdsl run`:

    ```ini
    usecompileserver=true
    ```

    To start the WebDSL nailgun server process, invoke:

    ```bash
    webdsl start
    ```




[1]: https://adoptopenjdk.net/
[2]: https://ant.apache.org/bindownload.cgi
[3]: https://www.h2database.com/
[4]: http://www.martiansoftware.com/nailgun/