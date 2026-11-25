# Seva Tracker

Login with username/password, then Punch In / Punch Out for Seva. Each session records
**Seva Place** (Home or Satsang Hall) and the duration is tracked per user.

## Structure

- `backend/` — Node.js + Express + PostgreSQL (via Prisma) API, JWT auth
- `web/` — React (Vite) web app
- `mobile/` — React Native (Expo) mobile app

Both the web and mobile apps call the same backend API.

## 1. Backend setup

```bash
cd backend
npm install
cp env.example.txt .env   # then edit .env with your PostgreSQL connection string and a JWT secret
npx prisma migrate dev --name init   # creates tables in your database
npm run dev                          # starts API on http://localhost:4000
```

You need a running PostgreSQL instance. Easiest local option:

```bash
docker run --name seva-postgres -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=seva_tracker -p 5432:5432 -d postgres
```

### API endpoints

| Method | Path                | Auth | Body                              |
|--------|---------------------|------|------------------------------------|
| POST   | /api/auth/register  | no   | `{ username, password }`           |
| POST   | /api/auth/login     | no   | `{ username, password }`           |
| GET    | /api/seva/status    | yes  | —                                   |
| POST   | /api/seva/punch-in  | yes  | `{ sevaPlace: "HOME"\|"SATSANG_HALL" }` |
| POST   | /api/seva/punch-out | yes  | —                                   |
| GET    | /api/seva/history   | yes  | —                                   |
| GET    | /api/seva/summary   | yes  | —                                   |

Authenticated requests need `Authorization: Bearer <token>`.

## 2. Web app

```bash
cd web
npm install
npm run dev   # http://localhost:5173, proxies /api to the backend
```

## 3. Mobile app

```bash
cd mobile
npm install
npm start     # opens Expo dev tools; scan the QR code with Expo Go
```

Edit `BASE_URL` in `mobile/src/api.js` to your computer's LAN IP (not `localhost`) when
testing on a physical device, e.g. `http://192.168.1.50:4000/api`.
