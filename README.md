# 🎵 MoodTunes — Emotion-Driven Music Recommender

<div align="center">

[![Live Demo](https://img.shields.io/badge/🚀_Live_Demo-moodtunes--alpha.vercel.app-22d3ee?style=for-the-badge&logo=vercel&logoColor=white)](https://moodtunes-alpha.vercel.app/)
[![React](https://img.shields.io/badge/React-18.3-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://react.dev/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.111-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Vite](https://img.shields.io/badge/Vite-5.2-646CFF?style=for-the-badge&logo=vite&logoColor=white)](https://vitejs.dev/)
[![TailwindCSS](https://img.shields.io/badge/Tailwind-3.4-38BDF8?style=for-the-badge&logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![Framer Motion](https://img.shields.io/badge/Framer_Motion-11-FF0055?style=for-the-badge&logo=framer&logoColor=white)](https://www.framer.com/motion/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)](LICENSE)

<br/>

**Point your face at the camera. Get the perfect song for your mood — instantly.**

<br/>

[🌐 Live Demo](https://moodtunes-alpha.vercel.app/) &nbsp;·&nbsp; [🐛 Report Bug](https://github.com/issues) &nbsp;·&nbsp; [💡 Request Feature](https://github.com/issues)

</div>

---

## 📌 What Is MoodTunes?

MoodTunes is a full-stack AI web application that detects your facial emotion in real time and recommends a matching Spotify track — without any manual input.

Here's how it works in 4 steps:

1. **Activate Camera** — opens your webcam with an animated scan overlay
2. **Capture & Analyse** — takes a single frame, sends it to the Python backend
3. **Detect Emotion** — DeepFace reads your face and returns one of 7 emotions with a confidence score
4. **Play Music** — Spotify is queried for a genre-matched track, embedded directly in the UI

No typing. No genre pickers. Just your face.

> **Note on the live demo:** [moodtunes-alpha.vercel.app](https://moodtunes-alpha.vercel.app/) hosts the React frontend. The ML backend (FastAPI + DeepFace) must run locally to process images. Follow the [Quick Start](#-quick-start) below to get the full working experience.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🎭 **7-emotion detection** | Happy · Sad · Angry · Surprise · Fear · Disgust · Neutral |
| 🎵 **Live Spotify fetch** | Searches real-time trending tracks by mood-matched genre |
| 🎬 **In-page playback** | Official Spotify iFrame embed with 30-sec audio preview fallback |
| 📊 **Confidence ring** | Animated SVG ring shows model certainty as a percentage |
| 🌊 **Waveform visualiser** | 10-bar animated equaliser reacts to each detected mood |
| 🎨 **Mood-reactive UI** | Card glow, border, ambient orb, and all accents shift per emotion |
| 🔒 **Privacy-first** | Frames decoded in RAM and discarded — never stored or logged |
| 📱 **Responsive** | Works on desktop and mobile browsers |

---

## 🎨 UI & Design

The interface uses a **dark glassmorphic** design system built with Tailwind CSS and Framer Motion:

- **Background** — near-black `#070a10` with two slow-drifting ambient colour orbs
- **Cards** — frosted glass with `backdrop-filter: blur(32px)` and semi-transparent borders
- **Mood colours** — every accent (glow, ring, waveform, scan-line, orb) derives from one `EMOTION_CONFIG` object and transitions smoothly on result
- **Typography** — `JetBrains Mono` for all UI text, `Syne` for the main heading
- **Noise grain** — subtle SVG fractal noise overlay for a tactile, analogue texture
- **Scan-line overlay** — animated horizontal line + corner brackets pulse on the live camera feed

| Emotion | Accent Colour | Label |
|---|---|---|
| 😊 Happy | `#fbbf24` Gold | EUPHORIC |
| 😢 Sad | `#60a5fa` Blue | MELANCHOLIC |
| 😠 Angry | `#f87171` Red | FIERCE |
| 😲 Surprise | `#a78bfa` Violet | ELECTRIC |
| 😨 Fear | `#34d399` Emerald | TENSE |
| 🤢 Disgust | `#fb923c` Orange | RAW |
| 😐 Neutral | `#94a3b8` Slate | CALM |

---

## 🧠 Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                   BROWSER  (React + Vite)                    │
│                                                              │
│  getUserMedia() ──► <video> stream  (CSS mirrored for UX)    │
│  canvas.translate + scale(-1,1)  ──► un-mirror for ML        │
│  canvas.toDataURL("image/jpeg") ──► strip data-URI prefix    │
│                                                              │
│              POST /analyze  { image: base64 }                │
└──────────────────────────┬───────────────────────────────────┘
                           │
                           ▼
┌──────────────────────────────────────────────────────────────┐
│                  BACKEND  (FastAPI + Python)                  │
│                                                              │
│  base64 ──► cv2.imdecode() ──► numpy array                   │
│  CLAHE pre-process on LAB L-channel (8×8 tiles)              │
│  — normalises uneven webcam lighting before inference        │
│                                                              │
│  DeepFace.analyze(detector_backend="retinaface")             │
│  ──► dominant_emotion  +  confidence %                       │
│                                                              │
│  emotion ──► random genre from EMOTION_GENRE_MAP             │
│  Spotipy.search(genre, offset=random(0,100))                 │
│  prefer tracks with preview_url ──► random.choice()          │
└──────────────────────────┬───────────────────────────────────┘
                           │
          JSON { emotion, confidence, track_name,
                 artist, album, album_art,
                 spotify_id, link, preview_url }
                           │
                           ▼
┌──────────────────────────────────────────────────────────────┐
│                   BROWSER  (React)                           │
│                                                              │
│  Framer Motion AnimatePresence ──► phase reveal animation    │
│  Emotion pill + ConfidenceRing SVG + Waveform bars           │
│  Spotify iFrame embed  /  <audio> 30-sec preview fallback    │
└──────────────────────────────────────────────────────────────┘
```

---

## 🗂️ Project Structure

```
moodtunes/
├── README.md
├── start.sh                    ← Mac / Linux one-command launcher
├── start.bat                   ← Windows one-command launcher
├── .gitignore
│
├── backend/
│   ├── main.py                 ← FastAPI · DeepFace · cv2 · Spotipy
│   ├── requirements.txt
│   └── .env.example            ← rename to .env and fill Spotify keys
│
└── frontend/
    ├── index.html              ← Vite entry + Google Fonts
    ├── vite.config.js          ← proxies /analyze + /health → :8000
    ├── tailwind.config.js      ← custom fonts + pulse_glow keyframe
    ├── postcss.config.js
    ├── package.json
    └── src/
        ├── main.jsx            ← React 18 createRoot
        ├── App.jsx             ← 5-phase state machine + all components
        └── index.css           ← Tailwind layers · waveform · noise grain
```

---

## 🚀 Quick Start

### Prerequisites

| Tool | Minimum |
|------|---------|
| Python | 3.10 |
| Node.js | 18 |
| npm | 9 |

### 1 · Get Spotify credentials (free)

1. Visit [developer.spotify.com/dashboard](https://developer.spotify.com/dashboard)
2. Click **Create App** → set Redirect URI to `http://localhost:8000`
3. Copy your **Client ID** and **Client Secret**

### 2 · Configure `.env`

```bash
cd backend
cp .env.example .env
# Open .env — paste your Client ID and Client Secret
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

**Manual (two terminals)**
```bash
# Terminal 1 — Backend
cd backend
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python main.py
# API  → http://localhost:8000
# Docs → http://localhost:8000/docs

# Terminal 2 — Frontend
cd frontend
npm install
npm run dev
# App  → http://localhost:5173
```

> ⚠️ **First run only:** DeepFace downloads model weights (~500 MB) on the first `/analyze` call. This is a one-time download — every call after is fast.

---

## 🗺️ Emotion → Genre Mapping

| Emotion | Genres Searched | Vibe |
|---------|----------------|------|
| 😊 Happy | pop · dance · feel-good · summer | High-energy, upbeat, major key |
| 😢 Sad | sad · acoustic · piano · rainy-day | Slow, introspective, minor key |
| 😠 Angry | metal · hard-rock · punk · rage | Distorted, aggressive, fast BPM |
| 😲 Surprise | electronic · edm · synth-pop · electro | Energetic, unexpected drops |
| 😨 Fear | ambient · dark-ambient · cinematic · atmospheric | Sparse, tense, unsettling |
| 🤢 Disgust | industrial · noise · experimental · alternative | Abrasive, unconventional |
| 😐 Neutral | indie · lo-fi · chill · study | Relaxed, melodic, background-friendly |

---

## 📡 API Reference

### `POST /analyze`

**Request**
```json
{ "image": "<raw base64 JPEG — no data:image/jpeg;base64, prefix>" }
```

**Response `200 OK`**
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

| Code | Cause |
|------|-------|
| `400` | Image could not be decoded |
| `422` | No face detected in the frame |
| `502` | Spotify returned no tracks — check credentials |

### `GET /health`
```json
{ "status": "ok" }
```
Polled by the frontend on load to show or hide the **"BACKEND OFFLINE"** warning banner.

---

## 🛠️ Tech Stack

### Frontend

| Library | Version | Role |
|---------|---------|------|
| React | 18.3 | UI components + hooks state machine |
| Vite | 5.2 | Dev server · HMR · `/analyze` proxy |
| Tailwind CSS | 3.4 | Utility styling + custom keyframes |
| Framer Motion | 11 | `AnimatePresence` · layout spring · SVG ring · scan-line |
| JetBrains Mono | — | Monospace UI typeface |
| Syne | — | Display heading typeface |

### Backend

| Library | Version | Role |
|---------|---------|------|
| FastAPI | 0.111 | Async REST framework |
| Uvicorn | 0.29 | ASGI server with hot-reload |
| DeepFace | 0.0.93 | Emotion recognition (VGG-Face backbone) |
| OpenCV | 4.9 | Image decode + CLAHE contrast normalisation |
| Spotipy | 2.23 | Spotify Web API client |
| python-dotenv | 1.0.1 | `.env` credential loading |

---

## 🔧 Troubleshooting

| Problem | Fix |
|---------|-----|
| **"BACKEND OFFLINE"** banner shows | Run `python main.py` in the `/backend` folder |
| **"No face detected"** error | Improve lighting · switch `detector_backend="opencv"` in `main.py` for looser detection |
| **Spotify returns no tracks** | Check Client ID + Secret are correct in `.env` |
| **First call is very slow (30–60s)** | DeepFace is downloading model weights — wait once |
| **CORS error in browser console** | Keep backend on `:8000` and frontend on `:5173` |
| **Blank Spotify embed** | Non-standard track URL — 30-sec audio preview fallback activates automatically |

---

## ⚙️ Customisation

**Switch DeepFace detector** in `backend/main.py`:
```python
detector_backend = "retinaface"  # default — most accurate
detector_backend = "mtcnn"       # good balance of speed + accuracy
detector_backend = "ssd"         # faster
detector_backend = "opencv"      # fastest, least strict
```

**Add genres** in `backend/main.py`:
```python
EMOTION_GENRE_MAP = {
    "happy": ["pop", "dance", "feel-good", "summer", "k-pop", "latin"],
    "sad":   ["sad", "acoustic", "piano", "rainy-day", "singer-songwriter"],
}
```

---

## 🔒 Privacy

- Webcam frames are **never stored** — decoded in RAM, analysed, immediately discarded
- No user accounts, no cookies, no analytics
- Camera permission is released as soon as the frame is captured
- No Spotify user account is linked — only the public catalogue API is used

---

## 🤝 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you'd like to change.

```bash
git checkout -b feature/your-feature
git commit -m "feat: your change"
git push origin feature/your-feature
# Then open a Pull Request
```

---

## 📄 License

[MIT](LICENSE) © 2025 — free to use, fork, and deploy.

---

<div align="center">

Built with DeepFace · Spotify Web API · React · FastAPI

⭐ **Star this repo if you found it useful!**

</div>
