# AI Proposal Generator

An n8n workflow that turns a client's project requirements into a complete, branded proposal — narrative, timeline, cost estimate, and scope document — exported as a polished PDF, with a human review step before anything reaches a client.

## The problem

Freelancers and agencies repeatedly write near-identical proposals from scratch for every new inquiry: understanding requirements, estimating cost, defining scope, and formatting all of it into something professional-looking — usually under deadline pressure.

## What this does

1. **Intake** — an n8n Form captures the client's project details. No webhook, no external tool needed to trigger it.
2. **Input validation** — a gate checks that required fields are actually present and substantive *before* anything reaches the AI, preventing garbled output from thin or incomplete submissions.
3. **Generation** — an LLM (Groq, `llama-3.3-70b-versatile`) generates the proposal narrative, timeline, cost estimate, and scope document as a single structured JSON object, grounded against a real rate card so pricing is never invented.
4. **Rendering** — the structured output is rendered into a branded HTML template and exported to PDF.
5. **Delivery** — the PDF is saved to Google Drive, logged to a Google Sheets tracker, and emailed to the freelancer for review — not sent directly to the client. Forwarding to the client is a deliberate manual step.

## Design decisions worth noting

- **Input validation before generation, not just output validation after.** Both directions matter — thin input produces broken proposals no matter how good the model is.
- **Rate-card grounding.** The AI estimates costs strictly within provided rate ranges instead of inventing numbers, so pricing stays defensible.
- **Human-in-the-loop by default.** Nothing reaches a client without a review step. Automation should remove the busywork, not the judgment call.
- **Non-blocking side effects.** Google Drive/Sheets logging failures can't prevent the actual review email from sending — the core function stays reliable even when secondary steps fail.

## Stack

n8n · Groq (LLM) · PDFShift (HTML→PDF) · Google Drive · Google Sheets · Gmail

## Setup

See [SETUP.md](./SETUP.md) for the credentials and configuration required to run this.

## Known limitation

PDF export currently uses PDFShift, whose free tier stamps a "Created via PDFShift" watermark on every page. A paid plan removes it; a self-hosted Puppeteer service is the alternative if you want full control.
