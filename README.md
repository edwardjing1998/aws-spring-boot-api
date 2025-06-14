# This workflow builds and pushes 4 Docker images individually
# using the Flume reusable Maven + Docker GitHub Actions workflow

name: Build 4 Images Individually with Flume Workflow

on:
  push:
    branches: [ deleted-case-j ]
  pull_request: {}
  workflow_dispatch: {}

jobs:
  # ──────────────── 1️⃣ gateway ────────────────
  build-gateway:
    name: Build & Push Gateway
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    secrets: inherit
    with:
      apm: APM0001099
      app_name: gateway
      app_version: 0.0.1-SNAPSHOT
      java_version: '21'
      sonar_enable: false
      image_name: nexus.fiserv.net/rapid/gateway
      image_tag: ${{ github.sha }}
      module_path: gateway

  # ──────────────── 2️⃣ review-deleted-case ────────────────
  build-review-deleted-case:
    name: Build & Push Review Deleted Case
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    secrets: inherit
    with:
      apm: APM0001099
      app_name: review-deleted-case
      app_version: 0.0.1-SNAPSHOT
      java_version: '21'
      sonar_enable: false
      image_name: nexus.fiserv.net/rapid/review-deleted-case
      image_tag: ${{ github.sha }}
      module_path: review-deleted-case

  # ──────────────── 3️⃣ service-b ────────────────
  build-service-b:
    name: Build & Push Service B
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    secrets: inherit
    with:
      apm: APM0001099
      app_name: service-b
      app_version: 0.0.1-SNAPSHOT
      java_version: '21'
      sonar_enable: false
      image_name: nexus.fiserv.net/rapid/service-b
      image_tag: ${{ github.sha }}
      module_path: service-b

  # ──────────────── 4️⃣ service-c ────────────────
  build-service-c:
    name: Build & Push Service C
    uses: fiserv/flume-reuseable-workflows/.github/workflows/maven.yml@main
    secrets: inherit
    with:
      apm: APM0001099
      app_name: service-c
      app_version: 0.0.1-SNAPSHOT
      java_version: '21'
      sonar_enable: false
      image_name: nexus.fiserv.net/rapid/service-c
      image_tag: ${{ github.sha }}
      module_path: service-c
