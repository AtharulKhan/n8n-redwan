# Apify Integration Guide for n8n Web Scraping

## Overview
Apify is a powerful web scraping and automation platform that can handle complex websites, including those with JavaScript rendering, anti-bot protection, and dynamic content. This guide will help you set up Apify with the n8n workflow for enhanced web scraping capabilities.

## Why Use Apify?

### Advantages over Native HTTP Scraping:
- **JavaScript Rendering**: Scrapes SPAs and dynamic content
- **Anti-Bot Bypass**: Handles CAPTCHAs and rate limiting
- **Proxy Rotation**: Automatic IP rotation for large-scale scraping
- **Cloud Processing**: Offloads heavy processing from your n8n instance
- **Pre-built Actors**: Ready-to-use scrapers for popular websites

## Setup Instructions

### Step 1: Create Apify Account
1. Visit [Apify.com](https://apify.com)
2. Sign up for a free account (includes $5 monthly credit)
3. Navigate to Settings → Integrations → API

### Step 2: Get Your API Token
1. In Apify Console, go to Settings → Integrations
2. Copy your Personal API token
3. Keep it secure - you'll need it for n8n

### Step 3: Configure n8n Credentials
1. In n8n, go to Credentials
2. Create new "Header Auth" credentials
3. Set the following:
   ```
   Name: Apify API
   Header Name: Authorization
   Header Value: Bearer YOUR_APIFY_API_TOKEN
   ```

### Step 4: Choose an Apify Actor

#### Recommended Actors for Web Scraping:

##### 1. **Website Content Crawler** (apify/website-content-crawler)
Best for: General website scraping with content extraction
```json
{
  "startUrls": [{"url": "https://example.com"}],
  "maxRequestsPerCrawl": 15,
  "linkSelector": "a[href]",
  "removeElementsCssSelector": "script, style, noscript",
  "keepUrlFragments": false
}
```

##### 2. **Web Scraper** (apify/web-scraper)
Best for: Complex scraping with custom data extraction
```json
{
  "startUrls": [{"url": "https://example.com"}],
  "pseudoUrls": [{"purl": "https://example.com/[.*]"}],
  "maxPagesPerCrawl": 15,
  "pageFunction": "async function pageFunction(context) { return context.jQuery('body').text(); }"
}
```

##### 3. **Cheerio Scraper** (apify/cheerio-scraper)
Best for: Fast HTML scraping without JavaScript
```json
{
  "startUrls": [{"url": "https://example.com"}],
  "maxRequestsPerCrawl": 15,
  "pseudoUrls": [{"purl": "https://example.com/[.*]"}]
}
```

## Workflow Configuration

### Using Apify in the n8n Workflow

1. **In the webhook trigger**, pass these parameters:
   ```json
   {
     "websiteUrl": "https://target-website.com",
     "maxPages": 15,
     "useApify": true
   }
   ```

2. **The workflow will automatically**:
   - Configure the Apify actor
   - Set crawling limits
   - Handle pagination
   - Extract content

### API Endpoints

#### Start a Scraping Run:
```
POST https://api.apify.com/v2/acts/{actorId}/runs
```

#### Get Run Results:
```
GET https://api.apify.com/v2/actor-runs/{runId}/dataset/items
```

## Cost Optimization

### Free Tier Limits:
- **$5 monthly credit** (approximately 1000 pages)
- **30 GB storage**
- **Shared proxies**

### Tips to Minimize Costs:
1. **Limit page depth**: Set `maxRequestsPerCrawl` to exactly what you need
2. **Filter URLs**: Use `pseudoUrls` to only crawl relevant pages
3. **Remove unnecessary elements**: Strip scripts, styles, and ads
4. **Use Cheerio Scraper** for simple HTML (cheaper than Puppeteer)
5. **Cache results**: Store scraped data to avoid re-crawling

## Advanced Configuration

### Custom Page Function (for Web Scraper actor):
```javascript
async function pageFunction(context) {
    const { $, request, log } = context;
    
    // Extract custom data
    const data = {
        url: request.url,
        title: $('title').text(),
        headings: $('h1, h2, h3').map((i, el) => $(el).text()).get(),
        paragraphs: $('p').map((i, el) => $(el).text()).get(),
        // Add custom selectors
        prices: $('.price').map((i, el) => $(el).text()).get(),
        products: $('.product-title').map((i, el) => $(el).text()).get()
    };
    
    return data;
}
```

### Proxy Configuration:
```json
{
  "proxyConfiguration": {
    "useApifyProxy": true,
    "apifyProxyGroups": ["RESIDENTIAL"],
    "apifyProxyCountry": "US"
  }
}
```

### Request Handling:
```json
{
  "maxRequestRetries": 3,
  "requestTimeoutSecs": 30,
  "handlePageTimeoutSecs": 60,
  "maxConcurrency": 10
}
```

## Troubleshooting

### Common Issues and Solutions:

1. **Rate Limiting**
   - Solution: Enable proxy rotation
   - Add delays: `"waitForSelectorTimeoutSecs": 10`

2. **JavaScript Content Not Loading**
   - Use Puppeteer Scraper instead of Cheerio
   - Add wait conditions: `"waitForSelector": ".content-loaded"`

3. **CAPTCHA Blocks**
   - Enable residential proxies
   - Reduce concurrency: `"maxConcurrency": 1`

4. **Memory Errors**
   - Reduce `maxRequestsPerCrawl`
   - Use `"maxRequestsPerMinute": 30`

5. **Incomplete Data**
   - Check selectors in browser DevTools
   - Use `"keepDuplicateUrls": true` for pagination

## Integration with n8n Workflow

### Webhook Payload Example:
```bash
curl -X POST https://your-n8n-instance.com/webhook/generate-proposal \
  -H "Content-Type: application/json" \
  -d '{
    "websiteUrl": "https://example.com",
    "maxPages": 15,
    "useApify": true,
    "apifyActor": "website-content-crawler",
    "customSelectors": {
      "products": ".product-card",
      "prices": ".price-tag"
    }
  }'
```

### Response Handling:
The workflow will return a downloadable markdown file with the generated proposal.

## Best Practices

1. **Test with small page counts first** (2-3 pages)
2. **Monitor usage** in Apify dashboard
3. **Use webhook notifications** for long-running tasks
4. **Implement error handling** in n8n workflow
5. **Store API credentials securely** in n8n
6. **Regular cleanup** of Apify datasets (auto-expires after 7 days)

## Alternative Scrapers

If Apify doesn't meet your needs, consider:

1. **ScrapingBee**: Great for JavaScript rendering
2. **Scrapy Cloud**: Python-based, very powerful
3. **Puppeteer/Playwright**: Self-hosted option
4. **Bright Data**: Enterprise-grade solution
5. **ScrapFly**: Good anti-bot bypass

## Support Resources

- [Apify Documentation](https://docs.apify.com)
- [Apify Academy](https://developers.apify.com/academy)
- [n8n Community Forum](https://community.n8n.io)
- [API Reference](https://docs.apify.com/api/v2)

## Conclusion

Apify integration significantly enhances the web scraping capabilities of your n8n workflow, especially for complex websites. While the native HTTP Request node works well for simple sites, Apify excels at handling modern web applications with anti-bot measures.