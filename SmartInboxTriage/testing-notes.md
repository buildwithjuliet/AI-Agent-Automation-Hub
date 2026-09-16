# Testing Notes

Findings from live testing, kept here as they happen and folded into the main README once testing is complete.

## Known limitation: two identical messages landing in the same thread at once

Gmail Trigger fires on new activity per thread, not per individual message. During live testing, two duplicate automated notifications arrived in the same Gmail thread within the same one-minute poll window. Only one of them triggered a fresh execution and got triaged (archived correctly, as clutter). The second message, sitting in that same thread, was never independently picked up, so it kept its INBOX label and sat untouched.

This isn't a bug in the triage logic itself, the message that did trigger was handled correctly. It's a gap in how Gmail Trigger surfaces new activity when two messages land in one thread close enough together to be seen as a single update.

**Decision:** not engineering around this. It's rare enough (mostly an automated-sender quirk, not something real 1:1 correspondence does) that it isn't worth adding complexity for. Left to a human to notice and archive manually, or to [SmartInboxCleanup](https://smartinboxcleanup.buildwithjuliet.com/) to sweep up during a periodic backlog clean.
