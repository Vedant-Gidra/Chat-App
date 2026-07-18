# Deployment Guide — Chat App (Split Architecture)

---

## Architecture Overview

```
Browser
  ├── Vercel (chat-app.vercel.app)
  │       └── React Static Build (frontend)
  │
  └── Railway / Render (api.your-app.com)
          ├── /api/*        → REST API (auth, chats, messages)
          ├── /socket.io    → WebSocket (real-time messaging)
          └── /health       → Health check endpoint

MongoDB Atlas (cloud database)
```

Frontend and backend are deployed as independent services. This gives you:
- Independent scaling and deployments
- Faster frontend loads via global CDN
- Separate CI/CD pipelines
- Better separation of concerns

---

## Prerequisites

1. **GitHub Repository** — Push your code to GitHub.

2. **MongoDB Atlas** — A cloud MongoDB instance.
   - Go to [mongodb.com/atlas](https://www.mongodb.com/atlas)
   - Create a free cluster (M0) for dev, M10+ for production
   - Create a database user (username + password)
   - Go to **Network Access** → Add IP `0.0.0.0/0` (allows access from anywhere)
   - Go to **Database** → Click **Connect** → Choose **Drivers** → Copy the connection string
   - It looks like: `mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/<dbname>?retryWrites=true&w=majority`

---

## Part 1 — Deploy the Backend (Railway or Render)

The backend needs a persistent process for Socket.IO WebSocket connections. Serverless platforms (Lambda, Vercel Functions) are not suitable here.

### Option A: Railway (Recommended)

1. Go to [railway.app](https://railway.app) and sign up with GitHub
2. Click **New Project** → **Deploy from GitHub Repo**
3. Select your Chat-App repository

#### Configure Settings

| Setting              | Value                      |
|----------------------|----------------------------|
| **Root Directory**   | `/` (project root)         |
| **Build Command**    | `npm install`              |
| **Start Command**    | `npm start`               |

#### Environment Variables

Add these in the Railway dashboard under **Variables**:

| Variable           | Value                                      |
|--------------------|--------------------------------------------|
| `MONGO_URI`        | Your MongoDB Atlas connection string       |
| `JWT_SECRET`       | A strong random string                     |
| `NODE_ENV`         | `production`                               |
| `FRONTEND_URL`     | Your Vercel frontend URL (set after Part 2)|

> Railway auto-assigns `PORT` — don't set it manually.

#### Custom Domain (Optional)

In Railway's **Settings** → **Networking**, you can add a custom domain like `api.your-app.com`.

---

### Option B: Render

1. Go to [render.com](https://render.com) and sign up
2. Click **New** → **Web Service**
3. Connect your GitHub repo and select the branch

#### Configure Settings

| Setting            | Value           |
|--------------------|-----------------|
| **Runtime**        | Node            |
| **Build Command**  | `npm install`   |
| **Start Command**  | `npm start`     |
| **Plan**           | Free or Starter ($7/mo for always-on) |

#### Environment Variables

Same as Railway above.

> On Render's free tier, the service sleeps after 15 min of inactivity.

---

## Part 2 — Deploy the Frontend (Vercel)

### Step 1 — Create a Vercel Account

Go to [vercel.com](https://vercel.com) and sign up with GitHub.

### Step 2 — Import Project

1. Click **Add New** → **Project**
2. Import your GitHub repository
3. Set the **Root Directory** to `frontend`

### Step 3 — Configure Build Settings

| Setting              | Value                          |
|----------------------|--------------------------------|
| **Framework Preset** | Create React App               |
| **Build Command**    | `npm run build`                |
| **Output Directory** | `build`                        |
| **Install Command**  | `npm install --legacy-peer-deps` |

### Step 4 — Environment Variables

Add in the Vercel dashboard:

| Variable              | Value                                         |
|-----------------------|-----------------------------------------------|
| `REACT_APP_API_URL`   | Your backend URL (e.g. `https://your-app.up.railway.app`) |

### Step 5 — Deploy

Click **Deploy**. Vercel will build and serve your React app on a global CDN.

Your frontend will be live at: `https://your-app.vercel.app`

---

## Part 3 — Code Changes Required

### 3.1 — Backend: Add CORS and Health Check

Update `backend/server.js`:

```javascript
// Add at the top with other requires
const cors = require("cors");

// After app initialization
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true,
}));

// Health check endpoint
app.get("/health", (req, res) => {
  res.status(200).json({ status: "ok" });
});
```

Install the cors package:

```bash
npm install cors
```

Update Socket.IO CORS config:

```javascript
const io = require("socket.io")(server, {
  pingTimeout: 60000,
  cors: {
    origin: process.env.FRONTEND_URL,
    credentials: true,
  },
});
```

Remove the static file serving block (the `if (process.env.NODE_ENV === "production")` section that serves the React build) since the frontend is deployed separately now.

### 3.2 — Frontend: Use API Base URL

Create or update `frontend/src/config/api.js`:

```javascript
const API_URL = process.env.REACT_APP_API_URL || "http://localhost:5000";
export default API_URL;
```

Update all Axios calls to use the base URL:

```javascript
import API_URL from "../config/api";

// Before
const { data } = await axios.get("/api/chat", config);

// After
const { data } = await axios.get(`${API_URL}/api/chat`, config);
```

Update Socket.IO client connection:

```javascript
import API_URL from "../config/api";

const socket = io(API_URL);
```

### 3.3 — Remove Proxy

Remove the `"proxy"` field from `frontend/package.json` — it's no longer needed since you're using explicit API URLs.

---

## Part 4 — Post-Deployment Checklist

- [ ] Backend is running and `/health` returns `{ "status": "ok" }`
- [ ] Frontend loads and can reach the backend API
- [ ] WebSocket connection establishes (check browser console for "Connected to socket.io")
- [ ] User registration and login work
- [ ] Real-time messaging works between two browser tabs
- [ ] Set `FRONTEND_URL` env var on backend to your actual Vercel URL
- [ ] (Optional) Add custom domains to both services

---

## CI/CD Pipeline

Both Vercel and Railway/Render auto-deploy on push to your main branch. For a more robust pipeline:

1. **Branch protection** — Require PR reviews before merging to `main`
2. **Preview deployments** — Vercel creates preview URLs for every PR automatically
3. **Environment separation** — Use separate env vars for staging vs production
4. **Monitoring** — Add error tracking (Sentry) and uptime monitoring (UptimeRobot)

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| CORS errors in browser console | Verify `FRONTEND_URL` env var matches your exact Vercel URL (no trailing slash) |
| WebSocket fails to connect | Check that backend CORS allows your frontend origin |
| Build fails with OpenSSL error | Already handled — `--openssl-legacy-provider` flag is in frontend scripts |
| MongoDB connection timeout | Whitelist `0.0.0.0/0` in Atlas Network Access |
| `404` on page refresh (Vercel) | Add a `vercel.json` with rewrites (see below) |
| `legacy-peer-deps` errors | Set install command to `npm install --legacy-peer-deps` in Vercel |

### Vercel Rewrites (for client-side routing)

Create `frontend/vercel.json`:

```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/index.html" }
  ]
}
```

---

## Cost Breakdown

| Service         | Free Tier                        | Paid (Production)        |
|-----------------|----------------------------------|--------------------------|
| **Vercel**      | 100GB bandwidth/mo, auto-SSL     | $20/mo (Pro)             |
| **Railway**     | $5 free credit/mo                | ~$5-20/mo usage-based    |
| **Render**      | Free (sleeps after 15min)        | $7/mo (Starter)          |
| **MongoDB Atlas**| M0 free (512MB)                 | M10 ~$57/mo (dedicated)  |

For a side project or portfolio piece, the free tiers of all three services are sufficient.

---

## Security Considerations

- Never commit `.env` files — use platform env vars
- Use strong, unique values for `JWT_SECRET`
- Enable MongoDB Atlas audit logging for production
- Set up rate limiting on the backend API (`express-rate-limit`)
- Consider adding Helmet.js for HTTP security headers