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
  ```

### 📊 9. Update Row in Google Sheet
- **Action:** Adds the generated questions into the same user’s row.

### 💬 10. HTTP Request — Send Back to WhatsApp
- **Function:** Sends generated questions to the user’s WhatsApp number using Cloud API.

---

## 🪄 How It Works (Flow Summary)

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

---

## 🔑 Environment Variables (Recommended)

| Variable | Description |
|-----------|-------------|
| `WHATSAPP_TOKEN` | Meta WhatsApp API access token |
| `GOOGLE_API_KEY` | Google Custom Search API key |
| `GEMINI_API_KEY` | Gemini AI key |
| `SHEET_ID` | Google Sheet ID where data is stored |

Replace hardcoded values in HTTP and Google Sheets nodes with these variables for secure configuration.

---

## 🚀 Setup Steps

### 1. Import Workflow
In n8n, click **Import from File** and select `correct one (1).json`.

### 2. Configure Credentials
Add credentials for:
- **HTTP Header Auth** (WhatsApp API)
- **Google Sheets OAuth2**
- **Google Gemini / PaLM API**

### 3. Deploy Webhook
Copy the webhook URL from n8n and paste it into your **Meta Developer App** under:
> WhatsApp → Configuration → Webhook → Callback URL

### 4. Test the Flow
Send a WhatsApp message:  
`topic: arrays`  
You’ll receive 3 coding questions related to arrays.

---

## 📂 Google Sheet Example

| TIMESTAMP | NAME | NUMBER | MESSAGE | TOPIC | QUESTION 1 | QUESTION 2 | QUESTION 3 |
|------------|------|---------|----------|--------|-------------|-------------|-------------|
| 2025-11-01 | Varun | 91XXXXXXXXXX | topic: arrays | arrays | What is an array? | How do you reverse an array in Java? | Difference between array and ArrayList? |

---

## 📸 Optional Enhancements

- Add a reminder workflow to send unanswered questions after 24h.
- Integrate **OpenAI / Gemini Flash** for better question generation.
- Add a **daily scheduler** to automatically send questions at a fixed time.

---

## 🧑‍💻 Author

**Varun Sai Mekala**  
Creator of *CodeBuddy.AI* — a smart WhatsApp-based programming tutor powered by n8n & Gemini AI.

---

## 🪪 License

This workflow is open-source and free to use for educational purposes.  
Feel free to fork, modify, and enhance.

---
