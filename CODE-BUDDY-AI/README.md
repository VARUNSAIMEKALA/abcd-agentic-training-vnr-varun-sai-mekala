# 🤖 CodeBuddy.AI — WhatsApp-Based Programming Question Generator (n8n Workflow)

## 📘 Overview
**CodeBuddy.AI** is an n8n workflow that automates the process of generating and delivering personalized programming questions via WhatsApp.  
When a user sends a topic to your WhatsApp number (e.g., `topic: arrays`), the bot:
1. Detects the topic from the message.
2. Searches Google for relevant content.
3. Uses Google Gemini AI to generate 3 short, topic-related coding questions.
4. Sends the questions back to the user via WhatsApp.
5. Stores all messages and generated questions in Google Sheets.

This workflow makes it easy to run a learning assistant or daily programming challenge bot using **n8n**, **WhatsApp Cloud API**, and **Google Gemini**.

---

## ⚙️ Prerequisites

Before importing this workflow, make sure you have:

1. **n8n Instance** (Self-hosted / Cloud)
2. **Meta WhatsApp Cloud API** account  
   - Create a WhatsApp Business App at [Meta for Developers](https://developers.facebook.com/)
   - Get your `Phone Number ID`, `Access Token`, and set up webhook verification.
3. **Google Gemini API Key** (via Google AI Studio)
4. **Google Sheets** integration connected in n8n
5. (Optional) **Google Custom Search API Key** for fetching reference data.

---

## 🧩 Workflow Components

### 🧲 1. Webhook
- **Purpose:** Receives incoming messages from WhatsApp Cloud API.
- **Path:** `/whatsapp`
- **Method:** `POST`

### ⚖️ 2. IF Node
- **Logic:** Checks if the message contains greetings like “hi” or “hello”.
- **If True:** Sends a friendly greeting message via HTTP Request.
- **If False:** Passes data to the `Topic Finder`.

### 🧠 3. Topic Finder (Code Node)
- **Function:** Parses the WhatsApp message to extract:
  - User’s name
  - WhatsApp number
  - Message text
  - Topic (from `topic:` syntax)
- **Output:** Clean JSON with structured fields.

### 📄 4. Append Row in Sheet
- **Action:** Logs user info and topic in Google Sheets.  
  Columns: `NAME`, `NUMBER`, `MESSAGE`, `TOPIC`, `TIMESTAMP`.

### 🔍 5. HTTP Request — Google Custom Search
- **Purpose:** Fetches top 3 links/snippets from Google about the given topic.
- **API:** `https://www.googleapis.com/customsearch/v1`

### 🧰 6. Code Node — Build Reference Text
- **Function:** Combines top 3 results into readable context text for AI.

### 🧩 7. Question Generator (LangChain Agent)
- **Prompt:** Uses Gemini AI to generate 3 programming questions.
- **Input:** Topic + Reference content.
- **Output:** Plain text list of 3 questions.

### 🤖 8. Code Node — Split Questions
- **Logic:** Extracts each question (1, 2, 3) using regex and outputs:
  ```json
  {
    "Question1": "...",
    "Question2": "...",
    "Question3": "..."
  }



### 🪄 How It Works (Flow Summary)
```
User Message (WhatsApp)
        ↓
n8n Webhook
        ↓
Check (Hi / Hello?)
        ↙︎             ↘︎
Send Greeting     Extract Topic → Log to Sheets
                                  ↓
                     Google Search API
                                  ↓
                       Build Reference Text
                                  ↓
                          Gemini AI
                                  ↓
                      Generate 3 Questions
                                  ↓
                   Send Back via WhatsApp
                                  ↓
                   Update Google Sheets
```

