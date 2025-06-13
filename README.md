FROM eclipse-temurin:21-jre-alpine

# ── install Supervisor robustly ───────────────────────────────────────────────
RUN set -eux; \
    echo "https://dl-cdn.alpinelinux.org/alpine/edge/community" \
      >> /etc/apk/repositories || true; \
    (apk add --no-cache --update-cache py3-supervisor 2>/tmp/apk.err \
     || apk add --no-cache supervisor) ; \
    sed -i '$d' /etc/apk/repositories || true   # remove edge line again

WORKDIR /opt
COPY gateway/target/gateway-0.0.1-SNAPSHOT.jar      /opt/gateway.jar
COPY review-deleted-case/target/review-deleted-case-0.0.1-SNAPSHOT.jar  /opt/review-deleted-case.jar
COPY service-b/target/service-b-0.0.1-SNAPSHOT.jar  /opt/service-b.jar
COPY service-c/target/service-c-0.0.1-SNAPSHOT.jar  /opt/service-c.jar
COPY supervisord.conf                /etc/supervisord.conf

EXPOSE 8084 8081 8082 8083
CMD ["supervisord","-c","/etc/supervisord.conf"]
