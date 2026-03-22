# capstone-project-1

# Capstone Project #1 — AI-Based Student Attendance Verification System

## Project Overview

This project is a real-time facial recognition system designed to streamline student attendance verification during evening self-study (야자) sessions at school. The traditional method of manually checking student ID cards is time-consuming and prone to human error. This system replaces that process with an automated, camera-based face authentication solution that can identify students within 1–2 seconds.

---

## Problem Statement

In many Korean high schools, students are required to present their physical ID cards during evening study hall check-ins. This manual process creates bottlenecks when large numbers of students arrive simultaneously, and can lead to inaccurate records. The goal of this project is to solve that inefficiency through computer vision and AI.

---

## Solution

A web-based facial recognition attendance system that:
- Automatically detects and identifies a student's face through a laptop webcam
- Matches the live face against pre-registered student ID photos stored in a database
- Records attendance in real time with a timestamp
- Displays the result (verified / already checked in / no match) immediately on screen
- Prevents duplicate check-ins within a 30-minute window

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML, CSS, JavaScript |
| Backend | Python, FastAPI |
| AI / Face Recognition | DeepFace (Facenet512 model), OpenCV |
| Database | PostgreSQL, SQLAlchemy ORM |
| Dev Environment | Visual Studio Code, Uvicorn |

---

## System Architecture

```
[Browser / Laptop Webcam]
        ↓  (Base64 image via HTTP POST)
[FastAPI Backend]
        ↓
[DeepFace Engine]
  - Face detection (OpenCV)
  - Embedding extraction (Facenet512, 512-dim vector)
  - Cosine similarity comparison
        ↓
[PostgreSQL Database]
  - students (student info)
  - face_embeddings (512-dim vectors)
  - attendance_logs (check-in records)
        ↓
[Result displayed on screen]
  ✅ Authentication complete
  ↩ Already verified
  ❌ No matching face
  👤 Please face the camera
```

---

## Key Features

- **Real-time auto-scan** — The system captures and analyzes a frame every 0.8 seconds without any button press required
- **Instant feedback** — A color-coded message bar at the bottom of the camera view always shows the current status
- **Duplicate prevention** — Students who have already checked in within 30 minutes receive an "already verified" message
- **Attendance dashboard** — A split view showing verified students (with timestamp and confidence score) and absent students simultaneously
- **Admin controls** — Incorrect attendance records and student registrations can be deleted directly from the UI
- **Bulk registration** — All student ID photos can be pre-registered via a Python script (`bulk_register.py`)

---

## How It Works

1. A student stands in front of the laptop camera
2. The system captures a frame every 0.8 seconds automatically
3. DeepFace extracts a 512-dimensional facial embedding from the captured image
4. The embedding is compared against all registered embeddings in the database using cosine similarity
5. If the similarity score exceeds the threshold (0.35), the student is identified and attendance is recorded
6. The result is displayed on screen within 1–2 seconds

---

## Privacy Considerations

- Original student photos are **not stored** in the database
- Only the 512-dimensional embedding vectors are stored, which cannot be reverse-engineered back into a face image
- This approach aligns with data minimization principles

---

## Project Timeline

| Phase | Description |
|---|---|
| Week 1 | Problem definition, tech stack selection, system design |
| Week 2 | Backend API development (FastAPI + PostgreSQL) |
| Week 3 | DeepFace integration, embedding storage, matching logic |
| Week 4 | Frontend development (camera capture, real-time UI) |
| Week 5 | Testing, accuracy tuning, UI/UX refinement |

---

## Challenges & Learnings

- **Python version compatibility** — TensorFlow (required by DeepFace) does not yet support Python 3.14; resolved by installing Python 3.11 in parallel
- **Accuracy vs. speed trade-off** — Switched from `retinaface` to `opencv` as the face detector to reduce latency, while tuning the cosine similarity threshold to maintain accuracy
- **Windows encoding issues** — PostgreSQL connection strings caused Unicode errors on Korean Windows systems; resolved by switching from `psycopg2` to `psycopg`
- **Real-world constraints** — Designed with a high-throughput scenario in mind (many students arriving at once), requiring sub-2-second recognition and automatic reset between verifications

---

## Future Improvements

- Add liveness detection to prevent photo spoofing
- Support mobile devices via PWA (Progressive Web App)
- Integrate with school management systems for automatic record syncing
- Add JWT-based admin authentication for the management dashboard

---

## Developer

- **Name:** 이시온
- **Grade/Class:** (학년/반 입력)
- **School:** (학교명 입력)
- **Project:** Capstone Project #1
- **Advisor:** Josh Teacher
