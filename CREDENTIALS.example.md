# Credentials example (no secrets)

Copy this checklist when wiring the starter pack. **Never** commit real API keys, OAuth tokens, `.env` files, or n8n credential export dumps.

---

## 1. OpenAI / OpenAI-compatible (`openAiApi`)

**Used by:** workflows `01`, `03`, `04`, `05` (LangChain **OpenAI** node).

| Field | Example value (fake) | Notes |
| --- | --- | --- |
| API Key | `sk-proj-REPLACE_ME` | Create in OpenAI dashboard or vendor console |
| Base URL (optional) | `https://api.openai.com/v1` | Point at Groq/Together/local proxy if needed |

In n8n: **Credentials → Add → OpenAI** → paste key → select that credential on each OpenAI node after import.

Placeholder left in JSON:

- Credential name: `OpenAI account`
- Credential id: `YOUR_OPENAI_CREDENTIAL_ID`

After import, remap; do not expect the placeholder id to resolve.

---

## 2. Header Auth for HTTP Chat Completions

**Used by:** workflow `02` (`LLM Summary (HTTP · OpenAI-compatible)`).

| Field | Example value (fake) | Notes |
| --- | --- | --- |
| Name | `Authorization` | Exact header name |
| Value | `Bearer sk-proj-REPLACE_ME` | Include the `Bearer ` prefix |

Optional env vars referenced by the node URL/body:

| Env | Purpose | Example |
| --- | --- | --- |
| `OPENAI_BASE_URL` | Host without trailing path quirks | `https://api.openai.com` |
| `OPENAI_MODEL` | Model id | `gpt-4o-mini` |

If env vars are unset, the workflow defaults to `https://api.openai.com` and `gpt-4o-mini`.

---

## 3. Slack API

**Used by:** workflow `03` (required for full demo); workflow `04` optional node (disabled by default).

| Field | Example | Notes |
| --- | --- | --- |
| Access Token | `xoxb-REPLACE_ME` | Bot User OAuth Token |
| Channel ID | `C0123456789` | Set on the Slack node; not a credential |

Bot scopes typically needed: `chat:write` (and channel membership).

Placeholder credential: `Slack account` / `YOUR_SLACK_CREDENTIAL_ID`.

---

## 4. SMTP (email digest)

**Used by:** workflow `04` (`Send Digest Email`).

| Field | Example | Notes |
| --- | --- | --- |
| User | `bot@example.com` | Your mail provider |
| Password | `app-password-REPLACE_ME` | Prefer app passwords |
| Host | `smtp.example.com` | Provider SMTP host |
| Port | `587` | Often 587 (STARTTLS) or 465 |
| From / To on node | `noreply@example.com` → `YOU@example.com` | **Change To** before Activate |

Placeholder credential: `SMTP account` / `YOUR_SMTP_CREDENTIAL_ID`.

---

## 5. CRM / generic HTTP API

**Used by:** workflow `05` posts to `https://httpbin.org/post` with **no** auth so the template runs cold.

When swapping to a real CRM:

| Option | When |
| --- | --- |
| Header Auth | `Authorization: Bearer …` or `X-API-Key: …` |
| OAuth2 | HubSpot / Salesforce style |
| Query auth | Rare; prefer headers |

Update **HTTP · Upsert CRM Stub** URL + authentication; keep the normalized JSON body shape or map fields in a Set node.

---

## Import remap checklist

After **Import from File**:

1. Open each red/warning credential node → **Credential** dropdown → create or select yours.
2. Replace `YOUR_SLACK_CHANNEL_ID` and `YOU@example.com` parameters.
3. Save → Test once with non-production data.
4. Activate only when webhook paths and destinations are intentional.

---

## What this file is not

- Not a `.env` template with real values
- Not an n8n credential export
- Not permission to store secrets in git

If a secret ever lands in a commit, rotate it at the provider and purge it from history.
