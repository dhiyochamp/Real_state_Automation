# PropBillion Complete Smart System - n8n Setup Guide

## 📋 Table of Contents
1. [System Overview](#system-overview)
2. [Prerequisites](#prerequisites)
3. [Required API Keys & Services](#required-api-keys--services)
4. [Step-by-Step Setup](#step-by-step-setup)
5. [Node Configuration Details](#node-configuration-details)
6. [Testing & Troubleshooting](#testing--troubleshooting)
7. [Maintenance & Updates](#maintenance--updates)

---

## 🎯 System Overview

**What This System Does:**
- Fetches call logs from Runo.in API every 5 minutes
- Downloads and transcribes call recordings using Google Gemini AI
- Analyzes customer intent and categorizes FAQs
- Sends personalized WhatsApp video messages based on customer interests
- Updates Runo CRM with intelligent lead scoring

**Technologies Used:**
- n8n (Workflow Automation)
- Runo.in API (Call Management)
- Google Gemini 1.5 Flash (AI Transcription & Analysis)
- WhatsApp Business API (Customer Communication)

---

## ✅ Prerequisites

### 1. **n8n Installation**
Choose one of the following:

**Option A: Docker (Recommended)**
```bash
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

**Option B: npm**
```bash
npm install n8n -g
n8n start
```

**Option C: Cloud Hosting**
- [n8n Cloud](https://n8n.io/cloud/) (Easiest, paid)
- Self-hosted on VPS (DigitalOcean, AWS, etc.)

### 2. **System Requirements**
- Node.js 18+ (if using npm)
- 2GB RAM minimum
- Stable internet connection
- Domain with SSL (for webhook triggers, optional for this workflow)

---

## 🔑 Required API Keys & Services

### 1. **Runo.in API Access**

**What You Need:**
- Runo.in account with call recording feature enabled
- API Key for authentication

**How to Get It:**
1. Log in to [Runo.in Dashboard](https://runo.in)
2. Go to **Settings → API Keys**
3. Create new API key
4. Copy the API key (you provided: `MXJycmNtNWRyYXptMTU2aA==`)

**API Endpoints Used:**
```
GET https://runo.in/call/logs
POST https://runo.in/crm/interaction
```

**Required Permissions:**
- Read call logs
- Write CRM interactions

---

### 2. **Google Gemini AI API**

**What You Need:**
- Google Cloud Project
- Gemini API key

**Setup Steps:**

1. **Create Google Cloud Project:**
   - Go to [Google Cloud Console](https://console.cloud.google.com/)
   - Create new project: "PropBillion AI"

2. **Enable Gemini API:**
   - Navigate to **APIs & Services → Library**
   - Search for "Generative Language API"
   - Click **Enable**

3. **Create API Key:**
   - Go to **APIs & Services → Credentials**
   - Click **Create Credentials → API Key**
   - Copy the API key
   - (Optional) Restrict key to Generative Language API

**Pricing:** (as of Feb 2025)
- Gemini 1.5 Flash: $0.00001875 per 1K characters input
- Very affordable for call transcription
- Approximately $0.02-0.05 per 5-minute call

---

### 3. **WhatsApp Business API**

**What You Need:**
- WhatsApp Business Account
- Phone Number ID
- Access Token

**Setup Steps:**

**Option A: Meta Business Suite (Official)**

1. **Create Meta Business Account:**
   - Go to [Meta Business Suite](https://business.facebook.com/)
   - Create business account

2. **Set Up WhatsApp Business:**
   - Go to **Settings → WhatsApp Accounts**
   - Click **Add Account**
   - Follow verification process

3. **Get Phone Number ID:**
   - Go to **WhatsApp Manager**
   - Select your phone number
   - Copy **Phone Number ID** (format: `123456789012345`)

4. **Generate Access Token:**
   - Go to **WhatsApp → API Setup**
   - Click **Generate Token**
   - Copy **Permanent Access Token**

**Option B: Third-Party Providers (Easier)**
- [Twilio WhatsApp API](https://www.twilio.com/whatsapp)
- [360Dialog](https://www.360dialog.com/)
- [MessageBird](https://www.messagebird.com/)

**Required WhatsApp Templates:**

You need to create these templates in WhatsApp Business Manager:

```
Template Name: property_urgent_followup
Category: MARKETING
Language: en_US

Header: VIDEO
Body: Hi {{1}}, thank you for your call! {{2}}

Buttons: 
- Call Us: +91XXXXXXXXXX
- Visit Website: https://propbillion.com
```

Create similar templates for:
- `property_education_followup`
- `property_investment_followup`
- `property_nri_followup`
- `property_clarification_followup`
- `property_financing_followup`

**WhatsApp API Costs:**
- Conversation-based pricing
- ~₹0.50-1.00 per conversation (India)

---

### 4. **Video CDN Setup**

**What You Need:**
- Video hosting with public URLs

**Recommended Options:**

**Option A: Cloudflare R2 (Recommended - Cheap)**
```bash
# Install Wrangler CLI
npm install -g wrangler

# Login to Cloudflare
wrangler login

# Create R2 bucket
wrangler r2 bucket create propbillion-videos

# Upload videos
wrangler r2 object put propbillion-videos/rera_compliance_guide.mp4 --file ./video.mp4
```

**Public URL Format:**
```
https://pub-XXXXXXXXXXXX.r2.dev/rera_compliance_guide.mp4
```

**Option B: AWS S3**
```bash
aws s3 cp video.mp4 s3://propbillion-videos/rera_compliance_guide.mp4 --acl public-read
```

**Option C: Simple HTTP Server**
- Any web hosting with direct file access
- Ensure HTTPS enabled
- Must be publicly accessible

**Video Requirements:**
- Format: MP4 (H.264 codec)
- Max size: 16 MB (WhatsApp limit)
- Duration: 30 seconds to 3 minutes recommended
- Resolution: 720p or 1080p

---

## 🚀 Step-by-Step Setup

### **Step 1: Import Workflow into n8n**

1. **Open n8n Interface:**
   - Go to `http://localhost:5678` (or your n8n URL)
   - Login to your account

2. **Import Workflow:**
   - Click **Workflows** → **+ Add Workflow**
   - Click **⋮** (three dots) → **Import from File**
   - Upload the provided JSON file or paste JSON content
   - Click **Import**

3. **Workflow Successfully Imported!**
   - You should see all 15 nodes connected

---

### **Step 2: Configure Credentials**

#### **2.1 Runo API Key Credential**

1. Click on node: **"Runo API: Get Call Logs"**
2. Click on **Credentials** dropdown
3. Click **Create New Credential**
4. Select **Header Auth**

**Configuration:**
```yaml
Credential Name: Runo API Key
Header Name: Authorization
Header Value: Bearer MXJycmNtNWRyYXptMTU2aA==
```

5. Click **Save**

**Alternative: API Key Authentication**
```yaml
Credential Name: Runo API Key
Auth Type: API Key
API Key Name: x-api-key
API Key Value: MXJycmNtNWRyYXptMTU2aA==
```

---

#### **2.2 Google Gemini API Credential**

1. Click on node: **"Gemini AI: Transcribe Call"**
2. Click **Credentials** dropdown
3. Click **Create New Credential**
4. Select **Google Gemini API**

**Configuration:**
```yaml
Credential Name: Google Gemini API
API Key: YOUR_GEMINI_API_KEY_HERE
```

5. Click **Save**
6. **Repeat for second Gemini node:** "Gemini AI: Analyze FAQ Category"

---

#### **2.3 WhatsApp Business API Credential**

1. Click on node: **"WhatsApp: Send Video Message"**
2. Click **Credentials** dropdown
3. Click **Create New Credential**
4. Select **Header Auth**

**Configuration:**
```yaml
Credential Name: WhatsApp Business API
Header Name: Authorization
Header Value: Bearer YOUR_WHATSAPP_ACCESS_TOKEN
```

5. Click **Save**

**Update WhatsApp Node URL:**
- Replace `YOUR_PHONE_NUMBER_ID` in the URL
- Final URL format:
```
https://graph.facebook.com/v19.0/123456789012345/messages
```

---

### **Step 3: Configure Video URLs**

1. Click on node: **"Code: Smart Video & Template Selector"**
2. Click **Edit**
3. Find the `videoLibrary` object (around line 10)
4. **Replace ALL placeholder URLs with your actual video URLs:**

```javascript
const videoLibrary = {
  // LEGAL & COMPLIANCE VIDEOS
  'legal': 'https://your-cdn.r2.dev/rera_compliance_guide.mp4',
  'documents': 'https://your-cdn.r2.dev/documentation_checklist.mp4',
  'construction': 'https://your-cdn.r2.dev/possession_quality_check.mp4',
  
  // FINANCIAL VIDEOS
  'finance': 'https://your-cdn.r2.dev/home_loan_process.mp4',
  'taxation': 'https://your-cdn.r2.dev/tax_guide_property.mp4',
  
  // ... update all URLs
  
  // DEFAULT FALLBACK (IMPORTANT!)
  'default': 'https://your-cdn.r2.dev/pune_real_estate_overview.mp4'
};
```

5. Click **Save**

**Video Naming Convention:**
```
Category Videos: {category}_guide.mp4
Property Videos: {bhk_type}_properties.mp4
Location Videos: {location}_properties.mp4
```

---

### **Step 4: Update WhatsApp Templates**

1. In the same **"Code: Smart Video & Template Selector"** node
2. Find `templateMapping` object
3. **Ensure these template names match your WhatsApp templates:**

```javascript
const templateMapping = {
  'INTERESTED_BUYER': 'property_urgent_followup',
  'INFORMATION_SEEKER': 'property_education_followup',
  'INVESTOR': 'property_investment_followup',
  'NRI_BUYER': 'property_nri_followup',
  'CONFUSED': 'property_clarification_followup',
  'PRICE_CONCERNED': 'property_financing_followup'
};
```

---

### **Step 5: Test Individual Nodes**

#### **Test 1: Runo API Connection**

1. Click on **"Runo API: Get Call Logs"** node
2. Click **Execute Node** (play button icon)
3. Expected output:
```json
{
  "statusCode": 0,
  "data": {
    "data": [
      {
        "id": "call_123",
        "phoneNumber": "+919876543210",
        "duration": 120,
        "recording_url": "https://..."
      }
    ]
  }
}
```

**If Error:**
- Check API key is correct
- Verify you have call logs for today
- Check API endpoint is accessible

---

#### **Test 2: Gemini AI Transcription**

1. Manually execute nodes up to **"Gemini AI: Transcribe Call"**
2. Click **Execute Node**
3. Expected output:
```json
{
  "text": "Agent: Hello, this is PropBillion...\nCustomer: Hi, I'm interested in 2BHK..."
}
```

**If Error:**
- Verify Gemini API key is valid
- Check API quota/billing is enabled
- Ensure audio file is accessible

---

#### **Test 3: WhatsApp Message**

1. Execute up to **"WhatsApp: Send Video Message"**
2. Click **Execute Node**
3. Expected output:
```json
{
  "messaging_product": "whatsapp",
  "messages": [
    {
      "id": "wamid.XXX"
    }
  ]
}
```

**If Error:**
- Verify WhatsApp access token
- Check phone number ID is correct
- Ensure template exists and is approved
- Verify video URL is publicly accessible

---

### **Step 6: Activate Workflow**

1. Click **Toggle** switch at top-right (turns blue when active)
2. Set workflow name: "PropBillion Smart System"
3. Click **Save**

**The workflow will now:**
- Run every 5 minutes automatically
- Process new call recordings
- Send WhatsApp messages
- Update CRM

---

## 🔧 Node Configuration Details

### **Node 1: Schedule Trigger**
```yaml
Type: Schedule Trigger
Interval: Every 5 minutes
Configuration:
  - Field: minutes
  - Interval: 5
```

**Why 5 minutes?**
- Balance between responsiveness and API rate limits
- Adjust to 1 minute for faster response if needed

---

### **Node 2: Runo API Get Call Logs**
```yaml
Type: HTTP Request
Method: GET
URL: https://runo.in/call/logs
Authentication: Header Auth (Bearer Token)

Query Parameters:
  - date: {{ $now.format('YYYY-MM-DD') }} (Today's date)
  - pageNo: 1
  - isLatestOnly: true (Only new calls)
```

**API Response Structure:**
```json
{
  "statusCode": 0,
  "data": {
    "data": [
      {
        "id": "call_id",
        "phoneNumber": "+919876543210",
        "name": "Customer Name",
        "duration": 180,
        "recording_url": "https://...",
        "date": "2025-02-17"
      }
    ]
  }
}
```

---

### **Node 3: Check API Success**
```yaml
Type: IF Conditional
Conditions:
  - statusCode equals 0 (Success)
  - data.data.length > 0 (Has calls)
```

---

### **Node 4: Split Loop Each Call**
```yaml
Type: Split Out
Mode: Multiplex
Value: {{ $json.data.data }}
```
Converts array of calls into individual items for processing.

---

### **Node 5: Check Valid Recording**
```yaml
Type: IF Conditional
Conditions:
  - recording_url is not empty
  - duration > 30 seconds (minimum call length)
  - phoneNumber exists
```

**Why these filters?**
- recording_url: Need audio to transcribe
- duration > 30: Filter out accidental/test calls
- phoneNumber: Need number for WhatsApp

---

### **Node 6: Set Extract Customer Data**
```yaml
Type: Set
Extracts:
  - customerName: From name or customer.name
  - customerPhone: From phoneNumber
  - customerEmail: From email (optional)
  - callId: Unique identifier
  - recordingUrl: Audio file URL
  - callDuration: Duration in seconds
  - callDate: Date of call
```

---

### **Node 7: HTTP Download Audio Recording**
```yaml
Type: HTTP Request
Method: GET
URL: {{ $json.recordingUrl }}
Response Format: File (binary)
Output Property: audioFile
Timeout: 30000ms (30 seconds)
```

**Downloads audio as binary data for AI processing.**

---

### **Node 8: Gemini AI Transcribe Call**
```yaml
Type: Google Gemini LLM
Model: gemini-1.5-flash
Prompt: Transcribe this PropBillion real estate customer call recording accurately.

Input: Binary audio file ({{ $binary.audioFile }})
Output: Full transcript text
```

**Gemini Features Used:**
- Multimodal: Processes audio directly
- Fast inference: ~5-10 seconds for 2-minute call
- High accuracy: ~95% for clear audio

---

### **Node 9: Set Save Transcript**
```yaml
Type: Set
Saves:
  - transcript: Full text from AI response
Options:
  - includeOtherFields: true (preserves customer data)
```

---

### **Node 10: Gemini AI Analyze FAQ Category**
```yaml
Type: Google Gemini LLM
Model: gemini-1.5-flash
Temperature: 0.3 (more consistent, less creative)

Task: Analyze customer intent and categorize questions
Output: Structured JSON with 15 FAQ categories
```

**AI Prompt Structure:**
1. Customer info + transcript
2. 15 predefined FAQ categories
3. 6 intent types (INTERESTED_BUYER, INVESTOR, etc.)
4. Property preferences (BHK, location, budget)
5. Urgency scoring (HIGH/MEDIUM/LOW)

**Output Format:**
```json
{
  "primaryCategory": "legal",
  "secondaryCategories": ["finance", "documents"],
  "customerIntent": "INTERESTED_BUYER",
  "propertyType": "2BHK",
  "location": "Hinjewadi",
  "keyQuestions": ["RERA compliance?", "OC certificate?"],
  "concerns": ["Legal documentation clarity"],
  "urgencyLevel": "HIGH",
  "budgetMentioned": "40-50 lakhs",
  "timeline": "1-3_MONTHS"
}
```

---

### **Node 11: Code Parse AI Analysis**
```yaml
Type: Code (JavaScript)
Purpose: Extract JSON from AI response with fallback logic

Key Functions:
1. Parse JSON from AI text response
2. Fallback text-based extraction if JSON parse fails
3. Merge with customer data from previous nodes
```

**Fallback Logic:**
If AI doesn't return valid JSON, uses keyword matching:
- "legal" or "rera" → legal category
- "loan" or "emi" → finance category
- "2bhk" → 2BHK property type
- etc.

---

### **Node 12: Code Smart Video & Template Selector**
```yaml
Type: Code (JavaScript)
Purpose: Select best video and WhatsApp template

Priority Logic:
1. Property Type (if specified) → residential_{type} video
2. Location (if mentioned) → location_{area} video
3. FAQ Category → category-specific video
4. Fallback → default overview video

Template Selection:
- Maps customer intent to WhatsApp template
- E.g., INTERESTED_BUYER → property_urgent_followup

Priority Scoring:
- HIGH urgency or INTERESTED_BUYER → Priority 3
- MEDIUM urgency or INVESTOR → Priority 2
- Default → Priority 1
```

**Video Selection Examples:**
```javascript
// Customer interested in 2BHK in Hinjewadi
propertyType: "2BHK" → 'residential_2BHK' video
location: "Hinjewadi" → OVERRIDES with 'location_Hinjewadi' video

// Customer asking about RERA
primaryCategory: "legal" → 'legal' category video

// No specific match
→ 'default' overview video
```

---

### **Node 13: WhatsApp Send Video Message**
```yaml
Type: HTTP Request
Method: POST
URL: https://graph.facebook.com/v19.0/{PHONE_ID}/messages

Body Structure:
{
  "messaging_product": "whatsapp",
  "to": "919876543210",
  "type": "template",
  "template": {
    "name": "property_urgent_followup",
    "language": {"code": "en_US"},
    "components": [
      {
        "type": "header",
        "parameters": [{"type": "video", "video": {"link": "..."}}]
      },
      {
        "type": "body",
        "parameters": [
          {"type": "text", "text": "Customer Name"},
          {"type": "text", "text": "Personalized message"}
        ]
      }
    ]
  }
}
```

**WhatsApp Template Variables:**
- {{1}}: Customer Name
- {{2}}: Personalized message content

---

### **Node 14: Runo CRM Update**
```yaml
Type: HTTP Request
Method: POST
URL: https://runo.in/crm/interaction

Updates CRM with:
- Customer details (name, phone, email)
- Priority level (1-3)
- Detailed notes with all AI analysis
- 12 custom fields:
  - FAQ_Category
  - Customer_Intent
  - Property_Type
  - Location_Interest
  - Budget_Range
  - Purchase_Timeline
  - Urgency_Level
  - Key_Questions
  - Video_Sent
  - WhatsApp_Template
  - Call_Duration_Sec
  - Last_Contact_Date
```

**CRM Notes Format:**
```
PropBillion AI Analysis | Category: legal | Intent: INTERESTED_BUYER | 
Property: 2BHK | Location: Hinjewadi | Budget: 40-50L | 
Timeline: 1-3_MONTHS | Urgency: HIGH | 
Questions: 1. RERA compliance? | 2. OC certificate? | 
Video: Location: Hinjewadi | Concerns: Legal clarity
```

---

### **Node 15: Set Log Success**
```yaml
Type: Set
Creates execution summary:
- processingStatus: SUCCESS
- completedAt: ISO timestamp
- executionSummary: One-line summary for monitoring

Example Summary:
✅ Rahul Sharma | legal (INTERESTED_BUYER) | 2BHK in Hinjewadi | 
Priority: 3 | Video: Location: Hinjewadi
```

---

## 🧪 Testing & Troubleshooting

### **Complete End-to-End Test**

**Test Scenario: Simulate a real customer call**

1. **Prepare Test Data:**
   - Create a test call recording in Runo.in
   - Ensure it has:
     - Recording URL
     - Duration > 30 seconds
     - Valid phone number (your test WhatsApp number)

2. **Manual Execution:**
   - Click **Execute Workflow** button
   - Watch each node execute in sequence
   - Check output data at each step

3. **Expected Results:**
   - ✅ Call fetched from Runo API
   - ✅ Audio downloaded successfully
   - ✅ Transcript generated
   - ✅ Analysis JSON returned
   - ✅ Video URL selected
   - ✅ WhatsApp message sent (check your phone!)
   - ✅ CRM updated with all fields

---

### **Common Issues & Solutions**

#### **Issue 1: "No executions found" or workflow not running**

**Solution:**
```yaml
Check:
1. Workflow is ACTIVE (toggle is blue)
2. Schedule trigger is properly configured
3. Check n8n logs: Settings → Log Streaming
4. Verify n8n has internet access
```

---

#### **Issue 2: Runo API returns empty data**

**Solution:**
```yaml
Verify:
1. You have call recordings for today
2. API key is correct and active
3. Query parameters are correct:
   - date format: YYYY-MM-DD
   - isLatestOnly: true
4. Test API manually:
   curl -H "Authorization: Bearer YOUR_KEY" \
   "https://runo.in/call/logs?date=2025-02-17&pageNo=1"
```

---

#### **Issue 3: Gemini AI transcription fails**

**Possible Causes:**
```yaml
1. Invalid API key → Check Google Cloud Console
2. Billing not enabled → Enable billing on project
3. Audio format not supported → Use MP3/WAV/MP4 audio
4. File too large → Ensure < 10MB
5. Quota exceeded → Check API usage limits

Solution:
- Go to Google Cloud Console
- Check API & Services → Enabled APIs
- Verify Generative Language API is enabled
- Check quotas: APIs → Quotas
```

---

#### **Issue 4: WhatsApp message not sending**

**Common Errors:**

**Error: "Recipient phone number not valid"**
```yaml
Solution:
- Ensure country code included: 919876543210
- Remove + symbol: NOT +91, just 91
- Check number is registered on WhatsApp
```

**Error: "Template not found"**
```yaml
Solution:
- Template must be APPROVED in WhatsApp Manager
- Template name must match exactly (case-sensitive)
- Language code must match: en_US
- Check template status: WhatsApp Manager → Message Templates
```

**Error: "Media URL not accessible"**
```yaml
Solution:
- Video URL must be HTTPS (not HTTP)
- URL must be publicly accessible (no auth required)
- Test URL in browser: should download/play directly
- File size must be < 16MB (WhatsApp limit)
- Format must be MP4 (H.264 codec)
```

---

#### **Issue 5: CRM not updating**

**Solution:**
```yaml
Check:
1. Runo API key has write permissions
2. Process name exists: "PropBillion Sales Pipeline"
3. Custom fields exist in CRM
4. Test CRM API manually:
   curl -X POST https://runo.in/crm/interaction \
   -H "Authorization: Bearer YOUR_KEY" \
   -H "Content-Type: application/json" \
   -d '{"customer":{"phoneNumber":"919876543210"},"priority":2}'
```

---

### **Debugging Tips**

**Enable Debug Mode:**
1. Click workflow settings (⚙️)
2. Enable **Save Execution Progress**
3. Enable **Save Manual Executions**
4. Set timeout to 5 minutes

**View Execution Logs:**
```yaml
Location: Executions tab (left sidebar)
Shows:
- Each node's input/output data
- Error messages
- Execution time
- Success/failure status
```

**Test Individual Nodes:**
```yaml
Method:
1. Click on any node
2. Click "Execute Node" button
3. See input/output in right panel
4. Verify data structure is correct
```

**Add Error Notifications:**
```yaml
Add new node after each critical step:
Type: Send Email / Slack / Discord
Trigger: On Error
Message: Include error details and node name
```

---

## 📊 Monitoring & Maintenance

### **Performance Metrics**

**Expected Processing Time:**
```yaml
Full workflow execution: 30-60 seconds per call

Breakdown:
- Fetch call logs: 1-2 seconds
- Download audio: 2-5 seconds
- Transcription: 5-15 seconds
- Analysis: 3-8 seconds
- WhatsApp send: 1-2 seconds
- CRM update: 1-2 seconds
```

**API Usage Estimates:**
```yaml
For 100 calls/day:

Gemini AI:
- 200 API calls (transcribe + analyze)
- ~$2-5 per day

WhatsApp:
- 100 conversations
- ~₹50-100 per day (~$0.60-1.20)

Total Cost: ~$3-7 per day for 100 calls
```

---

### **Scaling Considerations**

**For High Volume (500+ calls/day):**

```yaml
1. Reduce schedule interval to 1 minute:
   - Faster response time
   - Higher n8n load

2. Add parallel processing:
   - Use SplitInBatches node
   - Process 5 calls simultaneously

3. Implement deduplication:
   - Store processed call IDs in database
   - Skip already processed calls

4. Add error retry logic:
   - Retry failed API calls 3 times
   - Exponential backoff: 1s, 5s, 15s
```

**Advanced Architecture:**
```yaml
Component               | Free Tier      | Paid Tier
------------------------|----------------|------------------
n8n                     | Self-hosted    | n8n Cloud
Database                | SQLite         | PostgreSQL
File Storage            | Local          | S3/R2
Monitoring              | n8n Logs       | Datadog/Grafana
Queue System            | -              | Redis/BullMQ
```

---

### **Regular Maintenance Tasks**

**Daily:**
```yaml
- Check execution logs for errors
- Verify WhatsApp messages are sending
- Monitor CRM data quality
```

**Weekly:**
```yaml
- Review video performance (which videos get best response)
- Update FAQ categories if needed
- Check API quota usage
- Clean up old execution data
```

**Monthly:**
```yaml
- Update video content
- Optimize AI prompts based on results
- Review and improve categorization logic
- Check API costs and optimize
```

---

## 🎓 Advanced Customizations

### **Add SMS Fallback**

If WhatsApp fails, send SMS:

```yaml
1. Add IF node after WhatsApp node
2. Check if WhatsApp response has error
3. If error, trigger SMS node (Twilio/AWS SNS)
4. Send shortened message with video link
```

---

### **Add Lead Scoring**

Enhance CRM with ML-based scoring:

```yaml
1. Add Python node after analysis
2. Load ML model (scikit-learn)
3. Score based on:
   - Call duration
   - Intent keywords
   - Budget vs property price
   - Location interest
4. Update CRM with score
```

---

### **Add Dashboard**

Create real-time monitoring:

```yaml
1. Use n8n webhook to send data to database
2. Build dashboard with:
   - Grafana (time-series graphs)
   - Metabase (business analytics)
   - Custom React app
3. Visualize:
   - Calls per day
   - Category distribution
   - Conversion rates
   - Video performance
```

---

## 📞 Support & Resources

**Documentation:**
- [n8n Documentation](https://docs.n8n.io/)
- [Runo API Docs](https://runo.in/docs/api)
- [Google Gemini API Docs](https://ai.google.dev/docs)
- [WhatsApp Business API Docs](https://developers.facebook.com/docs/whatsapp/)

**Community:**
- [n8n Community Forum](https://community.n8n.io/)
- [n8n Discord](https://discord.gg/n8n)
- Stack Overflow: Tag `n8n`

**Video Tutorials:**
- [n8n YouTube Channel](https://www.youtube.com/@n8nio)
- WhatsApp Business API Setup
- Google Gemini AI Integration

---

## ✅ Final Checklist

Before going live, verify:

- [ ] All API keys are configured and valid
- [ ] Video URLs are updated and accessible (test in browser)
- [ ] WhatsApp templates are created and APPROVED
- [ ] Test call processed successfully end-to-end
- [ ] WhatsApp message received on test phone
- [ ] CRM updated with correct data
- [ ] Workflow is ACTIVE (toggle is blue)
- [ ] Error handling nodes added (optional but recommended)
- [ ] Monitoring/logging configured
- [ ] Backup workflow exported (Download → JSON)

---

## 🎉 Congratulations!

Your PropBillion Smart System is now live and will:
- ✅ Automatically process customer calls every 5 minutes
- ✅ Send personalized videos based on interests
- ✅ Update CRM with intelligent lead data
- ✅ Save your team hours of manual work
- ✅ Improve customer response time from hours to minutes

**ROI Estimate:**
- Time saved: 30 minutes per call = 50 hours/month for 100 calls
- Cost: ~$100-150/month (APIs + hosting)
- Manual labor cost saved: $500-1000/month
- **Net benefit: $400-900/month + better customer experience**

---

**Need help? Have questions?**
- Check the Troubleshooting section above
- Visit n8n Community Forum
- Consult API documentation

**Happy Automating! 🚀**

---

*Last Updated: February 2025*
*Version: 1.0*
*Compatible with: n8n v1.0+*
