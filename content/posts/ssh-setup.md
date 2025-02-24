+++
date = '2025-02-24T21:41:18+05:45'
draft = false
title = 'SSH Setup'
+++

#### Copy the public and private key to the .ssh directory

```bash
cp ~/[filePath]/id_rsa* ~/.ssh/
```

#### Setup config file

```bash
sudo nano ~/.ssh/config
```

#### Config file configuration

```bash
Host acme-server
    HostName 34.231.213.345
    User pratik
    IdentityFile ~/.ssh/id_rsa
```

#### SSH to the server

```bash
ssh acme-server
```
