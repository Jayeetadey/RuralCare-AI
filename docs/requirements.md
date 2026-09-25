# RuralCare AI — Requirements

## 1. Project Title

RuralCare AI — Multilingual Offline-First Symptom Triage for Rural Health Workers

## 2. Problem

Rural and frontline health workers may have limited access to doctors, reliable internet connectivity, and digital healthcare tools.

RuralCare AI is designed as a decision-support system that helps health workers collect patient information, understand reported symptoms, identify safety red flags, and provide a triage recommendation.

The system is not a replacement for a doctor and does not provide a medical diagnosis.

---

## 3. Target User

Primary user:

- Frontline/rural health worker

The health worker uses the application to:

- Register patients
- Record patient information
- Record vital signs
- Collect symptoms
- Use multilingual input
- Receive AI-assisted symptom understanding
- Check safety red flags
- Receive a triage recommendation
- Store patient history
- Work when internet connectivity is unavailable
- Synchronize records when connectivity returns

---

## 4. Core Features

### 4.1 Patient Registration

The system shall allow a health worker to create a patient record.

Patient information may include:

- Patient ID
- Name
- Age
- Sex
- Village
- Household
- Consent

---

### 4.2 Vital Collection

The system shall allow the health worker to record available vital information.

Examples:

- Temperature
- Blood pressure
- SpO2
- Weight

---

### 4.3 Symptom Collection

The system shall support two methods of symptom input:

1. Guided questionnaire
2. Free-text symptom description

---

### 4.4 Multilingual Input

The MVP shall support:

- English
- Bengali

The system should be designed so additional languages can be added later.

---

### 4.5 AI-Assisted Symptom Understanding

The AI/NLP component shall help convert natural-language symptom descriptions into structured information.

The AI may extract:

- Language
- Symptoms
- Duration
- Severity
- Mentioned vital information
- Possible red-flag candidates

The AI shall not independently provide a medical diagnosis.

---

### 4.6 Safety Rule Engine

A separate rule-based safety layer shall evaluate important red-flag conditions.

Example:

- Chest pain
- Severe breathing difficulty
- Other configured emergency conditions

Safety-critical rules shall be able to escalate a case regardless of the AI's general interpretation.

---

### 4.7 Triage

The system shall provide three triage levels:

1. Home Care
2. Clinic Visit
3. Urgent Referral

The triage result shall include an explanation of the important factors that led to the recommendation.

---

### 4.8 Offline Operation

The application shall continue to support core patient data entry when internet connectivity is unavailable.

Offline records shall be stored locally on the device.

---

### 4.9 Synchronization

When internet connectivity becomes available, locally stored pending records shall be synchronized with the backend.

The system shall maintain synchronization status for records.

Possible states:

- Pending
- Synced
- Failed

---

### 4.10 Patient History

The health worker shall be able to view previous visits and relevant patient information.

Patient history may include:

- Previous visits
- Previous symptoms
- Previous vitals
- Previous triage results

---

### 4.11 Medical Disclaimer

The application shall clearly state that:

- It is a decision-support tool.
- It does not replace a qualified medical professional.
- It does not provide a definitive medical diagnosis.

---

## 5. Non-Functional Requirements

### 5.1 Offline-First

Core data collection should remain usable without an internet connection.

### 5.2 Usability

The interface should be simple enough for a frontline health worker to understand quickly.

### 5.3 Performance

The application should provide responsive interactions during normal use.

### 5.4 Reliability

Patient records should not be lost when connectivity is interrupted.

### 5.5 Privacy

Patient information should be handled carefully and should not be unnecessarily exposed.

### 5.6 Safety

Safety-critical red-flag rules should be deterministic and auditable.

---

## 6. MVP Scope

### Must Have

- Health worker interface
- Patient registration
- Patient details
- Vital collection
- Guided symptom questionnaire
- Free-text symptom input
- English and Bengali
- AI symptom extraction
- Safety/red-flag rules
- Three-level triage
- Triage explanation
- Offline storage
- Synchronization
- Patient history
- Medical disclaimer
- Test cases

### Optional / Bonus

- Voice input
- Referral PDF
- Village analytics
- Symptom heatmap
- Outbreak trend visualization

---

## 7. Out of Scope for MVP

The following are not part of the core MVP:

- Medical diagnosis
- Prescription generation
- Medical imaging analysis
- X-ray/MRI analysis
- Medicine recognition
- Physiotherapy modules
- Full hospital management system

---

## 8. High-Level Workflow

Health Worker
↓
Patient Registration
↓
Vitals
↓
Symptom Collection
↓
AI Symptom Understanding
↓
Safety Rule Engine
↓
Triage
↓
Explanation
↓
Save Record
↓
Offline Storage / Cloud Synchronization
↓
Patient History

---

## 9. Evaluation

The prototype shall be tested using representative synthetic test cases covering:

- Normal symptoms
- Moderate-risk symptoms
- Red-flag symptoms
- Bengali input
- Offline data entry
- Synchronization after connectivity returns

The prototype shall not claim clinically validated diagnostic accuracy unless appropriate validation has actually been performed.
