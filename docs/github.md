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

- `/github help`
- `/github connect` - Connect your Mattermost account to your GitHub account
- `/github disconnect` - Disconnect your Mattermost account from your GitHub account
- `/github todo` - Get a list of unread messages and pull requests awaiting your review
- `/github subscribe list` - Will list the current channel subscriptions
- `/github subscribe [owner]/[repo] [features] [flags]` - Subscribe the current channel to receive notifications about opened pull requests and issues for an organization or repository
  - `owner`: organization name.
  - `repo`: repository name.
  - `features` is a comma-delimited list of one or more the following:
    - `issues` - includes new and closed issues
    - `pulls` - includes new and closed pull requests
    - `pushes` - includes pushes
    - `creates` - includes branch and tag creations
    - `deletes` - includes branch and tag deletions
    - `issue_comments` - includes new issue comments
    - `pull_reviews` - includes pull request reviews
    - `label:"<labelname>"` - must include "pulls" or "issues" in feature list when using a label
    Defaults to "pulls,issues,creates,deletes"
  - `flags` currently supported: 
    - --exclude-org-member - events triggered by organization members will not be delivered (the Github organization config should be set, otherwise this flag has not effect)
- `/github unsubscribe owner/repo` - Unsubscribe the current channel from a repository
- `/github me` - Display the connected GitHub account
- `/github settings [setting] [value]` - Update your user settings
  - `setting` can be "notifications" or "reminders"
  - `value` can be "on" or "off"

:exclamation: Default feature doesn't have `pushes`.
## Issues

Description|Reason|Solution
-----------|------|--------
Type `/github connect`, click the link and get `Not authorized`|Maybe we need to use company's email to connect.|In Github **Settings**, select **Emails** and enter Designveloper email to *Add email address* then press **Add** and verify email.

