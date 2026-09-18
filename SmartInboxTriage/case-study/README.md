# Five Scenarios, One Inbox: Testing SmartInboxTriage v2 Live

I tested v2 against real email, sent from one of my own accounts to another, scenarios covering the actual range of things a founder's inbox sees: a genuine partnership request, a vague message with no context, a non-urgent idea, and a pricing question that hit a real infrastructure failure mid-run. For each one I'm showing what I expected the system to do, next to what it actually did, mistakes and one real deviation included.

## The one that used the wrong prompt

Before any of the numbered scenarios, my very first live run used the previous prompt version by mistake, I hadn't confirmed the updated one was actually deployed. That's on me, not the system. Worth showing anyway, because it's an honest look at what happens when the wrong ruleset is live.

The email was a partnership inquiry. Under the current design, this type is supposed to escalate to me for a decision, not get an automatic reply. Under the old prompt, it replied directly instead, an acknowledgment, reasonable in tone, just not the intended behavior. It took a noticeably long time to send.

It still tried to escalate the case to Telegram afterward, but Telegram couldn't return a response to the workflow, so that run stalled on the approval step.

![The run stuck waiting on a Telegram yes/no that never came back](first-execution-telegram-stuck.png)
![The acknowledgment reply that went out under the old prompt, it took close to 5 minutes to appear in my sent folder](first-execution-acknowledgement-reply.png)

[Watch the full run](https://youtu.be/4joLbwLMPOA)

## Scenario 1 & 3: "quick one before end of day" / "can we hop on a call?"

**Expectations:** Content asks for a call, so it should land as a Meeting again, reason already given. No reply and no label upfront, straight to a Telegram yes/no. Yes sends the Calendly link and archives. No archives with no reply sent.

**Results:** A time-sensitive partnership request came in. Because it was urgent, the agent didn't act on its own, it escalated straight to me on Telegram and waited for a decision. I clicked yes, and it sent my Calendly link to the person, it read as a warm response, not a robotic one. If I'd clicked no instead, that's my signal that I want to write a personal reply myself, the agent hands me the thread link in the same Telegram message either way. Matched expectations.

[Watch the full run](https://youtu.be/keIgmvQo9gI)

## Scenario 2: "collab idea"

**Expectations:** True Urgent category, not time-sensitive. Labeled "Urgent for Julie" only, no Telegram, no reply, no archive, just sits labeled for me to review whenever.

**Results:** A collaboration idea came in with no time pressure attached. The agent labeled it "Urgent for Julie" and did not ping me on Telegram or send a reply, that part matched. Where it deviated: the expectation was that it stays labeled and unarchived, sitting in the inbox for me to review, but the agent archived it as well. That label still feeds the end-of-day digest I get before I even open the inbox myself, so nothing was lost, but it's an honest, confirmed deviation from what was expected, not a silent success.

[Watch the full run](https://youtu.be/AiPLljCuElo)

## Scenario 4: "are you free this week?", in two parts

**Expectations:** First message, the agent asks what it's about, labels the thread "awaiting response," archives it, no Telegram yet. My reply with the real reason: label clears, Telegram yes/no fires with the reason, then Calendly or silence, same as Scenario 1 & 3 from there.

**Results:** John's opening email was just "are you free this week?", nothing else. The agent replied asking him what he actually wanted to talk about, and labeled the thread "awaiting response." One thing didn't go cleanly here: archiving the original message failed with a Gmail API error (visible in the video), a known, occasional quirk, not something that affected the reply or the labeling. John replied in the same video with his real reason, that he wants to automate his processes and doesn't know where to start. His reply landed on the same thread, still carrying the label, so the agent recognized it had unfinished business there, summarized what John wanted, and sent it to me on Telegram. I clicked yes, and my Calendly link went out to him. Matched expectations, aside from the archive error.

[Watch the full run](https://youtu.be/GqpRyrW0o68)

## Scenario 5b: pricing question, not in the knowledge base

**Expectations:** Holding reply sent, labeled, Telegram escalation fires. Now correctly defined: whichever I click, yes or no, it just archives right after, no different sender-facing action either way.

**Results:** This is the one I'm proudest of, not because everything worked, but because of what happened when something didn't. Someone asked a pricing question, something the agent's knowledge base is built to answer directly. But the embeddings model behind that knowledge base had been deprecated and pulled by Cohere without warning. The lookup failed outright:

```
NotFoundError, Status code: 404
"model 'embed-english-v2.0' was removed on April 4, 2026."
```

The agent didn't crash, and it didn't guess at a price. It treated the failure exactly the way it treats a genuine "I don't know," sent the person a professional holding reply acknowledging their question and promising to confirm the price range, labeled the thread, and escalated to me on Telegram. I already knew the model had been deprecated before I even opened Telegram, a separate error workflow on this project flags failures like this on its own. I chose to reply to that person myself, using the link the agent handed me, matching the expectation that either choice just archives the thread with no extra message sent on the system's own initiative.

The fallback logic wasn't built to catch a dead embeddings model specifically, it was built around "if the answer isn't confidently known, don't guess, ask." An API error turned out to be just another form of not knowing, and the system handled it the same way either way. Matched expectations.

![The error that surfaced when the knowledge base lookup failed](scenario-5-rag-error.png)

[Watch the full run](https://youtu.be/Q8ao5GZbZpY)

## Full findings

Every quirk mentioned above, plus a couple more found during testing that didn't make it into a numbered scenario, are written up in more technical detail in [testing-notes.md](../testing-notes.md) in the parent folder.

## Back to the overview

[← Back to SmartInboxTriage](../README.md)
