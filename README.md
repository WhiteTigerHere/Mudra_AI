<h1 align="center">✨ MudraAI — Indian Sign Language Translator</h1>

<p align="center">
  <b>Convert text, audio, and video into animated Indian Sign Language using a real-time 3D avatar · Full-Stack AI · Accessibility Tech</b><br/>

</p>

<p align="center">
  <img src="https://img.shields.io/badge/FastAPI-0.135-009688?style=for-the-badge&logo=fastapi" />
  <img src="https://img.shields.io/badge/React-18-61DAFB?style=for-the-badge&logo=react" />
  <img src="https://img.shields.io/badge/Gemini-2.5_Flash-4285F4?style=for-the-badge&logo=google" />
  <img src="https://img.shields.io/badge/Whisper-OpenAI-412991?style=for-the-badge&logo=openai" />
  <img src="https://img.shields.io/badge/MediaPipe-Google-FF6F00?style=for-the-badge&logo=google" />
  <img src="https://img.shields.io/badge/MongoDB-Atlas-47A248?style=for-the-badge&logo=mongodb" />
</p>

---

##  What is MudraAI?

MudraAI is an AI-powered accessibility tool that translates **English/Hindi content into Indian Sign Language (ISL)** animations in real-time. The system drives a **VRM 3D avatar** using actual motion-capture data extracted from reference sign videos.

It supports **three input modes**:

| Mode | Input | How it works |
|------|-------|--------------|
| 📝 **Text** | Type anything in English or Hindi | Gemini converts to ISL Gloss → Avatar signs it |
| 🎙️ **Audio** | Upload `.wav` / `.mp3` | Whisper transcribes → Gemini → ISL Gloss → Avatar |
| 🎬 **Video / Reel** | Upload an MP4 (e.g. Instagram Reel) | Gemini Vision OCR on keyframes + Whisper audio → merged → ISL Gloss → Avatar |

---

## 🌍 Impact

Over **63 million** people in India are Deaf or Hard of Hearing. Most digital content is inaccessible to them. MudraAI bridges that gap by providing an automated, real-time ISL translation layer for any content — no human interpreter required.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         FRONTEND  (React + Vite)                │
│                                                                 │
│  Text / Voice / Video Input                                     │
│       │                                                         │
│       ▼                                                         │
│  FastAPI Backend (/text-to-sign, /transcribe-and-sign,          │
│                   /video-ocr-to-sign)                           │
│       │                                                         │
│       ├── Whisper (local STT)                                   │
│       ├── Gemini 2.5 Flash (ISL Gloss + Vision OCR)            │
│       └── MongoDB Atlas (motion JSON store)                     │
│                                                                 │
│       ▼                                                         │
│  Animation Sequence → React Three Fiber + three-vrm            │
│  Kalidokit IK Solver → VRM 3D Avatar Bone Rotations            │
│  Dual HTML Canvas → Neon Stick Figure Overlay                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

**Frontend**
- [React 18](https://react.dev/) + [Vite](https://vitejs.dev/)
- [React Three Fiber](https://docs.pmnd.rs/react-three-fiber) — declarative Three.js
- [@pixiv/three-vrm](https://github.com/pixiv/three-vrm) — VRM avatar loader & controller
- [Kalidokit](https://github.com/yeemachine/kalidokit) — Inverse Kinematics solver (landmarks → bone rotations)
- [react-speech-recognition](https://www.npmjs.com/package/react-speech-recognition) — live voice input

**Backend**
- [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/)
- [OpenAI Whisper](https://github.com/openai/whisper) (`base` model, runs 100% locally)
- [Google Gemini 2.5 Flash](https://ai.google.dev/) — ISL grammar conversion + Vision OCR
- [MediaPipe](https://mediapipe.dev/) — Pose (33 landmarks) + Hands (21 landmarks) + FaceMesh (468 landmarks)
- [OpenCV](https://opencv.org/) — video frame extraction
- [PyMongo](https://pymongo.readthedocs.io/) + [MongoDB Atlas](https://www.mongodb.com/atlas) — motion JSON cloud storage

---

## 🚀 Getting Started

The project has two parts: `client` (frontend) and `server` (backend). You need **two terminals**.

### Prerequisites
- Python 3.10+
- Node.js 18+
- A [Google Gemini API Key](https://aistudio.google.com/app/apikey)

---

### 1. Backend Setup

```bash
# Navigate to server
cd server

# Create and activate a virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # Mac/Linux

# Install dependencies
pip install -r requirements.txt
pip install "mediapipe==0.10.14" opencv-python

# Create your .env file
echo GEMINI_API_KEY=your_key_here > .env

# Start the backend
uvicorn main:app --port 8000 --reload
```

Backend is live at → `http://localhost:8000`

---

### 2. Frontend Setup

```bash
# In a new terminal, navigate to client
cd client

# Install dependencies
npm install --legacy-peer-deps

# Start the dev server
npm run dev
```

Frontend is live at → **http://localhost:5173/codecrafters/home**

---

## 🧩 Features

- **Text → ISL**: Type English or Hindi → Gemini applies ISL grammar rules (SOV order, removes articles/prepositions) → Avatar signs the gloss
- **Audio → ISL**: Upload audio → local Whisper transcription → same pipeline
- **Video Reel → ISL**: Upload MP4 → OpenCV keyframe extraction + Gemini Vision OCR + Whisper audio → merged text → ISL gloss → Avatar
- **Fingerspelling fallback**: Any word without a recorded sign is spelled out letter-by-letter (A–Z)
- **Speed & Pause controls**: Adjust how fast the avatar signs and the gap between signs
- **Compound word matching**: Tries two-word combinations (e.g., `SAVING_ACCOUNT`) before falling back to individual signs
- **Auto-interpolation**: Missing hand frames are carry-forward interpolated for smooth animations

---

## ➕ Adding New Signs

Record yourself signing a new word, then run:

```bash
cd server
python mediapipe_extractor.py "path/to/sign_video.mp4" --out WORD_motion.json
```

The generated `WORD_motion.json` is automatically saved to `client/public/`. The system recognizes it immediately — no code changes needed.

---

## 📁 Project Structure

```
reel/
├── server/
│   ├── main.py                  # FastAPI app — all 3 endpoints
│   ├── mediapipe_extractor.py   # Offline tool to record new signs
│   └── requirements.txt
│
├── client/
│   └── src/
│       ├── Pages/
│       │   ├── Convert.js       # Main studio — avatar + all 3 input modes
│       │   ├── Home.js
│       │   ├── LearnSign.js
│       │   └── ...
│       ├── Animations/
│       │   ├── Alphabets/       # A.js – Z.js (fingerspelling)
│       │   └── Words/           # TIME, HOME, PERSON, YOU
│       └── Models/
│           ├── xbot/xbot.glb
│           └── ybot/ybot.glb
│
└── visualize_motion.py          # Debug tool: preview motion JSON with OpenCV
```

---

## 📡 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/text-to-sign` | Text → ISL gloss → animation sequence |
| `POST` | `/transcribe-and-sign` | Audio file → Whisper → ISL gloss → animation sequence |
| `POST` | `/video-ocr-to-sign` | MP4 → Vision OCR + Whisper → ISL gloss → animation sequence |
| `GET`  | `/motion/{word}` | Fetch motion JSON for a word from MongoDB |
| `GET`  | `/available-animations` | List all locally available word animations |

---