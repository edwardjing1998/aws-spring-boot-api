#6 0.514 WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.20/community: Permission denied
#6 0.514 ERROR: unable to select packages:
#6 0.514   openjdk21-jre-headless (no such package):
#6 0.514     required by: world[openjdk21-jre-headless]
#6 0.514   supervisor (no such package):
#6 0.514     required by: world[supervisor]
#6 ERROR: process "/bin/sh -c apk add --no-cache openjdk21-jre-headless supervisor" did not complete successfully: exit code: 2
------
 > [2/8] RUN apk add --no-cache openjdk21-jre-headless supervisor:
0.336 289B67F33D7F0000:error:0A000126:SSL routines::unexpected eof while reading:ssl/record/rec_layer_s3.c:687:
0.337 WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.20/main: Permission denied
0.337 fetch https://dl-cdn.alpinelinux.org/alpine/v3.20/community/x86_64/APKINDEX.tar.gz
0.512 289B67F33D7F0000:error:0A000126:SSL routines::unexpected eof while reading:ssl/record/rec_layer_s3.c:687:
0.514 WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.20/community: Permission denied
0.514 ERROR: unable to select packages:
0.514   openjdk21-jre-headless (no such package):
0.514     required by: world[openjdk21-jre-headless]
0.514   supervisor (no such package):
0.514     required by: world[supervisor]
------
Dockerfile:2
--------------------
   1 |     FROM alpine:3.20
   2 | >>> RUN apk add --no-cache openjdk21-jre-headless supervisor
   3 |     
   4 |     WORKDIR /opt
