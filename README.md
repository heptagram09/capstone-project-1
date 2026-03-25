# Face Authentication Attendance System
### Capstone Project #1 — AI-Based Real-Time Student Check-In

---

## Why I Built This

Every evening before mandatory self-study (야자), our school's supervising teacher manually checked attendance by going through each student one by one — writing names by hand, flipping through lists, calling out. The first ten minutes of every session were noisy and disorganized, and the teacher's time was spent on something a machine could do in a second.

I am the head of the school's media department, which gave me access to a professional Canon DSLR camera that most students don't have. I thought: we already have photos of every student on file. We have a high-resolution camera. Why are we still doing this by hand?

That question turned into this project.

---

## What It Does

A real-time face recognition system that replaces manual ID-card attendance checks with an automated camera-based solution.

- A student walks up to an external webcam
- The system captures a frame every **0.8 seconds** automatically — no button press needed
- DeepFace extracts a **512-dimensional facial embedding** using the Facenet512 model
- The embedding is compared against every registered student using **cosine similarity**
- If matched, attendance is logged to PostgreSQL with a **KST timestamp**
- A color-coded message bar always shows the current status — the screen is never blank

Students who check in within **30 minutes** of a previous entry receive an "already verified" message instead of a duplicate log.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend | Python 3.11, FastAPI, Uvicorn |
| Face Recognition AI | DeepFace (Facenet512), OpenCV, rembg |
| Database | PostgreSQL, SQLAlchemy ORM |
| Security | JWT (python-jose), bcrypt (passlib) |
| Deployment | Cloudflare Pages (frontend) + Cloudflare Tunnel (backend) |

---

## How the Face Recognition Works

```
External Webcam Frame (1080p)
        ↓
OpenCV — detect and crop face region only
(background, clothing, lighting all ignored)
        ↓
Histogram equalization — normalize lighting differences
        ↓
Brightness / contrast / sharpness enhancement
        ↓
DeepFace Facenet512 — extract 512-dim embedding vector
        ↓
Cosine similarity vs. all registered embeddings in PostgreSQL
        ↓
Result: ✅ Verified  ↩ Already checked in  ❌ No match  👤 Face not detected
```

**Registration** uses rembg AI background removal to produce clean embeddings from Canon camera photos.
**Verification** skips rembg entirely — only face crop and enhancement — keeping latency under 1 second.

---

## Key Features

- **External webcam only** — if USB webcam is not connected, a red warning appears. The laptop's built-in camera is never used.
- **Admin authentication** — JWT-protected dashboard. Student data, attendance records, and registration are only accessible after login.
- **Split attendance view** — real-time side-by-side display of verified students (with timestamp and confidence %) and absent students.
- **Bulk registration** — `bulk_register.py` reads filenames from the `photos/` folder automatically and skips already-registered students.
- **Photo preprocessing** — `preprocess.py` uses rembg to strip backgrounds and crop faces from Canon DSLR photos before registration.

---

## Challenges I Solved

| Problem | Solution |
|---|---|
| Python 3.14 incompatible with TensorFlow | Installed Python 3.11 in parallel |
| Korean Windows encoding broke PostgreSQL driver | Switched from psycopg2 to psycopg[binary] |
| Laptop camera + cluttered background caused 40%+ false match rate | Replaced with 1080p external webcam + Canon DSLR for registration photos |
| rembg on every frame made verification take 5 seconds | Split into two modes: rembg only at registration, fast crop-only at verification |
| Backlight caused face detection to fail consistently | Added OpenCV histogram equalization to normalize lighting before embedding extraction |
| Anyone could match as any student | Added face-only crop via OpenCV to strip background and clothing from embeddings |

---

## Project Structure

```
face-auth/
├── frontend/
│   ├── index.html       # UI structure
│   ├── style.css        # Design
│   └── app.js           # Camera, verify loop, admin auth
│
├── backend/
│   ├── main.py          # FastAPI endpoints
│   ├── face_service.py  # DeepFace, rembg, face crop logic
│   ├── models.py        # DB schema
│   ├── database.py      # PostgreSQL connection
│   ├── bulk_register.py # Batch student registration
│   ├── add_student.py   # Single student registration
│   └── preprocess.py    # Canon photo background removal and crop
│
└── .gitignore           # photos/, venv/, .env excluded
```

---

## Privacy

- Student photos are **never stored in the database** and are excluded from this repository via `.gitignore`
- Only **512-dimensional embedding vectors** are stored — these cannot be reverse-engineered into a face image
- All admin endpoints are protected by **JWT authentication**
- The only public endpoint is `/verify` — it returns a name and confidence score, nothing else

---

*Built by Lee Si-on — Media Department Head, CS Major*
*Capstone Project #1 | Advisor: Josh Teacher*
