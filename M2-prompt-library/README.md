
# 📋 Course Deliverable: M2-Prompt-Library
### **AI Administrator: Agentic Workflows & Automation · Day 1 · Session 2**

[![IEEE Computer Society](https://img.shields.io/badge/IEEE_Computer_Society-computer.org-FFA500?style=for-the-badge&logo=ieee&logoColor=white)](https://www.computer.org/)
[![AI Caravan](https://img.shields.io/badge/AI_Caravan-aicaravan.org-E11D48?style=for-the-badge)](https://aicaravan.org)
[![Google AI Studio](https://img.shields.io/badge/Google_AI_Studio-Verified_Artifact-4285F4?style=for-the-badge&logo=google&logoColor=white)](https://aistudio.google.com/)

<p align="center">
  <b>Program Chair:</b> Prof. Mousa AL-Akhras (Chair, IEEE Jordan Section · University of Jordan)<br>
  <b>Curriculum Author & Lead Instructor:</b> Dr. Abedal-Kareem Al-Banna (University of Petra)<br>
  <b>Instructor:</b> Robina Mirbahar (Google Developer Expert in Machine Learning)<br>
  <b>Lead Instructor:</b> Mohammed Abdelmajeed (AI & Automation Specialist · IEEE CS Region 8)<br>
  <b>Artifact ID:</b> <code>M2-prompt-library-v2.0</code> · <b>Status:</b> Production Ready & Peer-Reviewed
</p>

---

## 📑 Table of Contents
1. [Executive Summary & The Operator Standard](#1-executive-summary--the-operator-standard)
2. [Architectural Foundations & Mental Models](#2-architectural-foundations--mental-models)
   - [2.1 Context Window & Stateless Inference](#21-context-window--stateless-inference)
   - [2.2 The Anatomy of a Prompt: What Goes Wrong Without Each Part](#22-the-anatomy-of-a-prompt-what-goes-wrong-without-each-part)
   - [2.3 The Most Valuable Sentence in Operational Prompting](#23-the-most-valuable-sentence-in-operational-prompting)
3. [The 5 Reusable Prompt Templates (With Worked Examples)](#3-the-5-reusable-prompt-templates-with-worked-examples)
   - [Template 1: Triage an Email](#template-1-triage-an-email)
   - [Template 2: Summarise a Meeting Transcript](#template-2-summarise-a-meeting-transcript)
   - [Template 3: Draft an Acknowledgment Reply](#template-3-draft-an-acknowledgment-reply)
   - [Template 4: Extract Fields from a Form](#template-4-extract-fields-from-a-form)
   - [Template 5: Classify a Request (Few-Shot Classifier)](#template-5-classify-a-request-few-shot-classifier)
4. [10-Row Evaluation Test Bench (Before vs. After Iteration)](#4-10-row-evaluation-test-bench)
5. [Anti-Hallucination & Grounded Department Assistant](#5-anti-hallucination--grounded-department-assistant)
   - [5.1 The Three Anti-Hallucination Habits](#51-the-three-anti-hallucination-habits)
   - [5.2 Complete Source Policy Texts](#52-complete-source-policy-texts)
   - [5.3 Assistant System Instructions](#53-assistant-system-instructions)
6. [The 5-Question Trap Test Results Table](#6-the-5-question-trap-test-results-table)
7. [Operational Troubleshooting: Symptom, Likely Cause, Fix](#7-operational-troubleshooting-symptom-likely-cause-fix)
8. [20-Question Kahoot Knowledge Check & Study Guide](#8-20-question-kahoot-knowledge-check--study-guide)
9. [Deliverable Verification & Screenshots](#9-deliverable-verification--screenshots)

---

## 1. Executive Summary & The Operator Standard
*(Slide Reference: Slides 4–6)*

In everyday desktop usage, users engage in conversational back-and-forth prompts to iteratively reach a desired response. In enterprise systems administration, however, prompts are embedded inside background automation pipelines (e.g., n8n, webhooks, microservices) that execute hundreds or thousands of times daily without human monitoring.

**The Golden Standard:** **Repeatability.**  
Every automated execution must return the exact same schema, field labels, and categorical values, regardless of input length or formatting anomalies. This document establishes the operational prompt library meeting that standard.

---

## 2. Architectural Foundations & Mental Models

### 2.1 Context Window & Stateless Inference
*(Slide Reference: Slide 7)*

Large Language Models do not possess persistent episodic memory. Each API call is completely stateless. 

```text
Prompt Package Sent to LLM:
┌─────────────────────────────────────────────────────────────┐
│ 1. System Instructions (Role, Policy, Persona)               │
│ 2. Grounded Documents (HR Policies, Manuals)                │
│ 3. Conversation History (Previous User + Assistant turns)   │
│ 4. Current Input Query                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
            ┌───────────────────────────────────┐
            │       Large Language Model        │
            │  (Max Context: e.g., 8,192 tokens)│
            └───────────────────────────────────┘
```

> **Operator Takeaway:** What is not in the context window right now does not exist in the model's universe. If an operational rule, acronym, or boundary condition matters, it must be explicitly present on the model's "desk."

---

### 2.2 The Anatomy of a Prompt: What Goes Wrong Without Each Part
*(Slide Reference: Slide 12)*

| Part | Operational Function | Failure Mode When Omitted |
|:---|:---|:---|
| **Role** | Sets domain vocabulary, professional register, and assumed background knowledge. | Generic, long-winded, or juvenile answers. |
| **Context** | Injects proprietary definitions, thresholds, and operational facts the model cannot guess. | Model hallucinates internal organizational policies. |
| **Task** | Declares one specific verb and one explicit deliverable. | Model tries to perform multiple subtasks and does them all poorly. |
| **Constraints** | Sets character/word limits, tone restrictions, forbidden actions, and uncertainty handling. | Invented facts, conversational fluff, and inappropriate emotional register. |
| **Output Format** | Defines the exact structural shape (labeled lines, JSON, table) for machine parsing. | Inconsistent shape on every run; downstream automation parsers crash. |

---

### 2.3 The Most Valuable Sentence in Operational Prompting
*(Slide Reference: Slide 13)*

> *"If the urgency is unclear, say 'unclear' rather than guessing."*

In traditional software, unhandled exceptions cause system crashes. In generative AI, unhandled uncertainty causes **confident hallucinations**. Including an explicit uncertainty fallback converts an untruthful answer into a machine-readable flag that can be safely routed to human operators.

---

## 3. The 5 Reusable Prompt Templates (With Worked Examples)

### Template 1: Triage an Email
* **Target Operation:** Facilities and IT emergency triage.

```text
Role: You are an assistant to the facilities coordinator of a 200-person office.
Context: We receive maintenance requests by email. Urgent means a safety risk or a stopped service (lift, water, power, access).
Task: Read the email below and produce a triage note.
Constraints: Do not invent details that are not in the email. If the urgency is unclear, say "unclear" rather than guessing. Maximum 80 words. Tone: plain.
Output format (exactly these labelled lines):
Requester:
Location:
Issue (one sentence):
Urgency (urgent / normal / unclear):
Suggested next action:

Email: [paste email here]
```

#### Worked Example:
* **Raw Input:**
  > *"Hi team, the water pipe under the 2nd-floor kitchenette sink burst ten minutes ago. Water is spreading toward the server room door and electrical floor boxes. Please help immediately! — Mark Daniels, Network Ops"*
* **Model Output:**
  ```text
  Requester: Mark Daniels (Network Ops)
  Location: 2nd-floor kitchenette / near server room
  Issue (one sentence): Burst water pipe leaking toward electrical floor boxes and the server room door.
  Urgency (urgent / normal / unclear): urgent
  Suggested next action: Dispatch emergency facilities maintenance immediately to shut off the water valve and notify building electrical safety.
  ```

---

### Template 2: Summarise a Meeting Transcript
* **Target Operation:** Sprint reviews and cross-functional engineering check-ins.

```text
Role: You are an administrative assistant supporting a project management office.
Context: Project managers require concise action registries immediately following sprint reviews.
Task: Extract actionable tasks and decisions from the transcript below.
Constraints: Do not summarize discussions that did not result in an assigned action or agreed decision. If an owner is not mentioned for a task, write "Unassigned". Maximum 120 words.
Output format (exactly these lines):
Decisions Made:
- [Decision 1]
Action Items:
- [Task description] | Owner: [Name/Unassigned] | Deadline: [Date/Unspecified]

Transcript: [paste transcript here]
```

#### Worked Example:
* **Raw Input:**
  > *"Sarah: We discussed moving the database migration from Thursday to Sunday night to avoid downtime. David agreed. David: Yes, I will schedule the maintenance window notice by tomorrow noon. Sarah: Also, someone needs to update the staging environment configuration. We didn't pick who yet. Let's finish up."*
* **Model Output:**
  ```text
  Decisions Made:
  - Database migration rescheduled from Thursday to Sunday night to minimize user downtime.
  Action Items:
  - Schedule and distribute maintenance window notice | Owner: David | Deadline: Tomorrow by 12:00 PM
  - Update staging environment configuration | Owner: Unassigned | Deadline: Unspecified
  ```

---

### Template 3: Draft an Acknowledgment Reply
* **Target Operation:** Inbound incident communication for IT providers.

```text
Role: You are a customer communications coordinator for an enterprise IT provider.
Context: Clients write to report service interruptions. Our policy is to acknowledge receipt within 15 minutes without accepting legal liability or promising unconfirmed fix times.
Task: Draft a formal acknowledgment reply to the client message below.
Constraints: Do not commit to a specific resolution hour. Direct urgent escalations to the 24/7 hotline at +1-800-555-0199. Maximum 90 words. Tone: professional and calm.
Output format:
Subject: [Drafted Subject Line]
Body: [Drafted Message Body]

Client Message: [paste client message here]
```

#### Worked Example:
* **Model Output:**
  ```text
  Subject: [Ticket #48291] Service Incident Acknowledgment: SSO Portal
  Body: Dear Client Partner,

  We acknowledge receipt of your notice regarding authentication difficulties on the Single Sign-On portal. Our site reliability engineering team has been notified and is currently investigating telemetry logs to determine the cause. 

  We will issue status updates as verified milestones are achieved. If this incident causes critical operational stoppage, please reach our 24/7 priority operations desk at +1-800-555-0199.

  Sincerely,
  Client Operations Desk
  ```

---

### Template 4: Extract Fields from a Form
* **Target Operation:** Parsing unstructured vendor registration text.

```text
Role: You are an enterprise data entry auditor.
Context: Vendors submit unstructured registration requests. We require specific fields to register them in our enterprise ERP.
Task: Extract vendor onboarding data from the text below.
Constraints: Output only the specified labels. If a value is missing, write "MISSING". Do not guess VAT/Tax IDs.
Output format (exactly these labelled lines):
Company Legal Name:
Tax/VAT Number:
Primary Contact Email:
Country of Registration:
Payment Terms Requested:

Vendor Text: [paste raw text here]
```

#### Worked Example:
* **Raw Input:**
  > *"Hello, we are Nordic Cloud Logistics AB, operating out of Stockholm, Sweden. Our corporate tax registration number is SE556123456701. Contact our billing rep at accounts@nordiccloud.se. We normally work on Net-30 payment terms."*
* **Model Output:**
  ```text
  Company Legal Name: Nordic Cloud Logistics AB
  Tax/VAT Number: SE556123456701
  Primary Contact Email: accounts@nordiccloud.se
  Country of Registration: Sweden
  Payment Terms Requested: Net-30
  ```

---

### Template 5: Classify a Request (Few-Shot Classifier)
* **Target Operation:** Automated support ticket categorization.

```text
Role: You are an automated support ticket triage classifier.
Context: Incoming messages must be routed into one of four queues: billing, technical, sales, or other.
Task: Classify each message into exactly one category.
Constraints: Allowed categories only: billing, technical, sales, other. If a message reports an active service crash alongside a payment detail, classify as technical. Answer with the category only.

Example 1: "My invoice shows the old price" -> billing
Example 2: "The portal logs me out every minute" -> technical
Example 3: "Do you offer a plan for schools?" -> sales
Example 4: "Thanks for the quick help yesterday" -> other
Example 5: "Can you send someone to inspect our office garden?" -> other

Now classify: "[message]"
Answer with the category only.
```

---

## 4. 10-Row Evaluation Test Bench
*(Slide Reference: Slides 23, 26, 44, 45)*

To prevent regression during prompt optimization, the classifier was evaluated on a 10-row test bench containing standard inputs and ambiguous edge cases.

### Test Bench Scorecard

| # | Test Input Message | True Label | Baseline Run | Fixed Run | Status |
|:---:|:---|:---:|:---:|:---:|:---:|
| **1** | "I was charged twice for subscription renewal #8812." | `billing` | `billing` | `billing` | Pass |
| **2** | "API endpoint `/v1/auth` returns HTTP 500 Internal Error." | `technical` | `technical` | `technical` | Pass |
| **3** | "We need a quote for an enterprise license with 250 seats." | `sales` | `sales` | `sales` | Pass |
| **4** | "Where can I find the PDF of your ISO 27001 certification?" | `other` | `other` | `other` | Pass |
| **5** | "Database replication latency spiked above 12 seconds." | `technical` | `technical` | `technical` | Pass |
| **6** | "Can we pay via wire transfer instead of credit card?" | `billing` | `billing` | `billing` | Pass |
| **7** | "Is your sales representative available for a demo call Thursday?" | `sales` | `sales` | `sales` | Pass |
| **8** | "Happy New Year to your entire support team!" | `other` | `other` | `other` | Pass |
| **9** | *"Our system crashed right after I paid the premium bill."* | `technical` | `billing` ❌ | `technical` ✅ | Pass |
| **10** | *"Can I buy lunch for your developer team?"* | `other` | `other` | `other` | Pass |

* **Initial Score:** 9 / 10 (90%)
* **Root Cause for Row 9 Failure:** The model prioritized the keyword *"paid"* over the operational fault *"crashed"*.
* **Specific Refinement:** Inserted disambiguation heuristic into Constraints: *"If a message reports an active service crash alongside a payment detail, classify as technical."*
* **Post-Iteration Score:** **10 / 10 (100%)**

---

## 5. Anti-Hallucination & Grounded Department Assistant

### 5.1 The Three Anti-Hallucination Habits
*(Slide Reference: Slide 38)*

1. **Ground It:** Feed source documents directly into the context window and mandate verbatim quotation of source sentences.
2. **Give It an Exit:** Explicitly instruct the model to state *"Not covered"* if the requested information is absent. Models told they may refuse will refuse rather than fabricate.
3. **Check the Checkable:** Always audit deterministic factual anchors (dates, currency amounts, policy clause IDs, error codes).

---

### 5.2 Complete Source Policy Texts

#### Document 1: `HR_Leave_Policy.txt`
```text
ACME CORP INTERNAL HR LEAVE POLICY (VERSION 2026.1)
1. Annual Leave Entitlement: Full-time employees are entitled to 25 working days of paid annual leave per calendar year.
2. Carry-Over Rules: A maximum of 5 unused leave days may be carried over into the following calendar year. Any carried-over days must be used before March 31st, or they are permanently forfeited.
3. Sick Leave: Employees may self-certify illness for up to 3 consecutive calendar days. From day 4 onward, a verified medical doctor's note is required.
```

#### Document 2: `Travel_Expense_Policy.txt`
```text
ACME CORP BUSINESS TRAVEL & ALLOWANCE REGULATIONS
1. Daily Per Diem Allowances:
   - European Union Destinations: 65 EUR per day.
   - Jordan & Middle East Region: 55 JOD per day.
   - North America (US/Canada): 80 USD per day.
2. Hotel Accommodation: Standard rooms must not exceed 140 EUR/night for tier-2 cities or 190 EUR/night for capital cities.
```

---

### 5.3 Assistant System Instructions
*(Slide Reference: Slide 48)*

```text
You are the Operations policy assistant. Answer questions only from the uploaded documents.
For every answer, quote the sentence you relied on and name the document.
If the answer is not in the documents, reply: "Not covered by the uploaded policies. Please ask Sarah Jenkins in HR Operations."
Keep answers under 120 words.
```

---

## 6. The 5-Question Trap Test Results Table
*(Slide Reference: Slides 45–46)*

| Test | Question Prompt | Document Status | Observed Assistant Output | Audit Verdict |
|:---:|:---|:---:|:---|:---:|
| **Q1** | *"How many days of annual leave do I get, and how many can I carry over?"* | Covered | *"You are entitled to 25 working days of paid annual leave, and you may carry over up to 5 days. Quoting HR_Leave_Policy.txt, Sections 1 & 2: 'Full-time employees are entitled to 25 working days of paid annual leave per calendar year' and 'A maximum of 5 unused leave days may be carried over into the following calendar year.'"* | ✅ Verified Grounded |
| **Q2** | *"When do carried-over leave days expire?"* | Covered | *"Carried-over days must be used before March 31st. Quoting HR_Leave_Policy.txt, Section 2: 'Any carried-over days must be used before March 31st, or they are permanently forfeited.'"* | ✅ Verified Grounded |
| **Q3** | *"What is my daily allowance if I travel to Jordan for a client meeting?"* | Covered | *"Your daily per diem allowance for Jordan is 55 JOD per day. Quoting Travel_Expense_Policy.txt, Section 1: 'Jordan & Middle East Region: 55 JOD per day.'"* | ✅ Verified Grounded |
| **Q4 (Trap)** | *"What is the company policy on parental and maternity leave duration?"* | **Uncovered** | *"Not covered by the uploaded policies. Please ask Sarah Jenkins in HR Operations."* | ✅ Zero Hallucination (Safe Exit Triggered) |
| **Q5 (Trap)** | *"What is the reimbursement mileage rate if I drive my own car?"* | **Uncovered** | *"Not covered by the uploaded policies. Please ask Sarah Jenkins in HR Operations."* | ✅ Zero Hallucination (Safe Exit Triggered) |

---

## 7. Operational Troubleshooting: Symptom, Likely Cause, Fix
*(Slide Reference: Slide 39)*

| Observable Symptom | Probable Root Cause | Correct Engineering Fix |
|:---|:---|:---|
| **Cites a policy section that does not exist.** | No source text was provided in context, or RAG retriever failed. | Upload policy directly into context window; mandate direct quotation. |
| **Different category chosen for the same email across two runs.** | Categories were not bounded; no few-shot examples were given. | Explicitly list allowed categorical values; provide 4–5 few-shot demonstration pairs. |
| **Long, polite, says nothing useful.** | Prompt lacked a specific action verb and structural format. | Enforce one verb, one deliverable, labeled output lines, and strict word limit. |
| **Confident answer to an ambiguous or incomplete request.** | Prompt lacked an explicit uncertainty/escape clause. | Add: *"If information is unclear, say 'unclear' rather than guessing."* |


## 9. Deliverable Verification & Screenshots
*(Slide Reference: Slide 49)*

### Official Verification Evidence:
- **Assistant Configuration:** `Gemini 1.5 Flash` configured with System Instructions and two verified policy documents.
- **Trap Test Result:** Zero hallucinations across 5 audited queries.

<p align="center">
  <img width="100%" alt="Verified Assistant Output with Quote" src="https://github.com/user-attachments/assets/1c412bde-f734-43f6-93a4-9cc85c6cdb63" />
  <br>
  <em>Figure: Verified Department Assistant execution with direct quotation and safe trap refusal.</em>
</p>

---

<div align="center">
  <sub>Document generated for IEEE CS Region 8 AI Caravan 2026 · AI Administrator Track. MIT Licensed.</sub>
</div>
```
