# Vertex AI Setup Guide for Openclaw Railway Deployment

This guide explains how to use Google Cloud's **Vertex AI** with the modified Openclaw Railway template.

## Prerequisites

- Google Cloud Project with Vertex AI API enabled
- Vertex AI service account with appropriate permissions (e.g., "Vertex AI User" role)
- Service account JSON key (downloaded from Google Cloud Console)
- Railway CLI installed (`railway` command available)

## Step 1: Create Vertex AI Service Account

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Select your project
3. Navigate to **IAM & Admin → Service Accounts**
4. Click **Create Service Account**
5. Give it a name (e.g., `openclaw-vertex-ai`)
6. Grant it the **Vertex AI User** role (minimum required)
7. Click **Create Key** → **JSON**
8. A `.json` file will download - **keep this safe**, you'll need it for setup

## Step 2: Deploy Modified Template to Railway

### Option A: Using an existing Railway project

If you already have an Openclaw deployment on Railway:

```bash
# 1. Clone the modified template locally
git clone https://github.com/your-fork/openclaw-railway-template.git
cd openclaw-railway-template

# 2. Link to your Railway project
railway link

# 3. Redeploy
railway up
```

### Option B: Fresh Railway Deployment

1. Go to [Railway Dashboard](https://railway.app)
2. Create a new project
3. Click **Deploy from GitHub**
4. Fork this repo and select it
5. Click **Deploy**

## Step 3: Configure Vertex AI via Setup Wizard

1. Wait for the Railway deployment to complete
2. Visit your app URL (e.g., `https://your-app.up.railway.app/setup`)
3. Enter the `SETUP_PASSWORD` you configured
4. In the setup form:
   - **Provider group**: Select **Google**
   - **Auth method**: Select **Google Vertex AI (service account JSON)**
   - **Key / Token**: Open your service account JSON file and paste its entire contents
   - Configure other options (channels, workspace, etc.) as needed
5. Click **Run setup**

The wrapper will:
- Save your JSON key securely to the volume (`/data/.openclaw/vertex-ai-key.json`)
- Set `GOOGLE_APPLICATION_CREDENTIALS` automatically
- Configure Openclaw to use Vertex AI for models

## Step 4: Verify Configuration

After setup completes:

1. Visit your app's main URL (`https://your-app.up.railway.app`)
2. You should see the Openclaw Control UI (at `/openclaw`)
3. Go to Settings → Models to confirm Vertex AI is configured
4. Start chatting with your Vertex AI model!

## Environment Variables

The modified template automatically manages:

- `GOOGLE_APPLICATION_CREDENTIALS` → Points to `/data/.openclaw/vertex-ai-key.json`
- All other standard Railway/Openclaw variables (managed as before)

## Troubleshooting

### JSON Key Won't Parse
- Make sure you paste the **entire** JSON file contents, not just a piece
- Check that there are no extra line breaks or corrupted characters

### Model Not Working After Setup
- Check the Openclaw logs in Railway Dashboard
- Verify your service account has the **Vertex AI User** role
- Ensure your Google Cloud project has Vertex AI API enabled

### "GOOGLE_APPLICATION_CREDENTIALS not found"
- This shouldn't happen - contact support or check Railway volume is mounted
- Redeploy and run setup wizard again

## Using Different Vertex AI Models

After setup, you can change models via:
- Openclaw Control UI (Settings → Models)
- Or directly edit `/data/.openclaw/openclaw.json` config file

Example config snippet:
```json
{
  "agent": {
    "model": "google/vertex-ai/gemini-2.0-flash-thinking-exp-01-21"
  }
}
```

## Billing & Cost Notes

- Vertex AI uses your Google Cloud **project credits** (not separate billing)
- Costs depend on model and token usage
- Check [Vertex AI pricing](https://cloud.google.com/vertex-ai/pricing)

## Resetting Vertex AI Configuration

To remove Vertex AI and reconfigure:

1. Visit `/setup/api/reset` endpoint (requires `SETUP_PASSWORD` auth)
2. Or manually delete the config file and rerun setup

## Security Notes

- The service account JSON key is stored with `mode 0o600` (read-only by owner)
- Stored at `/data/.openclaw/vertex-ai-key.json` on the Railway volume
- Volume is private to your Railway project
- Never commit the JSON key to git - it's only pasted during setup

## Support

For issues:
- Openclaw docs: https://docs.openclaw.ai/
- Vertex AI docs: https://cloud.google.com/vertex-ai/docs
- Railway docs: https://railway.app/docs
