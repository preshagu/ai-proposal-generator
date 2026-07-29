# Setup

## Required credentials (create these in n8n under Credentials)

| Credential | Type | Notes |
|---|---|---|
| Groq API | HTTP Header Auth | Name: `Authorization`, Value: `Bearer YOUR_GROQ_API_KEY` |
| Google Drive account | Google Drive OAuth2 | Standard OAuth connection |
| Google Sheets account | Google Sheets OAuth2 | Standard OAuth connection |
| Gmail account | Gmail OAuth2 | Standard OAuth connection |
| PDFShift API | HTTP Basic Auth | Your PDFShift API key |

If your Google OAuth consent screen is in "Testing" mode, refresh tokens expire every 7 days and Drive/Gmail/Sheets nodes will start throwing invalid-grant errors on a schedule. Publish the app to Production to stop that.

## Placeholders to replace before running

- **Normalize Input** node — replace `your-email@example.com` with your real email address (this is where every generated proposal gets sent for review)
- **Business & Rate Card (Edit Me)** node — set your real `businessName`, `tagline`, `brandColor` (hex), and `rateCardText`
- **Upload to Drive** node — replace `YOUR_DRIVE_FOLDER_ID` with a real Google Drive folder ID
- **Log to Tracker** node — replace `YOUR_GOOGLE_SHEET_ID` with a real Google Sheet ID (needs a "Proposals" tab, or edit the sheet name to match an existing one)

## Running it

1. Import `ai-proposal-generator-workflow.json` into n8n
2. Configure the credentials and placeholders above
3. Activate the workflow
4. Open the Form Trigger node to get your hosted form URL
5. Fill it in and submit — the generated proposal lands in your inbox for review

## Architecture notes

- Complex request bodies (the Groq API call, the review email) are built in Code nodes rather than as inline n8n expressions — n8n's expression parser struggles with heavily nested template literals and escaped quotes, so keeping that logic in plain JavaScript avoids a class of hard-to-diagnose "invalid syntax" errors.
- Binary PDF data has to be explicitly re-attached (`binary: item.binary`) in every Code node it passes through. Several n8n nodes (Google Sheets append, any Code node that reconstructs its return object) silently drop binary data otherwise — there's no warning until a downstream node complains it's missing.
