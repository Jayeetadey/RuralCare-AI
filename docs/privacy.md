# RuralCare AI — Privacy and Data Protection

## 1. Purpose

This document defines the basic privacy and data-protection principles for the RuralCare AI prototype.

RuralCare AI is designed to assist rural health workers with symptom collection, risk assessment, and triage support.

The hackathon prototype must prioritize responsible handling of patient information.

---

## 2. Data Minimization

The application should collect only information required for the MVP workflow.

Examples include:

* Patient name or demo identifier
* Age
* Gender
* Village/location
* Contact information when required
* Vitals
* Reported symptoms
* Symptom duration
* Triage result
* Visit information

Unnecessary personal information should not be collected.

---

## 3. Synthetic Data for Demonstration

The hackathon demonstration should use **synthetic patient data**.

Example:

```text
Patient Name: Demo Patient 01
Age: 35
Village: Demo Village
```

Real patient medical records should not be used for testing or demonstration.

---

## 4. Local Data Storage

Because RuralCare AI is offline-first, some information may temporarily be stored on the user's device.

```text
Health Worker
      ↓
RuralCare AI
      ↓
Local IndexedDB
      ↓
Sync Queue
```

Local data should be protected from unnecessary exposure.

---

## 5. Cloud Storage

When connectivity is available, approved records can be synchronized with the backend and Supabase database.

```text
Local Record
      ↓
Sync Queue
      ↓
Backend API
      ↓
Supabase
```

Only required application data should be synchronized.

---

## 6. Authentication and Access

The application should use authenticated access for protected data.

Health workers should only be given access appropriate to their role.

The prototype should not expose patient records through publicly accessible URLs or public database queries.

---

## 7. AI Data Handling

The AI component should receive only the information required for symptom understanding.

The application should avoid unnecessarily sending identifying patient information to an external AI service.

Where possible, symptom-processing requests should use the minimum necessary information.

---

## 8. AI Safety Boundary

The AI must not:

* Provide a definitive medical diagnosis.
* Generate prescriptions.
* Invent patient information.
* Override safety rules.
* Make unsupported medical claims.

The AI is primarily responsible for understanding natural-language symptom input.

```text
Natural Language
       ↓
AI / NLP
       ↓
Structured Symptoms
       ↓
Safety Rule Engine
       ↓
Triage
```

---

## 9. Safety-Critical Processing

Safety-critical decisions should not depend solely on an AI-generated response.

Predefined deterministic rules should be used for red-flag detection.

```text
AI
 ↓
Symptom Understanding
 ↓
Safety Rules
 ↓
Risk Assessment
 ↓
Triage Recommendation
```

This separation makes the prototype's safety logic easier to inspect and test.

---

## 10. Data Transmission

When data is synchronized with the backend, communication should use secure network connections.

The application should avoid transmitting patient information over insecure connections.

---

## 11. Error and Sync Handling

If synchronization fails:

```text
Local Record
      ↓
Sync Attempt
      ↓
Failure
      ↓
Keep Local Record
      ↓
Retry Later
```

Patient information should not be deleted simply because a synchronization attempt fails.

---

## 12. Data Retention

The hackathon prototype should retain only the information required for demonstrating:

* Patient registration
* Patient visits
* Symptom collection
* Vitals
* Triage
* Patient history
* Offline synchronization

A production system would require a formally defined data-retention policy.

---

## 13. Privacy in the Hackathon Demo

During the demonstration:

* Use synthetic patients.
* Do not display real phone numbers.
* Do not display real addresses.
* Do not use real medical records.
* Do not expose database credentials.
* Do not expose API keys.
* Do not commit `.env` files or secrets to GitHub.

---

## 14. Production Deployment Considerations

The hackathon prototype is not a complete clinical production system.

Before real-world deployment, additional measures would be required, including:

* Formal privacy policies
* Appropriate authentication and authorization
* Encryption
* Audit logging
* Secure secret management
* Data-retention controls
* Clinical validation
* Security testing
* Regulatory and ethical review

---

## 15. Privacy Principle

The core principle is:

```text
Collect Minimum Data
        ↓
Protect Local Data
        ↓
Secure Data Transmission
        ↓
Restrict Access
        ↓
Use Synthetic Data for Demo
```

RuralCare AI should treat patient information as sensitive data throughout the application workflow.
