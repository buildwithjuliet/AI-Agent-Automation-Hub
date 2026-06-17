# Automation-Agent-Hub.

Custom AI agents| Automation built to handle repetitive executive tasks like inbox management, research, and documentation so founders can focus on business growth.

### Why I build these
I know how it feels to have your time eaten up by tasks that aren't actually helping your business grow. You are an expert at your mission, but you're drowning in the day-to-day work required to keep it moving.

I build custom AI agents to act as your digital team. They don't just "automate"—they think, organize, and handle the heavy lifting so you can get back to building your vision.

### How I work
I am not just writing code; I am building systems that work for you. I focus on:

* **Saving you time**: Taking the repetitive work off your plate.
* **Reducing errors**: Making sure your systems run smoothly every single time.
* **Supporting growth**: Helping you focus on the big-picture decisions that actually matter.

### Let’s connect
I am always looking to connect with founders who are ready to stop managing tasks and start scaling their impact. If you want to see how an AI agent can transform your daily workflow, let’s talk.


## SmartInboxCleanup: Automated Inbox Cleanup

**SmartInboxCleanup** is an autonomous agent designed to process your existing email backlog. It executes a deep-clean of your inbox based on categorization rules you define via a Tally form. It is the ultimate tool for reclaiming your digital workspace by systematically purging unwanted bulk content and archiving important communications in bulk.

### The Tech Stack
*   **Form & Input:** Tally
*   **Automation Engine:** n8n
*   **Intelligence:** Claude Haiku
*   **Core API:** Gmail API
*   **Logging & Tracking:** Notion

### How It Works
*   **The Handshake:** The process begins with a secure link to grant the agent permission to your Gmail. The agent requests "modify" access specifically to trash emails and remove labels to archive them—these are the only two actions it performs.
*   **Redirection:** After the handshake, you are immediately redirected to a Tally form.
*   **The Cleanup:** Submitting the Tally form triggers the email cleanup workflow. It cleans your email based on your specific commands; the emails you want to be trashed are removed, and those for archiving are processed accordingly.
*   **AI Logic:** For emails that do not explicitly fall into your predefined categories, the AI analyzes their importance to make an autonomous decision.
*   **Confirmation:** Once the cleanup is complete, a summary email is sent to you detailing exactly how many emails were handled.
