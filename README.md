# Dental Clinic Automation

A full-stack AI automation system that runs the entire patient lifecycle at a dental clinic — from the moment a lead comes in to the weekly management report. Built with **n8n** and **OpenAI GPT-5.2**, the system replaces manual front-desk work with 7 interconnected workflows that handle intake, scheduling, reminders, follow-ups, reviews, no-show recovery, and reporting.

> **110+ automation nodes · 7 production workflows · Zero manual intervention**

---

## What It Does

### 1. Captures Leads From Every Channel

New patients arrive through three different entry points — a **website form**, **Meta/Facebook ad leads**, and **missed phone calls**. Each source triggers a webhook that feeds into a single unified pipeline. The system normalizes the data, deduplicates against existing records (merging if found), and stores everything in a central Google Sheet.

### 2. Qualifies Leads With AI

Every incoming lead is scored by **OpenAI GPT-5.2**. The AI evaluates the patient's needs, urgency, and fit based on the intake data, then assigns a qualification score (0–100) with reasoning notes. Staff receive an instant email notification with the AI assessment so they know exactly who to prioritize.

### 3. Books Appointments via Patient-Facing Form

Qualified leads are directed to a booking form (built with n8n Form Trigger). The system checks slot availability against existing appointments, creates the booking in both **Google Sheets** and **Google Calendar**, and sends an AI-personalized confirmation email to the patient. WhatsApp confirmation is pre-built and ready to enable.

### 4. Sends Smart Reminders & Manages the Waitlist

Once an appointment is booked, the system automatically schedules a **2-hour pre-appointment reminder**. Patients can confirm or cancel via one-click webhook links. If a patient cancels, the system immediately checks the waitlist and offers the now-open slot to the next person in queue — fully automated.

### 5. Requests Reviews After Visits

One hour after a completed appointment, the patient receives an **AI-personalized review request email**. The message is tailored to their specific visit details (procedure, duration, dentist) to maximize response rates and feel genuinely personal, not templated.

### 6. Recovers No-Shows & Reactivates Dormant Patients

Two automated campaigns run on schedules:
- **Daily**: Scans for no-shows from the previous day and sends AI-crafted reschedule emails
- **Weekly**: Identifies patients who haven't visited in **6+ months** and sends reactivation outreach to bring them back

### 7. Delivers Weekly Management Reports

Every Monday morning, the system pulls all lead and appointment data, calculates KPIs (conversion rates, booking volume, no-show rates, revenue trends), and has **OpenAI generate actionable business insights**. The report is sent as a formatted HTML email to the clinic manager.

### 8. Self-Monitors With an Error Handler

A centralized error handler catches failures across all workflows. When something breaks, it logs the error to Google Sheets, runs **AI-powered root cause analysis** on the failure, and sends a diagnostic alert to staff — so issues get caught and explained before they snowball.

---

## Architecture

```
  Patient Touchpoints                         Clinic Operations
  ─────────────────                           ─────────────────

  Website Form ──┐
  Meta/FB Ads ───┤──▶ LEAD INTAKE ──▶ AI QUALIFICATION ──▶ Staff Alert
  Missed Calls ──┘         │
                           ▼
                    APPOINTMENT BOOKING ──▶ Google Calendar Sync
                           │
                    ┌──────┼──────┐
                    ▼      ▼      ▼
              REMINDERS  WAITLIST  CONFIRMATION
              (2h prior)  (auto)   (AI email + WhatsApp)
                    │
                    ▼
             VISIT COMPLETED ──▶ AI REVIEW REQUEST (1h post)
                    │
              ┌─────┴─────┐
              ▼           ▼
         NO-SHOW      INACTIVE 6mo+
        RESCHEDULE     REACTIVATION
        (daily)        (weekly)

              ──── WEEKLY KPI REPORT ────
              (every Monday, AI insights)

         ┌────── ERROR HANDLER ──────┐
         │  monitors all workflows   │
         │  AI diagnosis + alerts    │
         └───────────────────────────┘
```

---

## Tech Stack

| Component | Technology | Role |
|-----------|-----------|------|
| Automation Engine | **n8n** | Orchestrates all workflows, webhooks, and scheduling |
| AI Layer | **OpenAI GPT-5.2** | Lead qualification, email personalization, report insights, error diagnosis |
| Data Store | **Google Sheets** | Leads, appointments, waitlist, error logs ([schema →](docs/GOOGLE_SHEETS_SCHEMA.md)) |
| Calendar | **Google Calendar** | Staff appointment sync |
| Email | **SMTP** | All patient and staff communications |
| Messaging | **WhatsApp Business** | Booking confirmations (pre-built, enable when ready) |
| Triggers | **Webhooks** | Multi-channel lead intake and patient actions |

---

## Repository Structure

```
├── workflows/                        # n8n workflow definitions (import into n8n)
│   ├── DENTAL_0_Error_Handler.json
│   ├── DENTAL_1_Lead_Intake_Booking.json
│   ├── DENTAL_2_Reminders_Waitlist.json
│   ├── DENTAL_3_Review_Request.json
│   ├── DENTAL_4_No_Show_Reschedule_Patient_Reactivation.json
│   ├── DENTAL_5_Management_Reporting.json
│   ├── DENTAL_6_Appointment_Scheduling_Form.json
│   └── DENTAL_Test_Harness.json
├── docs/
│   └── GOOGLE_SHEETS_SCHEMA.md       # Data model documentation
├── .env.example                      # Required credentials template
├── .gitignore
├── LICENSE
└── README.md
```

---

## Getting Started

### Prerequisites

- [n8n](https://n8n.io) instance (cloud or self-hosted)
- OpenAI API key
- Google Cloud project with Sheets + Calendar APIs enabled
- SMTP email account (Gmail App Password works)
- *(Optional)* WhatsApp Business API credentials

### Setup

1. **Clone**
   ```bash
   git clone https://github.com/Cubeedge180/Dental-Clinic-Automation.git
   cd Dental-Clinic-Automation
   ```

2. **Configure credentials**
   ```bash
   cp .env.example .env
   # Fill in your API keys and credentials
   ```

3. **Prepare Google Sheets**
   - Create a new Google Spreadsheet
   - Add tabs: `Leads`, `Appointments`, `Waitlist`, `Error_Log`
   - Follow the schema in [`docs/GOOGLE_SHEETS_SCHEMA.md`](docs/GOOGLE_SHEETS_SCHEMA.md)

4. **Import workflows into n8n**
   - Go to **Workflows → Import from File**
   - Import each file from `workflows/` in order (0 → 6)
   - Set up credentials (OpenAI, Google Sheets, Google Calendar, SMTP) in n8n
   - Update webhook URLs to match your n8n instance

5. **Activate**
   - Enable `DENTAL 0 - Error Handler` first (always on)
   - Activate workflows 1–6
   - Run `DENTAL_Test_Harness` to validate the pipeline end-to-end

---

## Bilingual Support

All patient-facing emails support **English** and **Romanian**, automatically selected based on the lead's language preference captured during intake.

---

## License

MIT — see [LICENSE](LICENSE) for details.
