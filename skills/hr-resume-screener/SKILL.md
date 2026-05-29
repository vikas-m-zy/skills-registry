---
name: hr-resume-screener
description: Screen a candidate resume against a Job Description and return a FIT / PARTIAL FIT / NOT A FIT verdict with requirement match, strengths, gaps, salary check, and an Excel-ready summary row. Use when the user provides both a JD and a resume and asks whether the candidate fits — e.g. "screen this resume against the JD", "is this candidate a fit for this role?", or "check this profile against the job description".
metadata:
  version: 1.0.0
  author: Deepak Padmanabha
  email: deepak@zysk.tech
  category: business-sales
  tags: [hr, resume, screening, jd-matching, recruitment]
  product: zysk
  sprint: 1
  tested_with: claude-sonnet-4-6
---

# HR Resume Screener

> Screen a resume against a JD and instantly determine FIT, PARTIAL FIT, or NOT A FIT — with a structured breakdown ready to paste into Excel.

## When to use

- Activate when: the user uploads or shares both a Job Description and a candidate resume together.
- Activate when: the user asks "is this candidate fit for this role?", "check this resume against the JD", "screen this profile", or any variation of matching one resume to one job description.
- Do NOT activate when: only a resume or only a JD is provided — ask for the missing document first before proceeding.

## Prerequisites

- [ ] Job Description (JD) — PDF, Word file, or pasted text
- [ ] Candidate Resume / CV — PDF, Word file, or pasted text

## Steps

### Step 0: Collect Both Inputs

You need exactly two things before proceeding: the Job Description and the candidate's resume. Both can be provided as a file upload (PDF or Word .docx) or text pasted directly in chat.

If the JD is missing, ask:
> "Please share the Job Description — you can upload the file (PDF or Word) or paste the text."

If the resume is missing, ask:
> "Please share the candidate's resume — you can upload the file or paste the text."

If both are present, proceed immediately. Do not ask any further questions.

### Step 1: Extract from the JD

Read the JD and identify:

```
Role Title:
Department / Team:
Experience Required: (years and level)
Must-Have Requirements: (list every hard requirement explicitly stated)
Good-to-Have Requirements: (preferred but optional)
Key Responsibilities: (top 3–5 in your own words)
Industry / Domain: (if specified)
Location / Work Mode: (city, on-site / hybrid / remote)
Budget / CTC Band: (if mentioned)
```

### Step 2: Extract from the Resume

Read the resume and identify:

```
Candidate Name:
Current Role / Title:
Total Experience: (years)
Current Location:
Notice Period / Availability:
Education:
Core Skills:
Industry Background:
Key Achievements: (top 2–3 if quantified)
Current CTC: (if mentioned)
Expected CTC: (if mentioned)
```

### Step 3: Match Every Must-Have Requirement

For each Must-Have requirement from the JD, mark:

- ✅ **MET** — clearly evidenced in the resume
- ⚠️ **PARTIAL** — some evidence but incomplete or not current
- ❌ **NOT MET** — no evidence in the resume

**Verdict Logic:** evaluate top to bottom and stop at the first row that matches.

| Situation | Verdict |
|---|---|
| Any single critical Must-Have is ❌ (e.g. mandatory cert, minimum years) | **NOT A FIT** regardless of other scores |
| 2 or more Must-Haves are ❌ | **NOT A FIT** |
| Every Must-Have is ✅ | **FIT** |
| All Must-Haves ✅ except 1–2 that are ⚠️, **or** exactly 1 non-critical Must-Have is ❌ | **PARTIAL FIT** |

**Match Score (0–100)** — this fills the `Score` column in the Excel row:

1. Score each Must-Have: ✅ MET = 1, ⚠️ PARTIAL = 0.5, ❌ NOT MET = 0.
2. `Score = round( (sum of points / number of Must-Haves) × 100 )`.
3. If any **critical** Must-Have is ❌, cap the Score at 40 regardless of the formula.

*Worked example:* 5 Must-Haves, 3 MET + 1 PARTIAL + 1 NOT MET, none critical →
`(3 + 0.5 + 0) / 5 × 100 = 70`. If that NOT MET were a critical requirement, the
Score would be capped at 40.

### Step 4: Salary Band Check

| Situation | Salary Fit Label |
|---|---|
| Expected CTC at or below JD budget | 💰 FIT |
| Expected CTC >0% to ≤15% above budget | ⚠️ STRETCH |
| Expected CTC >15% to ≤30% above budget | ⚠️ BUDGET RISK |
| Expected CTC >30% above budget | ❌ MISMATCH |
| CTC not mentioned in either | UNKNOWN — verify in screening call |

### Step 5: Produce the Structured Report

Use this exact structure. Keep it verdict-first and readable in under 90 seconds.

---

## 📋 Resume Screening Report

**Candidate:** [Name]  
**Role Applied:** [Role from JD]  
**Screened by:** Zysk HR Screening Assistant  
**Date:** [today's date]

---

### [🟢 FIT / 🟡 PARTIAL FIT / 🔴 NOT A FIT]

> *[One sentence explaining the verdict — e.g. "Candidate meets all core requirements and has directly relevant industry experience." or "Strong on skills but does not meet the minimum experience requirement."]*

---

### 📊 Profile Snapshot

| Field | Details |
|---|---|
| Current Role | |
| Total Experience | |
| Education | |
| Location / Notice | |
| Current CTC | |
| Expected CTC | |
| Salary Fit | |

---

### ✅ JD Requirements Match

| Requirement (from JD) | Status | Evidence from Resume |
|---|---|---|
| [Requirement 1] | ✅ MET | [What the resume shows] |
| [Requirement 2] | ⚠️ PARTIAL | [What is there vs. what is missing] |
| [Requirement 3] | ❌ NOT MET | [Completely absent or below bar] |

*(Extract 4–6 key requirements from the JD)*

---

### 💪 Strengths

- [Strength 1]
- [Strength 2]
- [Strength 3]

### 🚩 Gaps

- [Gap 1 — specific, not vague]
- [Gap 2]

*(Write "None identified" if there are no gaps)*

---

### 📞 Recommendation

> [One clear sentence — exactly what HR should do next. E.g. "Schedule technical round immediately." or "Arrange a 15-minute screening call to clarify the S/4HANA experience before advancing." or "Reject — does not meet minimum experience and module requirements."]

---

### 📤 Excel Row Summary *(copy-paste ready)*

| Candidate | Role | Experience | Verdict | Score | Salary Fit | Strengths | Gaps | Next Step |
|---|---|---|---|---|---|---|---|---|
| [Name] | [Role] | [X yrs] | [FIT/PARTIAL/NOT A FIT] | [0-100] | [FIT/STRETCH/MISMATCH/UNKNOWN] | [top strength] | [top gap] | [action] |

## Output

- **Format:** structured report in chat, delivered after both inputs are collected
- **Location:** the conversation
- **Example:** verdict header (🟢 FIT), profile snapshot table, JD requirements match table with ✅/⚠️/❌ statuses, strengths and gaps bullets, one-line recommendation, and a copy-paste Excel row

## Example

**User says:** *"Screen this resume against the JD."* (pastes both)

> **JD snippet:** SAP FICO Consultant, 5+ yrs, S/4HANA mandatory, Bangalore hybrid, budget ₹18 LPA.
> **Resume snippet:** Priya R., SAP FICO Consultant, 6 yrs, ECC + 1 yr S/4HANA, Bangalore, expected ₹19 LPA.

**Claude produces:**

### 🟢 FIT

> *Meets the experience and S/4HANA requirements with directly relevant FICO background; salary is a minor stretch.*

### 📊 Profile Snapshot

| Field | Details |
|---|---|
| Current Role | SAP FICO Consultant |
| Total Experience | 6 yrs |
| Location / Notice | Bangalore / — |
| Expected CTC | ₹19 LPA |
| Salary Fit | ⚠️ STRETCH (≈6% above budget) |

### ✅ JD Requirements Match

| Requirement (from JD) | Status | Evidence from Resume |
|---|---|---|
| 5+ years SAP FICO | ✅ MET | 6 yrs as FICO Consultant |
| S/4HANA experience | ✅ MET | 1 yr S/4HANA (rest on ECC) |
| Bangalore hybrid | ✅ MET | Based in Bangalore |

### 💪 Strengths
- Exceeds the minimum experience bar
- Hands-on S/4HANA in addition to ECC

### 🚩 Gaps
- Only 1 year of S/4HANA — confirm depth in screening

### 📞 Recommendation
> Schedule a technical round; confirm S/4HANA depth and the ₹1 LPA salary gap.

### 📤 Excel Row Summary

| Candidate | Role | Experience | Verdict | Score | Salary Fit | Strengths | Gaps | Next Step |
|---|---|---|---|---|---|---|---|---|
| Priya R. | SAP FICO Consultant | 6 yrs | FIT | 100 | STRETCH | S/4HANA + ECC | 1 yr S/4HANA only | Technical round |

## Screening principles & edge cases

- Works for any role — tech, non-tech, ERP, sales, operations, finance, HR, and more
- **Verdict first** — HR sees the decision before anything else
- **Never invent** — if something is not in the resume, mark it NOT MET, do not infer
- **Be direct** — "Does not meet the 5-year requirement" not "may have slightly less experience"
- **Only judge against the JD** — don't penalise for skills the JD never asked for

**Edge cases:**

| Situation | How to Handle |
|---|---|
| JD is vague / missing some details | Extract what you can; note which requirements are inferred |
| Resume is very thin | Flag: "Sparse profile — low match confidence. Recommend a brief call to verify basics." |
| Candidate appears overqualified | Flag: "Candidate may be overqualified — verify role scope and salary expectations." |
| No salary in JD or resume | Label salary fit as UNKNOWN; recommend verifying in first call |
| Resume is not in English | Translate key fields, note the original language, proceed with the match |
