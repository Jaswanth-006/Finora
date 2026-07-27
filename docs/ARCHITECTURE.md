# Finora System Architecture & Component Design

## 🏛️ High-Level System Architecture (Mermaid)

```mermaid
graph TD
    subgraph ClientLayer ["Client Layer (React 18 + Vite + Redux)"]
        UI["React Single Page Application"]
        RTK["RTK Query / Redux Store"]
        UI <--> RTK
    end

    subgraph APILayer ["Backend Server Layer (Node.js + Express)"]
        Router["Express Router (/api/v1)"]
        AuthMiddleware["Passport JWT Middleware"]
        Controller["Controllers (Auth, Transaction, Analytics, Report)"]
        Service["Services Layer (Business Logic)"]
        
        Router --> AuthMiddleware --> Controller --> Service
    end

    subgraph DB [Database Layer]
        MongoDB[(MongoDB Database)]
    end

    subgraph Integrations [External AI & Cloud Services]
        GeminiAI["Google Gemini 1.5 Vision AI"]
        CloudinaryCDN["Cloudinary Asset Storage"]
        ResendEmail["Resend Email API Provider"]
    end

    RTK <== HTTP REST / JSON ==> Router
    Service <--> MongoDB
    Service <--> GeminiAI
    Service <--> CloudinaryCDN
    Service <--> ResendEmail
```

---

## 🤖 AI Receipt OCR Sequence Flow (Mermaid)

```mermaid
sequenceDiagram
    autonumber
    actor User as User / Client
    participant Frontend as React Client UI
    participant Backend as Express API Server
    participant Gemini as Google Gemini 1.5 AI
    participant Cloudinary as Cloudinary CDN

    User->>Frontend: Uploads Receipt Image
    Frontend->>Backend: POST /api/v1/transaction/scan-receipt (Multer File)
    Backend->>Cloudinary: Upload Receipt Image File
    Cloudinary-->>Backend: Return Secure CDN Image URL
    Backend->>Backend: Convert Image Buffer to Base64
    Backend->>Gemini: generateContent(Base64 Image + System Prompt)
    Gemini-->>Backend: Returns Raw Structured JSON String
    Backend->>Backend: Clean & Parse JSON (Title, Amount, Date, Category)
    Backend-->>Frontend: Return Extracted Transaction Payload
    Frontend->>User: Auto-populates Transaction Form Drawer
```

---

## 🔄 Cron Automation Engine Architecture (Mermaid)

```mermaid
flowchart TD
    Start([node-cron Scheduler]) --> BranchA{Task Type}

    BranchA -->|Daily Cron Job| RecurrJob[Process Recurring Transactions]
    RecurrJob --> QueryRecurr[(Query Transactions: isRecurring = true & nextRecurringDate <= NOW)]
    QueryRecurr --> LoopRecurr[Iterate Over Matching Transactions]
    LoopRecurr --> CreateNext[Create New Transaction Instance]
    CreateNext --> AdvanceDate[Advance nextRecurringDate to Next Interval]

    BranchA -->|Monthly 1st Cron Job| ReportJob[Process Monthly Reports]
    ReportJob --> QuerySettings[(Query ReportSettings: isEnabled = true & nextReportDate <= NOW)]
    QuerySettings --> AggregateData[MongoDB Aggregation Pipeline: Calculate Total Income & Expenses]
    AggregateData --> GenAI[Google Gemini AI: Generate Financial Insights]
    GenAI --> CompileEmail[Compile Handlebars HTML Template]
    CompileEmail --> SendResend[Send Email via Resend API]
    SendResend --> SaveHistory[(Save Report Model Record & Update nextReportDate)]
```

---

## 🗄️ Database Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    User ||--o{ Transaction : "owns"
    User ||--o{ Report : "receives"
    User ||--|| ReportSetting : "configures"

    User {
        ObjectId _id PK
        string name
        string email UK
        string password
        string profilePicture
        date createdAt
        date updatedAt
    }

    Transaction {
        ObjectId _id PK
        ObjectId userId FK
        string title
        number amount
        string type "INCOME | EXPENSE"
        string category
        string paymentMethod
        date date
        boolean isRecurring
        string recurringInterval "DAILY | WEEKLY | MONTHLY | YEARLY"
        date nextRecurringDate
        date lastProcessed
        date createdAt
        date updatedAt
    }

    Report {
        ObjectId _id PK
        ObjectId userId FK
        date sentDate
        string period
        string status "SENT | FAILED | NO_ACTIVITY"
        date createdAt
        date updatedAt
    }

    ReportSetting {
        ObjectId _id PK
        ObjectId userId FK
        string frequency "MONTHLY"
        date lastSentDate
        date nextReportDate
        boolean isEnabled
        date createdAt
        date updatedAt
    }
```
