#6 0.889   supervisor (no such package):
#6 0.889     required by: world[supervisor]
#6 ERROR: process "/bin/sh -c apk add --no-cache supervisor" did not complete successfully: exit code: 1
------
 > [2/8] RUN apk add --no-cache supervisor:
0.203 fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/main/x86_64/APKINDEX.tar.gz
0.656 289B81F55C7F0000:error:0A000126:SSL routines::unexpected eof while reading:ssl/record/rec_layer_s3.c:687:
0.657 WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.21/main: Permission denied
0.657 fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/community/x86_64/APKINDEX.tar.gz
0.887 289B81F55C7F0000:error:0A000126:SSL routines::unexpected eof while reading:ssl/record/rec_layer_s3.c:687:
0.888 WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.21/community: Permission denied
0.889 ERROR: unable to select packages:
0.889   supervisor (no such package):
0.889     required by: world[supervisor]
------
Dockerfile:2
--------------------
   1 |     FROM eclipse-temurin:21-jre-alpine
   2 | >>> RUN apk add --no-cache supervisor
   3 |     
   4 |     WORKDIR /opt
--------------------
ERROR: failed to solve: process "/bin/sh -c apk add --no-cache supervisor" did not complete successfully: exit code: 1
Error: Process completed with exit code 1.
