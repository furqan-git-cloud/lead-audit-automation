# lead-audit-automation
# 🚀 AI Lead Assignment & Website Performance Audit Automation

An **n8n-based business automation system** designed to automate two important operational processes:

1. 🎯 **Lead Assignment Automation** — automatically assigns available leads to team members based on location and requested quantity.
2. 🌐 **Website Performance & Technical Audit** — automatically analyzes business websites for performance, SEO, accessibility, technical health, design, and conversion readiness.

The workflows integrate **n8n, Google Sheets, Gmail, Google PageSpeed Insights, and website analysis APIs** to reduce manual work and create a structured automation pipeline.

---

## ✨ Key Features

### 🎯 Lead Assignment

* Employee submits a lead request through an n8n form.
* Captures:

  * Name
  * City
  * Email
  * Number of leads required
* Searches Google Sheets for leads matching:

  * Requested city
  * `unassigned` status
* Randomly selects the requested number of leads.
* Updates assigned leads automatically.
* Records:

  * Assigned employee
  * Assignment date
  * Lead status
* Generates an HTML-formatted email.
* Sends assigned lead information through Gmail.

The workflow filters the master lead sheet by `unassigned` status and location before selecting leads.

---

### 🌐 Website Performance & Technical Audit

The audit workflow automatically processes websites and generates structured technical reports.

It evaluates:

* ⚡ Performance
* 📱 Mobile performance
* 🖥️ Desktop performance
* 🔍 SEO
* ♿ Accessibility
* 🔒 HTTPS
* 🛠️ Technical health
* 🎨 Basic design/mobile usability
* 💰 Conversion readiness
* 📸 Website screenshots
* 🚨 Top website problems

The final results are written back to Google Sheets with fields including performance, accessibility, SEO, design, conversion, overall score, classification, technical score, and screenshot URL.

---

# 🏗️ System Architecture

```text
                    ┌─────────────────────┐
                    │     n8n Platform     │
                    └──────────┬──────────┘
                               │
             ┌─────────────────┴─────────────────┐
             │                                   │
             ▼                                   ▼
    ┌──────────────────┐               ┌──────────────────────┐
    │ Lead Assignment  │               │ Website Audit        │
    │ Automation       │               │ Automation           │
    └────────┬─────────┘               └──────────┬───────────┘
             │                                    │
             ▼                                    ▼
      Google Sheets                         Website URL
             │                                    │
             ▼                           ┌────────┴─────────┐
      Filter Leads                      │                  │
             │                          ▼                  ▼
             ▼                   PageSpeed Insights   HTML Analysis
      Random Selection           Mobile + Desktop      SEO + Accessibility
             │                          │                  │
             ▼                          └────────┬─────────┘
      Update Lead Status                         │
             │                                   ▼
             ▼                          Performance Report
          Gmail                                  │
             │                                   ▼
             ▼                           Screenshot Capture
       Assigned Leads                           │
                                                 ▼
                                          Google Sheets
```

---

# 🔄 Workflow 1 — Lead Assignment

## Flow

```text
n8n Form
   ↓
Get Leads from Google Sheets
   ↓
Filter by City + Unassigned Status
   ↓
Randomize Available Leads
   ↓
Select Requested Number
   ↓
Loop Through Leads
   ↓
Update Lead Status
   ↓
Generate HTML Email
   ↓
Send Gmail
```

### Input

The form accepts:

| Field        | Type   | Required |
| ------------ | ------ | -------- |
| Name         | Text   | ✅        |
| City         | Text   | ✅        |
| Email        | Email  | ✅        |
| No. of leads | Number | ✅        |

These fields are defined directly in the workflow's form trigger.

### Lead Selection Logic

The workflow searches for leads where:

```text
status = unassigned
Location = requested city
```

It then randomizes the available leads and selects the requested quantity.

### Lead Update

Assigned leads are updated with:

```text
status        = Assigned
assigned_to   = Employee Name
assigned_date = Current Date/Time
```

The original lead information such as company name, phone, address, URL, and location is retained.

### Email Delivery

The workflow generates an HTML table containing:

* Company Name
* Phone
* Address
* Location
* Website
* Status

and sends it to the email submitted through the form.

---

# 🔍 Workflow 2 — Website Performance & Technical Audit

## Flow

```text
Google Sheets Trigger
        ↓
Filter Valid Website
        ↓
Wait
        ↓
Prepare & Config
        ↓
Run Website Audit
        ↓
Accessibility & SEO Audit
        ↓
       ┌───────────────┐
       │               │
       ▼               ▼
PSI Mobile       PSI Desktop
       │               │
       └───────┬───────┘
               ▼
       Merge Strategies
               ↓
    Build Performance Report
               ↓
       Screenshot Capture
               ↓
       Update Google Sheets
```

The workflow starts from a Google Sheets row-added trigger and routes website data through validation, auditing, PageSpeed analysis, report generation, and final spreadsheet storage.

---

# ⚡ Performance Analysis

The workflow evaluates important performance metrics including:

* Largest Contentful Paint — LCP
* Cumulative Layout Shift — CLS
* Total Blocking Time — TBT
* Speed Index
* First Contentful Paint — FCP
* Time to Interactive — TTI
* Total Page Weight
* Network Request Count

Configured targets include:

```text
LCP              < 2.5s
CLS              < 0.1
TBT              < 200ms
Speed Index      < 3.4s
FCP              < 1.8s
TTI              < 3.8s
Page Weight      < ~1.6MB
Requests         < 50
```

The workflow also uses a mobile-first performance blend:

```text
Mobile   = 60%
Desktop  = 40%
```

---

# 📱 Mobile & 🖥️ Desktop Testing

The system runs PageSpeed Insights separately for:

```text
Mobile
   +
Desktop
   ↓
Merge Results
   ↓
Build Performance Report
```

Both PageSpeed branches feed into the same merge and reporting process.

---

# 🔎 SEO & Accessibility Audit

The workflow fetches the live website HTML and checks:

### SEO

* Page title
* Meta description
* H1 presence
* LocalBusiness/Organization schema

### Accessibility

* Total images
* Images missing `alt` attributes

The workflow then calculates local SEO and accessibility scores from these checks.

---

# 🛡️ Technical Health

The audit checks technical indicators such as:

* HTTPS
* Browser console errors
* Text compression
* Lighthouse best-practices score
* Mobile viewport
* Horizontal content overflow

The workflow produces structured findings with:

```text
Category
Test
Observed Value
Expected Threshold
Severity
Evidence Source
Recommendation
```

---

# 📸 Website Screenshots

The system also requests full-page website screenshots using an HTTP request with:

```text
screenshot = true
screenshot.fullpage = true
screenshot.waitFor = 3000
```

The resulting screenshot URL is stored with the audit record.

---

# 📊 Audit Output

The final Google Sheets record contains fields such as:

```text
lead_id
business_name
final_url
platform
performance_score
technical_score
accessibility_score
seo_score
design_score
conversion_score
overall_score
classification
top_5_problems
screen_url
website_url
```

These values are mapped into the final spreadsheet update node.

---

# 🧮 Classification

The workflow classifies websites using the overall score:

```text
80–100  → GOOD
60–79   → NEEDS IMPROVEMENT
0–59    → POOR / CRITICAL
```

If PageSpeed data is unavailable, the workflow can return:

```text
INCONCLUSIVE
```

and flag the audit for manual review.

---

# 🛠️ Technology Stack

| Technology                    | Purpose                         |
| ----------------------------- | ------------------------------- |
| **n8n**                       | Workflow automation             |
| **Google Sheets**             | Lead database & audit database  |
| **Gmail**                     | Automated lead delivery         |
| **Google PageSpeed Insights** | Website performance analysis    |
| **Lighthouse data**           | Performance & technical metrics |
| **JavaScript**                | Data processing and scoring     |
| **Microlink API**             | Website screenshots             |
| **HTML Parsing**              | SEO & accessibility checks      |

---

# 📁 Project Structure

```text
.
├── Lead Assignment.json
├── Performance & Technical Audit (Furqan).json
└── README.md
```

---

# 🚀 Installation & Setup

## 1. Install n8n

Use either:

* n8n Cloud
* Self-hosted n8n
* Docker-based n8n installation

## 2. Import the Workflows

Open n8n and select:

```text
Workflows
   ↓
Import from File
```

Import:

```text
Lead Assignment.json
```

and:

```text
Performance & Technical Audit (Furqan).json
```

## 3. Configure Credentials

Connect the required services:

```text
Google Sheets
Gmail
Google PageSpeed Insights
Microlink
```

## 4. Configure Google Sheets

Prepare the required lead/audit columns according to the workflow mappings.

## 5. Configure API Credentials

**Important:** Never commit API keys, OAuth credentials, webhook secrets, or private credentials to GitHub.

Use n8n credentials/environment variables instead.

---

# 🔐 Security

Before publishing this project publicly:

* Remove API keys from workflow JSON files.
* Remove personal Google account credentials.
* Remove private spreadsheet IDs where appropriate.
* Remove private webhook URLs.
* Store secrets inside n8n Credentials or environment variables.
* Rotate any API key that has already been exposed publicly.

---

# 💡 Use Cases

### Sales & Lead Management

* Lead distribution
* Sales team assignment
* Regional lead allocation
* Automated lead notification
* CRM preparation

### Website Auditing

* Agency website audits
* SEO prospecting
* Performance optimization
* Technical website analysis
* Client reporting
* Website improvement recommendations

---

# 🎯 Business Value

This automation reduces repetitive manual work by connecting **lead management, website auditing, reporting, and communication** into automated workflows.

Instead of manually:

```text
Find Lead
   ↓
Assign Lead
   ↓
Update Spreadsheet
   ↓
Email Employee
```

the system performs the process automatically.

Similarly, website auditing becomes:

```text
Website URL
   ↓
Performance Test
   ↓
SEO Test
   ↓
Accessibility Test
   ↓
Technical Analysis
   ↓
Scoring
   ↓
Screenshot
   ↓
Google Sheets Report
```

---

# 📌 Current Workflow Status

### Lead Assignment

**Active in the supplied workflow configuration.**

### Performance & Technical Audit

The supplied workflow configuration contains the complete audit pipeline, but its exported configuration currently shows it as **inactive**.

---

# 👨‍💻 Author

**Muhammad Furqan**

BS Computer Science
AI Automation & Workflow Engineering

### Areas of Work

* AI Automation
* n8n Workflow Development
* Python
* Web Automation
* Lead Generation
* Google Workspace Automation
* API Integration
* Website Performance Analysis

---

# ⭐ Project Highlights

```text
🎯 Automated Lead Assignment
📊 Google Sheets Integration
📧 Automated Gmail Delivery
🌐 Website Performance Auditing
📱 Mobile + Desktop Analysis
🔍 SEO & Accessibility Checks
🛡️ Technical Health Analysis
📸 Automated Screenshots
📈 Automated Scoring
⚙️ End-to-End n8n Automation
```

---

## ⭐ If you find this project useful

Consider giving the repository a **star ⭐** and following the project for future workflow automation updates.
