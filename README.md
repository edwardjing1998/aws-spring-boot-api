FROM alpine:3.20 AS builder        # this stage *may* reach the internet
RUN apk add --no-cache python3 py3-pip
