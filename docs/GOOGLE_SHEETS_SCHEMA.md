# Google Sheets Data Schema

All workflows share a single Google Spreadsheet with the following tabs:

## Tab: `Leads`

| Column | Type | Description |
|--------|------|-------------|
| `lead_id` | String | Unique ID (auto-generated) |
| `name` | String | Patient full name |
| `email` | String | Patient email |
| `phone` | String | Phone number |
| `source` | String | `webform` / `meta` / `missed_call` |
| `language` | String | `EN` / `RO` |
| `status` | String | `new` / `qualified` / `booked` / `no_show` / `inactive` |
| `ai_score` | Number | AI qualification score (0–100) |
| `ai_notes` | String | AI qualification reasoning |
| `created_at` | DateTime | Lead creation timestamp |
| `updated_at` | DateTime | Last update timestamp |

## Tab: `Appointments`

| Column | Type | Description |
|--------|------|-------------|
| `appointment_id` | String | Unique ID |
| `lead_id` | String | Reference to Leads tab |
| `date` | Date | Appointment date |
| `time` | Time | Appointment time |
| `status` | String | `booked` / `confirmed` / `completed` / `cancelled` / `no_show` |
| `reminder_2h_sent` | Boolean | Whether 2h reminder was sent |
| `reschedule_sent` | Boolean | Whether reschedule email was sent |
| `review_sent` | Boolean | Whether review request was sent |
| `created_at` | DateTime | Booking timestamp |

## Tab: `Waitlist`

| Column | Type | Description |
|--------|------|-------------|
| `lead_id` | String | Reference to Leads tab |
| `preferred_date` | Date | Preferred date |
| `preferred_time` | Time | Preferred time slot |
| `notified` | Boolean | Whether slot offer was sent |

## Tab: `Error_Log`

| Column | Type | Description |
|--------|------|-------------|
| `timestamp` | DateTime | When the error occurred |
| `workflow_name` | String | Which workflow failed |
| `node_name` | String | Which node failed |
| `error_message` | String | Error details |
| `ai_diagnosis` | String | AI root cause analysis |
| `resolved` | Boolean | Whether it was resolved |
