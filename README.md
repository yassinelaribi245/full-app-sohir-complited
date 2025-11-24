# School Portal

A full‑stack school management portal with:

- Role‑based access: **Student**, **Teacher**, **Admin**
- Course, class, quiz and exam management
- Student–teacher interaction via posts/questions
- REST API built with **Laravel**
- Frontend built with **Angular**

---

## Table of Contents

1. [Tech Stack](#tech-stack)  
2. [Architecture](#architecture)  
3. [Getting Started](#getting-started)  
   - [Prerequisites](#prerequisites)  
   - [Clone the Repository](#clone-the-repository)  
   - [Backend Setup (Laravel API)](#backend-setup-laravel-api)  
   - [Frontend Setup (Angular)](#frontend-setup-angular)  
   - [Ports](#ports)  
4. [Usage Overview](#usage-overview)  
5. [API Overview](#api-overview)  
   - [Authentication](#authentication)  
   - [Public Endpoints](#public-endpoints)  
   - [Student Endpoints](#student-endpoints)  
   - [Teacher Endpoints](#teacher-endpoints)  
   - [Admin Endpoints](#admin-endpoints)  
   - [Teacher Course CRUD (Enseignant/Cours)](#teacher-course-crud-enseignantcours)  
6. [Frontend Overview](#frontend-overview)  
7. [Environment & Configuration](#environment--configuration)

---

## Quick Start – Setup Commands

```bash
# 1) Clone the repo
git clone <your-repo-url>.git
cd "sohir"   # or the folder name of your project
```

### Backend (Laravel API)

```bash
# 2) Go to backend folder
cd school-portal

# 3) Install PHP dependencies
composer install

# 4) Copy environment file (Windows PowerShell)
copy .env.example .env

# 5) Generate app key
php artisan key:generate

# 6) Run migrations (and seed if you have seeders)
php artisan migrate
# php artisan db:seed   # optional

# 7) Start Laravel server on port 8000
php artisan serve --host=127.0.0.1 --port=8000
# API is now at: http://127.0.0.1:8000/api
```

Leave this terminal open.

### Frontend (Angular)

Open a **new** terminal in the project root (`sohir`), then:

```bash
# 8) Go to frontend
cd frontend

# 9) Install JS dependencies
npm install

# 10) Run Angular dev server on port 4200
ng serve --port 4200
# or
npm start
# Frontend is now at: http://localhost:4200
```

---

## Tech Stack

- **Backend**
  - PHP / Laravel (Sanctum for API tokens)
  - MySQL (or compatible) database
- **Frontend**
  - Angular
  - TypeScript
- **Package managers**
  - Composer (PHP)
  - npm (Node.js)

---

## Architecture

- `school-portal/` – Laravel backend (REST API)
  - Main routes file: `school-portal/routes/api.php`
  - Controllers: `school-portal/app/Http/Controllers`
- `frontend/` – Angular SPA
  - API base URL config: `frontend/src/app/core/api-config.ts`

The Angular frontend consumes the Laravel API via HTTP requests and attaches a **Bearer token** (Sanctum) to authenticated endpoints.

---

## Getting Started

These steps are for any team member who clones the project from GitHub.

### Prerequisites

- **Global**
  - Git
- **Backend**
  - PHP (compatible with the Laravel version)
  - Composer
  - MySQL / MariaDB (or other DB you configure in `.env`)
- **Frontend**
  - Node.js (LTS)
  - npm
  - Angular CLI (recommended):  
    `npm install -g @angular/cli`

---

## Clone the Repository

From any directory on your machine:

```bash
git clone <your-repo-url>.git
cd "sohir"
```

Replace `sohir` with the real folder name if different.

Project root on this machine:

```text
C:\Users\larib\Desktop\well i can explain\sohir
```

---

## Backend Setup (Laravel API)

From the project root:

```bash
cd school-portal
```

### 1. Install PHP dependencies

```bash
composer install
```

### 2. Create environment file

On Windows PowerShell, from `school-portal/`:

```bash
copy .env.example .env
```

### 3. Configure `.env`

At minimum, set:

```env
APP_NAME="School Portal"
APP_URL=http://127.0.0.1:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=school_portal    # choose a name
DB_USERNAME=your_user
DB_PASSWORD=your_password
```

Configure any other settings you need (mail, files, etc.).

### 4. Generate app key

```bash
php artisan key:generate
```

### 5. Run migrations (+ optional seeders)

```bash
php artisan migrate
# php artisan db:seed   # optional, if you have seeders
```

### 6. (Optional) Create storage symlink

```bash
php artisan storage:link
```

### 7. Run the backend server

```bash
php artisan serve --host=127.0.0.1 --port=8000
```

- Backend URL: `http://127.0.0.1:8000`
- **API base URL:** `http://127.0.0.1:8000/api`

Keep this terminal open while developing.

---

## Frontend Setup (Angular)

Open a **new terminal** (do not stop Laravel). From the project root:

```bash
cd frontend
```

### 1. Install JS dependencies

```bash
npm install
```

### 2. Configure API base URL

File: `src/app/core/api-config.ts`

```ts
export const API_CONFIG = {
  baseUrl: 'http://127.0.0.1:8000/api'
};
```

If the backend runs on another host/port, adjust this value.

### 3. Run the Angular dev server

```bash
ng serve --port 4200
# or
npm start
```

- Frontend URL: `http://localhost:4200`

---

## Ports

Default local development ports:

- **Backend (Laravel API):** `http://127.0.0.1:8000`  
- **API base path:** `http://127.0.0.1:8000/api`  
- **Frontend (Angular):** `http://localhost:4200`

If you change ports:

- Laravel: `php artisan serve --port=XXXX`
- Angular: `ng serve --port=YYYY`
- Update `frontend/src/app/core/api-config.ts` with the new base URL.

---

## Usage Overview

Typical flows:

- **Student**
  - Register / login
  - Join classes, access courses
  - Take quizzes and exams
  - View grades
  - Create posts/questions for teachers

- **Teacher**
  - Manage classes and students
  - Create and manage courses
  - Create quizzes and exams, manage questions
  - View and grade results
  - Answer student posts

- **Admin**
  - Manage users (create/update/delete)
  - Approve class and course requests
  - View stats and perform administrative actions

Authentication is token‑based using Laravel Sanctum. The frontend stores the token after login and sends it in the `Authorization: Bearer <token>` header.

---

## API Overview

**Base URL:** `http://127.0.0.1:8000/api`

The full list of routes is defined in `school-portal/routes/api.php`. Below is a structured overview.

### Authentication

Using **Laravel Sanctum** with personal access tokens.

- **Register**
  - **POST** `/register`
  - **Body (JSON)**:
    - `name`: string, required
    - `email`: string (email), required
    - `password`: string, min 6, required
    - `password_confirmation`: string, must match `password`
    - `role`: optional, `"student"` or `"teacher"` (default `"student"`)
  - **Response 201**:
    - `user`: user object
    - `token`: string (store and use as Bearer token)
    - `message`: info about pending/active status for teachers

- **Login**
  - **POST** `/login`
  - **Body (JSON)**:
    - `email`: string, required
    - `password`: string, required
  - **Responses**:
    - `200`: `{ user, token }`
    - `401`: `{"message": "Invalid credentials"}`
    - `403`: for pending teacher accounts

- **Auth header for protected routes**
  - Add header to requests:
    - `Authorization: Bearer <token>`

---

### Public Endpoints

No authentication required.

- **List public courses**
  - **GET** `/public-courses`

- **Show one public course**
  - **GET** `/public-courses/{id}`

---

### Student Endpoints

All routes require: `middleware: ['auth:sanctum', 'student']`.

- **Get profile**
  - **GET** `/student/profile`

- **Update profile**
  - **PUT** `/student/profile`
  - **Body**: `{ "name": "New Name" }`

- **My classes**
  - **GET** `/student/my-classes`

- **Courses in a class**
  - **GET** `/student/class/{class}/courses`

- **Send join request to a class**
  - **POST** `/student/join-request/{class}`

- **My join requests**
  - **GET** `/student/my-requests`

- **My grades**
  - **GET** `/student/my-grades`

#### Student – Quizzes & Exams

- **List quizzes by course**  
  **GET** `/student/course/{courseId}/quizzes`

- **List exams by course**  
  **GET** `/student/course/{courseId}/exams`

- **Check if quiz taken**  
  **GET** `/student/quiz/{quiz}/taken`

- **Check if exam taken**  
  **GET** `/student/exam/{exam}/taken`

- **Get quiz details**  
  **GET** `/student/quiz/{quiz}`

- **Get exam details**  
  **GET** `/student/exam/{exam}`

- **Get quiz questions**  
  **GET** `/student/quiz/{quiz}/questions`

- **Get exam questions**  
  **GET** `/student/exam/{exam}/questions`

- **Attempt/submit quiz**  
  **POST** `/student/quiz/{quiz}/attempt` or `/student/quiz/{quiz}/submit`
  - **Body**:
    ```json
    {
      "answers": {
        "questionId1": "chosenOption",
        "questionId2": "chosenOption"
      }
    }
    ```

- **Attempt/submit exam**  
  **POST** `/student/exam/{exam}/attempt` or `/student/exam/{exam}/submit`
  - **Body**:
    ```json
    {
      "answers": {
        "questionId1": "text answer 1",
        "questionId2": "text answer 2"
      }
    }
    ```

- **Get quiz result**  
  **GET** `/student/quiz/{quiz}/result`

- **Get exam result**  
  **GET** `/student/exam/{exam}/result`

- **Get all results for a course**  
  **GET** `/student/course/{courseId}/results`

#### Student – Posts (questions to teacher)

- **Create post**  
  **POST** `/posts`  
  **Auth**: student (auth:sanctum + student)

- **List posts by course**  
  **GET** `/posts/course/{courseId}`  
  **Auth**: any authenticated user (auth:sanctum)

---

### Teacher Endpoints

All routes require: `middleware: ['auth:sanctum', 'teacher']`.

- **List join requests to my classes**  
  **GET** `/teacher/join-requests`

- **Accept join request**  
  **POST** `/teacher/join-requests/{request}/accept`

- **Reject join request**  
  **POST** `/teacher/join-requests/{request}/reject`

- **Create class**  
  **POST** `/teacher/class`

- **Create class creation request**  
  **POST** `/teacher/class-requests`

- **List my class requests**  
  **GET** `/teacher/class-requests`

- **My classes**  
  **GET** `/teacher/my-classes`

- **Search students**  
  **GET** `/teacher/search-student`

- **Students in a class**  
  **GET** `/teacher/class/{class}/students`

- **Courses in a class**  
  **GET** `/teacher/class/{class}/courses`

- **Add student to class**  
  **POST** `/teacher/class/{class}/student`

- **Update class**  
  **PUT** `/teacher/class/{class}`

- **Delete class**  
  **DELETE** `/teacher/class/{class}`

- **Remove student from class**  
  **DELETE** `/teacher/class/{class}/student/{student}`

#### Teacher – Quizzes & Exams (REST resources)

- **Quizzes**  
  `Route::apiResource('teacher/quiz', QuizController::class);`

  Base path: `/teacher/quiz`
  - `GET /teacher/quiz`
  - `POST /teacher/quiz`
  - `GET /teacher/quiz/{id}`
  - `PUT/PATCH /teacher/quiz/{id}`
  - `DELETE /teacher/quiz/{id}`

- **Exams**  
  `Route::apiResource('teacher/exam', ExamController::class);`

  Base path: `/teacher/exam`
  - `GET /teacher/exam`
  - `POST /teacher/exam`
  - `GET /teacher/exam/{id}`
  - `PUT/PATCH /teacher/exam/{id}`
  - `DELETE /teacher/exam/{id}`

#### Teacher – Quiz questions

- **List**  
  **GET** `/teacher/question/quiz/{quiz}`

- **Create**  
  **POST** `/teacher/question/quiz/{quiz}`

- **Update**  
  **PUT** `/teacher/question/quiz/{quiz}/{question}`

- **Delete**  
  **DELETE** `/teacher/question/quiz/{quiz}/{question}`

#### Teacher – Exam questions

- **List**  
  **GET** `/teacher/question/exam/{exam}`

- **Create**  
  **POST** `/teacher/question/exam/{exam}`

- **Update**  
  **PUT** `/teacher/question/exam/{exam}/{question}`

- **Delete**  
  **DELETE** `/teacher/question/exam/{exam}/{question}`

#### Teacher – Results & Post answers

- **Quiz results**  
  **GET** `/teacher/quiz/{quiz}/results`

- **Exam results**  
  **GET** `/teacher/exam/{exam}/results`

- **Update exam score**  
  **PUT** `/teacher/exam/{exam}/result/{resultId}/score`

- **Answer a student post**  
  **POST** `/posts/{post}/answer`

---

### Admin Endpoints

All routes require: `middleware: ['auth:sanctum', 'admin']`.

- **Manage users**  
  `Route::apiResource('admin/users', AdminUserController::class);`  
  Base path: `/admin/users`  
  Standard REST: index, show, store, update, destroy

- **Dashboard stats**  
  **GET** `/admin/stats`

- **List all classes**  
  **GET** `/admin/classes`

- **Class requests (approval)**
  - **GET** `/admin/class-requests`
  - **POST** `/admin/class-requests/{requestRecord}/approve`
  - **POST** `/admin/class-requests/{requestRecord}/reject`

- **Course requests (approval)**
  - **GET** `/admin/course-requests`
  - **POST** `/admin/course-requests/{course}/approve`
  - **POST** `/admin/course-requests/{course}/reject`

- **Cascade deletes**
  - **DELETE** `/admin/teachers/{user}`
  - **DELETE** `/admin/classes/{class}`
  - **DELETE** `/admin/courses/{course}`

---

### Teacher Course CRUD (Enseignant/Cours)

Routes under prefix `/enseignant/cours`, `auth:sanctum`:

- **Create course**  
  **POST** `/enseignant/cours`

- **List courses**  
  **GET** `/enseignant/cours`

- **Search courses**  
  **GET** `/enseignant/cours/recherche`

- **Courses by teacher**  
  **GET** `/enseignant/cours/enseignant/{enseignantId}`

- **Show course**  
  **GET** `/enseignant/cours/{id}`

- **Update course**  
  **PUT** `/enseignant/cours/{id}`

- **Add supports (files)**  
  **POST** `/enseignant/cours/{id}/supports`

- **Delete course**  
  **DELETE** `/enseignant/cours/{id}`

---

## Frontend Overview

- **API config**  
  `frontend/src/app/core/api-config.ts` – single source of truth for API base URL:

  ```ts
  export const API_CONFIG = {
    baseUrl: 'http://127.0.0.1:8000/api'
  };
  ```

- **Services**  
  Angular services call the Laravel endpoints and handle tokens.

- **Guards / Interceptors**  
  Responsible for protecting routes and attaching `Authorization` headers.

- **Role‑based UI**  
  Different sections for students, teachers, and admins, mapped to the API endpoints above.

Key rules for frontend contributors:

- Use `API_CONFIG.baseUrl` instead of hardcoding URLs.
- Always send the Bearer token where needed.
- Respect role separation (student vs teacher vs admin).

---

## Environment & Configuration

- **Backend**
  - Controlled via `.env` in `school-portal/`.
  - Change DB, app URL, storage, mail, etc. here.

- **Frontend**
  - API base URL set in `frontend/src/app/core/api-config.ts`.
  - You can also use Angular environment files (`environment.ts`) if you add multiple environments (dev/staging/prod).

---

For any new team member:

1. Install prerequisites.
2. Clone the repo and go to `C:\Users\larib\Desktop\well i can explain\sohir` (or your own clone directory).
3. Follow **Backend Setup**.
4. Follow **Frontend Setup**.
5. Open `http://localhost:4200` and start using the app.
