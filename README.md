# 📧 AI Gmail Email Classifier — n8n Automation

An intelligent email automation workflow built with **n8n** that automatically classifies incoming Gmail messages into categories, applies labels, sends personalised auto-replies, logs everything to Google Sheets, and delivers a daily summary via Telegram.

Built and maintained by [Rushikesh Thakare](https://rushikeshthakare.in) — Web Developer & AIML Enthisiast

---

## ✨ Features

- **AI-powered classification** — uses Groq's `llama-3.1-8b-instant` (free) to classify emails into 5 categories
- **Smart sender name extraction** — greets senders by name using LLM information extraction
- **Auto-reply** — sends personalised replies for 4 out of 5 categories automatically
- **Gmail labelling** — applies the correct label to every incoming email
- **Google Sheets logging** — logs all 8 fields per email: Sender Name, Sender Email, Subject, Timestamp, Category, Reply Sent, Message ID, Thread ID
- **Daily Telegram report** — sends a 9 PM digest of the last 24 hours with category breakdown
- **Loop-safe** — filters out your own sent emails so replies never trigger themselves

---

## 🗂️ Email Categories

| Category | What it catches |
|---|---|
| 📚 College Academic | Government Polytechnic Mumbai — faculty, exams, timetables, notices |
| 💼 Internship / Work | GigaWebZone LLP — client tasks, project updates, work communication |
| 🚀 Opportunities | Job offers, internship calls, hackathons, tech events, scholarships |
| 🎓 Tech Learning | Infosys Springboard, certifications, tech newsletters, webinars |
| 🗂️ Others | Anything that doesn't fit above — labelled only, no auto-reply |

---

## 🔧 Tech Stack

| Tool | Purpose |
|---|---|
| [n8n](https://n8n.io) | Workflow automation engine |
| [Groq API](https://console.groq.com) | Free LLM — `llama-3.1-8b-instant` |
| Gmail OAuth2 | Email trigger, labelling, and replies |
| Google Sheets | Email log and daily report data source |
| Telegram Bot | Daily digest notifications |

---

## 🏗️ Workflow Architecture

```
Gmail Trigger (every 1 min, unread, not from:me)
    │
    ▼
Information Extractor  ◄── Groq LLM
    │ Extract sender name
    ▼
IF: Name found?
    ├── YES → "Dear {Name},"
    └── NO  → "Hi,"
    │
    ▼
Merge → Clean Data
    │ (message_id, intro, threadId, text, sender_name, sender_email, subject, timestamp)
    ▼
Text Classifier  ◄── Groq LLM
    │
    ├── College Academic  → Add Label → Reply
    ├── Internship Work   → Add Label → Reply
    ├── Opportunities     → Add Label → Reply
    ├── Tech Learning     → Add Label → Reply
    └── Others            → Add Label (no reply)
    │
    ▼
Merge (5 inputs) → Append to Google Sheet
                        (all 8 columns filled)

─ ─ ─ ─ ─ ─ ─ ─ ─ Daily at 9 PM ─ ─ ─ ─ ─ ─ ─ ─ ─
Cron Trigger → Read Google Sheet → Filter last 24h
    → Format Summary → Send Telegram Message
```

---

## 🚀 Setup Guide

### Prerequisites

- n8n instance (self-hosted or cloud)
- Gmail account with OAuth2 set up
- [Groq API key](https://console.groq.com) (free)
- Google Sheets with the correct columns
- Telegram bot token + your chat ID

---

### Step 1 — Create Gmail Labels

Go to **Gmail → Settings → Labels → Create new label** and create these 5:

```
AI/College-Academic
AI/Internship-Work
AI/Opportunities
AI/Tech-Learning
AI/Others
```

After creating, note down each **Label ID** (inspect the label in Gmail settings — looks like `Label_1234567890123456789`).

---

### Step 2 — Set Up Groq Credentials in n8n

Groq is OpenAI-compatible, so use the **OpenAI credential type**:

1. Go to **Credentials → New → OpenAI API**
2. Set **API Key** to your Groq key
3. Set **Base URL** to `https://api.groq.com/openai/v1`
4. Save as `Groq API`

---

### Step 3 — Create the Google Sheet

Create a Google Sheet with these exact column headers in Row 1:

```
Sender Name | Sender Email | Subject | Timestamp | Category | Reply Sent | Message ID | Thread ID
```

Copy the **Sheet ID** from the URL:
`https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit`

---

### Step 4 — Import the Workflow

1. Open n8n → **Workflows → Import from File**
2. Upload `workflow.json` from this repo
3. Update the following in the imported workflow:

| Node | What to update |
|---|---|
| `Gmail Trigger1` | Select your Gmail OAuth2 credential |
| `Groq Model (Extractor)` | Select your Groq credential |
| `Groq Model (Classifier)` | Select your Groq credential |
| All 5 `Label:` nodes | Update Label ID + Gmail credential |
| All 4 `Reply:` nodes | Select your Gmail credential |
| `Read Google Sheet` | Update Sheet ID + Google Sheets credential |
| `Append row in sheet` | Update Sheet ID + Google Sheets credential |
| `Send Telegram Message` | Update your Chat ID + Telegram credential |

---

### Step 5 — Activate

Hit **Activate** on the workflow. Send a test email to yourself and watch it get classified within 1 minute. ✅

---

## 🧪 Test Emails

Use these subjects to test each category by sending from a different account:

| Subject | Expected Category |
|---|---|
| `Mid Semester Exam Schedule Update` | College Academic |
| `Task Assignment for Client Project` | Internship Work |
| `Invitation to Apply for Software Developer Internship` | Opportunities |
| `New AI Course Available on Infosys Springboard` | Tech Learning |
| `Your Amazon Order Has Been Shipped` | Others |

---

## 📊 Sample Telegram Report

```
📧 Daily Email Report
📅 02 Apr 2026
⏰ Last 24 hours
──────────────

1. Mid Semester Exam Schedule Update
   👤 Prof. Mahesh Patil
   🏷 College Academic
   ↩ Reply: Yes

2. Invitation to Apply for Software Developer Internship
   👤 Priya Sharma
   🏷 Opportunities
   ↩ Reply: Yes

──────────────
📊 Total: 2 email(s)

📁 By category:
  College Academic: 1
  Opportunities: 1
```

---

## ⚠️ Known Limitations

- Gmail Trigger polls every minute — uses n8n execution credits. Change to every 5–15 min on free cloud plans.
- LLM classification is not 100% accurate for ambiguous emails — the `Others` category acts as a safe fallback.
- Groq free tier has rate limits — sufficient for personal use, not for high-volume inboxes.

---

## 📁 Repository Structure

```
├── workflow.json       # Main n8n workflow — import this
└── README.md           # This file
```

---

## 🙋 Author

**Rushikesh Thakare**
Diploma in Computer Science Engineering — Government Polytechnic, Mumbai
Web Developer & AI Engineer Intern — GigaWebZone LLP

- 🌐 [rushikeshthakare.in](https://rushikeshthakare.in)
- 💼 [LinkedIn](https://www.linkedin.com/in/rushikesh-thakaree/)
- 🐙 [GitHub](https://github.com/Rushikesh-Thakare)

---

## 📄 License

MIT License — feel free to fork, modify, and use for your own inbox automation.
