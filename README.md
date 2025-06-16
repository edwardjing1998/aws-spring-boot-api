name: RAPID Admin Main

on:
  push:
    branches:
    - rapid-admin-j
    
env:
  FORCE_COLOR: 2
  NODE: 16

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Clone repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 23.x

      - name: Install npm dependencies
        run: npm install

      - name: Run build
        run: npm run build



name: RAPID Admin Main

on:
  push:
    branches:
    - rapid-admin-j
    
env:
  FORCE_COLOR: 2
  NODE: 16

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Clone repository
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 23.x

      - name: Install npm dependencies
        run: npm install

      - name: Run build
        run: npm run build
