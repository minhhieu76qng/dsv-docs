## Install Bitbucket Webhook Server

#### 1. Update your local package index and then install the packages.
Make sure you have installed python3 on your machine.</br>
For Python3:
```bash
# update local packages
sudo apt-get update

# install dependencies
sudo apt-get install python3-pip python3-dev nginx
```

#### 2. Clone `mattermost-bitbucket-bridge` repository
- Change directory: 
    ```bash
    cd /opt
    ```
- Clone this repository to your machine: `git clone https://github.com/cvitter/mattermost-bitbucket-bridge.git`
- Rename folder to `bitbucket-webhook`
- In **bitbucket-webhook** folder, make a copy of `config.sample`: `cp config.sample config.json`
- Edit `config.json` to update the following fields as needed:
  - Application host address and port (generally debug should be left set to false).
    - host: 0.0.0.0
    - port: 8989
  - Mattermost server_url and the user name or icon to override the webhook with if desired.
  - And the base url of your Bitbucket server. (Leave empty to use BitBucket Cloud)
- Rename `bitbucket.py` to `app.py`</br>

:exclamation: Due to Bitbucket API has changed, old source code caused some issues. To fix it, follow those steps:
- Edit `helper.py`:
    - `pullrequest:declined` =>  `pullrequest:rejected`
- Edit `app.py`:
    ```python
    # from
    if event_key == 'pullrequest:comment_deleted':
        attachment["fields"] = {}

    # to
    if event_key == 'pullrequest:comment_deleted':
        attachment["fields"] = []
    ```

#### 3. Create *virtualenv*
Install virtualenv in python3:
```bash
sudo pip3 install virtualenv
```
In `bitbucket-webhook` folder, create Virtualenv:
```bash
virtualenv bitbucket-envs
# 'bitbucket-envs' is the name of vitualenv-name. You can change any thing you want.
```
Activate virtualenv:
```bash
source bitbucket-envs/bin/activate
```

#### 4. Setup Flask application
**Install Flask, requests and Gunicorn**
```bash
pip install flask gunicorn requests
```

Run your application
```bash
python3 app.py
```

If terminal shows `Running on http://0.0.0.0:9000/ (Press CTRL+C to quit)`, go to next step.

#### 5. Create the WSGI Entry Point
Next, we’ll create a file that will serve as the entry point for our application. This will tell our Gunicorn server how to interact with the application.

```bash
nano wsgi.py
```
Paste below code to `wsgi.py`
```python
from app import app

if __name__ == "__main__":
    app.run()
```
Save and close file.</br>
**Folder structure:**
```
bitbucket-webhook
            |_________ app.py
            |_________ wsgi.py
            |_________ bitbucket-envs
```

#### 6. Testing Gunicorn’s Ability to Serve the Project
```bash
gunicorn --bind 0.0.0.0:5000 wsgi:app
```

If terminal shows `Listening at: http://0.0.0.0:5000 (12204)`, it works.</br>
Deactivate virtualenv:
```bash
deactivate
```

#### 7. Create a `systemd` Unit File
It allow Ubuntu to automatically start Gunicorn and serve our Flask application whenever the server boots.</br>

Create a unit file ending in .service within the /etc/systemd/system directory to begin:

```bash
sudo nano /etc/systemd/system/bitbucket.service
```
Copy below text to `bitbucket.service`, then save and close file.
```
[Unit]
#  specifies metadata and dependencies
Description=Gunicorn instance to serve Bitbucket webhook
After=network.target
# tells the init system to only start this after the networking target has been reached
# We will give our regular user account ownership of the process since it owns all of the relevant files
[Service]
# Service specify the user and group under which our process will run.
User=root
# give group ownership to the www-data group so that Nginx can communicate easily with the Gunicorn processes.
Group=www-data
# We'll then map out the working directory and set the PATH environmental variable so that the init system knows where our the executables for the process are located (within our virtual environment).
WorkingDirectory=/opt/bitbucket-webhook
Environment="PATH=/opt/bitbucket-webhook/bitbucket-envs/bin"
# We'll then specify the commanded to start the service
ExecStart=/opt/bitbucket-webhook/bitbucket-envs/bin/gunicorn --workers 3 --bind unix:app.sock -m 007 wsgi:app
# This will tell systemd what to link this service to if we enable it to start at boot. We want this service to start when the regular multi-user system is up and running:
[Install]
WantedBy=multi-user.target
```

Now we start service and enable it at boot:
```bash
# start
systemctl start bitbucket

# enable
systemctl enable bitbucket
```

**Folder Structure**
```
bitbucket-webhook
            |_________ app.py
            |_________ wsgi.py
            |_________ bitbucket-envs
            |_________ app.sock
```

#### 8. Final step: Configuring nginx
```bash
cd /etc/nginx
nano /etc/nginx/sites-avalable/default
```
Paste below code to `default` (must be below `location / {...}`):
```nginx
location /bitbucket {
    rewrite ^/bitbucket(.*) $1 break;
    include proxy_params;
    proxy_pass http://unix:/opt/bitbucket-webhook/app.sock;
}
```

Check syntax errors by: `nginx -t`. If it's ok, we cant restart nginx service
```bash
systemctl restart nginx
```

The last thing we need to do is adjust our firewall to allow access to the Nginx server:
```bash
sudo ufw allow 'Nginx Full'
```

## Configuration
### Configure Mattermost
1. Go to **Integrations > Incomming Webhooks** in Mattermost.
2. Click button **Add Incomming Webhook**
3. Fill the form.
    - **Title**: title to displayed in settings page.
    - **Description**: describe about this incomming webhook.
    - **Channel**: channel will receive notifications.
    - **Username**: the display name.
    - **Profile Picture**: avatar of the integration. This is a **URL** of `.png` or `.jpg` file at least 128px by 128px.
4. Click **Save**
5. Setup successful and you will see a URL like `https://chat.designveloper.com/hooks/<hook_id>`, copy this `hook_id` and click **Done**.


### Configure Bitbucket repository
1. Go to Bitbucket repository page.
2. Navigate to **Settings > Webhooks** then click **Add webhook** button.
3. Fill form:
    - **Title**: the name of webhook
    - **URL**: the URL of Bibucket webhook server like `http://128.199.168.53/bitbucket/hooks/<hook_id>`.
    - **Triggers**: select **Choose from a full list of triggers** and choose actions you want.
4. Press **Save**.
Now, when you execute actions (push, PR,...) with you repository, Bitbucket will fire event to Mattermost channel which you setup before.


## Testing

### Bitbucket plugin testing
1. Go to repository page, select **Settings > Webhooks**.
2. At your Bitbucket Webhook record, select **View requests**
3. And you can resend any requests in table.

:exclamation: If **Request history collection** is off, please turn on it and press **Load new requests** button.


### Supported events
Events|Work|Not Work|Not test|Note
---|:---:|:---:|:---:|---
Repository push|:heavy_check_mark:
Repository fork||:x:||Plugin does not support this event.
Repository updated||:x:||Plugin does not support this event.
Commit comment created||:x:||Plugin does not support this event.
Build status created||:x:||Plugin does not support this event.
Build status updated||:x:||Plugin does not support this event.
Pull request created|:heavy_check_mark:
Pull request updated|:heavy_check_mark:
Pull request approved|:heavy_check_mark:
Pull request approval removed|:heavy_check_mark:
Pull request merged|:heavy_check_mark:
Pull request declined|:heavy_check_mark:
Pull request comment created|:heavy_check_mark:
Pull request comment deleted|:heavy_check_mark:
Issue created||:x:||Plugin does not support this event.
Issue updated||:x:||Plugin does not support this event.
Issue comment created||:x:||Plugin does not support this event.

## Reference
1. https://github.com/cvitter/mattermost-bitbucket-bridge
2. https://medium.com/faun/deploy-flask-app-with-nginx-using-gunicorn-7fda4f50066a
3. https://docs.gunicorn.org/en/stable/settings.html
4. https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-18-04