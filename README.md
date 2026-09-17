# ContentFlow AI

> AI-powered content generation and review automation with controlled revisions and human-in-the-loop approval.

ContentFlow AI is an **n8n-based AI content automation workflow** designed to streamline the process of turning content ideas into reviewed, approval-ready social media posts.

Instead of directly publishing AI-generated content, the workflow combines **AI generation, structured review, controlled revision, and human approval** to keep a person in the loop before content is finalized.

---

## Overview

Creating consistent social media content involves multiple repetitive steps: understanding the topic, generating suitable content, checking quality, making revisions, and getting final approval.

ContentFlow AI automates this workflow while keeping **human approval as the final decision point**.

The workflow uses:

* **n8n** for workflow orchestration
* **Google Sheets** as the content calendar and workflow data store
* **Google Gemini** for content strategy, generation, and review
* **Telegram** for human approval
* **JavaScript** for data transformation, parsing, validation, and revision tracking

---

## Workflow

```text
Google Sheets
     │
     ▼
Get Pending Content
     │
     ▼
Select Pending Row
     │
     ▼
Validate Input
     │
     ▼
Mark Processing
     │
     ▼
AI Content Strategist
     │
     ▼
Parse Strategy JSON
     │
     ▼
AI Content Generator
     │
     ▼
Parse Generated JSON
     │
     ▼
AI Content Reviewer
     │
     ▼
Parse Review JSON
     │
     ▼
Review Decision
     │
     ├────────────── APPROVE ──────────────► Telegram Approval
     │
     │
     └────────────── REVISE
                         │
                         ▼
                  Revision Counter
                         │
                         ▼
                 Revision Limit Check
                    │            │
                  Continue       Limit
                    │              │
                    ▼              ▼
             AI Content       Needs Human
              Generator          Review
                    │
                    ▼
                 Reviewer
                    │
                    └──────► Review Decision
```

---

## Key Features

### 1. AI Content Strategy

The workflow first analyzes the content request and creates a structured strategy based on factors such as:

* Topic
* Target audience
* Platform
* Content goal
* Tone

This provides context for the content generation stage.

### 2. Structured AI Content Generation

The content generator creates structured output rather than relying on unformatted text.

Generated fields can include:

* Post title
* Post content
* Call to action
* Hashtags
* Platform-specific content

### 3. Automated AI Review

A separate AI reviewer evaluates the generated content and returns structured review information including:

* Quality score
* Decision
* Feedback
* Issues
* Suggested improvements

The reviewer can return:

```text
APPROVE
```

or

```text
REVISE
```

### 4. Controlled Revision Loop

When the reviewer requests changes, the workflow sends the content through a controlled revision cycle.

A JavaScript-based revision counter tracks the number of revision attempts and prevents an unlimited AI revision loop.

The workflow allows a maximum of **3 automatic revisions** before routing the content to human review.

### 5. Human-in-the-Loop Approval

Approved content is not automatically published.

Instead, the workflow sends the content to **Telegram for human approval**.

This provides a final review layer before content can be considered ready for publishing.

### 6. Google Sheets Content Management

Google Sheets acts as the content calendar and lightweight data store.

Example fields include:

| Field               | Purpose                   |
| ------------------- | ------------------------- |
| `id`                | Unique content identifier |
| `topic`             | Content topic             |
| `audience`          | Target audience           |
| `goal`              | Content objective         |
| `platform`          | Target platform           |
| `tone`              | Desired writing tone      |
| `status`            | Workflow status           |
| `generated_content` | AI-generated content      |
| `review_score`      | AI review score           |
| `review_feedback`   | Reviewer feedback         |
| `approved_content`  | Final approved version    |
| `created_at`        | Creation timestamp        |
| `updated_at`        | Last update timestamp     |

---

## Decision Logic

The workflow uses two important decision points.

### Content Review

```text
APPROVE → Telegram Approval
REVISE  → Revision Counter
```

### Revision Limit

```text
Revision 1 → Continue
Revision 2 → Continue
Revision 3 → Continue
Revision 4 → Human Review
```

This prevents the workflow from continuously calling the AI model when content repeatedly fails review.

---

## Example Review Output

The AI reviewer returns structured information similar to:

```json
{
  "score": 9,
  "decision": "APPROVE",
  "feedback": "The content is relevant and well structured.",
  "issues": [],
  "improvements": []
}
```

For content requiring changes:

```json
{
  "score": 6,
  "decision": "REVISE",
  "feedback": "The content needs stronger examples.",
  "issues": [
    "The explanation is too generic."
  ],
  "improvements": [
    "Add a specific real-world example."
  ]
}
```

---

## Technology Stack

| Technology        | Purpose                                               |
| ----------------- | ----------------------------------------------------- |
| **n8n**           | Workflow automation and orchestration                 |
| **Google Gemini** | Strategy, generation, and content review              |
| **Google Sheets** | Content calendar and data storage                     |
| **Telegram**      | Human approval interface                              |
| **JavaScript**    | Parsing, validation, branching, and revision tracking |

---

## Design Principles

ContentFlow AI follows a few important design principles:

* **Human-in-the-loop** — AI does not make the final publishing decision.
* **Structured outputs** — AI responses are parsed into predictable fields.
* **Controlled automation** — revision loops have a defined limit.
* **Separation of responsibilities** — strategy, generation, and review are handled as separate stages.
* **No automatic publishing in the current version** — final approval remains manual.

---

## Project Structure

```text
ContentFlow-AI/
│
├── README.md
│
├── workflow/
│   └── ContentFlow_AI.json
│
├── docs/
│   └── architecture.png
│
└── .gitignore
```

---

## Setup

### 1. Import the workflow

Download the workflow JSON from this repository and import it into your n8n instance.

### 2. Configure credentials

Configure your own credentials for:

* Google Sheets
* Google Gemini
* Telegram

Do not commit API keys, bot tokens, OAuth credentials, or environment files to the repository.

### 3. Configure the Content Calendar

Create a Google Sheet containing the required content fields and connect it to the workflow.

### 4. Configure Telegram

Connect a Telegram bot and configure the approval interaction.

### 5. Test the workflow

Test both decision paths:

```text
APPROVE → Telegram Approval
REVISE  → Revision Loop
```

Also verify that the revision counter stops automatic revisions after the configured limit.

---

## Current Scope

The current version focuses on:

* Content strategy
* AI content generation
* AI content review
* Structured JSON processing
* Controlled revision loops
* Human approval
* Google Sheets-based content management
* Telegram-based approval

### Future Improvements

Possible future extensions include:

* Automated publishing after approval
* Multi-platform content adaptation
* Analytics integration
* Content performance tracking
* Advanced approval roles
* Scheduling and publishing
* Content history and versioning

---

## Why I Built It

ContentFlow AI was built to explore how **AI agents, workflow automation, structured data, and human oversight** can work together in a practical content-production system.

The project focuses not only on generating AI content, but also on building the surrounding workflow required to make AI-generated output more controlled, reviewable, and usable.

---

## Author

**Shruti Verma**

B.Tech CSE (AIML) Student

GitHub: [Shrutii008](https://github.com/Shrutii008)

---

## License

This project is available for educational and portfolio purposes.
