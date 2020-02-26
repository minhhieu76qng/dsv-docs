## Installation

In Mattermost 5.16 and later, the GitHub plugin is included in the Plugin Marketplace which can be accessed from Main Menu > Plugins Marketplace. 

## Configuration

:exclamation: If you only need to add new repository to webhook, just folow **step 2**.

### Step 1: Register an OAuth application in GitHub

1. Go to https://github.com/settings/applications/new to register an OAuth app.

2. Set the following values:
- **Application Name**: `Mattermost GitHub Plugin - Designveloper`
- **Homepage URL**: `https://github.com/mattermost/mattermost-plugin-github`
- **Authorization callback URL**: `https://chat.designveloper.com/plugins/github/oauth/complete`

3. Submit. Copy the **Client ID** and **Client Secret** in the resulting screen.

4. Go to **System Console > Plugins > GitHub** and enter **GitHub OAuth Client ID** and **GitHub OAuth Client Secret** you copied in a previous step.

### Step 2: Create a webhook in GitHub

1. In **System Console > Plugins > GitHub**, generate a new value for **Webhook Secret**. Copy it as you will use it in a later step.

2. Hit **Save** to save the secret.

3. Go to the **Settings** page of your GitHub organization you want to send notifications from, then select **Webhooks** in the sidebar.

4. Click **Add webhook**

5. Set the following values:
   - **Payload URL**: `https://chat.designveloper.com/plugins/github/webhook`.
   - **Content Type**: `application/json`
   - **Secret**: the webhook secret you copied previously

6. Select **Let me select individual events** for "Which events would you like to trigger this webhook?", then select the following events: Issues, Issue comments, Pull requests, Pull request reviews, Pull request review comments, Pushes, Branch or Tag creation, Branch or Tag deletion

7. Hit **Add Webhook** to save it.

**Note:** At step 3, Github will fire events to all repositories in this organization (organization scope). If you want to use in repository scope, go to **Settings** in repository then select **Webhooks** in the sidebar.

### Step 3: Configure plugin in Mattermost

1. Go to **System Console > Plugins > GitHub** and do the following:
   - Generate a new value for **At Rest Encryption Key**.
   - (Optional) **GitHub Organization**: Lock the plugin to a single GitHub organization by setting this field to the name of your GitHub organization.
   - (Optional) **Enable Private Repositories**: Allow the plugin to receive notifications from private repositories by setting this value to true.
    When enabled, existing users must reconnect their accounts to gain access to private repositories. Affected users will be notified by the plugin once private repositories are enabled.

   - (Enterprise only) **Enterprise Base URL** and **Enterprise Upload URL**: Set these values to your GitHub Enterprise URLs, e.g. `https://github.example.com`. The Base and Upload URLs are often the same.
2. Hit **Save**.
3. Go to **System Console > Plugins > Management** and click **Enable** to enable the GitHub plugin.

You're all set! To test it, run the `/github connect` slash command to connect your Mattermost account with GitHub.


## Usage
- Before using any slash commands, you need to connect your Github account and Mattermost `/github connect`
- Using `/github help` slash command to show all supported commands.
- You can subscribe multiple repositories in each channel via `/github subscribe [owner]/[repo] [features] [flags]`

## Testing
### Github plugin testing
1. Navigate to project page and select **Settings > Webhooks** then select your Mattermost webhook.
2. In *Recent Deliveries* section, you can redeliver actions which you have sent.

### Supported events
Github supports a lot of events, but Mattermost plugin Github is not. Supported events are listed below:
|Events|Work|Not work|Not test|Note|
|---|:---:|:---:|:---:|---|
Branch or tag creation|:heavy_check_mark:
Branch or tag deletion|:heavy_check_mark:
Issues comments|:heavy_check_mark:
Issues|:heavy_check_mark:|||Work: `issue opened, closed`
Pull requests|:heavy_check_mark:|||Work: `PR opened, closed`
Pull request review|:heavy_check_mark:|||Work: `PR reviews submitted`
Pushes|:heavy_check_mark: