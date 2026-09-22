# Claude Implementation Prompt — AI Script Studio Webhook + React + Bulk Spreadsheet

You are modifying the attached n8n workflow and building the React frontend for it.

## PRIMARY GOAL

Turn the existing AI Script Studio into a React-powered system using an n8n Webhook, while preserving the existing AI generation behavior exactly.

The final system must support:

1. Manual script generation from React.
2. Bulk script generation from an uploaded CSV/XLSX spreadsheet.
3. The SAME existing AI pipeline for both manual and bulk rows.
4. Existing Google Sheet saving behavior: Abdul Hadi, Taabish, Random Account.
5. A downloadable XLSX result file for bulk generation.
6. Clean JSON output for manual generation.
7. Simple, reliable React UI.

## ABSOLUTE RULE: PRESERVE THE AI ENGINE

The existing workflow contains carefully tuned prompts and code for:

- research
- trend intelligence
- reference analysis
- audience psychology
- hooks
- strategy
- Roman Urdu / Urdish
- Gen Z behavior
- comedy and sarcasm
- script writing
- critic scoring
- quality gate
- revision loop
- credibility guardrails
- completion/readability rules

DO NOT rewrite, shorten, paraphrase, summarize, reorder, simplify, or stylistically change those prompts or rules.

Do not change the model prompts just to make webhook integration easier.

Do not change the creative logic.

Do not change the output quality rules.

Only make the minimum compatibility changes needed for input/output transport, workflow triggering, spreadsheet processing, security, and React integration.

The current AI pipeline must remain logically:

Research → Trend Intelligence → Reference Analysis → Strategy → Writer → Critic → Quality Gate → Revision if required → Final Output.

## IMPORTANT ARCHITECTURE

Use two n8n workflows:

### Workflow A — Webhook Gateway

React → Webhook → detect manual/bulk → normalize → execute AI engine → return result.

### Workflow B — AI Script Engine

This is the existing workflow's AI engine.

It is triggered by "When Executed by Another Workflow" and receives ONE normalized request item.

This is intentional. Bulk mode must call this engine once per spreadsheet row so the original AI pipeline is not duplicated or rewritten.

## MANUAL INPUT

React must send JSON equivalent to:

{
  "inputMode": "manual",
  "request": {
    "intent": "Create New Script",
    "contentType": "Instagram Reel",
    "niche": "Technology",
    "idea": "AI tools students should know",
    "existingScript": "",
    "platform": "Instagram Reels",
    "duration": "30 seconds",
    "targetAudience": "Gen Z",
    "market": "Pakistan",
    "language": "Natural Pakistani Roman Urdu + English",
    "scriptStyle": "Educational",
    "creatorPersonality": "Funny/Sarcastic",
    "tone": "Casual",
    "energy": "High",
    "showAlternatives": "Yes",
    "currentTrends": "Smart Mode",
    "research": "Basic",
    "referenceVideoUrl": "",
    "referenceTranscript": "",
    "referenceAnalysisFocus": "Hook",
    "saveScriptTo": "Abdul Hadi"
  }
}

The gateway must normalize this into the exact field names the existing request analyzer expects.

Do not change the existing analyzer's internal brief logic unless required for transport compatibility.

## MANUAL REACT FORM

The React frontend must contain the existing manual choices:

- What do you want to do?
- Content Type
- Niche
- Idea
- Existing Script
- Platform
- Duration
- Target Audience
- Market
- Language
- Script Style
- Creator Personality
- Tone
- Energy
- Show Alternative Hooks & Visual Ideas?
- Current Trends
- Research
- Reference Video URL
- Reference Transcript
- Reference Analysis Focus
- Save Script To

Use the same option values already present in the supplied workflow.

Do not invent different labels or option values.

## BULK MODE

React must support uploading:

- CSV
- XLSX

The first row is the header row.

Required column:

Idea

Other columns are optional and override defaults when present.

Recommended headers:

Idea
Niche
Platform
Duration
TargetAudience
Market
Language
ScriptStyle
CreatorPersonality
Tone
Energy
CurrentTrends
Research
ExistingScript
ReferenceVideoUrl
ReferenceTranscript
ReferenceAnalysisFocus
SaveScriptTo

Support reasonable header aliases such as:

idea / Idea
niche / Niche
Target Audience / TargetAudience
Creator Personality / CreatorPersonality
Save Script To / SaveScriptTo
Reference Video URL / ReferenceVideoUrl

Missing fields must use the existing workflow defaults.

Do not fail an entire batch because one row is missing an optional field.

If a row has no Idea, mark that row as Failed with a clear error and continue processing the other rows.

## BULK PROCESSING

Do NOT concatenate all spreadsheet rows into one AI prompt.

Do NOT rewrite the AI agents to generate 50 scripts in one response.

Instead:

1. Extract spreadsheet rows.
2. Normalize each row.
3. Execute the existing AI Script Engine once per row.
4. Preserve rowNumber and requestId throughout.
5. Collect the individual results.
6. Build the result spreadsheet.

Each row must get:

requestId
rowNumber
inputMode = bulk

This prevents outputs from being mixed between rows.

## GOOGLE SHEET SAVING

Preserve the existing "Save Script To" behavior:

- Abdul Hadi
- Taabish
- Random Account

Do not remove it.

Do not replace it with a new unrelated storage system.

The selected destination controls the existing Google Sheet tab behavior.

For bulk rows:

- If SaveScriptTo exists in the row, use it.
- Otherwise use the bulk-level default selected in React.

The Google Sheet save operation and the user's downloadable output spreadsheet are TWO DIFFERENT THINGS.

## OUTPUT: MANUAL

For manual requests, return JSON similar to:

{
  "success": true,
  "mode": "manual",
  "result": {
    "hook": "...",
    "body": "...",
    "cta": "...",
    "fullScript": "...",
    "alternativeHooks": [],
    "visualBeats": [],
    "analysis": {
      "overallScore": 91
    }
  }
}

React displays the result.

React must provide:

- Copy Script
- Download TXT

Do not expose API keys to React.

## OUTPUT: BULK

For bulk mode, return an XLSX file from n8n.

The result spreadsheet should contain these columns:

Row
Status
Idea
Niche
Platform
Duration
Script
Hook
CTA
Alternative Hooks
Visual Ideas
Score
Error

Every successful row must have its generated script.

Every failed row must remain in the spreadsheet with Status = Failed and a useful Error message.

The batch must not be lost because one row fails.

The XLSX should have a clear filename such as:

script-results.xlsx

React should automatically download the generated XLSX after a successful bulk request.

## REACT FRONTEND

Keep the frontend simple.

Do NOT build a large SaaS dashboard.

Use React + Vite.

Two modes:

Manual Script
Bulk Spreadsheet

Manual mode:

- form fields
- Generate Script button
- loading state
- error state
- generated script result
- score
- alternative hooks
- visual ideas
- copy button
- download button

Bulk mode:

- file picker
- selected file name
- default Save Script To
- Generate All Scripts button
- loading state
- success state
- automatic XLSX download
- clear error state

The React app must not contain Gemini, Tavily, Apify, Google Sheets or other private credentials.

Use:

VITE_N8N_WEBHOOK_URL

from .env.

## WEBHOOK

Use an n8n Webhook node:

POST

path:

script-studio

Respond mode:

Using Respond to Webhook Node

Use a Respond to Webhook node at the end.

Manual → JSON response.

Bulk → Binary XLSX response.

Add CORS response headers where applicable.

If the n8n deployment requires instance-level CORS configuration for browser requests, document exactly what environment/reverse-proxy setting is needed.

Do not put secrets in the frontend.

## SPREADSHEET NODES

Use current n8n file nodes, not the deprecated Spreadsheet File node.

Use:

Extract From File

for XLSX/CSV input.

Use:

Convert to File → Convert to XLSX

for the bulk output.

The current n8n ecosystem documents Extract From File as supporting CSV and XLSX, and Convert to File as supporting XLSX output. citeturn872228search0turn565785search0

## SECURITY

The supplied workflow currently contains API credentials/tokens inside node code.

Remove all real secret values from node code.

Use n8n environment variables or credentials instead.

Expected environment/config values include:

GEMINI_API_KEY
TAVILY_API_KEY
APIFY_API_TOKEN

Never place them in React.

Never return them in webhook responses.

## EXISTING GOOGLE SHEETS CREDENTIAL

Preserve the Google Sheets node credential mapping already present if possible.

Do not invent a new credential ID.

If import causes a credential mismatch, clearly mark the node as requiring the user's existing Google Sheets credential to be selected.

## ERROR HANDLING

Manual errors:

Return JSON with:

success: false
error: <clear message>

Bulk errors:

Never hide which row failed.

Return failed rows in the XLSX.

Do not silently drop rows.

## DO NOT CHANGE CREATIVE OUTPUT

The most important requirement is this:

A manual request sent through React with exactly the same values as the old n8n form must enter the same AI engine and produce the same style/behavior of script.

Do not change prompt wording merely because the transport changed.

Do not change default creative rules.

Do not remove the Roman-script-only rule.

Do not remove the Pakistani Urdish language rules.

Do not remove the Gen Z hook director.

Do not remove the comedy engine.

Do not remove the critic or quality gate.

Do not remove the revision loop.

Do not remove research/trend/reference routing.

## VALIDATION

Before finishing, test at minimum:

### Test 1 — Manual

One manual request from React.

Verify:

React → Webhook → existing AI engine → Google Sheet save → JSON response → React display.

### Test 2 — Manual Rewrite

Use Rewrite Existing Script.

Verify the existing script reaches the engine.

### Test 3 — Bulk CSV

Upload at least 3 rows.

Verify 3 independent AI executions and 3 result rows.

### Test 4 — Bulk XLSX

Upload at least 3 rows.

Verify XLSX extraction and generation.

### Test 5 — Missing optional columns

Upload only:

Idea
Niche

Verify defaults are applied.

### Test 6 — One invalid row

One row should have a blank Idea.

Verify that row becomes Failed while other rows still complete.

### Test 7 — Google save destinations

Test:

Abdul Hadi
Taabish
Random Account

Verify the selected destination is preserved.

### Test 8 — Output XLSX

Verify:

file opens correctly
headers are correct
scripts are in correct rows
failed rows are retained

## FINAL DELIVERABLES

Produce:

1. React frontend folder with all source files.
2. .env.example.
3. README with setup steps.
4. Webhook Gateway n8n JSON.
5. Preserved AI Engine n8n JSON.
6. This implementation prompt.

Do not give a conceptual answer only.

Actually make the code and workflow files.

The final answer should list exactly what was created and any unavoidable setup steps.
