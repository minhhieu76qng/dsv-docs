## Installation

In Mattermost 5.12 and later, the GitLab plugin is included in the Plugin Marketplace which can be accessed from Main Menu > Plugins Marketplace. 

## Configuration
### Step 1: Register an OAuth application in GitLab

1. Go to https://gitlab.com/profile/applications or https://gitlab.yourdomain.com/profile/applications to register an OAuth app.
2. Set the following values:
    - **Name**: `Mattermost GitLab Plugin - Designveloper`
    - **Redirect URL**: `https://chat.designveloper.com/plugins/com.github.manland.mattermost-plugin-gitlab/oauth/complete`
3. Select `api` and `read_users` in **Scopes**.
4. Save the application. Copy the *Application ID* and **Secret** field in the resulting screen.
5. In Mattermost, go to **Plugins Marketplace > GitLab > Configure**, and enter the **GitLab URL**, **GitLab OAuth Client ID**, and **Gitlab OAuth Client Secret**.

### Step 2: Configure plugin in Mattermost

1. Go to **System Console > Plugins > GitLab** and do the following:
    - Generate a new value for **Webhook Secret**. Copy it as you will use it in a later step.
    - Generate a new value for **At Rest Encryption Key**.
    - (Optional) **GitLab Group**: Lock the plugin to a single GitLab group by setting this field to the name of your GitLab group.
    - (Optional) **Enable Private Repositories**: Allow the plugin to receive notifications from private repositories by setting this value to true. When enabled, existing users must reconnect their accounts to gain access to private project. Affected users will be notified by the plugin once private repositories are enabled.
2. Hit **Save**.
3. Go to **Plugins Marketplace > GitLab > Configure > Enable Plugin** and click **Enable** to enable the GitLab plugin.

### Step 3: Create Gitlab webhook

**Note for each project you want to receive notifications for or subscribe to, you must create a webhook**

1. In GitLab, go to the project you want to subscribe to, select **Settings** then **Integrations** in the sidebar.
2. Set the following values:
    - **URL**: `https://chat.designveloper.com/plugins/com.github.manland.mattermost-plugin-gitlab/webhook`
    - **Secret Token**: the webhook secret you copied previously
3. Select all events in **Triggers**
4. And add webhook.
You're all set! To test it, run the `/gitlab connect` slash command to connect your Mattermost account with GitLab.

## Usage
- Before using any slash commands, you need to connect your Github account and Mattermost `/github connect`
- Using `/github help` slash command to show all supported commands.
- You can subscribe multiple repositories in each channel via `/github subscribe [owner]/[repo] [features] [flags]`

## Testing
### GitLab plugin testing
1. Navigate to project page then select **Settings > Integrations**.
2. At **Project Hooks** panel, click select box **Test** and choose event.
:exclamation: Some event only works when you turn on feature.
Ex: You must turn on **Issues** before using **Issue events**
:exclamation: Some events fire and get HTTP status 200 but nothing in Mattermost. But if you do actions with your repository, it work.
Ex: create Merge Request in GitLab

### Supported events

Events|Work|Not work|Not test|Note
|---|:---:|:---:|:---:|---|
|Push events|:heavy_check_mark:|
|Tag push event|:heavy_check_mark:|
|Comments|:heavy_check_mark:|||Work: `Merge Request comments`, `Issues comments`</br>Not work: `Commit comments`
|Confidental Comments||:heavy_check_mark:||Status code 400 in GitLab Recent Deliveries
|Issues events|:heavy_check_mark:
|Confidental issues events||:heavy_check_mark:||Status code 400 in GitLab Recent Deliveries
|Merge request events|:heavy_check_mark:|||Work: `open`, `close`, `re-open`, `merge`
|Job events|||:heavy_check_mark:
|Pipeline events|||:heavy_check_mark:
|Wiki page events|||:heavy_check_mark:
