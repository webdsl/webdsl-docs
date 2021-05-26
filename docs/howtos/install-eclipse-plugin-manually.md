# Install the WebDSL Eclipse Plugin
Perform a manual installation of the WebDSL plugin in [Eclipse 3.5][1] or newer.
    
1.  In Eclipse, go to menu _Help_ --> _Install New Software_.
2.  In the _Work with:_ text area, type:

    ```
    https://webdsl.org/update
    ```

3.  Uncheck _Group items by category_ to make the plugin visible.
4.  Check _WebDSL Editor_.
5.  Click _Install_ and go through the remaining steps.
6.  Restart Eclipse.

!!! warning ""

    It is recommended that the `eclipse.ini` of Eclipse is updated to give WebDSL enough stack space and memory to function correctly. Include the following options in `eclipse.ini`, below the line that starts with `-vmargs`.

    ```
    -Xss8m -Xms256m -Xmx1024m -XX:MaxPermSize=256m -server
    ```

    On {{ os.macos }} this file can be found at `Eclipse.app/Contents/MacOS/eclipse.ini`.

[1]: https://www.eclipse.org/