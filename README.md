# PFAS Weekly Update – Automated Newsletter Workflow  

This repository contains the **n8n workflow** that automatically generates a **PFAS Weekly Update Newsletter** using **RSS feeds, Google Sheets, OpenAI GPT-4o, PDF.co, and Gmail**.  

The newsletter contains:  

- **Fresh PFAS news** from multiple RSS sources  
- **Regulatory updates & global insights**  
- **Upcoming Webinars scraped from August Mack site**  
- **Expert quotes & Useful resources**  
- **A clean PDF delivered weekly via email**  

---

## ✨ Key Features  

✅ **Always fresh content** – RSS feeds + Google Sheets archive ensure no duplicates.  
✅ **No empty newsletters** – If RSS is empty, the archive fills the newsletter (NO fallback samples).  
✅ **Webinar integration** – Scrapes the live webinar page before sending.  
✅ **Dynamic HTML → PDF** conversion with a clean branded style.  
✅ **Google Sheets archive** stores every newsletter item for history & deduplication.  
✅ **Fully automated email delivery.**  

---

## 🏗 Workflow Overview  

1️⃣ **Fetch RSS feeds** from PFAS news sources.  
2️⃣ **Read Google Sheets archive** to compare old vs new items.  
3️⃣ **Deduplicate items** → Only keep new unique headlines.  
4️⃣ If feeds empty, **pull archived data** so the newsletter is never blank.  
5️⃣ **Generate HTML newsletter** using GPT-4o → Modular sections.  
6️⃣ **Scrape August Mack webinars** and merge with newsletter HTML.  
7️⃣ **Convert HTML → PDF** via PDF.co.  
8️⃣ **Send PDF email via Gmail**.  
9️⃣ **Append new items to Google Sheets archive** for next run.  

---

## 🔄 Data Flow  
RSS Feeds
↓
Merge All Feeds
↓
Google Sheets Archive (past items)
↓
Deduplication (filters only new headlines)
↓
IF RSS empty → Uses archived data
↓
GPT-4o Generates HTML Newsletter
↓
Scrape Webinars → Merge with HTML
↓
PDF.co Converts HTML → PDF
↓
Gmail Sends PDF Email
↓
Append New Items → Google Sheets Archive


So even if **no new RSS items** → it **reuses archived data from Sheets** to keep the newsletter filled.

---

## 📜 Newsletter Sections  

- **Cover Page** → Title & date  
- **What is PFAS?** → static educational section  
- **Weekly Highlights** → dynamic feed items (6+ cards)  
- **Regulatory Updates** → HTML table  
- **Global PFAS Impact** → 4-6 paragraphs  
- **Expert Insights** → quotes from feed/archive  
- **Useful Resources** → 6+ cards  
- **Events & Webinars** → live scraped webinars  

---

## 📧 Email Delivery  

After generating the PDF, the Gmail node sends a **pre-written email**:  

- **Subject:** PFAS Weekly Update – Key Insights & Regulatory News  
- **Body:** Brief email intro + CTA  
- **Attachment:** PDF newsletter  

---

## 🗂 Archive Logic  

- Google Sheets stores **all past headlines**.  
- Before each run, the archive is **read twice**:  
  1. **To check for duplicates** → Only new items move forward.  
  2. **To update archive after sending** → Ensures next run knows what’s already sent.  

This keeps the newsletter **dynamic & duplicate-free**.

---

## ✅ Dynamic vs Static Content  

- If RSS feeds are **empty**, the newsletter uses **archived data from Sheets**, so it **never falls back to generic placeholder content**.  
- Webinars always come live from scraping; if unavailable, the section gracefully skips without breaking layout.  

---

## 🧩 Node Summary  

| Stage | Node | Purpose |
|-------|------|---------|
| **Trigger** | `Manual/Schedule` | Starts workflow weekly |
| **RSS Feeds** | `Main RSS`, `RSS Read1`, `RSS Read2` | Collect PFAS news |
| **Merge RSS** | `Merge_RSSFeeds` | Combine all feeds |
| **Load Archive** | `Google Sheets Get Rows` | Fetch past newsletter history |
| **Deduplicate** | `Deduplication` | Compare new feeds vs archive |
| **Combine Archive** | `Merge2_CombineRSS+Archive` | Merge clean new + old |
| **Archive Update** | `Archive-New` | Append fresh items to Sheet |
| **Generate HTML** | `Message a Model (GPT-4o)` | Build newsletter HTML sections |
| **Scrape Webinars** | `Scrape Webinars` | Get live webinars |
| **Inject Webinars** | `Merge3_OpenAI+Webinar` | Merge webinar HTML into newsletter |
| **Convert PDF** | `PDF.co` | Generate newsletter PDF |
| **Email** | `Gmail Send` | Email PDF to recipients |
| **Log Run** | `Append Row in Sheet` | Log sent newsletter items |

---

## 📊 Node I/O Table  

| Node | Input | Process | Output |
|------|-------|---------|--------|
| **Trigger** | Manual/schedule | Starts flow | Start signal |
| **RSS Feeds** | Feed URLs | Fetch PFAS news | JSON (title, link, summary) |
| **Merge_RSSFeeds** | Multiple feed outputs | Combine into one | Unified RSS list |
| **Google Sheets Get Rows** | Sheet ID | Read all past items | Archive list |
| **Deduplication** | New RSS + archive | Remove duplicates | Only fresh items |
| **Merge2_Archive** | New deduped items | Merge with archive for reference | Combined dataset |
| **Archive-New** | Deduped list | Append to Sheet | Updated archive |
| **Message Model (GPT-4o)** | mergedText | Generate HTML newsletter | Newsletter HTML |
| **Scrape Webinars** | Webinar URL | Extract titles, dates | Webinar JSON |
| **webinarHtml** | Webinar JSON | Convert to HTML cards | Webinar HTML |
| **Merge3_OpenAI+Webinar** | Newsletter HTML + webinar HTML | Merge | Final HTML |
| **PDF.co** | Final HTML | Convert to PDF | PDF URL |
| **HTTP Request** | PDF URL | Download file | PDF binary |
| **Gmail Send** | PDF binary | Attach & send | Email sent |
| **Append Row in Sheet** | Sent items | Log run | Updated archive |

---

## 🚀 How to Run  

1. **Import the workflow JSON into n8n.**  
2. Connect required credentials:  
   - OpenAI GPT-4o  
   - Google Sheets API  
   - Gmail OAuth2  
   - PDF.co API  
3. Set your **Google Sheet ID** for archive tracking.  
4. Run manually first to populate archive.  
5. Schedule trigger for weekly automation.  

---

## 🛠 Future Improvements  

- Integrate QuickChart for **dynamic PFAS dashboards**.  
- Add Slack/Teams notifications after each run.  
- Enrich Expert Insights from scientific journals.  

---

## ✅ TL;DR  

- **No RSS?** → Newsletter still filled with **Google Sheets archive**.  
- **Always dynamic** → Deduplicated new items + auto webinars.  
- **End-to-end automation** → HTML → PDF → Email → Archive update.  

---

### 📄 Related Resources  

- **[n8n Docs](https://docs.n8n.io)**  
- **[OpenAI API](https://platform.openai.com/)**  
- **[PDF.co API](https://pdf.co/)**  

---
