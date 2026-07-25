# Finora API Specification & Endpoint Reference

Base Path: `/api/v1`

---

## 🔐 1. Authentication Endpoints (`/auth`)

### `POST /auth/register`
Creates a new user account.
- **Request Body**:
  ```json
  {
    "name": "John Doe",
    "email": "john@example.com",
    "password": "Password123!"
  }
  ```
- **Response** (`201 Created`):
  ```json
  {
    "message": "User registered successfully",
    "data": { "userId": "60d...123" }
  }
  ```

### `POST /auth/login`
Authenticates a user and issues a JWT token.
- **Request Body**:
  ```json
  {
    "email": "john@example.com",
    "password": "Password123!"
  }
  ```
- **Response** (`200 OK`):
  ```json
  {
    "message": "Login successful",
    "token": "eyJhbG..."
  }
  ```

---

## 👤 2. User Profile Endpoints (`/user`)

### `GET /user/profile`
Retrieves authenticated user profile details.

### `PUT /user/profile`
Updates user profile information and avatar photo.

---

## 💸 3. Transaction Endpoints (`/transaction`)

### `GET /transaction/all`
Fetches paginated transactions with search and date range filters.

### `POST /transaction/create`
Creates a new transaction record.

### `PUT /transaction/update/:id`
Updates an existing transaction.

### `DELETE /transaction/delete/:id`
Deletes a specific transaction.

### `POST /transaction/scan-receipt`
Uses Google Gemini AI to extract transaction details from receipt images.

### `POST /transaction/import-csv`
Bulk imports transaction records from a CSV file.

---

## 📊 4. Analytics Endpoints (`/analytics`)

### `GET /analytics/summary`
Calculates total income, total expense, and net balance.

### `GET /analytics/chart`
Provides expense pie chart data and line chart time-series values.

---

## 📄 5. Reports Endpoints (`/report`)

### `GET /report/settings`
Gets user automated email report preferences.

### `PUT /report/settings`
Updates monthly email report schedules.
