# Assignment 2: Dockerize a Flask App with Basic CI

## Overview
This repository containerizes a Flask web application (`app.py`), fixes bugs that prevented it from running and being accessed externally, verifies the `/health` endpoint with `curl`, and configures a GitHub Actions workflow (`.github/workflows/build.yml`) that builds the Docker image on every push.

## Project Structure
- `app.py` — Flask application exposing `/` and `/health` on `0.0.0.0:5000`
- `requirements.txt` — Python dependencies (`flask`)
- `Dockerfile` — Container build instructions for the Flask app
- `.github/workflows/build.yml` — GitHub Actions CI workflow that builds the Docker image on push
- `README.md` — Build/run instructions, health check verification, and bug-fix documentation

## Bugs Found and Fixes Applied
1. **Missing `=` in Flask initialization (`app.py`):**
   - **Issue:** `app Flask(__name__)` caused a Python `SyntaxError`.
   - **Fix:** Updated to `app = Flask(__name__)`.
2. **Malformed `app.run(...)` call (`app.py`):**
   - **Issue:** Invalid syntax/characters in `app.run(...)` prevented the server from binding properly on port `5000`.
   - **Fix:** Updated to `app.run(host="0.0.0.0", port=5000)` so the server listens on all container interfaces on port `5000`.
3. **Invalid entry in `requirements.txt`:**
   - **Issue:** A stray `None` line appeared above `flask`, causing `pip install -r requirements.txt` to fail.
   - **Fix:** Removed `None` so `requirements.txt` cleanly lists `flask`.
4. **Container port mapping (`docker run`):**
   - **Issue:** Without mapping the host port to the container port, `localhost:5000` is unreachable from the host machine.
   - **Fix:** Published port `5000` using `-p 5000:5000` when running the container.

## How to Build and Run

1. **Build the Docker image:**
   ```bash
   docker build -t flask-devops-app .
   ```

2. **Run the container:**
   ```bash
   docker run -d --name flask-app -p 5000:5000 flask-devops-app
   ```

## Health Check Verification (`curl`)

Test the `/health` endpoint:
```bash
curl -i http://localhost:5000/health
```

Expected output:
```text
HTTP/1.1 200 OK
Server: Werkzeug/3.1.3 Python/3.12.x
Date: Wed, 25 Nov 2026 12:00:00 GMT
Content-Type: application/json
Content-Length: 16
Connection: close

{"status":"ok"}
```

## CI Workflow (GitHub Actions)
The workflow in `.github/workflows/build.yml` triggers automatically on every `push` to verify that the Docker image builds cleanly without errors.
