# RuralCare AI — Test Cases

## 1. Purpose

This document defines synthetic test cases for evaluating the RuralCare AI MVP.

The test cases verify:

* Patient registration
* Vitals input
* English symptom input
* Bengali symptom input
* Mixed-language symptom input
* AI symptom extraction
* Red-flag detection
* Triage recommendation
* Offline storage
* Synchronization
* Patient history

All patient information used for testing must be synthetic.

---

## 2. Triage Test Cases

### TC-01 — Mild Symptoms

**Input:**

```text
Patient: Synthetic Patient 01
Age: 25

Symptoms:
"Mild headache since yesterday."
```

**Expected AI understanding:**

```text
Symptom: headache
Duration: 1 day
Severity: mild
```

**Expected safety result:**

```text
No predefined red flag detected.
```

**Expected triage:**

```text
HOME CARE
```

---

### TC-02 — Persistent Fever

**Input:**

```text
Patient: Synthetic Patient 02
Age: 32

Symptoms:
"I have fever for several days and I am feeling weak."
```

**Expected AI understanding:**

```text
Symptoms:
- fever
- weakness

Duration:
- several days
```

**Expected safety result:**

```text
No predefined urgent red flag detected.
Further assessment required.
```

**Expected triage:**

```text
CLINIC VISIT
```

---

### TC-03 — Bengali Input

**Input:**

```text
"আমার তিন দিন ধরে জ্বর আর মাথা ব্যথা।"
```

**Expected AI understanding:**

```text
Language: Bengali

Symptoms:
- fever
- headache

Duration:
- 3 days
```

**Expected result:**

The system should convert the Bengali input into the same structured symptom format used for English input.

---

### TC-04 — Mixed Bengali + English

**Input:**

```text
"Amar 3 din dhore fever aar headache hocche."
```

**Expected AI understanding:**

```text
Language: Mixed Bengali + English

Symptoms:
- fever
- headache

Duration:
- 3 days
```

**Expected result:**

The system should correctly extract the reported symptoms despite mixed-language input.

---

### TC-05 — Severe Breathing Difficulty

**Input:**

```text
Patient: Synthetic Patient 05
Age: 60

Symptoms:
"Patient is having severe difficulty breathing."
```

**Expected safety result:**

```text
Red Flag:
Severe breathing difficulty
```

**Expected triage:**

```text
URGENT REFERRAL
```

**Reason:**

```text
A predefined high-risk symptom was detected.
```

---

### TC-06 — Severe Chest Pain

**Input:**

```text
Patient: Synthetic Patient 06
Age: 55

Symptoms:
"Patient has severe chest pain."
```

**Expected safety result:**

```text
Red Flag:
Severe chest pain
```

**Expected triage:**

```text
URGENT REFERRAL
```

---

### TC-07 — Loss of Consciousness

**Input:**

```text
Patient: Synthetic Patient 07
Age: 48

Symptoms:
"Patient lost consciousness."
```

**Expected safety result:**

```text
Red Flag:
Loss of consciousness
```

**Expected triage:**

```text
URGENT REFERRAL
```

---

## 3. AI Validation Test

### TC-08 — Missing Information

**Input:**

```text
"I have fever."
```

Expected output:

```json
{
  "symptoms": [
    {
      "name": "fever",
      "duration": null,
      "severity": null
    }
  ]
}
```

The AI should not invent a duration or severity.

---

## 4. Safety Priority Test

### TC-09 — Red Flag Overrides Lower-Risk Symptoms

**Input:**

```text
"Patient has mild headache but is also having severe difficulty breathing."
```

Expected AI understanding:

```text
Symptoms:
- headache
- severe breathing difficulty
```

Expected safety result:

```text
Red Flag detected:
Severe breathing difficulty
```

Expected triage:

```text
URGENT REFERRAL
```

The mild headache must not cause the system to produce a lower-risk recommendation.

---

## 5. Offline Test Cases

### TC-10 — Save Visit Without Internet

**Scenario:**

```text
Internet: OFF

Health Worker
      ↓
Registers Patient
      ↓
Records Vitals
      ↓
Enters Symptoms
      ↓
Receives/records Triage Result
      ↓
Save Visit
```

**Expected result:**

The visit should be saved locally in IndexedDB.

---

### TC-11 — Synchronize After Internet Returns

**Scenario:**

```text
Visit stored locally
        ↓
Internet OFF
        ↓
Internet becomes available
        ↓
Sync Queue
        ↓
Backend API
        ↓
Supabase
```

**Expected result:**

The pending record should be uploaded successfully.

The record should then be marked as:

```text
synced
```

---

### TC-12 — Sync Failure

**Scenario:**

```text
Local Record
     ↓
Sync Attempt
     ↓
Network/API Failure
```

**Expected result:**

* Record remains stored locally.
* Sync status remains `pending` or `failed`.
* The application retries later.
* Patient information is not lost.

---

## 6. Patient History Test

### TC-13 — Multiple Visits

**Scenario:**

A synthetic patient has multiple visits.

```text
Patient
   ↓
Visit 1
   ↓
Visit 2
   ↓
Visit 3
```

**Expected result:**

The health worker should be able to view the patient's previous visit information.

---

## 7. Test Summary

| Test ID | Feature               | Expected Result         |
| ------- | --------------------- | ----------------------- |
| TC-01   | Mild symptoms         | Home Care               |
| TC-02   | Persistent symptoms   | Clinic Visit            |
| TC-03   | Bengali input         | Correct extraction      |
| TC-04   | Mixed language        | Correct extraction      |
| TC-05   | Breathing red flag    | Urgent Referral         |
| TC-06   | Chest pain red flag   | Urgent Referral         |
| TC-07   | Loss of consciousness | Urgent Referral         |
| TC-08   | Missing information   | No hallucinated data    |
| TC-09   | Safety priority       | Urgent Referral         |
| TC-10   | Offline save          | Local storage           |
| TC-11   | Synchronization       | Record synced           |
| TC-12   | Sync failure          | Record retained         |
| TC-13   | Patient history       | Previous visits visible |

---

## 8. Hackathon Demo Scenario

For the final demonstration, use a small number of clear synthetic cases.

Recommended demo flow:

```text
Demo Case
    ↓
Register Synthetic Patient
    ↓
Enter Vitals
    ↓
Enter Bengali / English Symptom
    ↓
AI Extracts Symptoms
    ↓
Safety Rule Engine
    ↓
Triage Result
    ↓
Save Visit
    ↓
Show Patient History
```

A second demonstration can show the offline workflow:

```text
Turn Internet OFF
       ↓
Create Visit
       ↓
Save Locally
       ↓
Turn Internet ON
       ↓
Synchronize
       ↓
Show Cloud Record
```

---

## 9. Important Testing Limitation

These are synthetic prototype test cases.

Successful execution of these tests does not establish clinical accuracy or clinical safety.

Real-world deployment would require appropriate clinical validation, healthcare-professional review, and additional safety testing.
