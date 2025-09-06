# TickTick OAuth2 Setup in n8n

## Your Credentials
- **Client ID**: `ytK7UMAD17MVlO8Mj8`
- **Client Secret**: `8IZ8G+fs4E@L&p&0(+bDae&N!1(xg6qg`
- **API Key**: `tp_173828d4297543158469868f0ff91a38`

## Step 1: Register Redirect URI in TickTick Developer Center
Add this EXACT URL (no trailing slash, must be HTTPS):
```
https://n8n-simplifai.saavatar.xyz/rest/oauth2-credential/callback
```

## Step 2: Create OAuth2 Credential in n8n

1. Go to Credentials → Add Credential → OAuth2 API
2. Configure as follows:

### OAuth2 Settings:
- **Grant Type**: Authorization Code
- **Authorization URL**: `https://ticktick.com/oauth/authorize`
- **Access Token URL**: `https://ticktick.com/oauth/token`
- **Scope**: `tasks:read tasks:write` (space-separated)
- **Client ID**: `ytK7UMAD17MVlO8Mj8`
- **Client Secret**: `8IZ8G+fs4E@L&p&0(+bDae&N!1(xg6qg`
- **Authentication**: Send as Basic Auth header (Header)
- **Ignore SSL Issues**: OFF

### IMPORTANT:
- Do NOT add any Auth URL Query Parameters
- Let n8n handle the state parameter automatically
- Click "Connect" button and authorize in the popup window

## Step 3: Test the Connection
After connecting, n8n will automatically handle:
- Access tokens
- Refresh tokens
- Token renewal

## Troubleshooting

### "Invalid state format" error:
- Don't manually craft URLs
- Use n8n's "Connect" button
- Let n8n generate the state parameter

### "Insufficient parameters" error:
- This means state is missing
- Always use n8n's Connect button
- Don't visit the OAuth URL directly

### "redirect_uri_mismatch" error:
- Ensure the TickTick Developer Center has EXACTLY:
  `https://n8n-simplifai.saavatar.xyz/rest/oauth2-credential/callback`
- No http, no trailing slash, exact match required

## Manual Token Test (Optional)
If you need to test manually:
```bash
# After getting auth code from redirect
curl -X POST "https://ticktick.com/oauth/token" \
  -H "Authorization: Basic $(echo -n 'ytK7UMAD17MVlO8Mj8:8IZ8G+fs4E@L&p&0(+bDae&N!1(xg6qg' | base64)" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "grant_type=authorization_code" \
  --data-urlencode "code=YOUR_CODE_HERE" \
  --data-urlencode "redirect_uri=https://n8n-simplifai.saavatar.xyz/rest/oauth2-credential/callback" \
  --data-urlencode "scope=tasks:read tasks:write"
```