# Install the Eclipse with WebDSL Plugin Bundle
Install an Eclipse instance with the WebDSL plugin pre-installed for your platform:

[:fontawesome-solid-download: WebDSL in Eclipse bundle](https://buildfarm.metaborg.org/view/WebDSL/job/webdsl-eclipsegen/lastSuccessfulBuild/artifact/dist/eclipse/){ .md-button .md-button--primary }



## Troubleshooting

### {{ os.macos }}: "Eclipse" cannot be opened because the developer could not be verified
macOS puts unverified binaries in 'quarantine' and disallows their execution. To remove the `com.apple.quarantine` attribute, do:

```bash
xattr -rc Eclipse.app
```

### Eclipse does not start, or complains about missing Java
Ensure you have [a distribution of Java][1] installed. Then in `eclipse.ini`, add a `-vm` line at the top of the file, followed by the path to the Java installation. For example, with SDKMan! on macOS:

```
-vm
/Users/myusername/.sdkman/candidates/java/current/jre/lib/jli/libjli.dylib
```


[1]: https://adoptopenjdk.net/