# ─────────────────────────────────────────────────────────────
# Build Maven project (Flume)  ➜  then build & push 4 images
# ─────────────────────────────────────────────────────────────
name: Build Maven Project

on:
  push:
    branches: [ rapid-case-service ]
  pull_request: {}
  workflow_dispatch: {}

jobs:
# ─────────────────────────────────────────────────────────────
# 1️⃣  Original reusable Maven workflow (unchanged)
# ─────────────────────────────────────────────────────────────
  ci-workflow:
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    secrets: inherit
    with:
      apm: APM0001099
      app_name: RAPIDadmin-microservices-java
      app_version: 0.0.1-SNAPSHOT
      java_version: '21'
      sonar_enable: false

      # If you still want one root image from Flume leave these, otherwise delete.
      image_name: edwardjing/rapid-case-service
      image_tag: ${{ github.sha }}

# ─────────────────────────────────────────────────────────────
# 2️⃣  NEW  job: build & push each micro-service image
# ─────────────────────────────────────────────────────────────
  docker-build-push:
    needs: ci-workflow          # run only after Maven build succeeds
    runs-on: ubuntu-latest

    strategy:
      matrix:
        svc: [ gateway, review-deleted-case, service-b, service-c ]
      fail-fast: false          # let others keep running if one fails

    env:
      REGISTRY: nexus.fiserv.net
      IMAGE_NS: rapid
      IMAGE_TAG: ${{ github.sha }}

    steps:
      # -- Check out code ------------------------------------------------------
      - name: Checkout repository
        uses: actions/checkout@v4

      # -- Set up Java & cache Maven (needed to re-package module jars) --------
      - name: Set up Temurin 21 + Maven cache
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'
          cache: maven

      # -- Build JAR for the current module only ------------------------------
      - name: Build ${{ matrix.svc }} with Maven
        run: |
          mvn -pl ${{ matrix.svc }} -am -B clean package -DskipTests

      # -- Log in to Nexus Docker registry ------------------------------------
      - name: Log in to Nexus Docker registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ secrets.NEXUS_USER }}
          password: ${{ secrets.NEXUS_PASSWORD }}

      # -- Build Docker image --------------------------------------------------
      - name: Build Docker image for ${{ matrix.svc }}
        run: |
          docker build \
            -t ${{ env.REGISTRY }}/${{ env.IMAGE_NS }}/${{ matrix.svc }}:${{ env.IMAGE_TAG }} \
            -f ${{ matrix.svc }}/Dockerfile \
            ${{ matrix.svc }}

      # -- Push Docker image ---------------------------------------------------
      - name: Push Docker image for ${{ matrix.svc }}
        run: |
          docker push ${{ env.REGISTRY }}/${{ env.IMAGE_NS }}/${{ matrix.svc }}:${{ env.IMAGE_TAG }}
