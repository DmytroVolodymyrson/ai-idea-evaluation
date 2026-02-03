# AI Idea Evaluation Automation

Automate competitive research for new business ideas using AI. This n8n workflow reduces idea evaluation from **20-40 hours of manual work** to **~15-25 minutes**.

## What It Does

Submit a business idea via a simple form, and the workflow will:

1. **Discover competitors** using AI-powered research (Perplexity)
2. **Deep-dive research** on each competitor's pricing, features, and weaknesses
3. **Synthesize findings** into a comprehensive analysis (Claude AI)
4. **Generate a Google Doc** with the complete evaluation report

## Workflow Architecture

```
[Form Input] → [2 Parallel Competitor Searches]
                        ↓
              [Merge & Parse Top 3 Competitors]
                        ↓
              [Loop: For each competitor]
                   ↓         ↓         ↓
            [Pricing]  [Features]  [Weaknesses]
                        ↓
              [Aggregate Research]
                        ↓
              [Claude AI Synthesis]
                        ↓
              [Create Google Doc with Report]
                        ↓
              [Return Success Page with Doc Link]
```

**Total Nodes:** 17 | **API Calls per Evaluation:** ~11 Perplexity + 1 Claude + 2 Google Docs

## Output Format

The generated report includes:

- **Summary** - Idea description and unique angle ("Hook")
- **Competitor Analysis** - For each of 3 competitors:
  - Name & URL
  - Business model
  - Pricing structure
  - Features offered
  - Weaknesses ("Attack Vectors")
- **Market Matrix** - Side-by-side comparison table
- **Business Analysis**
  - Target audience
  - Unique Value Proposition
  - Revenue streams
  - Final comparison table

See [example/The AI Biomarker Interpreter.md](example/The%20AI%20Biomarker%20Interpreter.md) for a sample output.

## Setup

### Prerequisites

- n8n instance (self-hosted or cloud)
- API keys for:
  - [Perplexity AI](https://www.perplexity.ai/) - For competitive research
  - [Anthropic](https://console.anthropic.com/) - For AI synthesis (Claude)
  - [Google Cloud](https://console.cloud.google.com/) - For Google Docs OAuth2

### Installation

1. **Import the workflow** into n8n:
   - Download `workflow_template.json`
   - In n8n, go to Workflows → Import from File
   - Select the downloaded file

2. **Configure credentials** in n8n:

   **Perplexity API:**
   - Create credential type: `Perplexity API`
   - Add your API key
   - Apply to all 5 Perplexity nodes

   **Anthropic API:**
   - Create credential type: `Anthropic API`
   - Add your API key
   - Apply to the "AI Analysis & Synthesis" node

   **Google Docs OAuth2:**
   - Create credential type: `Google Docs OAuth2`
   - Complete OAuth flow with your Google account
   - Apply to both Google Docs nodes

3. **Activate the workflow** and copy the form URL

See [SETUP_GUIDE.md](SETUP_GUIDE.md) for detailed setup instructions.

## Form Fields

| Field | Required | Description |
|-------|----------|-------------|
| Idea Name | Yes | Name of the business idea |
| Idea Summary | Yes | 2-3 sentence description |
| Target Industry | Yes | e.g., Health Tech, SaaS |
| Target Audience | Yes | Primary users description |
| Initial Pricing Thoughts | No | Pricing considerations |
| Known Competitors | No | Any competitors already known |

## Cost Estimates

Per idea evaluation:
- Perplexity API: ~$0.05-0.06
- Claude API: ~$0.01-0.03
- **Total: ~$0.07-0.10**

## Technologies Used

- **[n8n](https://n8n.io/)** - Workflow automation platform
- **[Perplexity AI](https://www.perplexity.ai/)** - AI-powered search for competitive research
- **[Anthropic Claude](https://www.anthropic.com/)** - AI synthesis and analysis
- **[Google Docs API](https://developers.google.com/docs/api)** - Document generation

## License

MIT License - Feel free to use and modify for your own purposes.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.
