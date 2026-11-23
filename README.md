# XycuiConverter — Universal File Converter

A complete, locally runnable full-stack File Converter web application with a clean, modern UI.

![XycuiConverter](https://img.shields.io/badge/Stack-React%2019%20%2B%20FastAPI%20%2B%20TailwindCSS-indigo)
![Security](https://img.shields.io/badge/Privacy-100%25%20Local%20Processing-emerald)
![Auto-Purge](https://img.shields.io/badge/Storage-30--min%20Auto--Purge-blue)

---

## 🚀 Features

- **Drag-and-Drop Dropzone:** Batch or single file uploads with automatic MIME/magic-byte and extension detection.
- **Smart Target Selector:** Dynamically filters valid target output formats based on the file category (e.g., uploading `.png` only shows `JPG, WEBP, AVIF, ICO, BMP, TIFF`).
- **Conversion Queue:** Real-time per-file status (`Ready`, `Converting...`, `Done`, `Error`), before vs. after file size comparisons with savings badges, single-click downloads, and **"Download All as ZIP"**.
- **Settings Drawer:** Precision controls for Image Quality (1–100%) and Audio Bitrate (`128k`, `192k`, `320k`), plus live host machine capability detection (`/api/capabilities`).
- **Backend Hygiene & Security:**
  - Validates file types with magic bytes (`filetype`) to prevent extension spoofing.
  - Zero unescaped shell strings (parameter lists passed directly to safe subprocesses).
  - Background asynchronous task automatically purges temporary uploads and outputs older than 30 minutes.
  - Graceful fallbacks for missing binaries (`imageio-ffmpeg` bundled fallback, Python native document rendering with `markdown`, `reportlab`, `python-docx`, `pypdf`).

---

## 🛠️ Tech Stack & Conversion Engines

| Category | Engine | Supported Formats | Special Features |
| :--- | :--- | :--- | :--- |
| **Images** | Pillow (`PIL`) + `pillow-heif` | PNG, JPG, WEBP, AVIF, ICO, BMP, TIFF | Automatic RGBA-to-RGB composite on JPEG export, quality slider |
| **Tabular Data** | Pandas + OpenPyXL | CSV, JSON, XLSX, TSV | Multi-encoding fallback, formatted Excel export, clean records JSON |
| **Audio & Video** | FFmpeg (`imageio-ffmpeg` / system) | MP4, MP3, WAV, AAC, WEBM, OGG, FLAC, MKV, AVI | Video to audio extraction, bitrate selection (128k, 192k, 320k) |
| **Documents** | LibreOffice (`soffice`) / `pdf2docx` / PyMuPDF (`fitz`) / Pandoc / Native Python | MD, HTML, DOCX, PDF, TXT | Priority 1 LibreOffice PDF->DOCX, pdf2docx fallback, PyMuPDF "Reflow Text (No Tables)" mode, ReportLab & python-docx native rendering |

---

## 📂 Project Structure

```
converter/
├── backend/
│   ├── app/
│   │   ├── config.py              # Directory paths, limits, retention settings
│   │   ├── cleanup.py             # Periodic 30-min file purge background task
│   │   ├── capabilities.py        # System binary detector (ffmpeg, pandoc, soffice)
│   │   ├── detector.py            # Magic byte & MIME type validation
│   │   ├── engines/
│   │   │   ├── base.py            # Base converter interface
│   │   │   ├── image_engine.py    # Pillow engine with RGBA->RGB handling
│   │   │   ├── tabular_engine.py  # Pandas & OpenPyXL engine
│   │   │   ├── media_engine.py    # Safe FFmpeg audio/video engine
│   │   │   └── doc_engine.py      # Pandoc, LibreOffice & Python native fallback
│   │   ├── routers/
│   │   │   ├── capabilities.py    # GET /api/capabilities
│   │   │   ├── convert.py         # POST /api/convert & POST /api/detect
│   │   │   └── download.py        # GET /api/download/{id} & POST /api/download-zip
│   │   └── main.py                # FastAPI app with CORS & lifespan manager
│   ├── tests/
│   │   ├── conftest.py
│   │   └── test_converters.py     # Comprehensive automated test suite
│   ├── run.py                     # Convenience backend runner
│   ├── requirements.txt
│   └── temp_storage/              # Ephemeral upload and output directory
│
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Header.tsx         # App bar with quick engine status pill
│   │   │   ├── Dropzone.tsx       # Drag & drop area with category badges
│   │   │   ├── QueueItem.tsx      # Per-file card, smart target selector & download
│   │   │   ├── BatchActionBar.tsx # Batch actions, convert all, and ZIP download
│   │   │   └── SettingsDrawer.tsx # Image quality, audio bitrate & system status
│   │   ├── types/index.ts         # TypeScript definitions
│   │   ├── utils/formatters.ts    # Format size, percentage savings, category detection
│   │   ├── App.tsx                # Main view with queue manager & confetti
│   │   └── index.css              # Tailwind CSS v4 styling
│   ├── vite.config.ts             # Vite config with backend /api proxy
│   └── package.json
│
└── README.md
```

---

## ⚙️ Quick Start

### 1. Prerequisites
- Python 3.10+
- Node.js 18+ & npm

### 2. Backend Setup
```bash
cd backend
python -m venv venv

# Windows:
.\venv\Scripts\activate
# Linux/macOS:
# source venv/bin/activate

pip install -r requirements.txt
python run.py
```
Backend will start at `http://127.0.0.1:8000`.

### 3. Frontend Setup
In a new terminal:
```bash
cd frontend
npm install
npm run dev
```
Frontend will start at `http://localhost:5173` with automatic API proxying to port 8000.

---

## 🧪 Automated Testing

To run the backend test suite:
```bash
cd backend
.\venv\Scripts\python.exe -m pytest -v tests/test_converters.py
```
Includes tests for:
- System capabilities detection
- PNG to WEBP conversion
- RGBA transparent PNG to JPEG with white composite
- CSV to JSON and CSV to XLSX
- Markdown to HTML
- WAV to MP3 with FFmpeg
- End-to-end API upload and ZIP download

