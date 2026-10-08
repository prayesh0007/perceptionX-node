# Deployment -- perceptionX-node (Render)

## Render configuration

This service is deployed as a Render **web service**, defined in [render.yaml](render.yaml):

- **Build command:** `npm install && npm run build` (installs server deps, then builds the React client into `public/react-build/`)
- **Start command:** `npm start` (runs `node app.js`, which serves the API and the built React app)

## Required environment variables (set in the Render dashboard)

| Variable | Notes |
|----------|-------|
| `NODE_ENV` | `production` |
| `MONGO_URI` | MongoDB Atlas (or other) connection string |
| `PYTHON_API_URL` | `https://prayesh007-perceptionx-python.hf.space` |
| `CLOUDINARY_CLOUD_NAME` | From your Cloudinary dashboard |
| `CLOUDINARY_API_KEY` | From your Cloudinary dashboard |
| `CLOUDINARY_API_SECRET` | From your Cloudinary dashboard |
| `JWT_SECRET` | Strong random secret, distinct from any local dev value |

## Cloudinary setup

1. Sign up at [cloudinary.com](https://cloudinary.com) (free tier is enough to start)
2. Dashboard -> copy **Cloud Name**, **API Key**, **API Secret** into the env vars above (and into `perceptionX-python`'s environment too, since it uploads processed media there as well)

### How storage works

- Videos and files over ~1MB upload to Cloudinary; small images may be stored directly in MongoDB for speed
- If Cloudinary quota is exceeded, the app falls back to MongoDB storage automatically
- Processed (annotated) output follows the same rule, with a truncation safeguard if it's too large for MongoDB

## Troubleshooting

- **"Cloudinary not configured"**: confirm all three `CLOUDINARY_*` variables are set in Render
- **Processing never completes**: confirm `PYTHON_API_URL` points to a live, awake Hugging Face Space (Spaces can sleep when idle)
- **Uploads succeed but nothing gets analyzed**: check the Node service logs for `ECONNREFUSED` -- it means the Python service is unreachable at `PYTHON_API_URL`
