# Table of Contents

- [Table of Contents](#table-of-contents)
- [Upgrading Mattermost (MM) server](#upgrading-mattermost-mm-server)
    - [Using MM release](#using-mm-release)
    - [Build from source code](#build-from-source-code)
- [Upgrading Mattermost webapp](#upgrading-mattermost-webapp)

<br />
<br />

# Upgrading Mattermost (MM) server

## Using MM release

### 1. Follow steps 1 - 14 at [Upgrading to the Latest Version](https://docs.mattermost.com/administration/upgrade.html#upgrading-to-the-latest-version)
- At step 3, choose MM Team Edition.

## Build from source code

### 1. Follow steps 1 - 4 at [Server Build (Team Edition)](https://developers.mattermost.com/extend/customization/server-build/)
- Clone server code at https://github.com/Designveloper/mattermost-server
- Your packages is located at `mattermost-server/dist`.

### 2. Connect to server and transfer your MM package at step 1 from local to server
- Connect to server: `ssh <user>@<server_address>`
- Transfer package:
    ```bash
    scp -r mattermost-team-linux-amd64.tar.gz <user>@<server_address>:/tmp
    ```
- Extract:
    ```bash
    cd /tmp
    tar -xvf mattermost-team-linux-amd64.tar.gz
    ```
    
### 3. Continue from step 3 to 14 at [Upgrading to Latest Version](https://docs.mattermost.com/administration/upgrade.html#upgrading-to-the-latest-version)

<br/>
<br/>

# Upgrading Mattermost webapp

### 1. Follow steps at [MM's Dev Webapp Setup Guide](https://developers.mattermost.com/contribute/webapp/developer-setup/)
- Make sure your webapp is installed.

### 2. Run command:
```bash
make package
```

### 3. Transfer your webapp package to server at location `/opt/mattermost`
```bash
# transfer file
scp -r mattermost-webapp.tar.gz <user>@<server_address>:/opt/mattermost/
```
### 4. Extract webapp
```bash
#extract
tar -xf mattermost-webapp.tar.gz
#change ownership of folders
chown -R mattermost:mattermost client/ plugins/
chown -R mattermost:mattermost /opt/mattermost
```
