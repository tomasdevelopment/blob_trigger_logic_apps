# blob_trigger_logic_apps
blob trigger logic apps code for medium article

Figure 0: Blob Triggered Logic App

Blob Trigger Logic Apps – CSV Automation with Event Grid

This repository contains an end-to-end example of automating CSV ingestion and processing in Azure Logic Apps, triggered by Blob Storage updates. It demonstrates two approaches:

Blob Trigger – baseline solution that reacts when a blob is added/updated.

Event Grid – improved, real-time, event-driven solution with precise filtering at the source.

🚀 Features

Blob Monitoring – Watches a Blob container for new or updated files.

CSV Parsing – Decodes base64 blob content into raw text, normalizes line breaks, and skips headers.

Row & Column Mapping – Splits rows into columns using split() and maps values dynamically.

Loop Actions – Iterates over CSV rows to trigger downstream actions (e.g., send emails, API calls, DB inserts).

Security – Masks sensitive inputs/outputs in Logic Apps settings.

Event Grid Upgrade – Uses prefix/suffix filters to only trigger on .csv files, eliminating noise and polling.

🛠 Setup
Prerequisites

Azure subscription

Azure Blob Storage account

Azure Logic Apps

(Optional) Azure Event Grid for real-time events

Steps (Blob Trigger)

Create a Logic App with the trigger:
When a blob is added or updated.

Add WDL expressions to filter files by name and timestamp.

@greaterOrEquals(
  ticks(triggerBody()?['properties']?['lastModified']),
  ticks(addMinutes(utcNow(), -5))
)

@contains(toLower(triggerBody()?['properties']?['blobName']), 'list')


Decode blob content using:

base64ToString(body('Read_blob_content')?['content']?['$content'])


Split CSV lines and skip header:

@skip(outputs('Compose_Lines'), 1)


Loop through rows, split by commas, and take action.
Example: split(item(), ',')[0]

Steps (Event Grid – Recommended)

Create an Event Subscription in your Blob Storage account.

Event Type: Microsoft.Storage.BlobCreated

Prefix filter: /blobServices/default/containers/<your-container>/blobs/

Suffix filter: .csv

Point the Event Grid subscription to your Logic App endpoint.

In Logic Apps, select trigger:
When a resource event occurs.

Authenticate with Managed Identity (recommended for private networking).

📊 Comparison
Feature	Blob Trigger	Event Grid
Execution Model	Polling	Push (real-time)
Filtering	Inside Logic App (WDL)	At the source (prefix/suffix)
Latency	Minutes	Instant
Cost Efficiency	Risk of extra executions	Lower, event-driven
Use Case	Quick start, simple flows	Scalable, production-grade
🔒 Security Best Practices

Mask sensitive inputs/outputs in Logic App actions.

Use Managed Identity for authentication where possible.

Restrict access to Blob containers with RBAC and private networking.

📂 Repo Structure
/blob_trigger_logic_apps
 ├── logicapp.json       # Example Logic App definition
 ├── README.md           # This file

📖 References

Medium Article – Stop Writing Cron Jobs. Start Automating with Event Grid + Logic Apps

Azure Logic Apps Docs

Azure Event Grid Docs

👨‍💻 Author

Tomas Suarez

🌐 GitHub: tomasdevelopment

💼 LinkedIn: Tomas Suarez
