+++
date = '2025-02-24T21:37:08+05:45'
draft = false
title = 'Github Actions'
+++

### Setup runner in the server

![Runner section in github](/github-runner.png)

As shown in the figure above, you can find the Runners tab in the Action section of your repository settings, if you have the proper access.

> Select the New self-hosted runner

![OS selection in runner section](/github-runner2.png)

> Select the Server OS (Most probably linux)

> Run in your terminal (Be sure you are connected to your server)

> **IMPORTANT** : Skip the following step

```bash
# Last step, run it!
$ ./run.sh
```

> Instead use (This allows the runner service to start in the background)

```bash
sudo ./svc.sh install
```

```bash
sudo ./svc.sh start
```

---

### Setup the Environment Variables

> Use this to store the value of .env file

- Under the Security section of the settings, click the Actions tab
- Click on the New repository secret
  ![Secret tab in the Actions](/github-runner3.png)

- Add name for your secret (Best practice is to use all caps e.g. ENV)
- Add your secret value
- Click the 'Add secret' button

![Secret tab in the Actions](/github-runner4.png)

---

### Setup the Workflow file

- Click on the Actions tab on the repository
  ![Secret tab in the Actions](/github-runner5.png)

- Scroll down to the Node.js section and click Configure
  ![Node.js workflow file](/github-runner6.png)

- Rename [name].yml to main.yml (Not compulsory. But good practice)

- Based on your application paste the following YAML

_May need to make additional changes to the file_

> For API

```yml
# This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs

name: Node.js CI

on:
  push:
    branches: ["main"]

jobs:
  setup:
    runs-on: self-hosted
    strategy:
      matrix:
        node-version: [16.x]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"

      - name: Install dependencies
        run: npm install

      - name: Create env file
        run: touch .env

      - name: Insert credentials to .env
        run: echo "${{secrets.ENV}}" >> .env

      # Create folder for storing images (Optional)
      - name: Create uploads folder
        run: mkdir ./server/uploads

      - name: Run migrations if any
        run: npx sequelize-cli db:migrate

      - name: Restart the API
        run: pm2 restart API
```

> For Frontend (Below workflow file is for Vue3 application)

```
# This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs

name: Node.js CI

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: self-hosted
    strategy:
      matrix:
        node-version: [16.x]
    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm i
    - run: touch .env
    - run: echo "${{secrets.ENV}}" >> .env
    - run: npm run build
    # Setup symlink for phpmyadmin
    - run: sudo ln -s /usr/share/phpmyadmin /path-to-the-dist-folder
```

> Click commit changes
