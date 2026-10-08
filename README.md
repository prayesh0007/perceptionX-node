# PerceptionX -- Node Service

Express API + React (Vite) frontend for PerceptionX. Handles authentication, file intake, storage (MongoDB + Cloudinary), real-time progress via Socket.IO, and serves the React SPA. Delegates object detection to the [perceptionX-python](../perceptionX-python) service.

Deployed to **Render**.

## Stack

- Express, Mongoose, Socket.IO, Multer, JWT auth (`jsonwebtoken` + `bcryptjs`), Cloudinary SDK
- React 18 + Vite + React Router, Tailwind CSS, Chart.js/Recharts, Three.js (landing page), Socket.IO client

## Project structure

```
perceptionX-node/
├── app.js                  # Express server entry point
├── middleware/auth.js       # JWT authentication middleware
├── models/                  # Mongoose schemas (User, File)
├── routes/auth.js           # Auth routes
├── utils/                   # Analytics computation (per service type)
├── scripts/                 # One-off maintenance scripts (env/cloudinary checks)
├── public/react-build/      # React production build output (generated, not source)
└── client/                  # React frontend source (Vite)
```

## Running locally

Requires Node 20+, MongoDB, and (optionally) a running [perceptionX-python](../perceptionX-python) instance for detection to work end-to-end.

```bash
npm install
npm run dev
```

This single command starts the Express API (with `nodemon`, port 3000) and the Vite dev server (port 5173) together. Open `http://localhost:5173` -- Vite proxies `/api`, `/process`, `/file`, `/progress`, and `/socket.io` to the Express server (see `client/vite.config.js`).

### Other scripts

| Command | Purpose |
|---------|---------|
| `npm run dev` | Start backend + frontend together for local development (the only command you need) |
| `npm start` | Start Express only, serving the pre-built React app from `public/react-build/` (production) |
| `npm run build` | Build the React app into `public/react-build/` |
| `npm run check:env` | Verify `.env` / Cloudinary variables are loading correctly |
| `npm run check:cloudinary` | Print current Cloudinary storage usage |
| `npm run cleanup:cloudinary` | Delete all resources in the configured Cloudinary account (destructive) |

## Environment variables

| Variable | Purpose |
|----------|---------|
| `MONGO_URI` | **Required.** MongoDB connection string |
| `JWT_SECRET` | Signing key for auth tokens |
| `PYTHON_API_URL` | Base URL of the `perceptionX-python` service (e.g. `http://localhost:8001`) |
| `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET` | CDN storage for videos/large files |
| `PORT` | HTTP port (default `3000`) |
| `PY_TIMEOUT` | Minutes to wait for Python processing before timing out (default `20`) |
| `NODE_ENV` | Set to `production` on Render |

See [DEPLOYMENT.md](DEPLOYMENT.md) for deployment steps.
