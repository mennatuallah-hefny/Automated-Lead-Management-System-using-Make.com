# Automated Lead Management (Typeform → Make.com → Pipedrive)

<div>
    <a href="https://www.loom.com/share/d2b87527fd99432c923a878309778fb8">
      <p>Automated Lead Management System - Watch Video</p>
    </a>
    <a href="https://www.loom.com/share/d2b87527fd99432c923a878309778fb8">
      <img style="max-width:300px;" src="https://cdn.loom.com/sessions/thumbnails/d2b87527fd99432c923a878309778fb8-afc7e458f55f8bd7-full-play.gif">
    </a>
  </div>

## Table of Contents
1. [Overview](#overview)  
2. [Architecture](#architecture)  
3. [Prerequisites](#prerequisites)  
4. [Setup & Deployment](#-setup--deployment) 
5. [Workflow Steps](#workflow-steps)  
6. [Testing](#testing)  
7. [Example Runs](#example-runs)  

---

## Overview
Automate capture of Typeform submissions into Pipedrive, with:
- Duplicate detection for **Contacts** & **Organizations**  
- Conditional creation of missing records  
- Final lead creation linking person + org  

_No manual data entry. No duplicates._

---

## Architecture
```text
[Typeform] ──▶ Make.com Webhook
    ├─ Search Person by email
    ├─ Search Org by name
    ├─ ▶ Router:
    │    ├─ New Org? ──▶ Create Org ──▶ Set orgID
    │    ├─ New Person? ──▶ Create Person (uses orgID) ──▶ Set personID
    │    └─ ▶ Create Lead (uses personID + orgID)
```

---

## Prerequisites
- Make.com account  
- Typeform form with fields: `first_name`, `last_name`, `email`, `phone`, `company name`  
- Pipedrive account + API token  

---

## Setup & Deployment

### Clone Repo
```bash
git clone https://github.com/your-username/automated-lead-management.git
cd automated-lead-management
```

### Import Make.com Scenario
1. In Make.com go to **Scenarios** → **Create a new scenario** → **Import**  
2. Upload `Automated Lead Management.json`
3. Configure Connections: 
   - Typeform → [Connect Typeform to Make](https://apps.make.com/typeform)
   - Pipedrive CRM → [Connect Pipedrive CRM to Make](https://apps.make.com/pipedrive)

---

## Workflow Steps

1. **Trigger**  
   - Typeform fires on submission.  
2. **Search for Duplicates**  
   - Module A: Search Person by `email` (exact match).  
   - Module B: Search Organization by `company` (exact match).  
3. **Router Logic**  
   - **Route 1**: `orgID` empty → Create Org → Set `orgID`.  
   - **Route 2**: `personID` empty → Create Person (map `orgID`) → Set `personID`.  
   - **Route 3**: Both IDs present → Create Lead (map `personID`, `orgID`).  
4. **Lead Creation**  
   - Final module: Create Lead in Pipedrive.  

---

## Testing

1. Submit test entries in Typeform (e.g. via Make’s “Run once” + “Use existing data”).  
2. Verify in Pipedrive UI under **Contacts**, **Organizations**, **Leads**.  
3. Edge cases:  
   - Same email, new company  
   - Same company, new email  
   - Existing both → only lead created  

---

## Example Runs

| Test Case                       | Result                                   |
|---------------------------------|------------------------------------------|
| New “Foo Co” + new “Alice”      | Org + Person + Lead                     |
| Existing “Foo Co” + new “Bob”   | Person + Lead                            |
| Existing “Foo Co” + existing    | Lead only                                |
| Existing “Alice” switches org   | New Org + Lead (person unchanged)        |

---

