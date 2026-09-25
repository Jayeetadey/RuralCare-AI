# RuralCare AI — User Flow

## 1. Main User

**Primary User:** Frontline / Rural Health Worker

The health worker uses RuralCare AI to collect patient information, record symptoms and vitals, perform AI-assisted symptom understanding, check safety rules, and receive a triage recommendation.

---

# 2. Main Application Flow

```text
┌──────────────┐
│    Login     │
└──────┬───────┘
       ↓
┌──────────────┐
│  Dashboard   │
└──────┬───────┘
       ↓
┌──────────────┐
│ New Patient  │
└──────┬───────┘
       ↓
┌──────────────┐
│Patient Details│
└──────┬───────┘
       ↓
┌──────────────┐
│    Vitals    │
└──────┬───────┘
       ↓
┌──────────────┐
│   Symptoms   │
└──────┬───────┘
       ↓
┌────────────────────────┐
│ AI Symptom Understanding│
└────────────┬───────────┘
             ↓
┌────────────────────────┐
│   Safety Rule Engine   │
└────────────┬───────────┘
             ↓
┌────────────────────────┐
│    Triage Result       │
└────────────┬───────────┘
             ↓
┌────────────────────────┐
│      Save Visit        │
└────────────┬───────────┘
             ↓
┌────────────────────────┐
│    Patient History     │
└────────────────────────┘
```

---

# 3. Login Flow

```text
Health Worker
      ↓
Open RuralCare AI
      ↓
     Login
      ↓
   Dashboard
```

The MVP uses a simple health-worker login interface.

---

# 4. Dashboard Flow

After login, the health worker sees:

* Total patients
* Patients visited today
* Pending synchronization records
* Recent patient records
* New Patient button

### Dashboard Actions

```text
                    ┌───────────────┐
                    │   Dashboard   │
                    └───────┬───────┘
                            │
          ┌─────────────────┼─────────────────┐
          ↓                 ↓                 ↓
   ┌─────────────┐   ┌─────────────┐   ┌────────────────┐
   │ New Patient │   │Patient List │   │ Sync Status    │
   └──────┬──────┘   └──────┬──────┘   └────────────────┘
          ↓                 ↓
   Patient Details     Patient History
```

The health worker can:

1. Create a new patient
2. Open an existing patient
3. View patient history
4. Check synchronization status

---

# 5. New Patient Flow

```text
Dashboard
    ↓
New Patient
    ↓
Patient Details
    ↓
Enter Patient Information
    ↓
Continue
```

### Patient Information

* Patient ID
* Name
* Age
* Sex
* Village
* Household
* Consent

---

# 6. Vitals Flow

```text
Patient Details
      ↓
    Vitals
      ↓
Record Available Vitals
      ↓
   Continue
```

### Vitals

* Temperature
* Blood pressure
* SpO2
* Weight

---

# 7. Symptom Collection Flow

```text
     Vitals
       ↓
    Symptoms
       ↓
 ┌─────┴─────┐
 ↓           ↓
Guided      Free
Questions   Text
 ↓           ↓
 └─────┬─────┘
       ↓
  Symptom Input
```

### Method 1 — Guided Questionnaire

The health worker selects relevant symptoms from predefined options.

Examples:

* Fever
* Cough
* Headache
* Chest pain
* Breathing difficulty

### Method 2 — Free Text

The health worker describes the patient's symptoms naturally.

**Example:**

> "Patient has fever and cough for three days."

---

# 8. Language Flow

The MVP supports:

* English
* Bengali

```text
User Input
    ↓
Language Selection / Detection
    ↓
English / Bengali
    ↓
AI Symptom Understanding
```

The system should preserve the meaning of the reported symptoms during AI processing.

---

# 9. AI Symptom Understanding Flow

```text
Symptom Input
      ↓
Language Understanding
      ↓
Symptom Extraction
      ↓
Structured Symptom Data
      ↓
Safety Rule Engine
```

The AI may extract:

* Language
* Symptoms
* Duration
* Severity
* Mentioned vital information
* Red-flag candidates

### Important

The AI **does not provide a definitive medical diagnosis**.

---

# 10. Safety Rule Flow

```text
Structured Symptom Data
          ↓
   Safety Rule Engine
          ↓
   Check Red Flags
          ↓
    ┌─────┴─────┐
    ↓           ↓
 No Red Flag   Red Flag
    ↓           ↓
Normal Triage  Escalation
```

### Example Red Flags

```text
Chest Pain
     OR
Severe Breathing Difficulty
     ↓
Safety Escalation
```

Safety-critical rules have priority over the normal AI interpretation.

---

# 11. Triage Flow

After AI symptom understanding and safety checks:

```text
             Triage
                ↓
       ┌────────┼────────┐
       ↓        ↓        ↓
   Home Care  Clinic   Urgent
              Visit    Referral
```

### 1. Home Care

For cases that do not meet configured escalation conditions.

### 2. Clinic Visit

For cases requiring evaluation at a clinic.

### 3. Urgent Referral

For cases that meet configured red-flag conditions.

The result should include a clear explanation.

---

# 12. Triage Result Screen

```text
        Triage Analysis
              ↓
      ┌───────────────┐
      │ Triage Level  │
      └───────┬───────┘
              ↓
      ┌───────────────┐
      │ Main Reason   │
      └───────┬───────┘
              ↓
      ┌───────────────┐
      │Key Symptoms   │
      └───────┬───────┘
              ↓
      ┌───────────────┐
      │Recommended    │
      │Next Action    │
      └───────┬───────┘
              ↓
      Medical Disclaimer
```

### Example

**Triage Level:**
`URGENT REFERRAL`

**Reason:**
Chest pain and breathing difficulty were reported.

**Action:**
Seek appropriate medical evaluation urgently.

---

# 13. Save Visit Flow

```text
Triage Result
      ↓
   Save Visit
      ↓
Check Internet Connection
      ↓
   ┌──┴──┐
   ↓     ↓
Online Offline
   ↓     ↓
Cloud   Local Storage
   ↓     ↓
Synced  Pending Sync
```

---

# 14. Offline Flow

When internet connectivity is unavailable:

```text
Application
     ↓
Local Storage
     ↓
Save Patient / Visit Data
     ↓
Mark Record as "Pending"
```

The health worker can continue using the core patient-data workflow.

---

# 15. Synchronization Flow

When internet connectivity returns:

```text
Pending Records
      ↓
Internet Available
      ↓
Synchronization Queue
      ↓
Backend API
      ↓
Cloud Database
      ↓
Record Marked as "Synced"
```

### Synchronization States

```text
Pending
   ↓
Syncing
   ↓
Synced
```

If synchronization fails:

```text
Sync Attempt
     ↓
   Failed
     ↓
Remain "Pending"
     ↓
Retry Later
```

---

# 16. Patient History Flow

```text
Dashboard
    ↓
Patient History
    ↓
Select Patient
    ↓
Patient Profile
    ↓
Previous Visits
```

### Patient History May Include

* Previous visit dates
* Previous symptoms
* Previous vitals
* Previous triage results

---

# 17. Complete MVP Flow

```text
                    ┌──────────────┐
                    │    LOGIN     │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │  DASHBOARD   │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │ NEW PATIENT  │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │PATIENT DETAILS│
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │    VITALS    │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │   SYMPTOMS   │
                    └──────┬───────┘
                           ↓
              ┌────────────────────────┐
              │ AI SYMPTOM UNDERSTANDING│
              └────────────┬───────────┘
                           ↓
              ┌────────────────────────┐
              │   SAFETY RULE ENGINE   │
              └────────────┬───────────┘
                           ↓
                    ┌──────────────┐
                    │    TRIAGE    │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │ EXPLANATION  │
                    └──────┬───────┘
                           ↓
                    ┌──────────────┐
                    │     SAVE     │
                    └──────┬───────┘
                           ↓
                 ┌─────────┴─────────┐
                 ↓                   ↓
             ┌────────┐        ┌──────────┐
             │ ONLINE │        │ OFFLINE  │
             └────┬───┘        └────┬─────┘
                  ↓                 ↓
             ┌────────┐       ┌────────────┐
             │  CLOUD │       │   LOCAL    │
             └────┬───┘       │  STORAGE   │
                  │           └─────┬──────┘
                  │                 ↓
                  │           ┌────────────┐
                  │           │PENDING SYNC│
                  │           └─────┬──────┘
                  │                 ↓
                  │          INTERNET RETURNS
                  │                 ↓
                  └────────→  ┌────────┐
                              │  SYNC  │
                              └───┬────┘
                                  ↓
                         ┌────────────────┐
                         │PATIENT HISTORY │
                         └────────────────┘
```

---

# 18. Important Safety Boundary

RuralCare AI is a **decision-support prototype**.

It does **not**:

* Provide a definitive medical diagnosis
* Prescribe medicines
* Replace a qualified doctor
* Override configured safety rules

The system assists the health worker in collecting information and performing structured triage.
