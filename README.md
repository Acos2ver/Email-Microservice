# Email-Microservice
Email confirmation microservice with SMTP integration

# 📧 Email Microservice

A Flask-based microservice for sending immediate and scheduled emails with SMTP integration.

## Features

-  **Immediate Email Sending** - Send emails instantly via SMTP
-  **Scheduled Emails** - Queue emails for future delivery
-  **HTML Support** - Rich HTML email templates
-  **Email Logging** - Track all sent emails in SQLite
-  **Background Scheduler** - Automatic processing of scheduled emails
-  **CORS Enabled** - Ready for frontend integration

---

## Quick Start

### Prerequisites

- Python 3.9+
- Gmail account with App Password

### Installation
```bash
# Clone repository
git clone https://github.com/yourusername/email-microservice.git
cd email-microservice

# Create virtual environment
python -m venv .venv

# Activate virtual environment
# Windows:
.\.venv\Scripts\Activate.ps1
# macOS/Linux:
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

### Configuration

Create `.env` file:
```properties
DATABASE_URL="sqlite:///email.db"
EMAIL="your-email@gmail.com"
SMTP_PASS="your-app-password"
SMTP_SERVER="smtp.gmail.com"
SMTP_PORT="587"
PORT=5002
CORS_ORIGINS="http://localhost:5173"
```

> **Note:** Get Gmail App Password from [Google Account Settings](https://support.google.com/accounts/answer/185833)

### Run
```bash
python app.py
```

Server runs at `http://localhost:5002`

---

## 📡 API Endpoints

### `POST /send-email`
Send immediate email

**Request:**
```json
{
  "program_key": "my-app",
  "recipients": ["user@example.com"],
  "subject_line": "Hello",
  "body": "<h1>Welcome!</h1>",
  "is_html": true
}
```

**Response (200):**
```json
{
  "status": "success",
  "message": "Email sent successfully",
  "statusCode": 200
}
```

---

### `POST /send-timed-email`
Schedule email for later

**Request:**
```json
{
  "program_key": "my-app",
  "recipients": ["user@example.com"],
  "subject_line": "Reminder",
  "body": "Your appointment is tomorrow",
  "is_html": false,
  "time_to_send": "14:30",
  "date_to_send": "2025-11-15"
}
```

**Response (201):**
```json
{
  "status": "success",
  "message": "Timed email created successfully",
  "details": {
    "schedule_id": "abc123...",
    "recipients": ["user@example.com"]
  },
  "statusCode": 201
}
```

---

### `GET /check-scheduled-email/<schedule_id>`
Check scheduled email status

**Response (200):**
```json
{
  "status": "success",
  "email_status": "sent",
  "scheduled_time": "2025-11-15T14:30:00+00:00",
  "sent_at": "2025-11-15T14:30:05+00:00",
  "statusCode": 200
}
```

---

### `GET /health`
Health check endpoint

**Response (200):**
```json
{
  "status": "ok",
  "service": "email-microservice"
}
```

---

## Testing

Test SMTP connection:
```bash
python send_test_email.py
```

Test API with curl:
```bash
# Send email
curl -X POST http://localhost:5002/send-email \
  -H "Content-Type: application/json" \
  -d '{
    "program_key": "test",
    "recipients": ["your@email.com"],
    "subject_line": "Test",
    "body": "Hello World",
    "is_html": false
  }'
```

---

## Project Structure
```
email-microservice/
├── app.py              # Main Flask application
├── database.py         # Database configuration
├── email_sender.py     # SMTP email sending logic
├── models.py           # SQLAlchemy models
├── scheduler.py        # Background email scheduler
├── send_test_email.py  # SMTP testing script
├── requirements.txt    # Python dependencies
└── .env               # Environment variables (not in repo)
```

---

## Tech Stack

- **Flask 3.0** - Web framework
- **SQLAlchemy 2.0** - ORM
- **SQLite** - Database
- **smtplib** - Email sending
- **Threading** - Background scheduler

---

## Database Schema

### EmailLog
```python
id: Integer (Primary Key)
program_key: String
recipients: Text (comma-separated)
subject_line: String
body: Text
is_html: Boolean
status: String (sent/failed)
status_code: Integer
created_at: DateTime
sent_at: DateTime
```

### ScheduledEmail
```python
id: Integer (Primary Key)
schedule_id: String (Unique)
program_key: String
recipients: Text
subject_line: String
body: Text
is_html: Boolean
scheduled_time: DateTime
status: String (scheduled/sent/failed)
created_at: DateTime
sent_at: DateTime
```


## Troubleshooting

**Email not sending?**
1. Check SMTP credentials in `.env`
2. Enable Gmail App Password
3. Test with: `python send_test_email.py`

**CORS errors?**
- Update `CORS_ORIGINS` in `.env`
- Restart server

**Database errors?**
```bash
# Delete and recreate
rm email.db
python app.py
```


## Security Notes

- Never commit `.env` file
- Use App Passwords, not account passwords
- Keep `JWT_SECRET` secure
- Use HTTPS in production


## License

MIT License - see [LICENSE](LICENSE) file

