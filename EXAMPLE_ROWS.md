# Example Output Rows

Here are some example rows to illustrate the expected format:

## Example 1: Contact with multiple events across platforms who replied

```csv
alice.johnson@techcorp.com,Alice M. Johnson,"[""heyreach"",""instantly"",""salesforge""]",Tech Industry Push,2024-01-15T09:00:00Z,2024-01-15T16:00:00Z,true,2024-01-15T16:00:00Z,"Yes, Tuesday at 2pm works for me.",3,2024-01-15T16:00:00Z
```

**Explanation:**
- Email appears in all 3 platforms
- Name is "Alice M. Johnson" (from most recent event)
- Sources array is alphabetically sorted: `["heyreach","instantly","salesforge"]`
- First outreach: 2024-01-15T09:00:00Z (earliest send)
- Last touch: 2024-01-15T16:00:00Z (latest reply)
- Replied: true
- Last reply details are populated
- Total 3 events across all platforms

## Example 2: Contact who never replied

```csv
bob.smith@startupco.io,Bob Smith,"[""heyreach"",""instantly""]",Startup Outreach,2024-01-15T09:15:00Z,2024-01-15T15:00:00Z,true,2024-01-15T15:00:00Z,Not interested at this time.,2,2024-01-15T15:00:00Z
```

**Explanation:**
- Contact appeared in heyreach and instantly only
- Has replied (so replied=true)
- Last reply text contains the reply body

## Example 3: Contact with only send events (no reply)

```csv
emily.chen@startup.io,Emily Chen,"[""instantly"",""salesforge""]",Startup Founders,2024-01-15T11:05:00Z,2024-01-15T11:10:00Z,false,,,2,2024-01-15T11:10:00Z
```

**Explanation:**
- Contact never replied, so:
  - `replied` = `false`
  - `last_reply_at` = empty (no value between commas)
  - `last_reply_text` = empty (no value between commas)

## CSV Escaping Example

If a reply contains commas or quotes:

```csv
carol.white@enterprise.com,Carol J. White,"[""enterprise"",""heyreach"",""instantly"",""salesforge""]",Enterprise Sales,2024-01-15T10:00:00Z,2024-01-16T13:00:00Z,true,2024-01-16T13:00:00Z,"Can we get on a call this week? Also, here's our budget: $50k annually.",4,2024-01-16T13:00:00Z
```

**Note:** The `last_reply_text` field is wrapped in quotes because it contains commas.

## Key Formatting Rules

1. **Timestamps**: Always `YYYY-MM-DDTHH:MM:SSZ` format with `Z` suffix
2. **Sources**: JSON array with double quotes escaped: `"[""heyreach"",""salesforge""]"`
3. **Boolean**: Lowercase `true` or `false`
4. **Empty fields**: No value between commas (e.g., `,,` for two empty fields)
5. **Text with commas**: Wrap the entire field in double quotes
6. **Sorting**: By email (ascending), then first_outreach_at (ascending)
