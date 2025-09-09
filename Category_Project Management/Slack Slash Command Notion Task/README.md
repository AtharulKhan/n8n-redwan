# Slack /Task Command to Notion Workflow

This n8n workflow automatically creates Notion tasks from Slack slash commands with intelligent field extraction and validation.

## Features

- **Intelligent Field Extraction**: Uses AI to parse natural language task descriptions
- **Required Field Validation**: Ensures all critical information is provided
- **Immediate Response**: Handles Slack's 3-second timeout requirement
- **User Feedback**: Provides clear success/error messages back to Slack
- **Flexible Input**: Accepts informal task descriptions and extracts structured data

## Required Fields

The workflow requires these fields to create a task:
1. **End Date** - Task deadline
2. **Owner** - Person responsible
3. **Client** - Client/company name
4. **Type of Project** - Project category
5. **Service Team** - Dev or Marketing
6. **Project Links** - Related URLs/documents
7. **Task Description** - What needs to be done

## Example Commands

### Valid Command Examples:
```
/task Create landing page for SimplifAI by 12/31/2025, assigned to John, dev team, project docs at docs.google.com/project

/task Design new logo for Tracked Transport by next Friday, Saidur to handle, marketing team, brief in Figma link

/task Fix login bug for BFD app by tomorrow, Redwan owns this, dev team, github issue #123
```

### Invalid Command (Missing Required Fields):
```
/task Fix the bug
```
This will return an error message listing the missing required fields.

## Workflow Components

1. **Webhook Node**: Receives Slack slash command
2. **Respond to Webhook**: Immediate acknowledgment to Slack
3. **LLM Chain**: Extracts task fields using AI
4. **Structured Output Parser**: Formats extracted data
5. **IF Node**: Validates required fields
6. **Notion Node**: Creates task in database
7. **HTTP Request Nodes**: Send success/error responses to Slack

## Setup Instructions

### 1. Import Workflow
- Open n8n
- Go to Workflows → Import
- Import the `slack-task-to-notion-workflow.json` file

### 2. Configure Slack App
- Create a Slack app at api.slack.com
- Add a slash command pointing to your webhook URL
- The webhook URL will be: `https://your-n8n-domain/webhook/4571cdc2-f409-4d88-8f13-e69678165626`

### 3. Configure Credentials
- **OpenRouter**: Add your API key in the OpenRouter Chat Model node
- **Notion**: Connect your Notion account and select the correct database

### 4. Update Notion Database ID
- In the "Create a database page" node, ensure the database ID matches your Notion task database

### 5. Activate Workflow
- Save and activate the workflow in n8n
- Test with a Slack command

## Field Mappings

| Slack Input | Notion Property | Type | Default |
|------------|-----------------|------|---------|
| End date mentions | property_end_date | Date | Required |
| Owner/assigned to | property_owner | People | Required |
| Client/company | property_client | Select | Required |
| Project type | property_type_of_project | Multi-select | Required |
| Team (dev/marketing) | property_service_team | Select | Required |
| URLs/links | property_project_links | Text | Required |
| Task details | property_information_about_request | Text | Required |
| Priority mentions | property_priority | Select | Medium |
| Start date | property_start_date | Date | Today |
| Service type | property_service_type | Select | One-Off |
| Status | property_status | Status | To Do |
| Slack username | property_requested_by | Text | Auto-filled |

## Troubleshooting

### Webhook Not Responding
- Ensure the webhook node's response mode is set to "Using 'Respond to Webhook' Node"
- Check that the immediate response node is connected

### Fields Not Extracting
- Review the LLM prompt for clarity
- Ensure OpenRouter credentials are valid
- Check that the structured output parser schema matches expected fields

### Notion Creation Failing
- Verify Notion credentials and permissions
- Ensure database ID is correct
- Check that field mappings match your Notion database schema

### Slack Not Receiving Responses
- Verify the response_url is being passed correctly
- Check HTTP Request node configurations
- Ensure the workflow is active

## Customization

### Adding Optional Fields
1. Update the LLM prompt to extract new fields
2. Add fields to the Structured Output Parser schema
3. Map fields in the Notion node

### Changing Required Fields
1. Modify the validation logic in the LLM prompt
2. Update the IF node conditions
3. Adjust error message in the error response node

### Modifying Response Messages
Edit the text in the HTTP Request nodes to customize success/error messages sent back to Slack.