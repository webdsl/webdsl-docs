# WebDSL
WebDSL is a domain-specific language for modeling web applications with a rich data model.

## Installation
The recommended way to get started with WebDSL is to install the WebDSL [Eclipse][1] plugin. The plugin also includes the WebDSL compiler. Choose the _Eclipse Bundle_ installation (recommended), the _Eclipse Plugin_ installation, or the _Command-Line_ installation:

=== "Eclipse Bundle"
    Download an Eclipse instance with the latest WebDSL plugin pre-installed for your platform:

    [:fontawesome-solid-download: WebDSL in Eclipse bundle](https://buildfarm.metaborg.org/view/WebDSL/job/webdsl-eclipsegen/lastSuccessfulBuild/artifact/dist/eclipse/){ .md-button .md-button--primary }

    [Installation instructions](howtos/install-eclipse-bundle.md).

=== "Eclipse Plugin"
    Perform a manual installation of the WebDSL plugin in [Eclipse 3.5][1] or newer through the update site:

    ```
    https://webdsl.org/update
    ```

    [Installation instructions](howtos/install-eclipse-plugin-manually.md).

=== "Command-Line"
    Download the WebDSL CLI for your platform:

    [:fontawesome-solid-download: WebDSL CLI](https://buildfarm.metaborg.org/job/webdsl-compiler/lastSuccessfulBuild/artifact/webdsl.zip){ .md-button .md-button--primary }

    [Installation instructions](howtos/install-cli.md).


## Quick Start
Once installed and started, create a new WebDSL project:

1.  Right-click the _Package Explorer_ and select _New WebDSL Project_ or _Example WebDSL Project_.
2.  Provide a name and click _Finish_.
3.  Open the main `projectname.app` file and press ++ctrl+alt+b++ (++cmd+alt+b++ on macOS) to build and deploy the project.

A browser window opens when the application is deployed.


[1]: https://www.eclipse.org/