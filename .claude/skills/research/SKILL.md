---
name: research
description: Web OSINT research skill. Use when the user types /research and provides a person name + location or business name + location to compile all publicly available information from the internet into a markdown report and JSON data file.
---

# Web Research / OSINT Skill

Compile every piece of publicly available information about a person or business from the internet, then save a Markdown report and JSON data file to the current directory.

## Input Format

The user will provide one of:
- A person: `/research John Smith, Miami FL`
- A business: `/research Mindo Tire Shop, Santa Cruz Aruba`

Parse the subject name and location from the user's input. If the subject is ambiguous (person vs. business), infer from context or ask one quick clarifying question.

## Workflow

Make a todo list covering all steps below and work through them one at a time.

---

### Step 1 — Parse Input

- Extract: **subject name**, **location**, **type** (person or business)
- Set the output filename slug: lowercase name with hyphens, e.g. `john-smith` or `mindo-tire-shop`
- Set today's date for filenames: `YYYY-MM-DD`

---

### Step 2 — Web Searches

Run ALL applicable searches using the WebSearch tool. Run independent searches in parallel where possible.

#### If subject is a PERSON:

| Category | Search Query |
|---|---|
| General | `"[Full Name]" "[City/Location]" contact phone email address` |
| LinkedIn | `"[Full Name]" "[City/Location]" site:linkedin.com` |
| Facebook | `"[Full Name]" "[City/Location]" site:facebook.com` |
| Twitter/X | `"[Full Name]" "[City/Location]" site:twitter.com OR site:x.com` |
| Instagram | `"[Full Name]" "[City/Location]" site:instagram.com` |
| News & Mentions | `"[Full Name]" "[City/Location]" news articles` |
| Public Records | `"[Full Name]" "[City/Location]" public records` |
| Court Records | `"[Full Name]" "[City/Location]" court records lawsuit` |

#### If subject is a BUSINESS:

| Category | Search Query |
|---|---|
| General | `"[Business Name]" "[City/Location]" phone address hours website` |
| Yelp | `"[Business Name]" "[City/Location]" site:yelp.com` |
| BBB | `"[Business Name]" "[City/Location]" site:bbb.org` |
| Google Reviews | `"[Business Name]" "[City/Location]" reviews rating` |
| LinkedIn | `"[Business Name]" "[City/Location]" site:linkedin.com` |
| Facebook | `"[Business Name]" "[City/Location]" site:facebook.com` |
| Owner & Contact | `"[Business Name]" "[City/Location]" owner contact email phone` |
| News & Press | `"[Business Name]" "[City/Location]" news press` |
| Business Registration | `"[Business Name]" "[City/Location]" business registration license incorporated` |
| Trustpilot | `"[Business Name]" "[City/Location]" site:trustpilot.com` |

---

### Step 3 — Fetch Key Pages

For the most relevant results from Step 2, use the WebFetch tool to pull full page content. Prioritize:
- The subject's official website (if a business)
- LinkedIn profile or company page
- Yelp listing
- BBB profile
- Facebook page
- Top 1–2 news articles

Extract all useful details: addresses, phone numbers, emails, hours, owner names, descriptions, ratings, review summaries, social links, registration numbers, etc.

---

### Step 4 — Compile & Deduplicate

Organize all collected information into these categories:

**For a Person:**
- Contact Information (phones, emails, addresses)
- Social Media Profiles (URLs, usernames, bio snippets)
- News & Public Mentions (headlines, dates, sources)
- Public Records (court cases, property, registrations)

**For a Business:**
- Contact Information (phone, email, address, website, hours)
- Business Details (description, founded, ownership, employees, registration/license numbers)
- Social Media Profiles (Facebook, LinkedIn, Instagram, Twitter/X)
- Reviews & Ratings (Yelp, Google, BBB, Trustpilot — avg rating + notable reviews)
- News & Mentions (headlines, dates, sources)
- Public Records (licenses, filings, legal actions)

Deduplicate any repeated data. Note confidence level (confirmed vs. possible) where relevant.

---

### Step 5 — Generate Output Files

Save both files to the **current working directory** using the Write tool.

#### Markdown Report: `[slug]-research-[YYYY-MM-DD].md`

```
# Research Report: [Name]
**Location:** [Location]
**Type:** Person | Business
**Generated:** [Date]

---

## Summary
[2–4 sentence overview of the most important findings]

## Contact Information
- **Phone:** ...
- **Email:** ...
- **Address:** ...
- **Website:** ... (business only)
- **Hours:** ... (business only)

## Social Media Profiles
- **LinkedIn:** [url]
- **Facebook:** [url]
- **Twitter/X:** [url]
- **Instagram:** [url]

## Business Details *(business only)*
- **Description:** ...
- **Owner / Key People:** ...
- **Founded:** ...
- **Registration / License:** ...
- **Employees:** ...

## Reviews & Ratings *(business only)*
- **Yelp:** [X/5 stars — N reviews] — [notable quote if available]
- **Google:** [X/5 stars — N reviews]
- **BBB:** [rating] — [accreditation status]
- **Trustpilot:** [X/5 stars]

## News & Mentions
- [Date] — [Headline] — [Source URL]

## Public Records
- [Record type] — [Details] — [Source]

## Sources
- [URL] — [Description]
```

#### JSON File: `[slug]-research-[YYYY-MM-DD].json`

```json
{
  "subject": "[Name]",
  "type": "person|business",
  "location": "[Location]",
  "generated": "[ISO date]",
  "contact": {
    "phone": [],
    "email": [],
    "address": [],
    "website": "",
    "hours": ""
  },
  "social_media": {
    "linkedin": "",
    "facebook": "",
    "twitter": "",
    "instagram": ""
  },
  "business_details": {
    "description": "",
    "owner": "",
    "founded": "",
    "registration": "",
    "employees": ""
  },
  "reviews": {
    "yelp": {"rating": null, "count": null, "url": ""},
    "google": {"rating": null, "count": null},
    "bbb": {"rating": "", "accredited": null, "url": ""},
    "trustpilot": {"rating": null, "url": ""}
  },
  "news_mentions": [
    {"date": "", "headline": "", "source": "", "url": ""}
  ],
  "public_records": [
    {"type": "", "details": "", "source": ""}
  ],
  "sources": [
    {"url": "", "description": ""}
  ]
}
```

Omit or null-out any fields where no data was found.

---

### Step 6 — Report to User

After saving both files, tell the user:
1. The filenames and where they were saved
2. A brief summary of what was found (top 5–10 findings)
3. Any categories where no data was found
4. Any limitations (e.g., paywalled pages, login-required profiles)

---

## Notes

- **Respect robots.txt and terms of service** — only fetch publicly accessible pages. Do not attempt to log in to any service.
- **Do not fabricate data.** If something was not found, say so — do not invent phone numbers, addresses, or records.
- **Flag uncertainty** — if a result might refer to a different person/business with the same name, note that explicitly.
- **Privacy:** This skill is intended for legitimate general research only. Handle findings responsibly.
