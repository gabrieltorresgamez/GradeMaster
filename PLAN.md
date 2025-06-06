# 📚 Course Management Platform – Technical Overview

## 🔎 Introduction

This project is a self-hosted, Dockerized **course management platform** designed specifically for **lecturers**. It allows them to create and manage courses, track student assessments, calculate final grades, and export course data in a structured format. The platform includes document management features for both student-specific and course-level files, and is built with simplicity, portability, and clean separation of concerns in mind.

There is **no student access** — the system is fully tailored for lecturers and their workflows.

---

## ✅ Logical Requirements

These describe the **functional behavior** expected from the platform.

### 👤 User Access & Roles

* Only **lecturers** can log in.
* Each lecturer can:

  * Manage **their own courses**.
  * Add **co-lecturers** to shared courses.
  * View and manage only the courses they are assigned to.

### 🏫 Course Management

* Create and edit courses (title, semester, etc.).
* Assign students to courses (name, ID).
* Add **graded assessments** (PDFs, comments, grades) per student.
* Add **non-graded documents** per student (e.g., attendance sheets, notes).
* Upload **course-level documents**, e.g. assessment scheme templates.

### 📊 Grading & Summaries

* Calculate final grades from assessments.
* Generate a **summary file** (`summary.txt`) per student:

  * Assessment overview
  * Final grade
  * Additional feedback

### 📦 Export Functionality

* Export a course as a **ZIP archive** with the structure:

  ```
  courseName/
    _course_docs/
      template.pdf
      ...
    student1/
      assessment1.pdf
      summary.txt
      notes.pdf
    student2/
      ...
  ```

---

## ⚙️ Technical Requirements

These describe the **implementation architecture and tooling**.

### 🧱 Stack & Architecture

| Layer     | Technology                                        | Role                               |
| --------- | ------------------------------------------------- | ---------------------------------- |
| Backend   | **Go** with embedded **PocketBase**               | Auth, database, file store, logic  |
| Frontend  | **Static HTML**, **Tailwind CSS (CDN)**, **HTMX** | UI without SPA complexity          |
| Database  | **SQLite** (via PocketBase)                       | Auto-initialized embedded database |
| Container | **Docker + docker-compose**                       | Unified deployment and dev env     |

### ⚙️ Development & Deployment

* **Single-command startup** via:

  ```bash
  docker-compose up --build
  ```
* Auto-creates tables/schemas if not present
* Entire stack runs inside Docker (also for development)
* Hot-reload for Go optional (e.g. via `air`)

### 🔒 Auth & Access Control

* Auth handled by PocketBase
* Custom Go middleware ensures:

  * Only assigned lecturers can access a course
  * File access is scoped per course and lecturer

### 🗂️ File Handling

* File upload/download per:

  * Student (graded & ungraded documents)
  * Course-level (global documents)
* Routes like:

  * `POST /upload/student/:studentId`
  * `POST /upload/course/:courseId`
  * `GET /export/zip/:courseId`

### 📁 Internal File Layout (PB file storage)

```
pb_data/
  storage/
    course_123/
      course_docs/
      student_001/
      student_002/
```
