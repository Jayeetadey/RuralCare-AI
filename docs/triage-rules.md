# RuralCare AI — Triage Rules

## 1. Purpose

This document defines the safety and triage rules used by RuralCare AI.

The system provides a triage recommendation to assist a health worker.

It does not provide a definitive medical diagnosis or replace a qualified healthcare professional.

The triage process is:

```text
Reported Symptoms + Vitals
          ↓
    Red-Flag Check
          ↓
     Risk Assessment
          ↓
   Triage Recommendation
```

---

## 2. Triage Categories

RuralCare AI uses three triage levels:

```text
┌─────────────────────┐
│     HOME CARE       │
│     Lower Risk      │
└─────────────────────┘

┌─────────────────────┐
│    CLINIC VISIT     │
│   Moderate Risk     │
└─────────────────────┘

┌─────────────────────┐
│  URGENT REFERRAL    │
│     High Risk       │
└─────────────────────┘
```

---

## 3. Priority Rule

Safety-critical red flags always take priority over the general symptom interpretation.

```text
Patient Information
       ↓
Symptom Understanding
       ↓
Red-Flag Check
       ↓
┌───────────────┐
│ Red Flag?     │
└───────┬───────┘
        │
   ┌────┴────┐
  YES        NO
   ↓          ↓
URGENT      Continue
REFERRAL    Assessment
```

---

## 4. Urgent Referral Rules

An urgent referral should be recommended when a predefined high-risk red flag is detected.

For the hackathon MVP, examples include:

### Rule U1 — Severe Breathing Difficulty

```text
IF severe breathing difficulty is reported
THEN → URGENT REFERRAL
```

### Rule U2 — Severe Chest Pain

```text
IF severe chest pain is reported
THEN → URGENT REFERRAL
```

### Rule U3 — Loss of Consciousness

```text
IF loss of consciousness is reported
THEN → URGENT REFERRAL
```

### Rule U4 — Severe Confusion

```text
IF severe confusion or inability to respond normally is reported
THEN → URGENT REFERRAL
```

### Rule U5 — Critical Oxygen Saturation

```text
IF oxygen saturation is critically low
THEN → URGENT REFERRAL
```

The exact clinical threshold should be treated as a configurable safety parameter and should not be presented as a clinically validated threshold for real-world deployment.

---

## 5. Clinic Visit Rules

If no urgent red flag is detected but the symptoms require professional evaluation, the recommendation can be:

**CLINIC VISIT**

Examples:

```text
Persistent symptoms
        +
No immediate red flag
        ↓
   CLINIC VISIT
```

Possible examples include:

* Persistent fever
* Persistent vomiting
* Moderate breathing-related symptoms without an urgent red flag
* Symptoms that are worsening
* Multiple symptoms occurring together
* Abnormal but non-critical vitals

These rules are intended for the hackathon prototype and require clinical validation before real-world deployment.

---

## 6. Home Care Rules

A **HOME CARE** recommendation may be used when:

* No predefined red flag is detected.
* No concerning vital-sign abnormality is identified by the prototype.
* Symptoms appear mild according to the defined prototype rules.
* The case does not require immediate clinical evaluation according to the configured rules.

Example:

```text
Mild symptom
     +
No red flag
     +
No concerning vital
     ↓
 HOME CARE
```

The system should still advise the health worker to seek professional medical evaluation if symptoms worsen.

---

## 7. Rule Priority

Rules should be evaluated in the following order:

```text
          Patient Data
               ↓
        Check Red Flags
               ↓
       ┌───────┴───────┐
       ↓               ↓
 Red Flag Found    No Red Flag
       ↓               ↓
URGENT REFERRAL   Check Risk Factors
                       ↓
                ┌──────┴──────┐
                ↓             ↓
             Moderate        Low
                ↓             ↓
          CLINIC VISIT    HOME CARE
```

This prevents a lower-risk recommendation from overriding a safety-critical red flag.

---

## 8. Rule Output

The rule engine should produce structured output.

Example:

```json
{
  "triage_level": "urgent_referral",
  "red_flags": [
    "severe_breathing_difficulty"
  ],
  "reason": "A predefined red flag was detected.",
  "priority": "high"
}
```

Another example:

```json
{
  "triage_level": "clinic_visit",
  "red_flags": [],
  "reason": "Symptoms require clinical evaluation but no urgent red flag was detected.",
  "priority": "medium"
}
```

Example for lower-risk input:

```json
{
  "triage_level": "home_care",
  "red_flags": [],
  "reason": "No predefined red flag was detected.",
  "priority": "low"
}
```

---

## 9. AI and Rule Engine Separation

The AI and safety rule engine have different responsibilities.

### AI / NLP

```text
Natural Language
      ↓
Understand Symptoms
      ↓
Extract Structured Information
```

### Safety Rules

```text
Structured Information
      ↓
Check Predefined Rules
      ↓
Determine Risk Level
```

Therefore:

**AI output must not directly override a safety rule.**

---

## 10. Explanation Requirement

Every triage recommendation should provide a simple explanation to the health worker.

Example:

```text
Triage: URGENT REFERRAL

Reason:
A predefined high-risk symptom was detected.

Action:
Refer the patient for urgent professional medical evaluation.
```

The explanation should be understandable to a health worker and should not claim a medical diagnosis.

---

## 11. Safety Disclaimer

The application should display a disclaimer such as:

```text
RuralCare AI provides decision-support and triage assistance.
It does not provide a definitive medical diagnosis.
Always use professional clinical judgment and appropriate medical care.
```

---

## 12. Important Prototype Limitation

These rules are designed for the hackathon prototype.

They are not clinically validated protocols.

Before real-world healthcare deployment, the rules would require:

* Clinical review
* Validation with healthcare professionals
* Testing on appropriate clinical datasets
* Safety evaluation
* Regulatory and ethical review
* Continuous monitoring

---

## 13. Final Decision Logic

The complete prototype logic is:

```text
Patient Data
     ↓
Symptoms + Vitals
     ↓
AI Symptom Understanding
     ↓
Structured Information
     ↓
Safety Rule Engine
     ↓
┌─────────────────────────────┐
│ Is a predefined red flag    │
│ detected?                   │
└──────────────┬──────────────┘
               │
          ┌────┴────┐
         YES        NO
          ↓          ↓
      URGENT       Risk
      REFERRAL    Assessment
                     ↓
               ┌─────┴─────┐
               ↓           ↓
          Moderate        Low
               ↓           ↓
        CLINIC VISIT   HOME CARE
```
