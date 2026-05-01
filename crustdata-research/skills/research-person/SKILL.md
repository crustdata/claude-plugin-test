---
name: research-person
description: Research a LinkedIn profile using Crustdata. Pulls a full profile (current/past employers, education, skills) plus recent LinkedIn posts, then summarizes the person for outbound, recruiting, or background research. Trigger when the user gives a LinkedIn profile URL or asks to "research" / "look up" a person.
---

# Research a Person

(v0.2-test — if you see this line in the loaded skill, auto-update from GitHub worked.)

Goal: produce a concise research brief on a single person from a LinkedIn URL.

## Inputs

The user will supply one of:
- A LinkedIn profile URL (e.g., `https://www.linkedin.com/in/jeffweiner08/`)
- A name + company (fallback — search first to find the URL)

If only a name is given, ask the user for the LinkedIn URL before proceeding.

## Steps

1. **Enrich the profile.** Call `crustdata_people_enrich` with the LinkedIn URL. Request all fields by default (work history, education, skills, contact info if available).

2. **Pull recent posts.** Call `crustdata_social_posts` with the same LinkedIn URL. Limit to the 10 most recent posts.

3. **Summarize.** Produce a brief in this exact structure:

   ```
   ## <Name> — <Current Title> @ <Current Company>

   **Snapshot**
   - Location, years of experience, connections
   - One-line summary of headline / what they do today

   **Career trajectory**
   - Most recent 3–4 roles with dates (newest first), highlighting promotions and company-size jumps
   - Note any industry/function pivots

   **Signals**
   - Recent post themes (last 90 days) — what they care about, what they share
   - Any "open to" cards (open to work, hiring, etc.)
   - Recent job change (if `recently_changed_jobs` is true)

   **Outreach hooks**
   - 2–3 concrete, non-generic angles to reference in a first message
   - Each hook cites the specific data point it came from
   ```

4. **Surface contact info if present.** Personal email, business email, Twitter — list at the bottom under `**Contact**`.

## Notes

- Do not invent facts. If a field is missing, omit it rather than guessing.
- Career trajectory should rank by `start_date` descending across both `current_employers` and `past_employers` (current roles get a "(current)" marker, but date order is what determines the list).
- Outreach hooks should reference *specific* posts, projects, or career moves — not "I see you work in X."
- If the enrichment returns no data (private profile, bad URL), report that plainly and stop.
