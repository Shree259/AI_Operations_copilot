# 🌐 Deployment Guide: Copilot AI

This guide provides step-by-step instructions to deploy the **Copilot AI** application to cloud environments so that anyone can access and use it via a public URL.

---

## 🛠️ Deployment Options

Since this is a full-stack application (FastAPI backend + React frontend), we recommend the following deployment architectures:

1. **Option A (Recommended & Easiest)**: Deploy the **Frontend on Vercel** (Free, fast global CDN) and the **Backend on Render** (Free Python Web Service).
2. **Option B (Single Container)**: Deploy as a **unified Docker container on Hugging Face Spaces** (Free, 16GB RAM, persistent).
3. **Option C (Railway)**: Deploy both frontend and backend on Railway (Unified monorepo support, paid).

---

## 🚀 Option A: Vercel (Frontend) + Render (Backend)

### Part 1: Deploy the FastAPI Backend on Render

Render is a cloud platform that offers free hosting for Python applications.

1. Sign up or log in to [Render](https://render.com).
2. Click **New +** and select **Web Service**.
3. Connect your GitHub repository.
4. Configure the Web Service with the following details:
   - **Name**: `ai-operations-copilot-backend`
   - **Environment**: `Python 3`
   - **Build Command**: `pip install -r backend/requirements.txt`
   - **Start Command**: `cd backend && uvicorn app:app --host 0.0.0.0 --port $PORT`
   - **Instance Type**: `Free`
5. Click **Advanced** and add the following **Environment Variables**:
   - `GROQ_API_KEY`: Your Groq API Key (from [console.groq.com](https://console.groq.com))
   - `HF_HOME`: `./hf_cache` (caches local transformer models)
6. Click **Create Web Service**. Render will build the environment, download the sentence-transformer models, and start the FastAPI server.
7. Copy your deployed backend URL once the deploy finishes (e.g., `https://ai-operations-copilot-backend.onrender.com`).

---

### Part 2: Deploy the React Frontend on Vercel

Vercel provides high-performance, free hosting for frontend frameworks.

1. Sign up or log in to [Vercel](https://vercel.com).
2. Click **Add New** and select **Project**.
3. Import your GitHub repository.
4. Configure the project:
   - **Framework Preset**: `Vite`
   - **Root Directory**: `frontend`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`
5. Under **Environment Variables**, add:
   - `VITE_API_BASE_URL`: The URL of your deployed backend (e.g., `https://ai-operations-copilot-backend.onrender.com`)
6. Click **Deploy**. Vercel will build your static assets and host the application globally.
7. Open the generated Vercel URL to run the application live!

---

## 🤖 Option B: Deploys via Hugging Face Spaces (Docker)

If you want the entire application (React + FastAPI) to run in a single container for free with **16GB of RAM** (great for sentence embeddings), you can deploy using a custom `Dockerfile` on Hugging Face Spaces.

### 1. Create a Dockerfile in the project root:

Create a file named `Dockerfile` at the root of the project with the following content:

```dockerfile
# Build frontend
FROM node:18-alpine AS frontend-builder
WORKDIR /app/frontend
COPY frontend/package*.json ./
RUN npm install
COPY frontend/ ./
RUN npm run build

# Build backend
FROM python:3.10-slim
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /lib/apt/lists/*

# Install python dependencies
COPY backend/requirements.txt ./backend/requirements.txt
RUN pip install --no-cache-dir -r backend/requirements.txt

# Copy backend files
COPY backend/ /app/backend/
COPY --from=frontend-builder /app/frontend/dist /app/frontend/dist

# Expose port 7860 (Hugging Face default)
EXPOSE 7860

# Run using uvicorn and configure FastAPI to serve frontend files
ENV PORT=7860
CMD ["python", "-m", "uvicorn", "backend.app:app", "--host", "0.0.0.0", "--port", "7860"]
```

### 2. Configure FastAPI to Serve React Static Files in Production

Add the following to the end of your `backend/app.py`:

```python
from fastapi.staticfiles import StaticFiles

# Serve built frontend assets
if os.path.exists("../frontend/dist"):
    app.mount("/", StaticFiles(directory="../frontend/dist", html=True), name="frontend")
```

---

## 📝 Post-Deployment Verification

Once deployed, perform the following sanity checks:

1. **Dashboard Verification**: Check if the dashboard renders and stats fetch successfully.
2. **File Ingestion**: Upload `sales_report.csv` or `checkout_failure.log` from the sample data.
3. **ChromaDB Indexing**: Verify that the files appear in the Recent Uploads list.
4. **LLM Diagnostics**: Head to the **Investigation** page, select the file, and ensure the AI correctly returns structured cards.
5. **ChatOps**: Ask a question like *"summarize sales_report.csv"* to verify the RAG system and vector retrieval pipeline.

---

## 🛡️ CORS Configuration Notice

If you deploy the frontend and backend on separate domains (e.g., frontend on Vercel, backend on Render), ensure that FastAPI's `CORSMiddleware` allows requests from your Vercel domain. In `backend/app.py`:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:5173",
        "https://your-vercel-project-name.vercel.app"
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```
