# Finora System Architecture & Component Design

## 🏛️ High-Level Architectural Flow

```
[ Browser / Client ] 
        │
   (HTTPS / JSON)
        │
        ▼
[ Node.js + Express Server ] ── (Passport JWT Auth) ── [ Auth Middleware ]
        │
        ├─────────► [ MongoDB / Mongoose ODM ] (Transactions, Users, Reports)
        ├─────────► [ Google Gemini AI Vision ] (Receipt OCR Scanning)
        ├─────────► [ Resend Email API ] (Monthly Statement Delivery)
        ├─────────► [ Cloudinary SDK ] (User Avatar Storage)
        └─────────► [ node-cron Engine ] (Background Automation Jobs)
```

---

## 🗄️ Database Schemas & Models

1. **User Model (`User`)**:
   - `name`, `email`, `password` (bcrypt hashed), `profilePicture` (Cloudinary URL).
2. **Transaction Model (`Transaction`)**:
   - `userId`, `title`, `amount`, `category`, `type` (`INCOME`/`EXPENSE`), `date`, `isRecurring`, `recurringInterval`.
3. **Report Model (`Report`)**:
   - `userId`, `period`, `sentAt`, `fileUrl`, `status`.
4. **Report Setting Model (`ReportSetting`)**:
   - `userId`, `frequency`, `email`, `isEnabled`.

---

## 🔄 Automation Cron Engine Flow

```
[ node-cron Scheduler ]
        │
        ├─► Every Minute / Day: Scans recurring transactions and generates next transaction instance.
        └─► Monthly Cron: Compiles aggregation statement, converts HTML template, and dispatches email via Resend.
```

---

## 🚀 Deployment Checklist

- [x] Configure production environment variables in `.env`
- [x] Set up MongoDB Atlas connection string
- [x] Configure Google Gemini AI Vision key
- [x] Set up Resend verified domain for email sending
- [x] Set up Cloudinary account keys
- [x] Validate production build with `npm run build`
