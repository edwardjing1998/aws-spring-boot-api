Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.317.0.zip", "$PWD")
Exception calling "ExtractToDirectory" with "2" argument(s): "The file 'C:\Users\F2LIPBX\actions-runner\config.cmd'
already exists."
At line:1 char:59
+ ... ileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodInvocationException
    + FullyQualifiedErrorId : IOException
