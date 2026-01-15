# SkyTravel

**SkyTravel** is a small airline management system providing an API backend and a React-based frontend to manage flights, passengers, reservations, boarding, and reporting. It includes features such as JWT-based auth, PDF ticket generation, QR code generation, email sending, role-based access, and a test suite.

---

## 🚀 Quick Overview

- **Backend:** Node.js, Express, MongoDB (Mongoose), JWT, PDF generation (`pdfkit`), QR (`qrcode`), email (`nodemailer`)
- **Frontend:** React, React Router, Material UI, Axios
- **Testing:** Jest, Supertest, mongodb-memory-server
- **License:** MIT

---

## 📁 Repository structure

- `backend/` - API server
  - `server.js` - Express app entrypoint
  - `src/` - application source
    - `controllers/` - request handlers
    - `models/` - Mongoose schemas (`User`, `Flight`, `Passenger`, `Reservation`)
    - `routes/` - route definitions
    - `services/` - `emailService`, `pdfService`, `qrService`
    - `config/database.js` - MongoDB connection
    - `tests/` - Jest tests
  - `scripts/createAdmin.js` - create admin & test users
  - `.env` - environment variables (not committed to VCS; see variables below)

- `frontend/` - React app
  - `src/` - UI components and contexts
  - `src/services/api.js` - API client wrapper (Axios)
  - `src/utils/constants.js` - routes, API base URL and roles

---

## ✅ Features

- User authentication (JWT)
- Role-based access control (roles: `passenger`, `agent_reservation`, `agent_embarquement`, `responsable_vols`, `admin`)
- Flight CRUD (protected endpoints for agents/responsibles/admin)
- Passenger management
- Create / search / cancel reservations
- Generate ticket PDF and QR code per reservation
- Send tickets via email
- Boarding validation via QR code
- Reports: occupancy, revenue, popular flights, dashboard stats
- Test coverage for core API flows (Jest + Supertest)

---

## 🛠️ Prerequisites

- Node.js (≥ 18 recommended)
- npm or yarn
- MongoDB (local or remote)

---

## 🧭 Setup & Run

### 1 Clone

```bash
git clone <repo-url>
cd <repo-folder>
```

### 2 Backend

1. Install dependencies

```bash
cd backend
npm install
```

2. Create a `.env` file in `backend/` (copy the provided example below and fill in secrets):

Required variables (examples, DO NOT commit secrets):

```
PORT=5000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/skytravel
JWT_EXPIRE=7d
EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_USER=eyabouazzi133@email.com
EMAIL_PASS=abcd efgh ijkl mnop
FRONTEND_URL=http://localhost:4200
ADMIN_EMAIL=admin@skytravel.com
ADMIN_PASSWORD=admin123
TEST_USER_PASSWORD=password123

```

3. Start server (dev or production):

```bash
# dev with nodemon (auto reload)
npm run dev

# production
npm start
```

> Note: `server.js` uses `process.env.PORT` (default in `.env`) — some files show `5002` as a fallback; ensure `PORT` matches your environment.

4. Create admin/test users (optional but useful for quick testing):

```bash
node scripts/createAdmin.js
```

5. Run backend tests

```bash
npm test
```

---

### 3 Frontend

1. Install dependencies

```bash
cd frontend
npm install
```

2. Start dev server

```bash
npm start
```

3. Build for production

```bash
npm run build
```

> The frontend expects the API base URL at `http://localhost:5000/api` by default (see `src/utils/constants.js`). Adjust if your backend runs on another port.

---

## 📡 API Overview (Main endpoints)

Base URL: `http://localhost:<PORT>/api`

- Auth
  - `POST /api/auth/register` - register a user
  - `POST /api/auth/login` - login (returns JWT)
  - `GET /api/auth/me` - get current user (protected)

- Flights
  - `GET /api/flights` - list flights (public)
  - `GET /api/flights/:id` - get flight details
  - `POST /api/flights` - create flight (protected: `agent_reservation`, `responsable_vols`, `admin`)
  - `PUT /api/flights/:id` - update flight (protected)
  - `DELETE /api/flights/:id` - delete flight (protected)

- Passengers (protected: reservation/boarding roles)
  - `GET /api/passengers`
  - `POST /api/passengers`
  - `GET /api/passengers/:id`
  - `PUT /api/passengers/:id`
  - `DELETE /api/passengers/:id`
  - `GET /api/passengers/passport/:passportNumber`

- Reservations
  - `GET /api/reservations` - list
  - `POST /api/reservations` - create (agent role required to create on behalf; passengers can create their own via frontend)
  - `GET /api/reservations/:id` - get
  - `PUT /api/reservations/:id/cancel` - cancel
  - `PUT /api/reservations/:id/checkin` - check-in
  - `GET /api/reservations/search/:bookingReference` - public search by booking reference

- Boarding
  - `POST /api/boarding/validate` - validate boarding by QR
  - `GET /api/boarding/flight/:flightId` - get boarding list
  - `PUT /api/boarding/flight/:flightId/status` - update flight status

- Reports (protected: `responsable_vols`, `admin`)
  - `GET /api/reports/occupancy`
  - `GET /api/reports/revenue`
  - `GET /api/reports/popular-flights`
  - `GET /api/reports/dashboard`

- Users (admin)
  - `GET /api/users`
  - `GET /api/users/:id`
  - `PUT /api/users/:id/role`
  - `PUT /api/users/:id/active`
  - `DELETE /api/users/:id`

---

## 🔒 Auth & Roles

- Auth uses JWT in `Authorization: Bearer <token>` header.
- Roles available: `passenger`, `agent_reservation`, `agent_embarquement`, `responsable_vols`, `admin`.
- Role checks are implemented in middleware (`src/middlewares/auth.js`).

---

## 🧪 Testing

- Backend: Jest + Supertest. Tests are in `backend/src/tests` and use `mongodb-memory-server` for an in-memory DB. Run with:

```bash
cd backend
npm test
```

- Frontend: React testing library (run `npm test` in `frontend/`).

---

## 📦 Deployment notes

- Ensure environment variables are set on the server (Mongo URI, JWT secret, email creds).
- Build frontend (`npm run build`) and serve static files via a static host or reverse proxy (Nginx, Vercel, Netlify, etc.).
- Upload folders (`uploads/tickets`, `uploads/qrcodes`) must be writable by the server process or stored in object storage (S3) for production setups.

---

## 🧭 Troubleshooting & Tips

- If emails fail to send, confirm SMTP settings (`EMAIL_HOST`, `EMAIL_PORT`, `EMAIL_USER`, `EMAIL_PASS`) and allow less-secure apps if using services like Gmail or set up application-specific passwords.
- If uploads or ticket generation fail, ensure the `uploads/` directory exists and the process has permission to write files.
- For CORS issues, check `server.js` CORS configuration and `FRONTEND_URL` variable.

---

## 🤝 Contributing

Feel free to open issues or PRs. Keep changes well-scoped and include tests for significant logic.

---

## 📄 License

This project uses the **MIT License**. See `backend/package.json` for metadata.

---
