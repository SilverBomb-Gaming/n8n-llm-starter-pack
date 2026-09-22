# n8n LLM Starter Pack

**Alfredo Cardona · [SilverBomb-Gaming](https://github.com/SilverBomb-Gaming)**

WHO THIS IS FOR???

Importable n8n workflows that show practical LLM patterns for AI automation / workflow IC roles: triage, document summary, webhook notifications, RSS digests, and lead normalization.

These are **templates**, not a hosted SaaS. You run them on your own n8n Cloud or self-hosted instance with your own API credentials.

---

## Why this exists

Recruiters and builders often ask for proof of n8n + LLM work that is more than screenshots. This repo is a shippable pack you can import, wire credentials into, and demo in under 10 minutes per workflow.

| Workflow | Pattern | Trigger → LLM → Action |
| --- | --- | --- |
| [`01-email-triage.json`](workflows/01-email-triage.json) | Classify & route | Webhook (email stub) → OpenAI Chat → labels / routes |
| [`02-pdf-to-summary.json`](workflows/02-pdf-to-summary.json) | Doc intelligence | Form upload → extract PDF → **HTTP** OpenAI-compatible → Markdown |
| [`03-webhook-to-slack.json`](workflows/03-webhook-to-slack.json) | Event notify | HTTP webhook → OpenAI Chat → Slack |
| [`04-rss-digest.json`](workflows/04-rss-digest.json) | Scheduled digest | Cron → RSS → OpenAI Chat → email (Slack optional) |
| [`05-form-to-crm.json`](workflows/05-form-to-crm.json) | Lead normalize | Form → OpenAI Chat → HTTP CRM stub |

---

## Prerequisites

- **n8n 1.x** — Cloud or self-hosted. Designed for **n8n ≥ 1.70** (LangChain OpenAI node `typeVersion` 1.8). Import may work on slightly older 1.x builds; if a node warns about version, accept the upgrade prompt in the editor.
- **LLM access** — OpenAI API key, **or** any OpenAI-compatible `/v1/chat/completions` endpoint (Groq, Together, Azure OpenAI proxy, local gateway, etc.).
- **Optional integrations** — Slack bot token, SMTP, CRM API — only for the workflows that use them. See [`CREDENTIALS.example.md`](CREDENTIALS.example.md).

No API keys, tokens, or private emails are stored in this repository.

---

## Import steps (n8n UI)

1. Open your n8n instance → **Workflows**.
2. Click **⋯** (or **Add workflow**) → **Import from File…**  
   (In some builds: create a blank workflow → **⋯** menu → **Import from File**.)
3. Choose a file from [`workflows/`](workflows/), e.g. `03-webhook-to-slack.json`.
4. n8n opens the canvas. Nodes that need credentials show warnings — click each and **select or create** the credential (placeholders like `YOUR_OPENAI_CREDENTIAL_ID` will not work until remapped).
5. Read the yellow/colored **sticky notes** on the canvas for the demo path.
6. **Save**, then **Test** (or **Activate** for production webhooks).

CLI alternative (self-host):

```bash
n8n import:workflow --input=workflows/03-webhook-to-slack.json
```

---

## Fastest demo: workflow 03 (Webhook → Slack)

Goal: prove trigger → LLM → Slack in one path.

1. Import [`workflows/03-webhook-to-slack.json`](workflows/03-webhook-to-slack.json).
2. On **LLM Format Slack Message**, attach an **OpenAI** credential (`openAiApi`).
3. On **Post to Slack**, attach a **Slack API** credential and set a real **Channel ID** (e.g. `C0123456789`). Bot needs `chat:write`.
4. Open **Event Webhook** → copy the **Test URL** (or Activate and use Production URL).
5. Send a sample event:

```bash
curl -X POST 'https://YOUR_N8N_HOST/webhook-test/notify-slack' \
  -H 'Content-Type: application/json' \
  -d '{"event":"deploy","status":"success","service":"billing-api","detail":"v1.4.2 live"}'
```

6. Confirm the Slack message and the JSON response from **Respond OK**.

If Slack is not ready yet, temporarily disable **Post to Slack** and connect **LLM Format Slack Message** → **Respond OK** to inspect the formatted payload only.

---

## LLM node styles (both documented)

This pack intentionally shows **two** ways to call models:

| Approach | Where | Credential |
| --- | --- | --- |
| **OpenAI Chat node** (`@n8n/n8n-nodes-langchain.openAi`, Message a model) | Workflows 01, 03, 04, 05 | n8n credential type `openAiApi` |
| **HTTP Request** to OpenAI-compatible Chat Completions | Workflow 02 | Header Auth: `Authorization: Bearer <token>` |

Both expect a chat-completions-compatible API. For non-OpenAI hosts, set base URL on the OpenAI credential (Cloud/self-host UI) or via `OPENAI_BASE_URL` in workflow 02.

Default model hint: **`gpt-4o-mini`** (cheap, fast for demos). Change freely.

---

## Credential placeholders (no secrets)

| Placeholder name in JSON | n8n type | Used by |
| --- | --- | --- |
| `OpenAI account` / `YOUR_OPENAI_CREDENTIAL_ID` | OpenAI (`openAiApi`) | 01, 03, 04, 05 |
| `OpenAI-compatible Header Auth` / `YOUR_HEADER_AUTH_CREDENTIAL_ID` | Header Auth | 02 |
| `Slack account` / `YOUR_SLACK_CREDENTIAL_ID` | Slack API | 03, 04 (optional) |
| `SMTP account` / `YOUR_SMTP_CREDENTIAL_ID` | SMTP | 04 |
| `YOUR_SLACK_CHANNEL_ID` | — (parameter) | 03, 04 |
| `YOU@example.com` | — (parameter) | 04 |

Full setup notes: [`CREDENTIALS.example.md`](CREDENTIALS.example.md).

---

## Honest scope

- Templates for learning, demos, and portfolio review — **not** production-hardened ops.
- Webhooks stub IMAP/email where useful; swap in Email Trigger (IMAP) when you have mail access.
- CRM write targets **httpbin.org** so the pack works without a real CRM.
- You own rate limits, PII handling, prompt quality, and error handling for real traffic.
- Sticky notes and node **Notes** call out the next swap-in steps.

Architecture sketch: [`docs/architecture.md`](docs/architecture.md).

---

## Repo layout

```
workflows/
  01-email-triage.json
  02-pdf-to-summary.json
  03-webhook-to-slack.json
  04-rss-digest.json
  05-form-to-crm.json
CREDENTIALS.example.md
docs/architecture.md
.gitignore
README.md
```

---

## License / use

Use and adapt freely for demos, interviews, and internal workshops. Do not commit real credentials. If you fork for a client, replace example emails/channels and review prompts for your data policy.

Built by **Alfredo Cardona** ([SilverBomb-Gaming](https://github.com/SilverBomb-Gaming)).
