
# Clipbook Growth Engineering Challenge

This repository contains a scaffold implementation of the **Auto-Updating Unified Outreach Platform** that merges events from **HeyReach**, **Salesforge**, and **Instantly** into a single normalized contact table.

---

## 🚀 Goal

Build a service (API or CLI) that:

1. **Ingests** outreach + reply events from `mock/*.jsonl`
2. **Normalizes** them into a unified contact schema
3. **Stores** them in a local DB
4. **Continuously updates** via webhooks or polling
5. **Exports a sorted `unified.csv`** (following the sorting rules below)
6. Ensures identical output across implementations via consistent sorting

---

## 📂 Directory Structure

```
/
├── app.py                 # Entry point placeholder
├── README.md              # This file - challenge description
├── expected_output.csv    # CSV template showing exact column format
├── EXAMPLE_ROWS.md        # Example output rows with explanations
└── mock/
     ├── heyreach.jsonl    # 100 HeyReach events
     ├── salesforge.jsonl  # 100 Salesforge events
     └── instantly.jsonl   # 100 Instantly events
```

---

## 📥 Input Format (Mock Data)

You will ingest **JSONL** events from three sources:

### HeyReach fields
```
id, prospect_email, prospect_name, campaign, type, at, text?
```

### Salesforge fields
```
id, email, full_name, sequence, type, at, text?
```

### Instantly fields
```
id, contact{email,name}, campaign_name, type, timestamp, body?
```

---

## 🗄 Database & Storage

**You may use any database you prefer:**
- SQLite (recommended for simplicity)
- PostgreSQL
- In-memory data structure (if you implement persistence)
- Any other database of your choice

The database should store contact records that can be queried and exported to CSV.

---

## 📊 Unified Schema

Each contact appears **once**, keyed by email:

| Field | Description | Example |
|-------|-------------|---------|
| email | Primary key (lowercase) | `alice.johnson@techcorp.com` |
| full_name | Most recent name from events | `Alice M. Johnson` |
| sources | JSON array of unique platforms | `["heyreach","salesforge","instantly"]` |
| sequence_name | Most recent campaign/sequence name | `Tech Leaders Q1` |
| first_outreach_at | Earliest send event timestamp | `2024-01-15T09:00:00Z` |
| last_touch_at | Latest send OR reply timestamp | `2024-01-15T16:00:00Z` |
| replied | Has this contact ever replied? | `true` or `false` |
| last_reply_at | Timestamp of most recent reply (or empty) | `2024-01-15T16:00:00Z` |
| last_reply_text | Body of most recent reply (or empty) | `Yes, Tuesday at 2pm works for me.` |
| source_event_count | Total events across all platforms | `5` |
| updated_at | Timestamp of most recent event | `2024-01-15T16:00:00Z` |

### Key Rules:
- **Email normalization**: Store emails in lowercase for deduplication
- **Name conflicts**: When the same email has different names, use the name from the most recent event
- **Sources array**: Must be unique platform names, sorted alphabetically: `["heyreach","instantly","salesforge"]`
- **Empty values**: Use empty string `""` for `last_reply_at` and `last_reply_text` if contact never replied

---

## 📐 CSV Output Requirements

Your final output **must** be a file named `unified.csv` with the exact column names and order shown below.

### Required Column Order (Do Not Change!)
```
email,full_name,sources,sequence_name,first_outreach_at,last_touch_at,replied,last_reply_at,last_reply_text,source_event_count,updated_at
```

See `expected_output.csv` for the exact column format (header row).
See `EXAMPLE_ROWS.md` for example output rows with explanations.

### Sorting Rules (Required for Consistent Output)
**You MUST sort your output by:**
1. `email` ascending (case-insensitive alphabetical: a→z)
2. Then `first_outreach_at` ascending (earliest first)

**This ensures a single correct answer that can be verified across all implementations.**

### Formatting Rules
* **Timestamps** → ISO 8601 format: `YYYY-MM-DDTHH:MM:SSZ` (UTC timezone with Z suffix)
* **sources** → JSON array string with sorted platform names: `["heyreach","instantly","salesforge"]`
* **replied** → Lowercase boolean: `true` or `false`
* **last_reply_at** → ISO 8601 timestamp OR empty string `""` if never replied
* **last_reply_text** → Reply text OR empty string `""` if never replied
* **CSV escaping** → Wrap fields containing commas, quotes, or newlines in double quotes and escape internal quotes

---

## 🧩 Implementation Options

You can choose either approach:

### Option A: Simple Script (Recommended for 1-hour scope)
```bash
python app.py
# Output: unified.csv
```
A single script that:
1. Reads all three JSONL files from `mock/`
2. Processes and merges the events
3. Writes `unified.csv` to the current directory

### Option B: API
```
POST /webhooks/heyreach
POST /webhooks/salesforge
POST /webhooks/instantly
GET  /export.csv
```

### Option C: CLI
```bash
python app.py sync --source heyreach
python app.py sync --source salesforge
python app.py sync --source instantly
python app.py export --out unified.csv
```

---

## 🧪 Testing Your Solution

1. Run your implementation to generate `unified.csv`
2. Verify the file has exactly 11 columns in the correct order
3. Verify output is sorted by email (ascending), then first_outreach_at (ascending)
4. Check a few rows manually:
   - Contacts with multiple events should show merged data
   - Contacts who replied should have `replied=true` and reply details populated
   - All timestamps should be in ISO 8601 format with `Z` suffix

---

## 📝 What to Include in Your Submission

### Required Files:
1. **Source code** - Your implementation (any language)
2. **unified.csv** - Your output file (sorted as specified)
3. **README.md** or **EXPLANATION.md** - Brief writeup covering:
   - How to run your code
   - Which database you used and why
   - How you handle deduplication and merging
   - How you handle name conflicts (when same email has different names)
   - Any assumptions or edge cases
   - Time spent

### What We're Evaluating:
- ✅ Correctness: Does `unified.csv` have the right data, columns, and sorting?
- ✅ Code quality: Is your code readable and well-structured?
- ✅ Data handling: Do you correctly merge events, handle replies, and resolve conflicts?
- ✅ Edge cases: Do you handle missing fields, name variations, and out-of-order events?

---
