# AI-Powered Website Scraper to Proposal Generator v2

## Overview
This n8n workflow automatically scrapes up to 15 pages from any website and generates a comprehensive, customized business proposal using AI. Version 2 adds a beautiful form interface for easy client data collection and creates highly personalized proposals based on specific business requirements.

## Features
- 📝 **Dual Input Methods**: Beautiful web form OR webhook API for flexibility
- 🔍 **Intelligent Web Scraping**: Analyzes client website PLUS competitor sites
- 🤖 **AI-Powered Personalization**: Uses client data to create tailored proposals
- 🎯 **Client-Specific Content**: Proposals address specific goals, budget, and timeline
- 📄 **Professional Output**: 16-section proposals customized to service type
- 💾 **Smart Response Handling**: HTML response for forms, file download for API
- ⚡ **Parallel AI Processing**: 3 specialized AI nodes for optimal quality
- 📊 **Competitor Analysis**: Automatically analyzes up to 3 competitor websites

## Quick Start

### Prerequisites
1. **n8n Instance**: Self-hosted or cloud version
2. **OpenRouter API Key**: For AI model access (supports multiple models)
3. **Apify API Key** (Optional): For enhanced scraping capabilities

### Installation

1. **Import the Workflow**
   - Open n8n
   - Go to Workflows → Import
   - Select `proposal_generator_openrouter.json` (v3 - OpenRouter with form support)
   - Or use `proposal_generator_workflow_v2.json` (v2 - OpenAI with form support)
   - Or use `proposal_generator_workflow.json` (v1 - webhook only)
   - Click Import

2. **Configure Credentials**

   **For OpenRouter Version (v3):**
   - Go to Credentials → New
   - Select "Header Auth"
   - Set Header Name: `Authorization`
   - Set Header Value: `Bearer YOUR_OPENROUTER_API_KEY`
   - Save as "OpenRouter API"
   - Get your API key from: https://openrouter.ai/keys

   **For OpenAI Version (v2):**
   - Go to Credentials → New
   - Select "OpenAI API"
   - Enter your OpenAI API key
   - Save as "OpenAI API"

   **Apify Credentials (Optional - for both versions):**
   - Go to Credentials → New
   - Select "Header Auth"
   - Set Header Name: `Authorization`
   - Set Header Value: `Bearer YOUR_APIFY_TOKEN`
   - Save as "Apify API"

3. **Activate the Workflow**
   - Open the imported workflow
   - Click "Active" toggle
   - Copy the webhook URL

## Usage

### Method 1: Web Form (Easiest)
1. Activate the workflow
2. Navigate to: `https://your-n8n-instance.com/form/proposal-form`
3. Fill out the comprehensive intake form with:
   - Contact information
   - Business details
   - Service requirements
   - Budget and timeline
   - Competitor information
4. Submit to generate personalized proposal
5. Download directly from the results page

### Method 2: Webhook API (For Integration)

#### Basic Request
```bash
curl -X POST https://your-n8n-instance.com/webhook/generate-proposal-api \
  -H "Content-Type: application/json" \
  -d '{
    "email": "client@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "businessName": "Acme Corp",
    "businessDescription": "We provide innovative solutions...",
    "websiteUrl": "https://example.com",
    "businessGoals": "Increase online sales by 50%",
    "serviceType": "E-commerce Solutions",
    "targetLocations": "United States, California",
    "targetAudience": "Small business owners aged 25-45",
    "monthlyBudget": "$5,000-$10,000",
    "timeline": "1-2 months"
  }'
```

#### Full Request with All Fields
```bash
curl -X POST https://your-n8n-instance.com/webhook/generate-proposal-api \
  -H "Content-Type: application/json" \
  -d '{
    "email": "client@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "businessName": "Acme Corp",
    "businessDescription": "Leading provider of innovative business solutions",
    "websiteUrl": "https://example.com",
    "competitors": "competitor1.com, competitor2.com, competitor3.com",
    "businessGoals": "Increase market share and improve online presence",
    "serviceType": "Comprehensive Digital Marketing",
    "targetLocations": "United States, California, San Francisco Bay Area",
    "targetAudience": "Tech-savvy professionals and small business owners",
    "monthlyBudget": "$10,000-$25,000",
    "timeline": "ASAP (Within 1 week)",
    "campaignTypes": ["Search Ads", "Display Ads", "Social Media Ads"],
    "currentChallenges": "Low conversion rates and poor SEO rankings",
    "additionalInfo": "Looking for long-term partnership",
    "maxPages": 15,
    "useApify": false
  }'
```

### Form Fields Reference

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| **Contact Information** |
| `email` | string | Yes | Client email address |
| `firstName` | string | Yes | Client first name |
| `lastName` | string | Yes | Client last name |
| **Business Information** |
| `businessName` | string | Yes | Company name |
| `businessDescription` | string | Yes | Business overview |
| `websiteUrl` | string | Yes | Website to analyze |
| `competitors` | string | No | Competitor websites (comma-separated) |
| **Requirements** |
| `businessGoals` | string | Yes | Primary objectives |
| `serviceType` | string | Yes | Type of service needed |
| `targetLocations` | string | Yes | Geographic targets |
| `targetAudience` | string | Yes | Target demographics |
| **Budget & Timeline** |
| `monthlyBudget` | string | Yes | Budget range |
| `timeline` | string | Yes | Project urgency |
| **Campaign Details** |
| `campaignTypes` | array | No | Types of campaigns |
| `currentChallenges` | string | No | Current pain points |
| `additionalInfo` | string | No | Extra requirements |
| **Technical** |
| `maxPages` | number | No | Pages to scrape (default: 15) |
| `useApify` | boolean | No | Use Apify scraping |

## Workflow Components

### 1. **Webhook Trigger**
- Receives incoming requests
- Validates parameters
- Initiates the workflow

### 2. **Scraping Module**
Two paths based on configuration:

**Native Scraping:**
- HTTP Request node fetches pages
- HTML Extract parses content
- Code node manages crawling logic

**Apify Integration:**
- Configures Apify actor
- Handles JavaScript-rendered content
- Manages anti-bot measures

### 3. **Content Processing**
- Aggregates scraped content
- Extracts key information:
  - Page titles and meta descriptions
  - Headings (H1, H2, H3)
  - Main content paragraphs
  - Internal links for crawling

### 4. **AI Analysis (3 Parallel Nodes)**
The workflow uses three specialized AI nodes running in parallel for optimal quality:

**AI Part 1: Executive & Analysis (Sections 1-5)**
- Executive Summary
- Understanding of Your Business
- Industry Analysis
- Opportunities & Challenges
- Strategic Recommendations Overview

**AI Part 2: Solutions & Implementation (Sections 6-10)**
- Proposed Solutions
- Implementation Timeline
- Investment & Pricing Options
- Expected Results & ROI
- Success Metrics & KPIs

**AI Part 3: Closing & CTA (Sections 11-16)**
- Why Choose Us
- Our Guarantees
- Client Testimonials
- Next Steps
- Terms & Conditions
- Contact Information & Call to Action

### 5. **Proposal Assembly**
- Combines all three AI responses
- Adds table of contents with navigation links
- Formats into cohesive 16-section document
- Maintains consistent tone and style

### 6. **Output Formatting**
- Formats proposal in professional markdown
- Adds metadata and timestamps
- Converts to downloadable file
- Returns via webhook response

## Troubleshooting

### Common Issues

**1. Workflow Not Triggering**
- Check webhook URL is correct
- Ensure workflow is active
- Verify n8n instance is running

**2. Scraping Fails**
- Website may require JavaScript rendering (use Apify)
- Check for rate limiting
- Verify selectors match website structure

**3. AI Generation Errors**
- Verify OpenAI API key is valid
- Check API quota/limits
- Reduce content size if too large

**4. Empty Proposals**
- Ensure website has sufficient content
- Check scraping selectors
- Verify content extraction logic

### Debug Mode
Enable detailed logging:
1. Open workflow settings
2. Set "Save Execution Progress" to "Yes"
3. Run workflow
4. Check execution history for details

## Customization

### Modifying the AI Prompt
Edit the "Generate Proposal with AI" node to customize:
- Proposal sections
- Tone and style
- Industry-specific focus
- Language and formatting

### Adjusting Scraping Behavior
In "Configure Scraper" node:
```javascript
const maxPages = Math.min($input.first().json.maxPages || 15, 15);
// Modify maximum pages limit

const scrapingConfig = {
  // Add custom configuration
  includePDFs: true,
  followExternalLinks: false,
  respectRobotsTxt: true
};
```

### Custom Data Extraction
In "Extract HTML Content" node, add selectors:
```json
{
  "key": "prices",
  "cssSelector": ".price, .cost",
  "returnValue": "text",
  "multiple": true
}
```

## Best Practices

### 1. **Respect Rate Limits**
- Add delays between requests
- Use Apify for sites with rate limiting
- Monitor API usage

### 2. **Optimize Costs**
- Start with fewer pages (3-5) for testing
- Use native scraping for simple sites
- Cache results when possible

### 3. **Error Handling**
- Implement retry logic
- Log failed requests
- Provide fallback options

### 4. **Data Quality**
- Validate scraped content
- Remove duplicate information
- Focus on relevant pages

## Files Included

1. **proposal_generator_openrouter.json** - Latest v3 with OpenRouter AI support
2. **proposal_generator_workflow_v2.json** - v2 with OpenAI and form support
3. **proposal_generator_workflow.json** - Original v1 (webhook only)
4. **apify_integration_guide.md** - Detailed Apify setup instructions
5. **proposal_template.md** - Template structure for proposals
6. **README.md** - This documentation file

## Version Comparison

| Feature | v1.0 | v2.0 | v3.0 (OpenRouter) |
|---------|------|------|-------------------|
| Web Form Interface | ❌ | ✅ | ✅ |
| Webhook API | ✅ | ✅ | ✅ |
| Client Data Integration | Basic | Advanced | Advanced |
| Competitor Analysis | ❌ | ✅ | ✅ |
| Personalized Content | Limited | Extensive | Extensive |
| Service-Specific Proposals | ❌ | ✅ | ✅ |
| Budget-Based Packages | ❌ | ✅ | ✅ |
| HTML Response Page | ❌ | ✅ | ✅ |
| Parallel AI Processing | ✅ | ✅ | ✅ |
| AI Provider | OpenAI | OpenAI | OpenRouter |
| Model Selection | GPT-4 | GPT-4 | Multiple Models |
| Cost Optimization | ❌ | ❌ | ✅ |
| Sections | 16 | 16 (personalized) | 16 (personalized) |

## Support & Resources

### Documentation
- [n8n Documentation](https://docs.n8n.io)
- [OpenRouter API Docs](https://openrouter.ai/docs)
- [OpenAI API Docs](https://platform.openai.com/docs)
- [Apify Documentation](https://docs.apify.com)

### Community
- [n8n Community Forum](https://community.n8n.io)
- [GitHub Issues](https://github.com/n8n-io/n8n/issues)

## License
This workflow is provided as-is for use with n8n. Feel free to modify and distribute according to your needs.

---

**Created by:** AI Workflow Automation Team  
**Last Updated:** January 2025  
**Version:** 3.0.0