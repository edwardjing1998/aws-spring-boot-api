Remove-Item -Recurse -Force "$PWD\actions-runner"
mkdir actions-runner
cd actions-runner
# Re-run extraction here
Add-Type -AssemblyName System.IO.Compression.FileSystem
[System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD\..\actions-runner-win-x64-2.317.0.zip", "$PWD")
