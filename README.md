# Reclamo — Wage Theft Case Intelligence Platform

**Spring 2025 · Project Handoff**
**Authors:** Meghan Kret EE'26 · Lamiah Khan EE'26 · Yufei Lin Art'26

---
## Powerpoint & Video Demo
🔗  https://www.figma.com/slides/PY0H8Qujloc2kHFLdHhYeC

## Live Prototype

🔗 [khanlamiah019.github.io/wage_theft_reclamo/](https://khanlamiah019.github.io/wage_theft_reclamo/)

Browser-based dashboard aggregating wage theft cases from New York State courts. No login required. Built as a single HTML/CSS/JS file — no server needed.

| Stat | Value |
|------|-------|
| Total cases | 3,059 |
| Date range | 2005 – 2026 |
| Top county | Kings County (1,961 cases) |
| Top industry | Grocery (754 cases) |

---

## Data Sources

### 1. WebCivil Local

- **URL:** [iapps.courts.state.ny.us/webcivilLocal/LCSearch?param=P](https://iapps.courts.state.ny.us/webcivilLocal/LCSearch?param=P)
- **Operated by:** New York State Unified Court System
- **Access:** Free, public, no account required

**What it covers:**
Past, present, and future case information for all New York State local civil courts — 61 City Courts, the District Courts in Nassau and Suffolk Counties, and the New York City Civil Courts.

**Case types:**
- General civil cases (landlord-tenant, small claims, consumer debt)
- Local city and district court civil matters
- Active and disposed (decided) cases

> **Note:** Landlord-tenant cases are removed within 2 weeks of disposition.

**How to search:** Index Number · Party Name · Attorney / Firm Name · Judge · Calendar view by attorney/firm, judge, or court part

**Coverage:** All 62 NY counties

---

### 2. NYSCEF — New York State Courts Electronic Filing

- **URL:** [iapps.courts.state.ny.us/nyscef/CaseSearch](https://iapps.courts.state.ny.us/nyscef/CaseSearch)
- **Operated by:** New York State Unified Court System
- **Access:** Free to search as guest; account required to file

**What it covers:**
Electronic filing system and public document repository for civil cases filed in New York Supreme Court and select other courts. Provides access to actual case documents (PDFs), not just docket information.

**Case types:**
- Commercial cases
- Tort cases
- Tax certiorari cases
- Court of Claims (against the State of NY)
- Some Family Court and Surrogate's Court matters

> **Note:** Matrimonial, Mental Hygiene Law, election law, and Article 70 cases are confidential and not publicly viewable.

**Courts covered:** Supreme Court in Albany, Bronx, Erie, Essex, Kings, Monroe, Nassau, New York, Niagara, Onondaga, Queens, Richmond, Suffolk, Sullivan, and Westchester Counties (and expanding).

**Coverage dates:** 2003 – present

**How to search:** Index / claim number · Party name · Attorney name

---

### 3. PACER — Public Access to Court Electronic Records

- **URL:** [pacer.uscourts.gov](https://pacer.uscourts.gov)
- **Operated by:** Administrative Office of the U.S. Courts
- **Access:** Free account required; $0.10/page (waived under $30/quarter — ~75% of users pay nothing)

**What it covers:**
Federal court records across the entire United States. The primary system for accessing federal case filings and dockets.

**Case types:**
- Appellate court cases (U.S. Courts of Appeals)
- District court cases — civil and criminal
- Bankruptcy cases
- Court opinions from appellate, district, and bankruptcy courts

**Notable limitations:**
- Documents from pre-2003 bankruptcy cases not available
- Criminal case documents filed before Nov. 1, 2004, restricted to parties only
- Social Security and immigration case documents restricted
- Personal identifiers are redacted (SSNs, account numbers, minor names, DOBs, home addresses in criminal cases)

**How to search:**
- [PACER Case Locator](https://pcl.uscourts.gov) — nationwide index, all courts, updated nightly
- Court-specific search — log in to a court's CM/ECF system for real-time results
- Search by party name, case number, or attorney name
- Saved Cases and Saved Searches feature available

---

### 4. CourtListener

- **URL:** [courtlistener.com](https://courtlistener.com)
- **Operated by:** Free Law Project (nonprofit)
- **Access:** Completely free; no account for search, account needed for alerts

**What it covers:**
One of the most comprehensive free legal databases available. Collects case law from court websites, data donations, and partnerships. Also houses the RECAP Archive — the largest open collection of federal PACER documents.

**Content available:**
- U.S. Supreme Court opinions
- Federal Appellate Court opinions (all circuits)
- Federal District and Bankruptcy Court opinions
- State Supreme and Appellate Court opinions
- 10+ million legal opinions across 471 jurisdictions
- ~16.7 million PACER documents (via RECAP Archive)
- Oral argument audio recordings (federal appellate courts)
- Federal judge financial disclosures and profiles

**How to search:** Keyword search with advanced operators · Semantic / plain-language search via the CiteGeist relevancy engine · Filter by jurisdiction, court, date range, or judge · Citation search · Sort by relevance, date filed, or citation count · Email alerts for new matching opinions

> **Note for this project:** CourtListener provides a free REST API and bulk data downloads — useful for programmatically pulling wage theft opinions or building automated monitoring.

---

### 5. NY DOS Public Inquiry — Business Entity Search

- **URL:** [apps.dos.ny.gov/publicInquiry/#search](https://apps.dos.ny.gov/publicInquiry/#search)
- **Operated by:** NYS Department of State, Division of Corporations
- **Access:** Free, public, no account required

**What it covers:**
Registry of all business entities formally registered with New York State. Used to verify whether a defendant employer legally exists and to find their registered address and status.

**Entity types included:**
- Business corporations (C-corp, S-corp)
- Not-for-profit corporations
- LLCs, LPs, LLPs
- Assumed name (DBA) filings
- Professional LLCs (PLLCs) and Benefit Corporations

**Not included:** Sole proprietorships and general partnerships (filed with county clerk, not state)

**Information available per entity:**
- Legal entity name and DOS ID number
- Entity type and current status (active, inactive, dissolved)
- Date of first filing and county of formation
- Registered agent and service of process address
- Filing history and amendments

**How to search:** By entity name (exact, begins with, contains, or partial word) · By DOS ID number · Filter by entity type

**Use in this project:** Confirm a defendant employer's legal name, check active/inactive status, and find their registered address for verifying case data and cross-referencing court records.

---

## How the Sources Fit Together

| Source | Best used for |
|--------|---------------|
| WebCivil Local | Finding NY local civil court wage theft cases by party name |
| NYSCEF | Accessing actual court documents for Supreme Court cases |
| PACER | Federal wage and hour cases (FLSA violations, etc.) |
| CourtListener | Researching legal precedent and appellate decisions on wage theft |
| NY DOS Public Inquiry | Verifying defendant employer identity and registration status |

---

## Adding New Data to the Tool

All case data is stored in a JavaScript array inside the HTML file. To add new cases:

1. Open the main HTML file in any text editor.
2. Find the `const cases = [...]` array near the top.
3. Append new case objects using this schema:

```js
{
  title:     "Case title / plaintiff v. defendant",
  defendant: "Employer name",
  industry:  "Grocery",      // matches existing industry categories
  county:    "Kings County",
  filed:     "2024",         // year as string
  status:    "Active",       // "Active" or "Disposed"
  link:      "https://..."   // link to court record
}
```

4. Save the file and re-upload or re-host.
