# Gmail MCP Setup Guide

Follow these steps once to connect Claude Code to your Gmail inbox.

---

## Overview

Claude uses a Gmail MCP (Model Context Protocol) server to read your inbox. This requires a one-time OAuth 2.0 setup via Google Cloud Console. Your credentials are stored locally and never leave your machine.

---

## Step 1 — Create a Google Cloud Project

1. Go to [https://console.cloud.google.com](https://console.cloud.google.com)
2. Click **"New Project"** → name it `BuyerAgent` → **Create**
3. Make sure the new project is selected in the top dropdown

---

## Step 2 — Enable the Gmail API

1. In the left menu go to **APIs & Services → Library**
2. Search for **"Gmail API"**
3. Click it → **Enable**

---

## Step 3 — Create OAuth 2.0 Credentials

1. Go to **APIs & Services → Credentials**
2. Click **"+ Create Credentials" → OAuth client ID**
3. If prompted, configure the OAuth consent screen:
   - User Type: **External**
   - App name: `BuyerAgent`
   - Support email: `mgaehring@gmail.com`
   - Save and continue through the remaining screens
4. Back in Credentials → Create OAuth client ID:
   - Application type: **Desktop app**
   - Name: `BuyerAgent Desktop`
   - Click **Create**
5. Download the JSON file — save it as `resources/google-credentials.json`
   - **Keep this file private — do not commit to git**

---

## Step 4 — Get a Refresh Token

Run this one-time command in your terminal to authorize Claude to access Gmail:

```bash
npx -y @gptscript-ai/gmail-oauth-mcp --get-refresh-token \
  --client-id YOUR_CLIENT_ID \
  --client-secret YOUR_CLIENT_SECRET
```

This opens a browser window. Log in with `mgaehring@gmail.com` and grant the requested permissions. Copy the **refresh token** from the output.

---

## Step 5 — Add Credentials to Settings

Open `.claude/settings.json` and fill in your values:

```json
{
  "mcpServers": {
    "gmail": {
      "command": "npx",
      "args": ["-y", "@gptscript-ai/gmail-oauth-mcp"],
      "env": {
        "GMAIL_OAUTH_CLIENT_ID": "PASTE_YOUR_CLIENT_ID_HERE",
        "GMAIL_OAUTH_CLIENT_SECRET": "PASTE_YOUR_CLIENT_SECRET_HERE",
        "GMAIL_OAUTH_REFRESH_TOKEN": "PASTE_YOUR_REFRESH_TOKEN_HERE"
      }
    }
  }
}
```

---

## Step 6 — Test the Connection

In a Claude Code chat session, type:

```
List my Gmail inbox labels to confirm the MCP connection is working.
```

You should see your Gmail labels returned. If you get an auth error, double-check the credentials in settings.json.

---

## Security Notes

- Your credentials are stored only in `.claude/settings.json` on your local machine
- The OAuth scope requested is **read-only** — Claude cannot send or delete emails
- If you need to revoke access, go to [https://myaccount.google.com/permissions](https://myaccount.google.com/permissions) and remove `BuyerAgent`
- Do not share `settings.json` or `resources/google-credentials.json`
