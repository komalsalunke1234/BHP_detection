# BHP Detection — Banglore Home Price Prediction 🏡

A small Flask backend + static frontend (HTML/JS) for estimating Bangalore home prices using a pre-trained model.

---

## Quick Summary 
- Backend: `server/server.py` (Flask) — serves two endpoints:
  - `GET /get_location_names` — returns location list
  - `POST /predict_home_price` — form data: `total_sqft`, `bhk`, `bath`, `location`
- Frontend: `client/index.html` + `client/app.js` (AJAX calls to backend)
- Model artifacts: `server/artifacts/columns.json`, `server/artifacts/banglore_home_prices_model.pickle`

---

## Prerequisites 🔧
- Python 3.8+ (works with 3.8–3.11)
- Git (optional)

Recommended dependencies (install with pip):
- `flask`
- `numpy`
- `scikit-learn`

You can install them manually or using the commands below.

---

## Setup (Windows PowerShell) 🪄
1. Open PowerShell in project root `C:\Users\DELL\Documents\BHP Detection`.

2. Create & activate virtual environment (recommended):

```powershell
python -m venv .venv
. .venv\Scripts\Activate.ps1
```

3. Install dependencies:

```powershell
pip install flask numpy scikit-learn
```

> Tip: If you already have a `.venv` created (like `.venv` in this workspace), activate that instead.

---

## Run the Backend (Flask) ▶️
1. From the activated venv, start the server:

```powershell
cd server
python server.py
```

2. Expected messages in terminal:
```
Loading saved artifacts...
Artifacts loaded successfully
Starting server at http://127.0.0.1:5000/
```

The Flask app will be available at `http://127.0.0.1:5000/`.

---

## Serve the Frontend (two options) 🌐
Option A — Simple static server (recommended for dev):

```powershell
cd client
python -m http.server 8000
# open http://127.0.0.1:8000 in your browser
```

Option B — Serve frontend from Flask (single server):
Add this snippet to `server/server.py` to serve `client/` static files (optional):

```python
from flask import send_from_directory

@app.route('/')
@app.route('/<path:path>')
def serve_client(path='index.html'):
    return send_from_directory('../client', path)
```

Then run `python server.py` and open `http://127.0.0.1:5000/`.

---

## Frontend configuration note 💡
- The JavaScript in `client/app.js` must call the backend endpoints directly during development. Example values used in this repo:
  - `http://127.0.0.1:5000/get_location_names`
  - `http://127.0.0.1:5000/predict_home_price`

If you get 404 or 501 errors when the frontend is served with `python -m http.server`, it means the frontend tried to call `/api/...` paths (or a different host) — use the full `http://127.0.0.1:5000` URLs or serve the frontend from Flask to avoid cross-origin differences.

> The backend already sets `Access-Control-Allow-Origin: *` for the responses, so CORS is allowed.

---

## API Reference & Testing ⚙️
### 1) Get locations
- Request:
  - Method: `GET`
  - URL: `http://127.0.0.1:5000/get_location_names`
- Example:

```bash
curl http://127.0.0.1:5000/get_location_names
# Response: {"locations": ["electronic city", "rajaji nagar", ...]}
```

### 2) Predict price
- Request:
  - Method: `POST` (form-encoded)
  - URL: `http://127.0.0.1:5000/predict_home_price`
  - Fields: `total_sqft`, `bhk`, `bath`, `location`

- Example (curl):

```bash
curl -X POST \
  -d "total_sqft=1000" \
  -d "bhk=2" \
  -d "bath=2" \
  -d "location=Electronic City" \
  http://127.0.0.1:5000/predict_home_price

# Response: {"estimated_price": 12.34}
```

- Example (HTTPie):
```
http --form POST http://127.0.0.1:5000/predict_home_price total_sqft:=1000 bhk:=2 bath:=2 location="Electronic City"
```

---

## Troubleshooting ⚠️
- 404 for `/get_location_names` or `/predict_home_price` when using `python -m http.server`: this static server doesn't proxy API calls — use absolute backend URLs or serve the frontend from Flask.
- 501 Unsupported method: happened when POST requests were sent to the static `http.server` which doesn't support POST on custom paths. Run Flask server to accept POSTs.
- If the model fails to load, ensure `server/artifacts/banglore_home_prices_model.pickle` and `server/artifacts/columns.json` exist and are readable.

---

## Quick Checklist ✅
1. Activate venv
2. Start backend: `python server/server.py`
3. Start frontend: `python -m http.server 8000` (or serve via Flask)
4. Open `http://127.0.0.1:8000` (or `http://127.0.0.1:5000` if served by Flask)
5. Verify `GET /get_location_names` and `POST /predict_home_price` work (curl/Postman/browser)

---

If you want, I can: 
- Patch `server.py` to serve the `client/` folder directly (single-server setup), or
- Add a small `requirements.txt` and a `Makefile` / script to simplify commands.


