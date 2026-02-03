# AI Idea Evaluation Workflow - Setup Guide

## Workflow Overview

**Workflow Name:** AI Idea Evaluation - Automated Competitor Analysis
**Status:** Import from `workflow_template.json` and configure credentials

This workflow automates the idea evaluation process from 20-40 hours of manual work to ~15-25 minutes.

---

## Workflow Architecture

```
[Form Input] → [Competitor Discovery (2 parallel searches)]
                        ↓
              [Merge & Parse Competitors]
                        ↓
              [Loop: For each competitor]
                   ↓   ↓   ↓
         [Pricing] [Features] [Weaknesses]  (3 parallel searches)
                        ↓
              [Aggregate Research]
                        ↓
              [AI Synthesis (Claude)]
                        ↓
              [Markdown → HTML]
                        ↓
              [Create Google Doc]
                        ↓
              [Return Success Page]
```

**Total Nodes:** 18
**Estimated API Calls per Evaluation:** ~11 Perplexity + 1 Claude

---

## Required Credentials

### 1. Perplexity API (Native n8n Credential)

**Used by:** Discover Competitors (Search 1 & 2), Research Pricing, Research Features, Research Weaknesses

**Setup:**
1. Go to [Perplexity AI](https://www.perplexity.ai/) and sign up
2. Navigate to Settings → API
3. Generate an API key
4. In n8n, create a new credential:
   - Type: **Perplexity API**
   - Name: `Perplexity API`
   - API Key: `YOUR_PERPLEXITY_API_KEY`

**Note:** The workflow uses the native n8n Perplexity node (not HTTP Request), which provides:
- Built-in model selection (sonar, sonar-pro, sonar-reasoning, etc.)
- Simplified message configuration
- Native credential management

**Nodes using this credential:**
- Discover Competitors (Search 1)
- Discover Competitors (Search 2)
- Research Pricing
- Research Features
- Research Weaknesses

---

### 2. Anthropic API (Native n8n Credential)

**Used by:** AI Analysis & Synthesis

**Setup:**
1. Go to [Anthropic Console](https://console.anthropic.com/)
2. Create an API key
3. In n8n, create a new credential:
   - Type: **Anthropic API**
   - Name: `Anthropic API`
   - API Key: `YOUR_ANTHROPIC_API_KEY`

**Note:** The workflow uses the native n8n Anthropic node (`@n8n/n8n-nodes-langchain.anthropic`), which provides:
- Built-in model selection from dropdown
- Native credential management
- Simplified message configuration
- Options for temperature, max tokens, etc.

**Model Used:** `claude-sonnet-4-20250514`

**Node Configuration:**
- Resource: Text
- Operation: Message a Model
- Simplify Output: Yes (returns clean text)

---

### 3. Google Docs OAuth2

**Used by:** Create Google Doc, Update Doc Content

**Setup:**
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or use existing)
3. Enable the **Google Docs API**
4. Create OAuth 2.0 credentials:
   - Application type: Web application
   - Add redirect URI: Your n8n instance OAuth callback URL
5. In n8n, create a new credential:
   - Type: **Google Docs OAuth2**
   - Enter your Client ID and Client Secret
   - Complete the OAuth flow

---

## How to Configure Credentials in n8n

1. Open the workflow in n8n editor
2. Click on each HTTP Request node that needs Perplexity:
   - Click the node
   - In the "Authentication" section, select "Header Auth"
   - Click the credential dropdown and select your Perplexity credential
3. For the AI Synthesis node:
   - Select "Header Auth"
   - Choose your Anthropic credential
4. For Google Docs nodes:
   - Select your Google Docs OAuth2 credential

---

## Form Fields

The workflow collects the following information via form:

| Field | Required | Description |
|-------|----------|-------------|
| Idea Name | Yes | Name of the business idea |
| Idea Summary | Yes | 2-3 sentence description |
| Target Industry | Yes | e.g., Health Tech, SaaS |
| Target Audience | Yes | Primary users description |
| Initial Pricing Thoughts | No | Pricing considerations |
| Known Competitors | No | Any competitors already known |

---

## Output

The workflow generates:

1. **Google Doc** with full analysis including:
   - Idea Summary & Hook
   - Competitor Analysis (3 competitors)
   - Market Matrix comparison table
   - Business Analysis
   - Revenue stream recommendations
   - Final comparison table

2. **Confirmation Page** shown to user with link to Google Doc

---

## Activating the Workflow

After configuring credentials:

1. Click the toggle in the top-right to activate the workflow
2. Copy the Form URL from the "Idea Input Form" node
3. Share the form URL with team members

---

## Cost Estimates

**Per idea evaluation:**
- Perplexity API: ~11 calls × ~$0.005 = ~$0.055
- Claude API: 1 call × ~$0.01-0.03 = ~$0.02
- **Total: ~$0.07-0.10 per evaluation**

---

## Troubleshooting

### "Authentication failed" errors
- Verify API keys are correctly formatted
- Check that Perplexity header uses `Bearer ` prefix
- Ensure Anthropic key is directly in header value (no Bearer prefix)

### Google Docs creation fails
- Re-authenticate OAuth credentials
- Ensure Google Docs API is enabled in your Google Cloud project
- Check that OAuth redirect URI matches your n8n instance

### No competitors found
- Try different wording in the idea summary
- Provide more specific industry/target audience
- Check Perplexity API response in execution logs

---

## Maintenance

- **API Keys:** Rotate credentials periodically
- **Prompt Updates:** Modify the AI Synthesis prompt to adjust output format
- **Add Competitors:** Change `slice(0, 3)` in Parse & Dedupe node to analyze more competitors

---

## Support

For issues or modifications, contact your automation team.
