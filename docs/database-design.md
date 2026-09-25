# RuralCare AI — Database Design

## 1. Purpose

This document defines the data structure required for RuralCare AI.

The database stores health-worker information, patient information, visits, symptoms, vitals, triage results, and synchronization status.

The system should store only the information required for the hackathon MVP.

---

## 2. Main Entities

The MVP uses the following main entities:

```text
Health Worker
      │
      ↓
   Patient
      │
      ↓
    Visit
      │
      ├── Vitals
      ├── Symptoms
      ├── AI Extraction
      └── Triage Result
```

---

## 3. Health Worker

Stores information about the person using the application.

| Field      | Type      | Description             |
| ---------- | --------- | ----------------------- |
| id         | UUID      | Unique health worker ID |
| name       | Text      | Health worker name      |
| phone      | Text      | Contact number          |
| created_at | Timestamp | Account creation time   |

---

## 4. Patient

Stores basic patient information.

| Field            | Type      | Description                           |
| ---------------- | --------- | ------------------------------------- |
| id               | UUID      | Unique patient ID                     |
| health_worker_id | UUID      | Health worker associated with patient |
| name             | Text      | Patient name                          |
| age              | Integer   | Patient age                           |
| gender           | Text      | Patient gender                        |
| village          | Text      | Village/location                      |
| phone            | Text      | Optional contact number               |
| created_at       | Timestamp | Patient registration time             |

---

## 5. Visit

Each interaction with a patient creates a visit record.

| Field            | Type      | Description                    |
| ---------------- | --------- | ------------------------------ |
| id               | UUID      | Unique visit ID                |
| patient_id       | UUID      | Associated patient             |
| health_worker_id | UUID      | Health worker conducting visit |
| visit_date       | Timestamp | Date and time of visit         |
| language         | Text      | Language used                  |
| notes            | Text      | Additional notes               |
| sync_status      | Text      | Sync state                     |

Possible sync states:

```text
pending
synced
failed
```

---

## 6. Vitals

Stores basic patient measurements.

| Field             | Type      | Description              |
| ----------------- | --------- | ------------------------ |
| id                | UUID      | Unique vitals ID         |
| visit_id          | UUID      | Associated visit         |
| temperature       | Decimal   | Body temperature         |
| heart_rate        | Integer   | Heart rate               |
| respiratory_rate  | Integer   | Respiratory rate         |
| systolic_bp       | Integer   | Systolic blood pressure  |
| diastolic_bp      | Integer   | Diastolic blood pressure |
| oxygen_saturation | Decimal   | SpO2                     |
| recorded_at       | Timestamp | Measurement time         |

Optional fields may be left empty when the health worker does not have the required equipment.

---

## 7. Symptoms

Stores symptoms reported during a visit.

| Field        | Type | Description                       |
| ------------ | ---- | --------------------------------- |
| id           | UUID | Unique symptom record ID          |
| visit_id     | UUID | Associated visit                  |
| symptom_name | Text | Symptom name                      |
| duration     | Text | Duration of symptom               |
| severity     | Text | Reported severity                 |
| source       | Text | Guided questionnaire or free text |

Example:

```text
symptom_name = fever
duration = 3 days
severity = moderate
source = free_text
```

---

## 8. AI Extraction

Stores the structured information produced from free-text symptom input.

| Field          | Type      | Description                  |
| -------------- | --------- | ---------------------------- |
| id             | UUID      | Unique extraction ID         |
| visit_id       | UUID      | Associated visit             |
| input_text     | Text      | Original symptom description |
| extracted_data | JSON      | Structured symptoms          |
| language       | Text      | Input language               |
| created_at     | Timestamp | Extraction time              |

Example:

```json
{
  "symptoms": [
    "fever",
    "headache",
    "weakness"
  ],
  "duration": "3 days"
}
```

---

## 9. Triage Result

Stores the final triage recommendation.

| Field        | Type      | Description               |
| ------------ | --------- | ------------------------- |
| id           | UUID      | Unique result ID          |
| visit_id     | UUID      | Associated visit          |
| triage_level | Text      | Triage category           |
| red_flags    | JSON      | Detected red flags        |
| explanation  | Text      | Reason for recommendation |
| rule_version | Text      | Version of safety rules   |
| created_at   | Timestamp | Result creation time      |

Possible triage levels:

```text
home_care
clinic_visit
urgent_referral
```

---

## 10. Sync Queue

The offline-first application requires a local synchronization queue.

Local records can temporarily remain on the device when there is no internet connection.

```text
User enters data
       ↓
IndexedDB
       ↓
Sync Queue
       ↓
Internet Available
       ↓
Backend API
       ↓
Supabase
```

The local sync queue may contain:

| Field        | Description                  |
| ------------ | ---------------------------- |
| local_id     | Local record identifier      |
| record_type  | Type of record               |
| record_id    | Associated record            |
| operation    | Create / Update              |
| status       | Pending / Synced / Failed    |
| retry_count  | Number of retry attempts     |
| last_attempt | Last synchronization attempt |

---

## 11. Entity Relationships

```text
Health Worker
      │
      │ 1 : Many
      ↓
   Patient
      │
      │ 1 : Many
      ↓
    Visit
      │
      ├──────────────→ Vitals
      │
      ├──────────────→ Symptoms
      │
      ├──────────────→ AI Extraction
      │
      └──────────────→ Triage Result
```

---

## 12. Data Flow Example

Example patient journey:

```text
Health Worker
      ↓
Registers Patient
      ↓
Creates Visit
      ↓
Records Vitals
      ↓
Collects Symptoms
      ↓
AI Extracts Structured Symptoms
      ↓
Safety Rules Check Red Flags
      ↓
Triage Result Generated
      ↓
Visit Saved
      ↓
Cloud Sync
```

If there is no internet:

```text
Visit Saved
      ↓
IndexedDB
      ↓
Sync Queue
      ↓
Internet Available
      ↓
Supabase
```

---

## 13. Privacy Principles

The MVP should follow basic privacy principles:

* Store only necessary patient information.
* Do not expose patient information publicly.
* Use authenticated access for cloud data.
* Avoid storing unnecessary personal information.
* Use synthetic/demo patient data during the hackathon demonstration.
* Do not use real patient medical records for testing.

---

## 14. MVP Database Scope

For the hackathon, the minimum required data is:

```text
Health Worker
      ↓
Patient
      ↓
Visit
      ├── Vitals
      ├── Symptoms
      ├── AI Extraction
      └── Triage Result
```

Advanced analytics and outbreak-related data are outside the initial database scope.
