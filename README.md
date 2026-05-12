# 🎵 MoodTunes — Emotion-Driven Music Recommender

<div align="center">

[![Live Demo](https://img.shields.io/badge/🚀_Live_Demo-moodtunes--alpha.vercel.app-22d3ee?style=for-the-badge&logo=vercel&logoColor=white)](https://moodtunes-alpha.vercel.app/)
[![React](https://img.shields.io/badge/React-18.3-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Vite](https://img.shields.io/badge/Vite-5.2-646CFF?style=for-the-badge&logo=vite&logoColor=white)](https://vitejs.dev/)
[![TailwindCSS](https://img.shields.io/badge/Tailwind-3.4-38BDF8?style=for-the-badge&logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

<br/>

**Point your face at the camera. Get the perfect song for your mood — instantly.**

<br/>

[🌐 Live Demo](https://moodtunes-alpha.vercel.app/) &nbsp;·&nbsp; [🐛 Report Bug](https://github.com/issues) &nbsp;·&nbsp; [💡 Request Feature](https://github.com/issues)

</div>

---

## 📌 Overview

**MoodTunes** is a full-stack AI web app that:

1. **Reads your face** — accesses your webcam and captures a single frame
2. **Detects your emotion** — runs DeepFace (RetinaFace + VGG-Face) on the frame in real time
3. **Recommends a track** — maps the emotion to a music genre and fetches a live Spotify track
4. **Plays it instantly** — embeds the Spotify player directly in the UI

No manual input. No genre selection. Just look at the camera.

---

## 🎬 Demo

> 🌐 **[https://moodtunes-alpha.vercel.app/](https://moodtunes-alpha.vercel.app/)**

| Activate Camera | Capture & Analyse | Result |
|:-:|:-:|:-:|
| Click to open webcam stream with live scan overlay | One click captures + sends frame to ML backend | Emotion pill · confidence score · Spotify embed |

---

## ✨ Features

- 🎭 **7-emotion recognition** — Happy, Sad, Angry, Surprise, Fear, Disgust, Neutral
- 🎵 **Live Spotify search** — real-time genre-based track fetch via Spotify Web API
- 🎬 **In-page playback** — official Spotify iFrame embed (30-sec audio preview fallback)
- 📊 **Confidence ring** — animated SVG ring shows model certainty percentage
- 🌊 **Waveform visualiser** — 10-bar equaliser animation per track
- 🎨 **Mood-reactive UI** — glow colour, border, ambient orb, and accents all shift per emotion
- 🔒 **Privacy-first** — no frame is stored; analysed in-memory and discarded immediately

---

## 🧠 Architecture

```
Browser (React + Vite)
│
│  getUserMedia() → <video> stream
│  canvas.drawImage() → toDataURL("image/jpeg")
│  base64 JPEG
│
│  ──── POST /analyze ────────────────────────────►
│                                                  FastAPI (Python)
│                                                  │
│                                                  ├─ cv2.imdecode()
│                                                  ├─ CLAHE pre-process (LAB colour space)
│                                                  ├─ DeepFace.analyze() [RetinaFace]
│                                                  ├─ dominant_emotion + confidence %
│                                                  ├─ emotion → genre mapping
│                                                  └─ Spotipy.search() → random track
│
│  ◄─── JSON response ───────────────────────────
│
│  { emotion, confidence, track_name, artist,
│    album, album_art, spotify_id, link }
│
│  Framer Motion reveal → Emotion pill + Spotify iFrame
```

---

## 🗂️ Project Structure

```
moodtunes/
├── README.md
├── start.sh                  ← Mac/Linux one-command launcher
├── start.bat                 ← Windows one-command launcher
│
├── backend/
│   ├── main.py               ← FastAPI · DeepFace · cv2 · Spotipy
│   ├── requirements.txt
│   └── .env.example          ← rename → .env, add Spotify keys
│
└── frontend/
    ├── index.html
    ├── vite.config.js        ← proxies /analyze → localhost:8000
    ├── tailwind.config.js
    ├── postcss.config.js
    ├── package.json
    └── src/
        ├── main.jsx
        ├── App.jsx            ← state machine + all UI components
        └── index.css          ← Tailwind base + waveform keyframes
```

---

## 🚀 Getting Started

### Prerequisites

| Tool | Version |
|------|---------|
| Python | ≥ 3.10 |
| Node.js | ≥ 18 |
| npm | ≥ 9 |

### 1 · Spotify Credentials (free)

1. Go to [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Click **Create App** → set Redirect URI to `http://localhost:8000`
3. Copy your **Client ID** and **Client Secret**

### 2 · Configure `.env`

```bash
cd backend
cp .env.example .env
# paste your Client ID and Client Secret into .env
```

### 3 · Run

**Mac / Linux**
```bash
chmod +x start.sh && ./start.sh
```

**Windows**
```bat
start.bat
```

**Manual**
```bash
# Terminal 1 — Backend
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python main.py
# → http://localhost:8000

# Terminal 2 — Frontend
cd frontend
npm install && npm run dev
# → http://localhost:5173
```

> ⚠️ **First run:** DeepFace downloads model weights (~500 MB). This only happens once.

---

## 🗺️ Emotion → Genre Mapping

| Emotion | Genres | Vibe |
|---------|--------|------|
| 😊 Happy | pop · dance · feel-good · summer | High-energy, upbeat, major key |
| 😢 Sad | sad · acoustic · piano · rainy-day | Slow, introspective, minor key |
| 😠 Angry | metal · hard-rock · punk · rage | Distorted, aggressive, fast |
| 😲 Surprise | electronic · edm · synth-pop · electro | Energetic, unpredictable drops |
| 😨 Fear | ambient · dark-ambient · cinematic · atmospheric | Sparse, tense, unsettling |
| 🤢 Disgust | industrial · noise · experimental · alternative | Abrasive, unconventional |
| 😐 Neutral | indie · lo-fi · chill · study | Relaxed, melodic, background-friendly |

---

## 📡 API Reference

### `POST /analyze`

| Field | Type | Description |
|-------|------|-------------|
| `image` | `string` | Raw base64 JPEG — **no** `data:image/jpeg;base64,` prefix |

**Response `200`**
```json
{
  "emotion":     "happy",
  "confidence":  91.3,
  "track_name":  "Levitating",
  "artist":      "Dua Lipa",
  "album":       "Future Nostalgia",
  "album_art":   "https://i.scdn.co/image/...",
  "link":        "https://open.spotify.com/track/463CkQjx2Zk1yXoBuierM9",
  "preview_url": "https://p.scdn.co/mp3-preview/...",
  "spotify_id":  "463CkQjx2Zk1yXoBuierM9"
}
```

**Errors**

| Code | Reason |
|------|--------|
| `400` | Image could not be decoded |
| `422` | No face detected in frame |
| `502` | Spotify returned no results — check credentials |

### `GET /health`
```json
{ "status": "ok" }
```

---

## 🛠️ Tech Stack

### Frontend
| | Library | Purpose |
|--|---------|---------|
| ⚛️ | React 18 | UI components + hooks state machine |
| ⚡ | Vite 5 | Dev server · HMR · `/analyze` proxy |
| 🎨 | Tailwind CSS 3 | Utility styling + custom keyframes |
| 🎞️ | Framer Motion 11 | Layout animations · scan-line · SVG spring |
| 🔤 | JetBrains Mono + Syne | Monospace UI + display heading fonts |

### Backend
| | Library | Purpose |
|--|---------|---------|
| 🚀 | FastAPI | Async REST API framework |
| 🦄 | Uvicorn | ASGI server with hot-reload |
| 🧠 | DeepFace | Emotion recognition (VGG-Face backbone) |
| 👁️ | OpenCV (cv2) | Image decode + CLAHE pre-processing |
| 🎵 | Spotipy | Spotify Web API client |
| 🔐 | python-dotenv | `.env` credential management |

---

## 🔧 Troubleshooting

| Problem | Fix |
|---------|-----|
| **"BACKEND OFFLINE"** pill in UI | Run `python main.py` inside `/backend` |
| **"No face detected"** error | Improve lighting · try `detector_backend="opencv"` for faster, less strict detection |
| **Spotify returns no tracks** | Verify `.env` has correct Client ID + Secret |
| **First call is very slow** | DeepFace is downloading model weights — wait once |
| **CORS error in console** | Ensure backend is on `:8000` and Vite on `:5173` |

---

## ⚙️ Customisation

**Swap detector backend** (speed vs accuracy) in `backend/main.py`:
```python
detector_backend = "opencv"      # fastest
detector_backend = "ssd"         # balanced
detector_backend = "retinaface"  # most accurate (default)
detector_backend = "mtcnn"       # good alternative
```

**Add genres** by extending the map in `backend/main.py`:
```python
"happy": ["pop", "dance", "feel-good", "summer", "k-pop", "latin"],
```

---

## 🔒 Privacy

- Webcam frames are **never stored** — decoded in RAM, analysed, discarded
- No user accounts, no cookies, no analytics
- Camera permission is released immediately after capture
- No Spotify user account is linked — only public catalogue data is fetched

---

## 📄 License

[MIT](LICENSE) © 2025 — free to use, fork, and deploy.

---

<div align="center">

Built with DeepFace · Spotify Web API · React · FastAPI

⭐ **Star this repo if you found it useful!**

</div>
