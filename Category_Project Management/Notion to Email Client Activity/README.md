# Notion to Email Client Activity Workflow

This n8n workflow automatically fetches activities from your Notion database, groups them by client, generates AI-powered summaries using OpenRouter, and sends a weekly email report.

## Workflow Overview

The workflow performs the following steps:

1. **Weekly Schedule Trigger**: Runs every Friday at 10 AM (configurable)
2. **Get Notion Activities**: Fetches active tasks from your Notion database
3. **Extract and Group by Client**: Groups activities by client name
4. **Generate AI Summary**: Creates professional summaries using OpenRouter AI
5. **Format Email Content**: Combines all summaries into a formatted email
6. **Send Email Report**: Sends the report via SMTP

## Setup Instructions

### Prerequisites

1. n8n instance (self-hosted or cloud)
2. Notion workspace with a database containing client activities
3. OpenRouter API account
4. SMTP email server access

### Configuration Steps

1. **Import the Workflow**
   - Open n8n
   - Go to Workflows → Import
   - Upload the `notion-to-email-client-activity.json` file

2. **Configure Notion Connection**
   - In the "Get Notion Activities" node:
     - Replace `YOUR_DATABASE_ID_HERE` with your actual Notion database ID
     - Set up Notion credentials (you'll need a Notion integration token)
   - Update the filter conditions to match your database properties:
     - Modify `Status`, `Active`, `Client` field names if different
     - Adjust property names in the extraction code if needed

3. **Configure OpenRouter**
   - In the "OpenRouter Chat Model" node:
     - Add your OpenRouter API credentials
     - Select your preferred AI model (default: gpt-4-turbo)
     - Adjust temperature and max tokens if needed

4. **Configure Email Settings**
   - In the "Send Email Report" node:
     - Update `fromEmail` with your sender email
     - Update `toEmail` with recipient email(s)
     - Configure SMTP credentials (server, port, username, password)
     - Optionally add CC/BCC recipients

5. **Customize the Schedule**
   - In the "Weekly Schedule Trigger" node:
     - Modify the cron expression for different timing
     - Current: `0 10 * * 5` (Fridays at 10 AM)
     - Examples:
       - Daily at 9 AM: `0 9 * * *`
       - Mondays at 8 AM: `0 8 * * 1`
       - First of month at noon: `0 12 1 * *`

## Notion Database Structure

Your Notion database should include these properties (names can be customized):

- **Client**: Select/Text field with client name
- **Activity/Name**: Title field with activity name
- **Date**: Date field for activity date
- **Description**: Text field with activity details
- **Owner**: Person/Select field for responsible person
- **Priority**: Select field (High/Medium/Low/Normal)
- **Status**: Select/Status field (Active/Completed/etc.)
- **Active**: Checkbox field to filter active items

## Customization Options

### Modify Client Grouping Logic
Edit the "Extract and Group by Client" node to change how activities are grouped or sorted.

### Adjust AI Prompt
In the "Generate AI Summary" node, modify the prompt to change the summary format or focus areas.

### Email Template
Edit the "Format Email Content" node to customize the HTML/text email design and layout.

### Add Additional Filters
Modify the Notion query filters to include date ranges, specific owners, or priority levels.

## Troubleshooting

### Common Issues

1. **Notion Connection Error**
   - Verify your Notion integration has access to the database
   - Check that the database ID is correct
   - Ensure property names match your database schema

2. **OpenRouter API Error**
   - Verify API key is valid
   - Check API rate limits and quotas
   - Try a different model if one is unavailable

3. **Email Not Sending**
   - Verify SMTP credentials
   - Check firewall/port settings
   - Test with a simple email first
   - Check spam folders

4. **No Activities Found**
   - Verify filter conditions match your data
   - Check that activities exist with matching criteria
   - Test with broader filters initially

## Testing

1. Use the "Execute Workflow" button to test manually
2. Check each node's output to verify data flow
3. Start with a small dataset for initial testing
4. Use n8n's built-in debugging tools to inspect data

## Support

For workflow-specific issues, check:
- n8n documentation: https://docs.n8n.io
- Notion API docs: https://developers.notion.com
- OpenRouter docs: https://openrouter.ai/docs

## License

This workflow is provided as-is for use with n8n.