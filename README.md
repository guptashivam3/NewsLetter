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

## ❓ What is n8n?

**n8n** is an **open-source workflow automation platform** – similar to Zapier or Make, but **self-hosted and more customizable**.  

It lets you connect **apps, APIs, and data sources** with drag-and-drop nodes to build workflows without heavy coding.  

Each workflow acts like a pipeline, for example:  
**Fetch RSS → Clean & Deduplicate → Generate Newsletter → Convert to PDF → Send Email**  

You can run n8n **locally**, on a **server**, or use **n8n Cloud** for a hosted solution.  

👉 **Think of it as the automation brain that runs all the steps of this PFAS newsletter workflow automatically.**

---

## 🖥 How to Access n8n

You have **two ways** to use n8n:

1️⃣ **n8n Cloud (Recommended for Easy Setup)**  
- Go to [https://app.n8n.cloud](https://app.n8n.cloud)  
- Sign up → Login → Access your personal n8n dashboard  

2️⃣ **Self-Hosted (Advanced)**  
- Install on your machine or server → [Official Installation Docs](https://docs.n8n.io/hosting/)  
- Start the n8n service → Access via [http://localhost:5678](http://localhost:5678)  

Either way, you’ll land on the **workflow dashboard** where you can **import workflows, set credentials, and run automations.**

---

## 🔐 Logging in

- After signing up or setting up your self-hosted instance, you’ll get a **login screen**.  
- Enter your credentials (**email/password** or SSO).  
- Once logged in, you’ll see a **clean dashboard with a “Workflows” section**, where you can create or import workflows.

---

## ✅ Pre-requisites Before Running the PFAS Workflow

Before running this workflow, you must configure the following **credentials inside n8n**:

1. **OpenAI API Key**  
   - Go to [OpenAI API Keys](https://platform.openai.com/account/api-keys)  
   - Generate a key → In n8n, go to **Credentials → Add New → OpenAI** → Paste the API key  

2. **Google Sheets API**  
   - Create a **Service Account** in Google Cloud Console  
   - Enable the **Google Sheets API** & download JSON credentials  
   - In n8n, go to **Credentials → Add New → Google Sheets** → Upload the JSON credentials  
   - Share your **Google Sheet** with the **service account email**  

3. **Gmail OAuth2**  
   - In Google Cloud Console → Enable **Gmail API**  
   - Create OAuth2 credentials  
   - In n8n → **Credentials → Add New → Gmail OAuth2** → Follow the OAuth setup  

4. **PDF.co API Key**  
   - Sign up on [https://pdf.co/](https://pdf.co/)  
   - Get your API key → In n8n, go to **Credentials → Add New → PDF.co** → Paste the API key  

---

## 🚀 How to Run – Step by Step

1️⃣ **Login to n8n**  
- **Cloud:** [https://app.n8n.cloud](https://app.n8n.cloud)  
- **Self-Hosted:** [http://localhost:5678](http://localhost:5678)  

2️⃣ **Import Workflow JSON**  
- Click **Workflows** → **Import** → Paste or upload the JSON → **Save**  

3️⃣ **Connect All Required Credentials**  
- OpenAI GPT-4o (newsletter generation)  
- Google Sheets API (archive + deduplication)  
- Gmail OAuth2 (send PDF email)  
- PDF.co API (convert HTML → PDF)  

4️⃣ **Configure Google Sheets Archive**  
- Open your **Google Sheet**  
- Copy its **Sheet ID** (from the URL)  
- Paste it into the **Google Sheets nodes** in the workflow  
- Make sure the sheet has **headers** like: title | link | summary | date


5️⃣ **Run Workflow Manually Once**  
This first run will:  
- Fetch RSS feeds  
- Populate Google Sheets archive  
- Generate a sample newsletter PDF  
- Send a test email  

6️⃣ **Verify Results**  
- Check **Google Sheets** archive (should contain fetched headlines)  
- Check **email inbox** for the generated PDF newsletter  

7️⃣ **Enable Weekly Schedule Trigger**  
- Open the **Schedule Trigger Node**  
- Set it for weekly (e.g., every Monday at 9 AM)  
- **Activate the workflow** → It will now auto-run weekly  

---

## 📅 Weekly Run Lifecycle

Once automated, **every scheduled run** performs these steps:

1. **Fetch new RSS items** from multiple sources  
2. **Check Google Sheets archive** → Skip duplicates  
3. If **no new RSS**, it **uses existing archive items** (so the newsletter is never empty)  
4. **Generate newsletter HTML** with OpenAI GPT-4o  
5. **Scrape live webinars** → Inject into the newsletter  
6. **Convert newsletter HTML → PDF** via PDF.co  
7. **Send newsletter PDF via Gmail**  
8. **Append new items to Google Sheets** for future deduplication  

💡 **Result:** Fully hands-free, always fresh, deduplicated newsletters.

---

## ✅ First-Time Checklist

Before you run the workflow:  

- ✅ Do you have all API keys ready?  
- ✅ Is the Google Sheet created & shared with the service account?  
- ✅ Have you tested OpenAI manually (just to ensure valid API)?  
- ✅ Did you run the workflow once manually to verify all steps?  

If **YES** → You can safely enable the schedule → It will auto-run weekly 🎉

---

## 💡 Why This Setup?

- **Google Sheets archive** ensures the newsletter is **never empty** (it always uses fresh OR previously saved headlines).  
- **Deduplication** prevents repeating the same headlines in consecutive newsletters.  
- **Webinar scraping** dynamically pulls live events from August Mack’s website.  
- **OpenAI GPT-4o** formats content into a **clean, modular HTML** newsletter.  
- **PDF.co + Gmail** deliver a **ready-to-share PDF newsletter** with no manual effort.  

---

## 🖼 Why Use GitHub for Hosting Newsletter Images (and More)?

In this workflow, **newsletter images** (like banners, charts, and section visuals) are hosted on **GitHub** instead of embedding them directly inside the workflow.

---

### ✅ Why It’s Necessary

**Stable Image URLs**  
- Dynamic newsletters need reliable links that won’t expire.  
- GitHub provides **permanent RAW image URLs** that can be safely referenced in the HTML newsletter.  

**Lightweight Workflow**  
- No need to store or upload images directly inside n8n; GitHub acts as a **central asset repository**.  

**Easy Updates**  
- Update an image in GitHub, and the newsletter **automatically uses the new version** without workflow changes.  

---

### 🛠 How It’s Used

1. **Images are stored in a GitHub repo** (e.g., `/newsletter-assets/`).  
2. Each image has a **direct RAW URL**, for example:  

   ```text
   https://raw.githubusercontent.com/<username>/<repo>/main/newsletter-assets/PFAS_banner.png
   
3. The HTML newsletter references these URLs in <img> tags
    ```text
    <img src="https://raw.githubusercontent.com/.../PFAS_banner.png" alt="PFAS Banner" />
4. When converted to PDF or sent via email, the newsletter fetches images directly from GitHub.

   
### 🚀 Advantages of Using GitHub

✅ **Free & Reliable Hosting**  
- GitHub is stable, globally accessible, and doesn’t require extra hosting services.  

✅ **Version Control & History**  
- Every change to images (or any asset) is tracked.  
- You can see who updated what and roll back to previous versions anytime.  

✅ **Centralized Asset Management**  
- All newsletter assets (banners, charts, icons) are stored in **one repo**, making it easier to manage.  

✅ **Static URLs for Automation**  
- GitHub RAW URLs don’t change, making them perfect for automated workflows like **n8n HTML → PDF generation**.  

✅ **Project Management Features**  
- You can use **Issues, Projects, and Discussions** inside the same repo to:  
  - Track newsletter updates  
  - Plan improvements  
  - Manage tasks for your team  

✅ **Team Collaboration**  
- Multiple team members can contribute to the repo (images, docs, workflow updates) with proper permissions.  
- **Pull Requests** allow easy code/content reviews before updates go live.  

✅ **Documentation Hub**  
- GitHub can also host the **README & workflow docs** in the same repo.  
- Anyone can view, edit, and maintain docs alongside the workflow assets.  

✅ **Integration with CI/CD**  
- You can integrate **GitHub Actions** for automated testing, validation, or deployments if needed.  

✅ **Backup & Accessibility**  
- Your assets and docs are safely backed up in **GitHub’s cloud**.  
- Accessible anytime, from anywhere.  


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
