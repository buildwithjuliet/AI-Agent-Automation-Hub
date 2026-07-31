## SmartInboxTriage: Executive Inbox Agent (v1)

**SmartInboxTriage** is an always-on AI agent that watches a Gmail inbox, decides what each incoming email actually is, and acts on it — labeling, archiving, replying, or escalating — without a human touching the inbox first.

This was my first agent build, before SmartInboxCleanup. It's a different problem: SmartInboxCleanup is an on-demand, one-time deep clean of an existing backlog. SmartInboxTriage is a standing assistant that handles email as it arrives, every minute, forever.

### The Tech Stack

- **Trigger:** Gmail Trigger (polls every minute)
- **Automation Engine:** n8n
- **Intelligence:** Groq (Llama 3.3 70B) via a LangChain Agent node
- **Memory:** Per-thread conversational memory, so the agent doesn't lose context across a back-and-forth
- **Tools available to the agent:** Gmail (label, archive, reply), Slack (notify), Google Calendar (create events), Google Docs (knowledge base lookup)

### How It Works

- **Filter first:** Before the AI ever sees an email, a rule check skips anything already handled — already replied, or an obvious unsubscribe-pattern email — so the agent isn't burning tokens re-deciding what's already settled.
- **Decision matrix:** The agent classifies every email into one of four lanes: urgent/high-value (label + Slack ping, no auto-reply — this stays a human decision), meeting/calendar updates (Slack ping only), general business (checked against a Google Doc knowledge base — replies if the answer is known, sends a holding reply plus a lead alert if it isn't), or clutter/newsletters/automated senders (silently archived, no noise).
- **Tool use, not just classification:** The agent doesn't just label an email — it decides which of five tools to actually call (reply, label, archive, Slack, calendar) based on what it read.
- **Archive-last rule:** Whatever else happens, removing the inbox label is always the final action, so nothing gets archived before it's actually been handled.

### Coming Soon

This version works, but it's the first pass — a single do-everything agent carrying a long, rigid prompt. I'm actively rebuilding it into something sharper and more useful for founders specifically. Watch this space.
