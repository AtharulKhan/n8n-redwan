# CRO Website Automation Configuration Guide

## Required API Keys and Credentials

### 1. Apify API Configuration
- **Account Setup**: Create account at https://apify.com
- **API Token**: Get from https://console.apify.com/account/integrations
- **Required Actors**:
  - `apify/website-content-crawler` - For scraping website content
  - `apify/screenshot-url` - For capturing full-page screenshots
- **Proxy Configuration**: Ensure RESIDENTIAL proxy group is enabled in your Apify account

### 2. OpenRouter API Configuration
- **Account Setup**: Sign up at https://openrouter.ai
- **API Key**: Generate from https://openrouter.ai/keys
- **Model**: Using `openai/gpt-4o` (can be changed to other models)
- **Credits**: Add credits to your account for API usage

### 3. Google Services Setup

#### Google Sheets
- **OAuth2 Setup Required**:
  1. Go to Google Cloud Console
  2. Create new project or select existing
  3. Enable Google Sheets API
  4. Create OAuth2 credentials
  5. Add authorized redirect URI: `https://[your-n8n-instance]/rest/oauth2-credential/callback`

#### Google Drive
- **OAuth2 Setup Required**:
  1. Same project as Google Sheets
  2. Enable Google Drive API
  3. Use same OAuth2 credentials
  4. Ensure scope includes Drive file access

## n8n Credential Setup

### 1. Apify API (HTTP Header Auth)
```
Name: Apify API
Header Name: Authorization
Header Value: Bearer [YOUR_APIFY_API_TOKEN]
```

### 2. OpenRouter API (HTTP Header Auth)
```
Name: OpenRouter API
Header Name: Authorization
Header Value: Bearer [YOUR_OPENROUTER_API_KEY]
```

### 3. Google Sheets OAuth2
- Follow n8n's Google OAuth2 setup guide
- Scopes needed:
  - https://www.googleapis.com/auth/spreadsheets
  - https://www.googleapis.com/auth/drive.file

### 4. Google Drive OAuth2
- Can use same credentials as Google Sheets
- Or create separate credential with Drive scopes

## Google Sheet Structure

Required columns in your Google Sheet:

| Column Name | Type | Description |
|------------|------|-------------|
| Client Name | Text | Name of the client |
| URL | Text | Website URL to process |
| Status | Text | Trigger when contains "Positive" |
| Drive Link | Text | (Output) Link to generated files |
| Landing Page | Text | (Output) Direct link to HTML |
| Processed At | Text | (Output) Timestamp |
| Error Message | Text | (Output) Error details if failed |

## Workflow Variables to Update

1. **Sheet ID**: Replace `{{ $json.sheetId }}` with your actual Google Sheet ID
2. **Sheet Name**: Replace `{{ $json.sheetName }}` with your sheet tab name
3. **Drive Folder**: Optionally set a parent folder ID for organization
4. **Trigger Interval**: Adjust polling frequency (default: every minute)

## Testing the Workflow

1. **Manual Test**:
   - Use Test Workflow button in n8n
   - Add a row with "Positive" status to trigger

2. **Check Points**:
   - Apify actors are running (check Apify console)
   - Base64 conversion successful
   - OpenRouter API responding
   - Files created in Google Drive
   - Sheet updated with results

## Troubleshooting

### Common Issues:

1. **Apify Timeout**:
   - Increase wait time in "Wait for Processing" node
   - Check Apify dashboard for actor status

2. **OpenRouter Rate Limits**:
   - Add retry logic with exponential backoff
   - Consider using queue for batch processing

3. **Google Auth Errors**:
   - Re-authenticate OAuth2 credentials
   - Check API quotas in Google Cloud Console

4. **Large Screenshots**:
   - Reduce quality in screenshot settings
   - Consider compression before base64 encoding

## Manual Fallback Process

If automation fails:

1. Take manual screenshot using browser extension
2. Upload to designated Google Drive folder
3. Update sheet with "Manual" status
4. Process can continue from that point

## Performance Optimization

- **Batch Processing**: Process multiple URLs in parallel
- **Caching**: Store commonly accessed data
- **Error Recovery**: Implement retry mechanisms
- **Monitoring**: Set up alerts for failures

## Security Notes

- Never commit API keys to version control
- Use environment variables in production
- Regularly rotate API keys
- Monitor API usage for anomalies
- Set up IP restrictions where possible