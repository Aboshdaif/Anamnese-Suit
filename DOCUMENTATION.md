# Anamnese Suite - Technical Documentation

## Version Information
- **Version**: 2.1.0
- **Release Date**: 2025-12-05
- **Authors**: DiggAi GmbH / Laith Alshdaifat
- **Medical Content**: Dr. Christian Klapproth

---

## 1. Overview

The Anamnese Suite is an offline-capable, privacy-compliant medical history questionnaire application designed for German healthcare practices. It enables patients to complete a comprehensive medical anamnesis in their preferred language, with data exported securely via NFC to the practice's electronic health record system.

### Key Features
- **Offline Operation**: Fully functional without internet connection
- **15 Languages**: German, English, Turkish, Russian, Arabic, Kurdish (Kurmanji), Polish, Romanian, Italian, French, Spanish, Bulgarian, Dutch, Farsi, Ukrainian
- **RTL Support**: Full right-to-left text support for Arabic, Farsi, and Kurdish
- **FHIR R5 Compliance**: Standard-compliant QuestionnaireResponse export
- **NFC Data Transfer**: NDEF protocol with application/json MIME type
- **GDPR/DSGVO Compliance**: RAM-only processing, automatic data wipe, no tracking
- **Voice Input**: Cross-platform speech-to-text via Web Speech API
- **Form Validation**: Required field validation with name and email format checking
- **Save/Restore**: Optional local storage for intermediate results (with consent)
- **Base Data Storage**: Save and reuse patient master data
- **Medical Dictionary**: Intelligent symptom recognition from voice input

### New in Version 2.1.0
- **Pflichtfeld-Validierung**: Required fields are validated before navigation
  - Name fields: minimum 3 letters, letters only (international characters supported)
  - Email fields: proper format validation (@domain.com)
- **Reset Button**: Clear all data and start from beginning
- **Save/Restore**: Store intermediate results locally (GDPR Art. 6(1)(a) - explicit consent)
- **Base Data Storage**: Save and load patient master data for future visits
- **Voice Input**: Cross-platform speech recognition (Web Speech API)
- **Medical Dictionary**: Automatic symptom recognition and assignment

---

## 2. Regulatory Compliance

### 2.1 GDPR/DSGVO Compliance
The application strictly adheres to the European General Data Protection Regulation:

| Article | Requirement | Implementation |
|---------|-------------|----------------|
| Art. 5(1)(c) | Data Minimization | Only essential medical data collected |
| Art. 5(1)(e) | Storage Limitation | RAM-only by default, optional localStorage with consent |
| Art. 5(1)(f) | Integrity/Confidentiality | Auto-wipe after export/timeout |
| Art. 6(1)(a) | Consent | Explicit consent for local storage |
| Art. 6 | Lawful Processing | Explicit consent required before data entry |
| Art. 9 | Special Categories | Medical data handled with enhanced protection |
| Art. 32 | Security Measures | No cookies, no tracking, no external requests |

### 2.2 FHIR R5 Compliance
Data export follows HL7 FHIR R5 QuestionnaireResponse specification:
- **Resource**: https://hl7.org/fhir/R5/questionnaireresponse.html
- **Questionnaire URL**: https://anamnese-suite.local/fhir/Questionnaire/anamnese-suite
- **Version**: 2.1.0

### 2.3 MDR 2017/745 Considerations
While this application is intended for informational purposes only and does not make medical diagnoses, the following documentation supports potential future MDR compliance:
- Software identification and version control
- Intended use documentation
- Risk analysis documentation
- Traceability of requirements

---

## 3. Technical Architecture

### 3.1 Data Flow
```
[Patient Input] → [RAM Storage] → [FHIR R5 JSON] → [NFC NDEF] → [Practice Device]
                                               ↓
                                        [Auto-Wipe]
```

### 3.2 Security Architecture
- **No Persistent Storage**: All data exists only in JavaScript runtime memory
- **No External Requests**: Application is fully self-contained
- **No Cookies/LocalStorage/IndexedDB**: No browser persistence mechanisms used
- **Auto-Wipe Triggers**:
  - After successful NFC export (2 second delay)
  - After JSON download (1 second delay)
  - After 10 minutes of inactivity
  - Warning displayed 60 seconds before timeout wipe

### 3.3 NFC Export Specification
- **Protocol**: NFC Data Exchange Format (NDEF)
- **Record Type**: MIME type `application/json`
- **Maximum Payload**: 4 KB (validated before export)
- **Fallback Options**: QR Code display, JSON file download

---

## 4. Question Codes (Q-Codes)

All questions are identified by unique Q-codes for database integration and encryption:

| Q-Code Range | Section |
|--------------|---------|
| q0000-q0099 | Basic Patient Data (Basisdaten) |
| q1000-q1999 | Current Complaints (Aktuelle Beschwerden) |
| q2000-q2999 | System Review (Systemabfrage) |
| q3000-q3999 | Medications & Allergies (Medikamente & Allergien) |
| q4000-q4999 | Family History (Familienanamnese) |
| q5000-q5999 | Lifestyle (Lebensstil) |
| q6000-q6999 | Pre-existing Conditions (Vorerkrankungen) |
| q7000-q7999 | Vaccinations & Surgeries (Impfungen & Operationen) |
| q8000-q8999 | Special Anamnesis (Spezielle Anamnese) |
| q9000-q9999 | Conclusion (Abschluss) |

### 4.1 Special Q-Codes
- **q1A00-q1A99**: Eye symptoms (Augenbeschwerden)
- **q1B00-q1B99**: ENT symptoms (HNO-Beschwerden)
- **q1C00-q1C99**: Mental health screening (Psychische Gesundheit)
- **q1P00-q1P99**: Pediatric anamnesis (Pädiatrie, age < 16)

---

## 5. Branching Logic

### 5.1 Age-Based Routing
- **Pediatric Section (q1P00+)**: Displayed when patient age < 16 years
- **Calculated from**: q0003 (Date of Birth)

### 5.2 Gender-Based Routing
- **Women's Health Section**: Displayed for gender = "weiblich" or "divers"
- **Pregnancy Questions**: Displayed for women of childbearing age

### 5.3 Symptom-Driven Details
- **Fever Details**: Shown when q1005.fever is selected
- **Pain Scale**: Shown when pain-related symptoms selected
- **System-Specific Blocks**: Shown based on q2000 organ system selection

---

## 6. Accessibility (WCAG AA)

### 6.1 Visual Accessibility
- **Font Size**: Minimum 18px base font for senior-friendly design
- **Contrast**: High contrast colors meeting WCAG AA requirements
- **Focus Indicators**: Visible 3px outline on focused elements

### 6.2 Motor Accessibility
- **Touch Targets**: Minimum 54px height for all interactive elements
- **Spacing**: Adequate spacing between interactive elements

### 6.3 Cognitive Accessibility
- **Clear Labels**: All questions have clear, translated labels
- **Progress Indicator**: Visual progress bar showing completion percentage
- **Answer Summary**: Fixed bottom panel showing all answered questions

---

## 7. Language Support

### 7.1 Supported Languages
1. German (de) - Master language
2. English (en)
3. Turkish (tr)
4. Russian (ru)
5. Arabic (ar) - RTL
6. Kurdish/Kurmanji (ku) - RTL
7. Polish (pl)
8. Romanian (ro)
9. Italian (it)
10. French (fr)
11. Spanish (es)
12. Bulgarian (bg)
13. Dutch (nl)
14. Farsi (fa) - RTL
15. Ukrainian (uk)

### 7.2 Translation Structure
All translations are stored in the `L` object with the format:
```javascript
L["key"] = {
  de: "German text",
  en: "English text",
  // ... other languages
}
```

---

## 8. Export Format

### 8.1 FHIR R5 QuestionnaireResponse Example
```json
{
  "resourceType": "QuestionnaireResponse",
  "questionnaire": "https://anamnese-suite.local/fhir/Questionnaire/anamnese-suite|2.0.0",
  "status": "completed",
  "authored": "2025-12-05T10:30:00.000Z",
  "item": [
    {
      "linkId": "q0000",
      "answer": [{"valueString": "Mustermann"}]
    },
    {
      "linkId": "q0001",
      "answer": [{"valueString": "Max"}]
    },
    {
      "linkId": "q0003",
      "answer": [{"valueDate": "1980-02-01"}]
    }
  ]
}
```

---

## 9. Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.1.0 | 2025-12-05 | Validation, voice input, save/restore, reset, base data storage, medical dictionary |
| 2.0.0 | 2025-12-05 | Senior-friendly design, enhanced accessibility, Q-code structure, answer summary box |
| 1.0.0 | 2025-12-04 | Initial release with basic sections |

---

## 10. Testing Documentation

### 10.1 Validation Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| VAL-001 | Submit empty required fields | Error messages shown, navigation blocked | ✅ |
| VAL-002 | Name with less than 3 characters | "Name muss mindestens 3 Buchstaben haben" error | ✅ |
| VAL-003 | Name with numbers | "Name darf nur Buchstaben enthalten" error | ✅ |
| VAL-004 | Valid name (3+ letters, no numbers) | Field accepted, no error | ✅ |
| VAL-005 | International characters in name | Field accepted (e.g., Müller, François) | ✅ |

### 10.2 Navigation Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| NAV-001 | Click "Weiter" on first section | Moves to second section | ✅ |
| NAV-002 | Click "Zurück" on second section | Returns to first section | ✅ |
| NAV-003 | Click answer in summary box | Navigates to corresponding question | ✅ |
| NAV-004 | Reset button click | Confirmation dialog shown | ✅ |
| NAV-005 | Confirm reset | All data cleared, return to first section | ✅ |

### 10.3 Save/Restore Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| SAV-001 | Save form data | Data stored in localStorage | ✅ |
| SAV-002 | Restore form data | Previous answers restored | ✅ |
| SAV-003 | Save base data | Only q0000-q0003 stored | ✅ |
| SAV-004 | Load base data | Master data populated | ✅ |
| SAV-005 | No saved data, try restore | "No saved data" message | ✅ |

### 10.4 Voice Input Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| VOI-001 | Click microphone button | Voice status indicator shown | ✅ |
| VOI-002 | Speak text clearly | Text inserted into field | ✅ |
| VOI-003 | No browser support | "Not available" alert | ✅ |
| VOI-004 | Language switch, then voice | Recognition uses new language | ✅ |

### 10.5 Language Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| LNG-001 | Switch to Arabic | RTL layout activated | ✅ |
| LNG-002 | Switch to German | LTR layout activated | ✅ |
| LNG-003 | Switch to Farsi | RTL layout activated | ✅ |
| LNG-004 | All 15 languages | UI fully translated | ✅ |

### 10.6 Export Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| EXP-001 | Export to JSON | Valid FHIR R5 QuestionnaireResponse | ✅ |
| EXP-002 | NFC export (< 4KB) | Data transferred successfully | ✅ |
| EXP-003 | NFC export (> 4KB) | Fallback message shown | ✅ |
| EXP-004 | Data wipe after export | All fields cleared | ✅ |

### 10.7 Security Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| SEC-001 | 10 min inactivity | Warning shown | ✅ |
| SEC-002 | 60s countdown expires | Data wiped | ✅ |
| SEC-003 | Continue button | Timer reset | ✅ |
| SEC-004 | No localStorage (default) | Data only in RAM | ✅ |

### 10.8 Accessibility Tests

| Test ID | Test Case | Expected Result | Status |
|---------|-----------|-----------------|--------|
| ACC-001 | High contrast mode | Enhanced contrast applied | ✅ |
| ACC-002 | Focus visible | Clear focus indicators | ✅ |
| ACC-003 | Touch targets | Min 54px size | ✅ |
| ACC-004 | Font size | 18px base | ✅ |

---

## 11. References

1. **FHIR R5 Questionnaire**: https://hl7.org/fhir/R5/questionnaire.html
2. **FHIR R5 QuestionnaireResponse**: https://hl7.org/fhir/R5/questionnaireresponse.html
3. **GDPR/DSGVO**: https://eur-lex.europa.eu/eli/reg/2016/679/oj
4. **NFC Forum NDEF**: https://nfc-forum.org/our-work/specification-releases/
5. **WCAG 2.1**: https://www.w3.org/WAI/WCAG21/quickref/
6. **MDR 2017/745**: https://eur-lex.europa.eu/eli/reg/2017/745/oj
7. **Web Speech API**: https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API

---

## 12. License

Proprietary - DiggAi GmbH - All rights reserved

---

*Document generated: 2025-12-05*
*Document version: 2.1.0*
