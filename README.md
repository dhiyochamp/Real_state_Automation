# PropBillion v2.0 - Quick Start Guide
## Get Up and Running in 30 Minutes

---

## ⚡ Fast Track Setup

### Step 1: Import Workflow (2 minutes)

1. Open n8n at `http://localhost:5678`
2. Click **"Workflows"** → **"Import from File"**
3. Upload `propbillion_enhanced_workflow.json`
4. Workflow imported! ✅

### Step 2: Add Credentials (5 minutes)

Click the **"Credentials"** tab and add these four credentials:

#### A. Runo API Key
```
Type: HTTP Header Auth
Name: Runo API Key
Header Name: Authorization
Header Value: Bearer YOUR_RUNO_API_KEY
```

#### B. Google Gemini API  
```
Type: Google Gemini API
API Key: YOUR_GEMINI_API_KEY
```
Get your key: https://makersuite.google.com/app/apikey

#### C. WhatsApp Business API
```
Type: HTTP Header Auth
Name: WhatsApp Business API
Header Name: Authorization
Header Value: Bearer YOUR_WHATSAPP_TOKEN
```

#### D. Slack Webhook (Optional)
```
Just paste your Slack webhook URL in the Slack node
```

### Step 3: Update Phone Number (1 minute)

In the **"WhatsApp: Send Personalized Video Message"** node, find this line:
```
URL: https://graph.facebook.com/v19.0/YOUR_PHONE_NUMBER_ID/messages
```

Replace `YOUR_PHONE_NUMBER_ID` with your actual WhatsApp Business Phone Number ID.

### Step 4: Update Video URLs (10 minutes)

In the **"Code: Intelligent Video & Message Personalizer"** node, scroll to the `videoLibrary` object and update ALL video URLs:

```javascript
const videoLibrary = {
  // Replace these with your actual video URLs
  'property_1BHK': 'https://YOUR-CDN.com/1bhk.mp4',
  'property_2BHK': 'https://YOUR-CDN.com/2bhk.mp4',
  'property_3BHK': 'https://YOUR-CDN.com/3bhk.mp4',
  // ... etc
};
```

**Don't have videos yet?** Use one default video for testing:
```javascript
const videoLibrary = {
  'default': 'https://your-cdn.com/general-property-video.mp4'
};

// Then simplify the video selection:
let selectedVideo = videoLibrary['default'];
```

### Step 5: Create WhatsApp Templates (10 minutes)

Go to **Meta Business Suite** → **WhatsApp Manager** → **Message Templates**

Create these essential templates:

**Template 1: property_hot_lead_urgent**
```
Category: MARKETING
Name: property_hot_lead_urgent
Language: English (US)

Header: Video

Body:
Hi {{1}},

{{2}}

Our team will follow up with you {{3}}.

Buttons:
- Schedule Visit (Quick Reply)
- More Info (Quick Reply)
```

**Template 2: property_general_followup** (Fallback)
```
Category: MARKETING
Name: property_general_followup
Language: English (US)

Header: Video

Body:
Hi {{1}},

Thank you for your interest in PropBillion properties. {{2}}

We'll be in touch {{3}}.

Buttons:
- View Details (Quick Reply)
- Contact Us (Quick Reply)
```

Wait for WhatsApp to approve these templates (usually 15 minutes to 24 hours).

### Step 6: Test Run (2 minutes)

1. Click **"Execute Workflow"** button in n8n
2. Watch each node turn green ✅
3. Check for any red error nodes ❌
4. If errors, check the error message and fix credentials

### Step 7: Activate! (1 second)

Click the **toggle switch** at the top to activate the workflow.

**Done!** Your automation is now live and will process calls every 3 minutes.

---

## 🎯 What Happens Now?

Every 3 minutes, the workflow automatically:

1. ✅ Fetches new call logs from Runo
2. ✅ Downloads call recordings
3. ✅ Transcribes with AI (speaker labels, timestamps)
4. ✅ Analyzes customer intent, property needs, budget
5. ✅ Scores leads (0-100 with A+ to D grades)
6. ✅ Selects personalized video content
7. ✅ Sends WhatsApp message with video
8. ✅ Updates CRM with 25+ data points
9. ✅ Creates follow-up tasks
10. ✅ Sends Slack alerts for hot leads

---

## 📊 Monitoring Your System

### View Execution History
1. Go to **"Executions"** tab in n8n
2. See all workflow runs with success/fail status
3. Click any execution to see detailed data flow

### Check for Errors
- Red nodes = errors
- Click on red node to see error message
- Common fixes:
  - Invalid API key → Update credentials
  - Rate limit → Wait or upgrade API plan
  - Invalid phone number → Check formatting

### See What's Being Processed
Each execution shows:
- Customer name and phone
- Lead score and grade
- Video sent
- CRM update status

---

## 🚀 Quick Wins

### High-Priority Lead Alerts

When an A+ or A grade lead is detected, you'll get:
- **Slack notification** (if configured)
- **Automated task** in Runo CRM
- **Follow-up timing** (usually 2-4 hours)

Check Slack or your Runo tasks to see hot leads!

### Lead Distribution

Check your CRM to see leads organized by:
- **Score**: 0-100
- **Grade**: A+, A, B, C, D
- **Stage**: Hot Lead, Warm Lead, Qualified Lead, Cold Lead
- **Intent**: Interested Buyer, Investor, etc.
- **Property Type**: 1BHK, 2BHK, 3BHK, etc.
- **Location**: Hinjewadi, Kharadi, Baner, etc.

### Performance Metrics

Track these KPIs weekly:
- Total calls processed
- A+/A lead percentage
- Average lead score
- Conversion rate
- Response time

---

## 🐛 Common Issues & Quick Fixes

### Issue 1: "No calls being processed"

**Fix:**
```
1. Check Runo API is returning data
   - Test URL: https://runo.in/call/logs?date=2025-02-27&pageNo=1
   
2. Verify API key is correct
   - Go to Credentials → Runo API Key → Test

3. Check if calls meet validation criteria:
   - Has recording URL
   - Duration > 30 seconds
   - Has phone number
   - Status = completed
```

### Issue 2: "WhatsApp not sending"

**Fix:**
```
1. Verify phone number format
   - Should be: 91XXXXXXXXXX (no + sign)
   - Not: +91 XXXXX XXXXX

2. Check template is approved
   - Go to Meta Business Suite → Message Templates
   - Status should be "Approved" (green)

3. Verify WhatsApp token
   - Test in Postman or curl first
```

### Issue 3: "AI returning errors"

**Fix:**
```
1. Check Gemini API quota
   - Go to: https://makersuite.google.com/app/apikey
   - Check usage limits

2. Reduce token usage
   - Shorten transcript before sending to AI
   - Use gemini-1.5-flash instead of pro

3. Check audio file size
   - Max: 20MB for Gemini
   - Compress if needed
```

### Issue 4: "Duplicate calls being processed"

**Check:**
```
The workflow has deduplication built-in.

If duplicates still happen:
1. Check "Code: Advanced Validation + Deduplication" node
2. Verify static data is being saved
3. Clear static data and restart:
   - Workflow Settings → Static Data → Clear
```

---

## 📈 Optimization Tips

### Week 1: Monitor & Adjust

**Watch for:**
- What lead scores are most leads getting?
- Which video categories are sent most?
- Any error patterns?

**Adjust:**
- If too many low scores (C/D), review scoring weights
- If videos don't match well, expand video library
- If errors persist, check API limits

### Week 2: Improve Scoring

**Based on conversions:**
```javascript
// In "Code: Advanced Lead Scoring Engine" node
// Adjust these weights based on what converts best

const intentScores = {
  'INTERESTED_BUYER': 30,  // Increase if they convert well
  'INVESTOR': 25,          // Decrease if they're tire-kickers
  // etc.
};
```

### Week 3: Content Performance

**Track which videos work best:**
- Open rate
- Response rate
- Conversion rate

**Double down on winners:**
- Create more videos in successful categories
- Update selection logic to prefer high-performers

### Week 4: Automation Expansion

**Add more workflows:**
- Follow-up sequence workflow
- Site visit booking workflow
- Post-booking onboarding workflow
- Feedback collection workflow

---

## 🎓 Understanding the Flow

### What Each Node Does (Simplified)

1. **Schedule Trigger** → Runs every 3 minutes
2. **Fetch Calls** → Gets new call logs from Runo
3. **Validate** → Checks if calls are processable
4. **Extract Data** → Pulls customer info
5. **Download Audio** → Gets recording file
6. **Transcribe** → AI converts speech to text
7. **Analyze** → AI understands customer needs
8. **Score** → Calculates lead quality (0-100)
9. **Personalize** → Picks perfect video & message
10. **WhatsApp** → Sends to customer
11. **Update CRM** → Saves all insights
12. **Create Task** → Assigns follow-up
13. **Alert Team** → Notifies Slack

### Data Flow

```
Call Recording
    ↓
Transcript (Text)
    ↓
AI Analysis (Structured Data)
    ↓
Lead Score + Grade
    ↓
Personalized Content
    ↓
Customer Engagement
    ↓
CRM Intelligence
```

---

## 🎯 Success Metrics (30 Days)

Track these to measure success:

### Input Metrics
- Calls processed per day
- Processing success rate
- Average processing time

### Quality Metrics
- Lead score distribution
- A+/A lead percentage
- Deduplication accuracy

### Output Metrics
- WhatsApp delivery rate
- Message open rate
- Response rate
- Site visit bookings
- Conversion rate

### Efficiency Metrics
- Time saved per lead
- Cost per qualified lead
- Agent productivity increase

---

## 📞 Next Steps

**Day 1:**
- ✅ Setup complete
- Monitor first 10-20 calls
- Fix any immediate issues

**Day 2-7:**
- Review lead quality
- Check team is following up on hot leads
- Adjust video selection if needed

**Week 2:**
- Analyze first week's data
- Optimize scoring weights
- Add more video content

**Week 3:**
- Set up dashboard (optional but recommended)
- Create automated reports
- Train team on using enriched CRM data

**Week 4:**
- Plan expansion (more workflows)
- Consider MCP integration
- Scale to handle more volume

---

## 💡 Pro Tips

1. **Start Simple**
   - Use one default video for first week
   - Focus on getting the flow working
   - Add complexity gradually

2. **Monitor Daily**
   - Check n8n executions every day
   - Look for patterns in errors
   - Respond to hot leads within 2 hours

3. **Trust the AI**
   - The AI scoring is usually accurate
   - Don't override A+ grades
   - Do follow up on hot leads immediately

4. **Iterate Fast**
   - Make small changes weekly
   - Test one change at a time
   - Track impact on conversions

5. **Use the Data**
   - CRM now has rich intelligence
   - Share insights with sales team
   - Base strategy on actual data

---

## 🆘 Need Help?

### Check Logs First
```bash
# If using Docker
docker logs n8n

# If using systemd
journalctl -u n8n -f

# In n8n UI
Executions tab → Click failed execution → See error details
```

### Test Individual Nodes
1. Right-click any node
2. Click "Execute Node"
3. See output in right panel
4. Check for errors

### Common Debugging
```javascript
// Add this to any Code node to debug
console.log('Debug data:', JSON.stringify($input.item.json, null, 2));
return $input.item;
```

---

## ✅ Checklist

Before going live, verify:

- [ ] Workflow imported successfully
- [ ] All 4 credentials configured
- [ ] WhatsApp phone number ID updated
- [ ] Video URLs updated (or using default)
- [ ] WhatsApp templates created & approved
- [ ] Test execution successful
- [ ] Workflow activated (toggle ON)
- [ ] First real call processed successfully
- [ ] WhatsApp message received
- [ ] CRM updated with data
- [ ] Follow-up task created

If all checked ✅ → You're live! 🎉

---

## 📚 Additional Resources

- Full documentation: `PROPBILLION_DOCUMENTATION.md`
- Workflow file: `propbillion_enhanced_workflow.json`
- n8n docs: https://docs.n8n.io
- Gemini API: https://ai.google.dev/docs
- WhatsApp Business: https://developers.facebook.com/docs/whatsapp

---

**🚀 You're ready to automate! Good luck!**

**Questions?** Check the full documentation for advanced features, MCP integration, and optimization strategies.
