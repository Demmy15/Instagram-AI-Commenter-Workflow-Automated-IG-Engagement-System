# Instagram-AI-Commenter-Workflow-Automated-IG-Engagement-System
This project is an automated Instagram AI commenter workflow built with n8n and OpenAI GPT. The system intelligently scans Instagram posts, analyzes image content, and generates personalized, context-aware comments designed to boost engagement and reach.
# Instagram AI Comment Bot 🤖💬

An intelligent n8n workflow that automatically generates engaging, viral-style comments on Instagram posts using AI analysis and multi-model LLM processing.

![Workflow Overview](workflow-screenshot.png)

## 🎯 What It Does

This automation:
1. **Monitors Instagram posts** via Apify webhook
2. **Analyzes content** using GPT-4 Vision (images) or transcription (videos)
3. **Qualifies posts** based on relevance criteria
4. **Generates viral comments** in Alex Hormozi's style
5. **Prevents duplicates** using Supabase database
6. **Logs results** to Google Sheets for review

## 🏗️ Workflow Architecture

### Main Components

1. **Webhook Trigger** - Receives Instagram post data from Apify
2. **Content Processing Pipeline**:
   - Image posts → OpenAI GPT-4 Vision analysis
   - Video posts → Apify transcription service
   - Carousel posts → Image analysis
3. **AI Qualification** - GPT-5-nano determines if post is worth commenting on
4. **Comment Generation** - Claude Sonnet 4 generates viral comment
5. **Data Management** - Supabase + Google Sheets logging

### Post Qualification Criteria

Posts are approved if they relate to:
- Life sciences, biology, nature
- Human/animal/plant ecosystems
- Travel and adventure
- Documentaries on living subjects
- AND contain enough unique information for meaningful commentary

## 📋 Prerequisites

### Required Services & API Keys

- **n8n**
- **OpenAI API** - GPT-4 Vision & GPT-5-nano
- **Apify** - Instagram scraping & video transcription
- **Supabase** - Database for duplicate tracking
- **Google Sheets API** - Result logging

### Supabase Table Schema

Create a table named `IG Comment Log` with these columns:

```sql
CREATE TABLE "IG Comment Log" (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP DEFAULT NOW(),
  post_url TEXT,
  author TEXT,
  post_content TEXT,
  output TEXT,
  approved BOOLEAN,
  type TEXT,
  "Apify log" TEXT
);
```

## 🚀 Setup Instructions

### 1. Import the Workflow

```bash
# In n8n UI:
# 1. Go to Workflows → Import from File
# 2. Upload Final-work.json
```

### 2. Configure Credentials

You'll need to set up these credential connections:

#### OpenAI
- Node: "Analyze image", "GPT 5-nano", "OpenAI Chat Model"
- Get key from: https://platform.openai.com/api-keys

#### OpenRouter (Claude)
- Node: "Sonnet 4"
- Get key from: https://openrouter.ai/keys

#### Apify
- Nodes: "Run IG Transcriber / Get Result", "HTTP Request"
- Get key from: https://console.apify.com/account/integrations
- Update the token in both nodes

#### Supabase
- Nodes: "Check for Duplicate", "Add Row", "Add to Row"
- Get credentials from your Supabase project settings

#### Google Sheets
- Node: "Save Data to Sheet"
- Connect via OAuth2

### 3. Set Up the Webhook

1. Activate the workflow
2. Copy the webhook URL from the "Webhook" node
3. Configure Apify Instagram scraper to send results to this URL

### 4. Customize AI Prompts

Edit these nodes to match your brand voice:

- **AI Qualifier (T/F)** - Adjust qualification criteria
- **Viral Comment Writer** - Modify comment style and tone

## 🎨 Customization

### Change Comment Style

The default style is "Alex Hormozi" (direct, insightful, witty). To change:

1. Open "Viral Comment Writer" node
2. Modify the system message:
```
Style: Write in the voice of [YOUR STYLE HERE]
```

### Adjust Qualification Filters

Edit "AI Qualifier (T/F)" system message to change which posts get commented on.

### Add More Post Types

The "Switch by Format" node handles:
- Sidecar (Carousel)
- Image
- Video

Add more branches if needed.

## 📊 Output Format

### Supabase Log
Each processed post creates a record with:
- Post URL
- Author name
- Original caption
- Generated comment
- Approval status
- Post type

### Google Sheet
Approved comments are logged with:
- Date
- Post URL
- Post content
- Generated comment
- Author

## ⚙️ Configuration Variables

Key settings to adjust:

```javascript
// Loop node: Batch size for processing
batchSize: 1 // Process one post at a time

// AI Qualifier: Context length
contextWindowLength: 100 // Memory for comment variation

// Comment Writer: Temperature (creativity)
// Adjust in model settings
```

## 🔒 Security Notes

⚠️ **IMPORTANT**: The workflow JSON contains exposed API keys. Before committing:

1. Remove all API keys from the JSON
2. Use n8n environment variables instead:

```javascript
// Example: Replace hardcoded key
"openaiApiKey": "sk-proj-..."
// With:
"openaiApiKey": "{{ $env.OPENAI_API_KEY }}"
```

### Common Issues

**"No data returned" error**
- Check Apify webhook is sending data
- Verify API credentials are valid

**Comments not saving**
- Confirm Supabase table exists
- Check Google Sheets permissions

**AI responses are repetitive**
- Increase memory context window
- Add more variation prompts

## 📝 Workflow Execution Order

```
1. Webhook receives Instagram data
2. HTTP Request fetches full dataset
3. Filter: Only process Posts (not profiles)
4. Loop through each post
5. Check Supabase for duplicates
6. If new → Switch by format (Image/Video/Carousel)
7. Process content (Vision AI or Transcription)
8. AI Qualifier checks relevance
9. If approved → Generate comment
10. Save to Supabase + Google Sheets
11. Loop continues with next post
```

## 🤝 Contributing

Feel free to:
- Fork and improve the workflow
- Add new AI models
- Create alternative comment styles
- Submit issues for bugs

## 📄 License

MIT License - Feel free to use and modify for your projects


---

**Note**: This tool is for educational purposes. Always respect Instagram's Terms of Service and rate limits when automating interactions.
