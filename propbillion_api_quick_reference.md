# PropBillion API Configuration - Quick Reference

## 🔑 Required API Keys & Credentials

### 1. Runo.in API
```yaml
API Key: MXJycmNtNWRyYXptMTU2aA==
Base URL: https://runo.in
Authentication: Bearer Token

Endpoints:
  GET  /call/logs          # Fetch call recordings
  POST /crm/interaction    # Update CRM data
```

**n8n Credential Setup:**
```
Credential Type: HTTP Header Auth
Name: Runo API Key
Header Name: Authorization
Header Value: Bearer MXJycmNtNWRyYXptMTU2aA==
```

---

### 2. Google Gemini API
```yaml
Service: Google Generative AI (Gemini)
Model: gemini-1.5-flash
Get Key: https://makersuite.google.com/app/apikey

Cost: ~$0.00002 per request (very cheap!)
```

**n8n Credential Setup:**
```
Credential Type: Google Gemini API
API Key: [Your 39-character API key]
```

**How to Get:**
1. Go to https://makersuite.google.com/app/apikey
2. Create API key
3. Enable billing (required even for free tier)
4. Copy key

---

### 3. WhatsApp Business API
```yaml
Platform: Meta (Facebook) Business
Base URL: https://graph.facebook.com/v19.0
Required:
  - Phone Number ID (15 digits)
  - Access Token (permanent)

Cost: ~₹0.50-1.00 per conversation (India)
```

**n8n Credential Setup:**
```
Credential Type: HTTP Header Auth
Name: WhatsApp Business API
Header Name: Authorization
Header Value: Bearer EAAxxxxxxxxxxxxxxx [Your token]
```

**Configuration in Node:**
```javascript
URL: https://graph.facebook.com/v19.0/YOUR_PHONE_NUMBER_ID/messages

Replace YOUR_PHONE_NUMBER_ID with your actual 15-digit ID
Example: 123456789012345
```

---

### 4. Video CDN URLs

**Current Placeholders (MUST UPDATE!):**
```javascript
const videoLibrary = {
  'legal': 'https://your-cdn.com/videos/rera_compliance_guide.mp4',
  'finance': 'https://your-cdn.com/videos/home_loan_process.mp4',
  'residential_2BHK': 'https://your-cdn.com/videos/2bhk_best_projects.mp4',
  'location_Hinjewadi': 'https://your-cdn.com/videos/hinjewadi_properties.mp4',
  'default': 'https://your-cdn.com/videos/pune_real_estate_overview.mp4'
  // ... 20+ more videos
};
```

**Requirements:**
- HTTPS only (WhatsApp requirement)
- Publicly accessible (no authentication)
- Max 16 MB per file
- Format: MP4 (H.264 codec)
- Duration: 30 sec - 3 min recommended

**Recommended CDN Options:**
1. **Cloudflare R2** (Cheapest)
   - $0.015 per GB stored
   - Free bandwidth up to 10GB/month
   - URL: https://pub-xxxxx.r2.dev/video.mp4

2. **AWS S3 + CloudFront**
   - $0.023 per GB stored
   - $0.085 per GB transfer
   - URL: https://d111111abcdef8.cloudfront.net/video.mp4

3. **Simple Web Server**
   - Any hosting (DigitalOcean, Linode, etc.)
   - URL: https://cdn.propbillion.com/videos/video.mp4

---

## 📋 WhatsApp Template Configuration

**Required Templates in WhatsApp Business Manager:**

### Template 1: Property Urgent Followup
```
Name: property_urgent_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, thank you for your call to PropBillion! 

{{2}}

We're excited to help you find your perfect property. Our team will call you shortly to discuss your requirements.

Buttons:
[CALL] Call Us: +919876543210
[URL] View Properties: https://propbillion.com
```

### Template 2: Property Education Followup
```
Name: property_education_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, thanks for connecting with PropBillion!

{{2}}

Watch this video for detailed information. Feel free to reach out with any questions!

Buttons:
[CALL] Call Us: +919876543210
[URL] Learn More: https://propbillion.com/resources
```

### Template 3: Property Investment Followup
```
Name: property_investment_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, great to hear you're interested in real estate investment!

{{2}}

This video covers ROI analysis, rental yields, and market trends. Let's discuss your investment goals.

Buttons:
[CALL] Schedule Call: +919876543210
[URL] Investment Guide: https://propbillion.com/investment
```

### Template 4: Property NRI Followup
```
Name: property_nri_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, thank you for your interest in NRI property purchase!

{{2}}

This video covers FEMA compliance, required documents, and the complete remote purchase process.

Buttons:
[CALL] Call Us: +919876543210
[URL] NRI Guide: https://propbillion.com/nri
```

### Template 5: Property Clarification Followup
```
Name: property_clarification_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, we're here to help clarify your questions!

{{2}}

Watch this video for detailed answers. Our team is ready to provide any additional information you need.

Buttons:
[CALL] Call Us: +919876543210
[URL] FAQs: https://propbillion.com/faq
```

### Template 6: Property Financing Followup
```
Name: property_financing_followup
Category: MARKETING
Language: English (US)

Header: VIDEO

Body:
Hi {{1}}, let's explore financing options for your property!

{{2}}

This video explains home loans, EMI calculators, and flexible payment plans. We have tie-ups with major banks.

Buttons:
[CALL] Call Us: +919876543210
[URL] Loan Calculator: https://propbillion.com/emi-calculator
```

---

## 🎬 Video Library Requirements

**Minimum 25 videos needed:**

### Legal & Compliance (3 videos)
```
1. rera_compliance_guide.mp4 - MahaRERA verification, legal docs
2. documentation_checklist.mp4 - Complete document list
3. possession_quality_check.mp4 - OC/CC, snag list, defects
```

### Financial (2 videos)
```
4. home_loan_process.mp4 - Loan eligibility, EMI, banks
5. tax_guide_property.mp4 - Stamp duty, capital gains, GST
```

### Property Types (7 videos)
```
6. 1bhk_apartments_pune.mp4 - Compact living, best projects
7. 2bhk_best_projects.mp4 - Most popular configuration
8. 3bhk_premium_homes.mp4 - Spacious family homes
9. 4bhk_penthouses.mp4 - Luxury living
10. luxury_villas_pune.mp4 - Independent houses
11. plot_investment_guide.mp4 - Land purchase guide
12. commercial_opportunities.mp4 - Office, retail spaces
```

### Locations (5 videos)
```
13. hinjewadi_properties.mp4 - IT hub, connectivity
14. kharadi_projects.mp4 - EON IT Park area
15. baner_living.mp4 - Premium locality
16. wakad_developments.mp4 - Affordable + premium
17. pmrda_properties.mp4 - New Pune developments
```

### Investment & Services (4 videos)
```
18. roi_analysis_pune.mp4 - Returns, appreciation
19. rental_income_guide.mp4 - Rental yields, tenancy
20. resale_property_process.mp4 - Secondary market
21. society_maintenance_guide.mp4 - Charges, corpus fund
```

### Special Categories (4 videos)
```
22. nri_property_guide.mp4 - NRI purchase complete guide
23. propbillion_intro.mp4 - Company overview
24. booking_process_explained.mp4 - Step-by-step booking
25. pune_real_estate_overview.mp4 - DEFAULT/FALLBACK video
```

**Video 25 is CRITICAL** - This is the fallback for any unmatched category!

---

## 🔄 n8n Node Updates Required

### Update 1: Runo API Node
```
Node: "Runo API: Get Call Logs"
Update: Credentials → Select "Runo API Key"
Verify: Authorization header shows Bearer token
```

### Update 2: Gemini Nodes (2 nodes)
```
Node 1: "Gemini AI: Transcribe Call"
Node 2: "Gemini AI: Analyze FAQ Category"
Update: Both → Credentials → Select "Google Gemini API"
Verify: API key is 39 characters long
```

### Update 3: Video Selector Code Node
```
Node: "Code: Smart Video & Template Selector"
Location: Line 10-40 (videoLibrary object)
Action: Replace ALL 'https://your-cdn.com' with your actual CDN
Example: 'https://pub-abc123.r2.dev/videos/...'
```

### Update 4: WhatsApp Node
```
Node: "WhatsApp: Send Video Message"
Update 1: Credentials → Select "WhatsApp Business API"
Update 2: URL → Replace YOUR_PHONE_NUMBER_ID with actual ID
Final URL: https://graph.facebook.com/v19.0/123456789012345/messages
```

### Update 5: CRM Node
```
Node: "Runo CRM: Update with Intelligence"
Update: Credentials → Select "Runo API Key" (same as first node)
Verify: Same Bearer token
```

---

## ✅ Pre-Launch Testing Checklist

### Test 1: API Connectivity
```bash
# Test Runo API
curl -H "Authorization: Bearer MXJycmNtNWRyYXptMTU2aA==" \
  "https://runo.in/call/logs?date=2025-02-17&pageNo=1"

Expected: JSON with call logs or empty array

# Test Gemini API
curl "https://generativelanguage.googleapis.com/v1/models?key=YOUR_API_KEY"

Expected: List of available models

# Test WhatsApp API
curl -X POST "https://graph.facebook.com/v19.0/YOUR_PHONE_ID/messages" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"messaging_product":"whatsapp","to":"YOUR_TEST_NUMBER","type":"template","template":{"name":"hello_world","language":{"code":"en_US"}}}'

Expected: Message ID in response
```

### Test 2: Video Accessibility
```bash
# Test each video URL in browser
# Should play or download directly (no login required)

Test URLs:
1. https://your-cdn.com/videos/pune_real_estate_overview.mp4
2. https://your-cdn.com/videos/2bhk_best_projects.mp4
3. https://your-cdn.com/videos/hinjewadi_properties.mp4

Required:
✓ HTTPS protocol
✓ Direct file access (not HTML page)
✓ < 16 MB file size
✓ MP4 format
```

### Test 3: WhatsApp Templates
```
Go to: Meta Business Suite → WhatsApp Manager → Message Templates

Verify each template:
✓ property_urgent_followup - Status: APPROVED
✓ property_education_followup - Status: APPROVED
✓ property_investment_followup - Status: APPROVED
✓ property_nri_followup - Status: APPROVED
✓ property_clarification_followup - Status: APPROVED
✓ property_financing_followup - Status: APPROVED

If status is PENDING: Wait for approval (24-48 hours)
If status is REJECTED: Fix and resubmit
```

### Test 4: End-to-End Workflow
```
1. Add test call to Runo.in with:
   - Duration: 60 seconds (> 30 minimum)
   - Recording URL: Valid audio file
   - Phone: Your WhatsApp test number

2. Execute workflow manually in n8n

3. Verify:
   ✓ Call fetched
   ✓ Audio downloaded
   ✓ Transcript generated
   ✓ Analysis JSON created
   ✓ Video URL selected
   ✓ WhatsApp message received on phone
   ✓ CRM updated with all fields

4. Check WhatsApp message:
   ✓ Video plays
   ✓ Customer name correct
   ✓ Message personalized
   ✓ Buttons work
```

---

## 🚨 Common Setup Errors

### Error 1: "Invalid API Key"
```
Problem: Credential not configured or wrong key
Solution:
1. Double-check key in credential manager
2. Ensure no extra spaces before/after
3. For Runo: Include "Bearer " prefix
4. For WhatsApp: Include "Bearer " prefix
5. For Gemini: No prefix, just the key
```

### Error 2: "Template not found"
```
Problem: WhatsApp template name mismatch or not approved
Solution:
1. Check exact name in Meta Business Suite
2. Names are case-sensitive
3. Verify template status is APPROVED
4. Check language code is en_US
```

### Error 3: "Media URL not accessible"
```
Problem: Video URL not working
Solution:
1. Test URL in browser (should download/play)
2. Ensure HTTPS (not HTTP)
3. Check file size < 16 MB
4. Verify no authentication required
5. Test from different network
```

### Error 4: "Phone number not valid"
```
Problem: Phone format incorrect
Solution:
1. Include country code: 919876543210
2. Remove + symbol
3. Remove spaces and dashes
4. Check number is registered on WhatsApp
```

### Error 5: "Workflow not executing"
```
Problem: Schedule trigger not working
Solution:
1. Verify workflow is ACTIVE (toggle blue)
2. Check n8n is running
3. Review execution logs
4. Test manual execution first
5. Check n8n has internet access
```

---

## 💰 Cost Breakdown

### Monthly Costs (100 calls/day, 3000/month)

```
Service              | Cost          | Notes
---------------------|---------------|------------------------
Runo.in             | ₹5,000        | Call management platform
Google Gemini AI    | $3-5          | ~6000 API calls
WhatsApp Business   | ₹1,500-3,000  | ~3000 conversations
Video CDN (R2)      | $1-2          | Storage + bandwidth
n8n (self-hosted)   | $5-10         | VPS hosting (DigitalOcean)
---------------------|---------------|------------------------
TOTAL               | ~₹7,000       | ~$90-100/month
                    | ($85-95)      |
```

### ROI Analysis
```
Manual Processing Cost:
- 30 minutes per call
- 3000 calls/month = 1500 hours
- @ ₹200/hour = ₹300,000

Automation Cost: ₹7,000

SAVINGS: ₹293,000/month ($3,500)
ROI: 4100%
```

---

## 📞 Support Contacts

**Technical Issues:**
- n8n Community: https://community.n8n.io/
- Stack Overflow: Tag `n8n`

**API Issues:**
- Runo Support: support@runo.in
- Google Gemini: https://ai.google.dev/support
- WhatsApp: https://developers.facebook.com/support

**Video Issues:**
- Cloudflare R2: https://community.cloudflare.com/
- AWS S3: https://aws.amazon.com/support/

---

## 🎯 Next Steps After Setup

1. **Monitor First Week:**
   - Check execution logs daily
   - Verify message delivery rate
   - Track customer responses
   - Monitor API costs

2. **Optimize Based on Data:**
   - Which videos get best engagement?
   - Which FAQ categories are most common?
   - Adjust AI prompts if categorization is off
   - Update video library based on feedback

3. **Scale Gradually:**
   - Start with 10-20 calls/day
   - Monitor system performance
   - Increase to 50-100 calls/day
   - Add error handling and retry logic

4. **Enhance Features:**
   - Add SMS fallback for failed WhatsApp
   - Implement lead scoring
   - Create dashboard for analytics
   - Add customer sentiment analysis

---

**Setup Complete! 🎉**

Your PropBillion Smart System is ready to automate customer follow-ups and save your team hours of manual work.

**Remember:**
- Keep API keys secure (never commit to Git)
- Monitor execution logs regularly
- Update videos based on performance
- Optimize AI prompts as needed

---

*Last Updated: February 2025*
*Questions? Check the main setup guide: propbillion_n8n_setup_guide.md*
