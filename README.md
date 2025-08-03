
# 🏠 Interior Design Voice AI Agent — Ekam Apps Technical Assessment

This project automates the qualification of interior design leads using voice AI agents, Airtable, and the n8n automation platform. It simulates a fully automated outbound voice call workflow that collects lead information and updates records based on real call results.

---
## DEMO Video 

https://drive.google.com/file/d/1wDfqZXjUxgAVW5vPMCSoFCl6qvGlJ-cA/view?usp=drive_link


## 📌 Project Overview

The Interior Design Voice AI Agent system comprises:

- **Voice AI Agent**: Built using [Vapi.ai](https://vapi.ai) and powered by an OpenAI LLM.
- **Two n8n Workflows**:
  - **Workflow 1: Outbound Call Launcher**
  - **Workflow 2: Call Result Processor**
- **Airtable**:
  - `Leads` table – stores lead details and call status
  - `Call Records` table – logs all call metadata, duration, transcripts

---

## Interior Design Voice Agent System Flow Diagram

<img width="468" alt="Picture 1" src="https://github.com/user-attachments/assets/40a78eb1-94d5-454c-8a93-8d74cf24ea0a" />

## 🧠 Workflow Summary

### ▶️ Workflow 1: Outbound Call Launcher

This n8n workflow periodically scans the `Leads` table for new leads (status `TVC`) and triggers outbound voice calls.

#### Steps:
1. **Trigger**: Scheduled trigger (every minute or manual)
2. **Airtable**: Query `Leads` where Status = `TVC`
3. **Prepare Call Data**: Format payload for Vapi API
4. **HTTP Request**: Send POST to `https://api.vapi.ai/call` with lead info
5. **Airtable**: Update lead status to `In Progress` and increment attempt

### ⚡ Workflow 2: Call Feedback Listener

This webhook-based workflow handles post-call processing.

#### Trigger:
- **Webhook** (Production URL): `/webhook/vapi-callback`

#### Steps:
1. **Webhook**: Trigger on POST request from Vapi
2. **Validate Webhook Data**: Log and check structure
3. **Valid Data?**: Conditional node (true/false branch)
4. **Extract Call Data**: Parse phone number, time, duration, transcript
5. **Create Call Record**: Append to `Call Records` table
6. **Find Lead by Number**: Search `Leads` using mobile number
7. **Was Call Answered?**: If `status = completed`
8. **Update Status to Called**
9. **If Not Answered**:
    - Check if it was **First Attempt**
    - If yes: mark for **Retry Call**
    - If no: mark as **Failed**

---

## 📊 Airtable Setup

### Table: Leads
| Field         | Type      | Description                     |
|---------------|-----------|---------------------------------|
| ID            | Text      | Unique lead ID                  |
| First Name    | Text      | Lead first name                 |
| Last Name     | Text      | Lead last name                  |
| Mobile        | Phone     | Lead's mobile number            |
| Email         | Text      | Optional email address          |
| Assignee      | Single select | "Maya"                       |
| Status        | Single select | `TVC`, `In Progress`, `Called`, `Failed` |
| Attempt       | Number    | Call attempt count              |

### Table: Call Records
| Field         | Type      | Description                    |
|---------------|-----------|--------------------------------|
| ID            | Auto/Manual | Unique record ID             |
| callproviderID| Text      | Unique call ID from Vapi      |
| phonenumberID | Text      | Assistant/agent ID            |
| customernumber| Phone     | Customer number called        |
| type          | Text      | "outbound"                    |
| started       | DateTime  | Start time                    |
| ended         | DateTime  | End time                      |
| milliseconds  | Number    | Duration in ms                |
| cost fields   | Currency  | Cost (always 0 here)          |
| ended_reason  | Text      | Vapi call reason              |
| transcript    | Long Text | What the user said            |

---

## 💡 Features

- ✅ End-to-end call workflow with LLM agent
- ✅ Automatic retry logic on failed calls
- ✅ Transcript + cost logging
- ✅ Airtable status sync in real-time
- ✅ Modular workflows for easy scaling

---

## 🧱 Project Structure
![Screenshot 2025-05-15 at 10 35 44 PM](https://github.com/user-attachments/assets/b3da3e24-d56b-4c95-aed7-0edaa59d3e71)

