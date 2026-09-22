# Give this to Claude

Attach these files to Claude:

1. `../n8n/01_ai_script_studio_engine_preserved.json`
2. `../n8n/02_ai_script_studio_webhook_gateway.json`
3. `../frontend/`
4. `../CLAUDE_IMPLEMENTATION_PROMPT.md`

Then tell Claude:

> Execute the attached implementation prompt against these files. Do not redesign the system. Preserve the AI engine exactly and only repair transport/integration issues needed to make the React + Webhook + bulk XLSX/CSV + Google Sheets flow work. Validate the resulting JSON and React build before returning changes.

The full implementation prompt is the source of truth.
