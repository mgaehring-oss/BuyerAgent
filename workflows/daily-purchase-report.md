# Daily Purchase Request Workflow

Run this workflow each morning to process client purchase requests and generate vendor recommendations.

---

## Step 1 — Read Inbox

Use the Gmail MCP tools to:
- Search for emails received in the last 24 hours
- Query: `after:{yesterday's date in YYYY/MM/DD format} in:inbox`
- Also check: `subject:(quote OR request OR order OR "looking for" OR "can you source") newer_than:1d`

## Step 2 — Filter Purchase Requests

For each email found, decide if it is a genuine purchase request from a client:

**Include if the email contains:**
- A request for a specific chemical (by name, formula, or CAS number)
- Words like: "need", "looking for", "can you source", "quote", "order", "purchase", "request", "supply"
- Quantity information (grams, kilograms, liters, etc.)

**Exclude:**
- Automated / marketing / newsletter emails
- Replies to your own outbound messages (unless the client added new requests)
- Internal emails or system notifications

## Step 3 — Extract Request Details

For each qualifying email, extract and record:

| Field | Instructions |
|-------|-------------|
| Client Name | From the sender name or email signature |
| Client Email | Sender address |
| Chemical Name | Normalize to the most common trade name or IUPAC name |
| CAS Number | If mentioned; look it up if the chemical is unambiguous |
| Quantity | Include units (g, kg, L, mL, lb) |
| Purity/Grade | e.g., ACS reagent grade, 99%+, food grade, USP |
| Urgency | Any mentioned deadline or rush language |
| Special Notes | Packaging requirements, certifications needed, etc. |

If a single email contains multiple chemicals, create a separate row for each.

## Step 4 — Collate and Consolidate

1. Build a master table of all requests from Step 3
2. Group rows by chemical name (normalize spelling/synonyms — e.g., "sodium chloride" and "NaCl" are the same)
3. Flag any chemical requested by **2 or more clients** — these are consolidation opportunities where a single vendor order can serve multiple clients

## Step 5 — Vendor Research (per unique chemical)

For each unique chemical in the master list:

1. Search the web for: `"[chemical name]" supplier "drop ship" OR "drop shipping" USA`
2. Also search: `"[chemical name]" buy bulk chemical supplier United States`
3. Identify at least 5 candidate vendors
4. For each candidate, record:
   - Company name and website
   - US-based? (yes/no)
   - Year founded / years in business
   - Drop shipping available? (yes/no)
   - Price per unit (if listed or requestable)
   - Minimum order quantity
   - Any certifications (ISO, cGMP, etc.)
   - Source URL

5. Apply ranking rules (from CLAUDE.md):
   - **Prefer longer-established vendors** — a vendor founded in 1985 ranks above one founded in 2015
   - **Prefer US-based suppliers** unless international price is more than 10% cheaper
   - Drop shipping availability is a strong positive signal

6. Select the **top 3 vendors** and write a **2–3 sentence justification** for the #1 recommendation citing longevity, location, drop shipping, pricing, and certifications

## Step 6 — Label and Archive Processed Emails

For each email confirmed as a customer purchase request in Step 2:

1. **Apply the label "customer processed"** using the Gmail MCP
   - If the label does not exist yet, create it first, then apply it
2. **Archive the email** (remove it from the inbox) — this moves it out of the inbox without deleting it

Do this for every qualifying email before generating the report. If labeling or archiving fails for any email, note the failure in the report but continue processing the remaining emails.

## Step 7 — Generate Report

Using the template at `resources/report-template.md`, populate a complete report with:
- Today's date
- The master client request table
- A vendor recommendation section for each unique chemical
- A consolidation savings section for chemicals with multiple clients

## Step 8 — Save Report

Save the completed report to:
```
output/YYYY-MM-DD-daily-report.md
```
Use today's actual date in the filename (e.g., `output/2026-04-17-daily-report.md`).

---

## Notes

- Always cite every source URL used in vendor research
- If no purchase request emails are found, save a brief "No requests today" report
- If the Gmail MCP connection fails, note the error and attempt to re-authenticate
