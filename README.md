# 🧠 Content Creation Assistant

An AI-powered autonomous agent that:

* **Researches** a topic
* **Writes** a high-quality blog post
* **Stores** the content in a Neon PostgreSQL database
* And **marks completion** using a custom `done` tool

Built using **[@inngest/agent-kit](https://github.com/inngest/agent-kit)**, **Google Gemini**, and **Smithery’s MCP** to enable serverless SQL control via AI.

---

## ✨ Features

| Feature                     | Description                                                                                         |
| --------------------------- | --------------------------------------------------------------------------------------------------- |
| **Agent Workflow**          | Five-step AI workflow (research → create DB tables → generate content → save to DB → mark as done). |
| **MCP-Enabled SQL Access**  | Secure SQL execution using Smithery’s MCP + Neon. No DB client needed!                              |
| **LLM-Powered Generation**  | Uses Gemini 1.5 Flash to write SEO-friendly, engaging articles.                                     |
| **Custom Tool Integration** | `done` tool stores completion metadata and signals task success.                                    |
| **Network & Routing**       | Task ends automatically when agent flags the content as completed.                                  |
| **Local HTTP Server**       | Launches an ESM-ready server on `localhost:3010`.                                                   |

---

## 🔄 Agent Flow (Textual Flowchart)

```
START
  ↓
Receive Prompt (e.g., "Write a blog on urban gardening")
  ↓
[contentCreatorAgent Activated]
  ↓
1️⃣ Research Topic
  ↓
2️⃣ Check/Create Database Tables (via Neon MCP)
  ↓
3️⃣ Generate Blog Content (via Gemini 1.5 Flash)
  ↓
4️⃣ Store Content + Metadata in DB
  ↓
5️⃣ Call `done` Tool (store summary, mark complete)
  ↓
[Router Checks]
 └── If completed = true → Stop
 └── Else → Continue
  ↓
END
```

---

## 🚀 Getting Started

### 1. Clone & Install

```bash
git clone https://github.com/your-org/content-creation-assistant.git
cd content-creation-assistant
npm install
```

### 2. Configure Environment Variables

```env
# .env
SMITHERY_API_KEY=your-smithery-key
GEMINI_API_KEY=your-google-gemini-key
# or: GOOGLE_GENAI_API_KEY=...
```

### 3. Run Locally

```bash
npx tsx index.ts
```

Output:

```
🚀 Content Creation Assistant running on http://localhost:3010
🗄️ Connected to Neon PostgreSQL via MCP
```

### 4. Inngest Dev Server (Optional)

```bash
npx inngest-cli@latest dev -u http://localhost:3010/api/inngest
```

Open: [http://localhost:8288](http://localhost:8288)

### 5. Try a Prompt

Send a POST request:

```json
POST http://localhost:3010
{
  "prompt": "Create a 1000-word blog post on sustainable urban gardening with SEO tips."
}
```

---

## 🗄️ Database Schema

```sql
CREATE TABLE IF NOT EXISTS content_pieces (
  id SERIAL PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  content TEXT NOT NULL,
  topic VARCHAR(255),
  content_type VARCHAR(100) DEFAULT 'blog_post',
  word_count INTEGER,
  keywords TEXT[],
  research_summary TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS research_sources (
  id SERIAL PRIMARY KEY,
  content_id INTEGER REFERENCES content_pieces(id),
  source_title VARCHAR(255),
  source_summary TEXT,
  relevance_score INTEGER DEFAULT 5,
  created_at TIMESTAMP DEFAULT NOW()
);
```

---

## 📁 File Structure

```
.
├── index.ts           # Main logic (agent, tool, server)
├── package.json
├── tsconfig.json
└── README.md
```

---

## 🛠 Customisation Options

| Goal                       | How to do it                                                                           |
| -------------------------- | -------------------------------------------------------------------------------------- |
| Change model or LLM params | Edit `model: gemini({ ... })` block in `index.ts`.                                     |
| Add more agents/tools      | Define new `createAgent` or `createTool` functions and wire into `createNetwork`.      |
| Add more DB fields         | Modify SQL schema & update your prompt/system instructions.                            |
| Deploy to cloud            | Deploy as ESM server to Vercel, Fly.io, or Cloudflare. Ensure `.env` is set correctly. |

---

## 📜 License

MIT © 2025 Hrushikesh Kadudula
