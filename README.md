# 🤖 AI Gmail Email Classifier

An AI-powered Gmail automation workflow built using n8n.

This workflow automatically monitors incoming emails, classifies them using AI, applies Gmail labels, and sends contextual replies.

---

## ✨ Features

- 📩 Gmail Trigger (real-time monitoring)
- 🧠 AI-powered sender name extraction
- 🏷 Intelligent email classification
- 🔖 Automatic Gmail label assignment
- 💬 Automated contextual replies

---

## 🗂 Email Categories

- Services Requests
- Consultation Requests
- Payments
- Others

---

## 🛠 Tech Stack

- n8n
- OpenAI API (GPT model)
- Gmail API

---

## 🚀 Setup Instructions

1. Import the workflow into n8n.
2. Add your credentials:
   - Gmail OAuth2
   - OpenAI API
3. Create Gmail labels matching:
   - Services Requests
   - Consultation Requests
   - Payments
   - Others
4. Replace placeholder Label IDs with your own.
5. Activate the workflow.

---

## 🔒 Security Notice

This repository does NOT contain:
- API keys
- OAuth credentials
- Personal Gmail information

You must configure your own credentials inside n8n.

---

## 📌 Use Cases

- Small business inbox automation
- AI-powered email sorting
- Consulting inquiry management
- Customer support automation

---

## 👨‍💻 Author

Rushikesh Thakare  
AI Automation Developer
