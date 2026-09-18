# Testing Notes

Technical findings from live testing, for anyone who wants the deeper detail behind the scenario walkthroughs in the [case study](case-study/README.md).

## Known limitation: two identical messages landing in the same thread at once

Gmail Trigger fires on new activity per thread, not per individual message. During live testing, two duplicate automated notifications arrived in the same Gmail thread within the same one-minute poll window. Only one of them triggered a fresh execution and got triaged (archived correctly, as clutter). The second message, sitting in that same thread, was never independently picked up, so it kept its INBOX label and sat untouched.

This isn't a bug in the triage logic itself, the message that did trigger was handled correctly. It's a gap in how Gmail Trigger surfaces new activity when two messages land in one thread close enough together to be seen as a single update.

**Decision:** not engineering around this. It's rare enough (mostly an automated-sender quirk, not something real 1:1 correspondence does) that it isn't worth adding complexity for. Left to a human to notice and archive manually, or to [SmartInboxCleanup](https://smartinboxcleanup.buildwithjuliet.com/) to sweep up during a periodic backlog clean.

## Failure and resilience: RAG retrieval broke, the agent handled it correctly anyway

During a live test (a pricing question, covered in the case study as Scenario 5), the knowledge base lookup failed outright. The exact error, straight from Cohere's API:

```
NotFoundError, Status code: 404
"model 'embed-english-v2.0' was removed on April 4, 2026."
```

The embeddings model this system was built on got deprecated and pulled from Cohere's platform. Since both inserting into and searching the knowledge base depend on that same model, the whole retrieval side is currently non-functional, not a partial degradation.

What matters more: the agent didn't break. It hit the tool error, treated it the same as a genuine "not found in the knowledge base" case exactly as designed, sent a professional holding reply, labeled it, and escalated to me on Telegram for a decision. No crash, no silent failure, no email left unhandled. A separate error workflow on this project also flagged the deprecation independently, so I already knew about it before the Telegram message arrived. The human-in-the-loop fallback caught an infrastructure failure it was never specifically built to catch, because it was built around "if the answer isn't confidently known, don't guess, ask," and an API error is just another form of not knowing.

**Decision:** documenting this honestly rather than quietly patching it. The fix is known (swap to a current Cohere embedding model and re-run ingestion), it's just not the priority right now. What actually earned the trust here is the fallback design, not the embedding provider, the system stayed reliable even when a dependency underneath it didn't.

## Label cleanup targets the wrong message in a multi-message thread

In the two-part meeting flow (ask for a reason, then act once it arrives), the label removal step always targets whichever message triggered the current execution. When someone replies with their reason, Gmail Trigger fires on their *new* reply message, not the original message that actually carries the "awaiting response" label. The "remove awaiting response" call succeeds, but as a no-op, on a message that never had the label. The original message keeps the label permanently (harmless since it's already archived, but never actually cleared).

**Status:** confirmed, not yet fixed.

## Final archive step failed on a live run, cause not fully identified

In one execution, every step succeeded, reason captured, Telegram approval, Calendly reply sent, activity logged, except the very last one: removing the INBOX label to archive the thread. Gmail's API rejected it directly: `"Bad request - please check your parameters (item 0)"`. Same node, same message ID, same call structure as an earlier successful label removal two steps prior in the same execution. Traced the full tool call sequence and couldn't isolate the exact cause with confidence.

Seen again during the Scenario 4 test in the case study, same class of error, still non-blocking, reinforces this is a real recurring quirk rather than a one-off fluke.

**Status:** confirmed to happen, root cause not fully identified. Not guessing at a fix blind.
