# Unreleased Commits Notifier Action

This GitHub Action checks if the `HEAD` commit on a branch is tagged. If it's not, the action finds the last tag, compiles a list of commits made since that tag, and logs them to the console.

Optionally, if you provide Slack credentials, it will also send the list of commits to a specified Slack channel.

## Usage

### Log to Console Only

Create a workflow file (e.g., `.github/workflows/check-release.yml`) in your repository. To only log the unreleased commits in the GitHub Actions console, you don't need to provide any inputs.

```yaml
name: Check for Unreleased Commits

on:
  schedule:
    - cron: '0 7 * * *'
  workflow_dispatch:

jobs:
  check-for-unreleased:
    runs-on: ubuntu-latest
    steps:
      - name: Run Unreleased Commits Notifier
        uses: opsidao/unreleased-commits-slack-action@v0.1
```

### Log to Console and Post to Slack

To also send a Slack notification, provide the `slack-bot-token` and `slack-channel-id` inputs.

```yaml
name: Check for Unreleased Commits and Notify Slack

on:
  schedule:
    - cron: '0 7 * * *' # 09:00 CEST
  workflow_dispatch:

jobs:
  check-for-unreleased:
    runs-on: ubuntu-latest
    steps:
      - name: Run Unreleased Commits Notifier
        uses: opsidao/unreleased-commits-slack-action@v0.1
        with:
          slack-bot-token: ${{ secrets.SLACK_BOT_TOKEN }}
          slack-channel-id: ${{ secrets.SLACK_CHANNEL_ID }}
```

## Inputs

| Name               | Description                                           | Required |
| ------------------ | ----------------------------------------------------- | -------- |
| `slack-bot-token`  | The Slack Bot Token for authentication (`xoxb-...`).   | No       |
| `slack-channel-id` | The ID of the Slack channel to post the notification. | No       |

## Slack Bot Setup 🤖

To enable Slack notifications, you need to create a Slack App and provide its credentials to the action as GitHub secrets.

1. **Create a Slack App**
    * Go to the [Slack API: Your Apps](https://api.slack.com/apps) page and click **Create New App**.
    * Choose **From scratch**, give your app a name (e.g., "GitHub Release Notifier"), and select the workspace you want to install it in.

2. **Add Permissions (Scopes)**
    * After creating the app, you'll be redirected to its settings page. In the sidebar, navigate to **OAuth & Permissions**.
    * Scroll down to the **Scopes** section. Under **Bot Token Scopes**, click **Add an OAuth Scope**.
    * Add the `chat:write` scope. This permission allows the app to post messages in channels it's a member of. For more details, see the [official documentation on scopes](https://api.slack.com/scopes/chat:write).

3. **Install the App and Get the Token**
    * Scroll back to the top of the **OAuth & Permissions** page and click **Install App to Workspace**.
    * Follow the prompts to authorize the app.
    * Once installed, the page will display a **Bot User OAuth Token** that starts with `xoxb-`. Copy this token.

4. **Add the Token as a GitHub Secret**
    * In your GitHub repository, go to **Settings > Secrets and variables > Actions**.
    * Click **New repository secret** and create a secret named `SLACK_BOT_TOKEN`. Paste the `xoxb-` token you copied.

5. **Get the Channel ID and Add as a Secret**
    * In your Slack client, right-click on the name of the channel where you want to receive notifications and select **Copy link**.
    * The link will look like `https://your-workspace.slack.com/archives/C1234567890`. The part starting with `C` (or `G` for private channels) is the **Channel ID**.
    * Create another repository secret named `SLACK_CHANNEL_ID` and paste the channel ID.

6. **Invite the Bot to the Channel**
    * Finally, go to the channel in Slack and send the message `/invite @YourAppName`, replacing `YourAppName` with the name of the app you created. The bot must be a member of the channel to post messages in it.
