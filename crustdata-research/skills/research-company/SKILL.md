---
name: research-company
description: Research a company using Crustdata. Identifies the company, pulls full firmographics (headcount, funding, industry, HQ), and lists key decision-makers. Trigger when the user gives a company name, domain, or LinkedIn URL and asks to "research" / "look up" / "profile" a company.
---

# Research a Company

Goal: produce a concise research brief on a single company.

## Inputs

The user will supply one of:
- Company name (e.g., "Stripe")
- Company website (e.g., `stripe.com`)
- Company LinkedIn URL

## Steps

1. **Identify the company.** Call `crustdata_company_identify` with whatever the user provided. This is FREE — always start here. Confirm the match before moving on (name + domain).

2. **Enrich firmographics.** Call `crustdata_company_enrich` with the resolved identifier. Request all fields.

3. **Find decision-makers.** Call `crustdata_people_search_db` filtered on:
   - `current_employers.company_id` = the resolved company id
   - `current_employers.seniority_level` in `["CXO", "Vice President", "Director"]`
   - Limit to 25 results.

4. **Summarize.** Produce a brief in this exact structure:

   ```
   ## <Company Name> — <One-line description>

   **Firmographics**
   - HQ, founded, headcount, headcount growth (last 12 mo if available)
   - Industry, company type, website
   - Latest funding round (amount, date, lead investor) if available

   **What they do**
   - 2–3 sentences from the description, in plain language

   **Decision-makers (top 10)**
   - Name — Title — LinkedIn URL
   - Group by function (Eng, Sales, Product, Ops, etc.)

   **Recent signals**
   - Hiring velocity (if available from headcount trend)
   - Any obvious news or activity flag from enrich response
   ```

## Notes

- Always start with `crustdata_company_identify` — it's free and avoids wasting credits on the wrong company.
- If `crustdata_company_identify` returns multiple plausible matches, ask the user which one before enriching.
- Don't list every employee — cap decision-maker list at 10 across functions.
- If decision-maker search returns 0, mention it explicitly rather than silently dropping the section.
