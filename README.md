 Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.317.0.zip", "$PWD")
Exception calling "ExtractToDirectory" with "2" argument(s): "Access to the path
'C:\Users\F2LIPBX\actions-runner\bin\Runner.Listener.exe' is denied."
At line:1 char:59
+ ... ileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : UnauthorizedAccessException
