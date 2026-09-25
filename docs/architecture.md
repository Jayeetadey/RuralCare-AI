# RuralCare AI — System Architecture

## 1. Purpose

This document describes the technical architecture of RuralCare AI.

The system is designed as an offline-first, multilingual symptom triage application for rural health workers.

The system does not provide a definitive medical diagnosis.

Its workflow is:

**Symptom Understanding → Risk / Red-Flag Assessment → Triage Recommendation**

---

## 2. High-Level Architecture

```text
                    ┌──────────────────────┐
                    │    Health Worker     │
                    │  English / Bengali   │
                    └──────────┬───────────┘
                               │
                               ↓
                    ┌──────────────────────┐
                    │    React PWA App     │
                    │ Patient + Visit UI   │
                    └──────────┬───────────┘
                               │
                 ┌─────────────┴─────────────┐
                 ↓                           ↓
       ┌──────────────────┐        ┌──────────────────┐
       │    AI / NLP      │        │  Local Storage   │
       │ Symptom          │        │   IndexedDB      │
       │ Understanding    │        │ Offline Records  │
       └────────┬─────────┘        └────────┬─────────┘
                │                           │
                ↓                           ↓
       ┌──────────────────┐        ┌──────────────────┐
       │ Structured       │        │   Sync Queue     │
       │ Symptoms         │        │ Pending Records  │
       └────────┬─────────┘        └────────┬─────────┘
                │                           │
                └─────────────┬─────────────┘
                              ↓
                    ┌──────────────────────┐
                    │   Safety Rule Engine │
                    │ Red-Flag Assessment  │
                    └──────────┬───────────┘
                               │
                               ↓
                    ┌──────────────────────┐
                    │   Triage Engine      │
                    │                      │
                    │  Home Care           │
                    │  Clinic Visit        │
                    │  Urgent Referral     │
                    └──────────┬───────────┘
                               │
                               ↓
                    ┌──────────────────────┐
                    │    Backend API       │
                    └──────────┬───────────┘
                               │
                               ↓
                    ┌──────────────────────┐
                    │      Supabase        │
                    │ Patient + Visit Data │
                    └──────────────────────┘
```

---

## 3. Main Components

### 3.1 Presentation Layer

The presentation layer is the interface used by the health worker.

Responsibilities:

* Health worker login
* Dashboard
* Patient registration
* Patient details
* Vitals entry
* Symptom questionnaire
* Free-text symptom input
* Language selection
* Triage result
* Patient history
* Offline status
* Sync status

Technology:

**React + Vite + Progressive Web App (PWA)**

---

### 3.2 AI / NLP Layer

The AI/NLP layer converts natural-language symptom descriptions into structured information.

Example:

```text
Input:

"Amar 3 din dhore jor, matha betha aar khub durbol lagche."

                ↓

AI / NLP

                ↓

Structured Output:

{
  "symptoms": [
    "fever",
    "headache",
    "weakness"
  ],
  "duration": "3 days"
}
```

The AI layer is responsible for:

* Understanding free-text symptoms
* Handling English and Bengali input
* Extracting symptoms
* Extracting duration when available
* Producing structured symptom information

The AI layer must not independently make a final diagnosis.

---

## 4. Safety Rule Engine

The safety rule engine is deterministic and is used for safety-critical assessment.

It checks structured patient information against predefined red-flag conditions.

Example:

```text
Patient Information
        ↓
Symptoms + Vitals + Red Flags
        ↓
Safety Rule Engine
        ↓
┌───────────────────────────────┐
│ Red Flag Present?             │
└───────────────┬───────────────┘
                │
          ┌─────┴─────┐
         YES           NO
          ↓             ↓
   Urgent Referral   Continue
```

Important principle:

**Safety rules have priority over AI-generated interpretation.**

This means the AI helps understand symptoms, while deterministic rules control safety-critical escalation.

---

## 5. Triage Engine

The triage engine converts the assessment into one of three recommendations.

```text
              Assessment
                   ↓
        ┌──────────┼──────────┐
        ↓          ↓          ↓
    LOW RISK   MODERATE     HIGH RISK
        ↓          ↓          ↓
   HOME CARE   CLINIC VISIT  URGENT
                             REFERRAL
```

### Triage Levels

#### Level 1 — Home Care

Used when no important red flags are detected and the case does not require immediate clinical evaluation.

#### Level 2 — Clinic Visit

Used when the symptoms require evaluation by a healthcare professional but do not indicate an immediate emergency.

#### Level 3 — Urgent Referral

Used when important red flags or high-risk conditions are detected.

---

## 6. Offline-First Layer

RuralCare AI should continue working when internet connectivity is unavailable.

```text
              User Action
                   ↓
             React PWA
                   ↓
          Internet Available?
             /          \
           YES          NO
           ↓             ↓
      Backend API    IndexedDB
           ↓             ↓
       Supabase     Local Record
                         ↓
                    Sync Queue
                         ↓
                 Internet Returns
                         ↓
                    Backend API
                         ↓
                     Supabase
```

Local storage is used for:

* Patient information
* Visit information
* Symptoms
* Vitals
* Triage results
* Pending synchronization records

---

## 7. Synchronization Layer

The synchronization layer transfers locally stored records to the backend when connectivity becomes available.

Basic workflow:

```text
Offline Record
      ↓
Stored in IndexedDB
      ↓
Added to Sync Queue
      ↓
Internet Available
      ↓
Send to Backend API
      ↓
Successful Response
      ↓
Store as Synced
```

If synchronization fails:

```text
Sync Attempt
     ↓
Failed
     ↓
Keep Record Locally
     ↓
Retry Later
```

---

## 8. Backend API

The backend provides a controlled communication layer between the frontend and cloud database.

Responsibilities:

* Receive patient records
* Receive visit records
* Validate incoming data
* Handle synchronization
* Store records
* Retrieve patient history
* Provide structured API responses

Basic flow:

```text
React PWA
    ↓
Backend API
    ↓
Supabase
    ↓
PostgreSQL Database
```

---

## 9. Database Layer

Supabase will be used for cloud storage and backend services.

The database will contain information such as:

```text
Health Worker
     │
     ├── Patient
     │      │
     │      └── Visit
     │             ├── Vitals
     │             ├── Symptoms
     │             ├── AI Extraction
     │             └── Triage Result
     │
     └── Sync Information
```

The detailed database schema will be defined separately in:

`docs/database-design.md`

---

## 10. Complete Data Flow

```text
Health Worker
      ↓
Patient Registration
      ↓
Enter Vitals
      ↓
Collect Symptoms
      ↓
English / Bengali Input
      ↓
AI Symptom Understanding
      ↓
Structured Symptoms
      ↓
Safety Rule Engine
      ↓
Risk / Red-Flag Assessment
      ↓
Triage Engine
      ↓
┌──────────────┬────────────────┬──────────────────┐
│  Home Care   │  Clinic Visit  │ Urgent Referral  │
└──────────────┴────────────────┴──────────────────┘
                       ↓
                  Save Visit
                       ↓
             ┌─────────┴─────────┐
             ↓                   ↓
          Online              Offline
             ↓                   ↓
       Backend API          IndexedDB
             ↓                   ↓
         Supabase           Sync Queue
                                 ↓
                          Internet Returns
                                 ↓
                            Backend API
                                 ↓
                              Supabase
```

---

## 11. Safety Boundary

RuralCare AI is a decision-support and triage-assistance system.

It should:

* Understand reported symptoms
* Identify predefined red flags
* Organize patient information
* Provide a triage recommendation
* Help health workers decide the next appropriate level of care

It should not:

* Provide definitive medical diagnosis
* Replace a doctor or qualified healthcare professional
* Generate prescriptions
* Automatically make clinical decisions without safety rules
* Claim clinical accuracy without validation

---

## 12. Architecture Principle

The core architecture follows a **Hybrid AI + Rule-Based Safety** approach.

```text
              Natural Language
                     ↓
                  AI / NLP
                     ↓
           Structured Symptoms
                     ↓
          Deterministic Safety Rules
                     ↓
                 Triage
```

The AI provides flexibility in understanding language.

The deterministic safety layer provides predictable handling of predefined red flags.

This separation is important because the project is intended to support healthcare workers rather than replace clinical judgment.
