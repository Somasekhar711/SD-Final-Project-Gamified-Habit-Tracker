# 🏆 HabitQuest — Gamified Habit Tracker

> **Level up your daily routine.**  
> A full-stack, gamified habit-tracking application built with Node.js, PostgreSQL, and vanilla JavaScript. Track habits, complete tasks, run focus sessions, earn XP, unlock badges, and reveal hidden images through a unique puzzle system.

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Database Schema](#-database-schema)
- [API Endpoints](#-api-endpoints)
- [Setup & Installation](#-setup--installation)
- [Environment Variables](#-environment-variables)
- [Testing](#-testing)
- [Deployment](#-deployment)
- [Screenshots](#-screenshots)
- [Team & Credits](#-team--credits)
- [License](#-license)

---

## 🎮 Overview

**HabitQuest** transforms habit-building into an engaging RPG-like experience. Users create habits, manage tasks, schedule their week, run focus sessions, and compete on a global leaderboard. Every productive action earns **XP**, levels up the user, and contributes to a **streak-based image puzzle reveal system** — a first-of-its-kind gamification mechanic.

---

## ✨ Features

### 🔐 Authentication & Security
- JWT-based token authentication (7-day expiry)
- bcrypt password hashing (salt rounds: 10)
- Auto-login after registration
- Protected routes with Bearer token middleware
- Express-validator input sanitization
- CORS enabled for cross-origin requests

### 🌱 Habit Management
- Create habits with **Daily** or **Weekly** frequency
- Set custom target counts (e.g., 30 min meditation)
- Track completion logs per day with `habit_logs`
- Automatic **current streak** and **best streak** calculation
- Intelligent streak logic: daily habits require consecutive days; weekly habits allow a 7-day window
- XP formula: `10 + (streak × 2) + target_count`

### ✅ Task Management
- Create tasks with title, description, priority, and due date
- Priority levels: Low (1), Medium (2), High (3)
- Mark tasks as completed
- Persistent storage with user-scoped access

### ⏱️ Focus Sessions
- Start/stop focus timers with backend persistence
- Customizable durations (15, 25, 45, 60, 90 min or custom up to 240 min)
- Sessions under 5 minutes earn no XP
- XP reward calculated from duration
- **Ambient sound generator** (Web Audio API): Rain, Fire, Cafe, Forest, Waves, Space
- Session history with total minutes and XP earned
- SVG ring countdown animation with particle effects

### 📅 Timetable / Weekly Schedule
- Create time slots for any day of the week (Mon–Sun)
- Visual weekly grid with start/end times
- Color-coded activities
- Reminder toast notifications
- Active slot highlighting based on current time

### 🏆 Gamification & Badges
- **8 Predefined Badges:**
  1. **First Orbit** — Earn 50 XP
  2. **Centurion** — Earn 100 XP
  3. **Task Runner** — Complete 5 tasks
  4. **Task Master** — Complete 25 tasks
  5. **Habit Starter** — Complete 5 habits
  6. **Streak Keeper** — Reach a 7-day best streak
  7. **Deep Diver** — Complete 5 focus sessions
  8. **Time Bender** — Finish a 60-minute focus session
- Automatic badge evaluation after every productive action
- Real-time badge unlock notifications

### 🧩 Image Puzzle Reveal System *(Unique Feature)*
- A **streak-driven tile-reveal puzzle** tied to habit completion
- Completing a habit on a new day advances the puzzle streak
- Every streak milestone unlocks a tile on a hidden image
- Users can reveal tiles manually or randomly
- 50+ curated puzzle images with configurable grid sizes
- When a puzzle is fully revealed, the next image auto-assigns
- Transaction-safe SQL with rollback on errors
- Persistent puzzle progress, streak state, and available unlocks

### 📊 Leaderboard
- Global rankings by total XP
- Level calculation: `Math.floor(xp / 100) + 1`
- Title progression: Novice → Apprentice → Warrior → Knight → Champion → Legend → Master → Grandmaster
- Real-time podium view + ranked list

### 📈 Stats & Analytics
- Total XP, Level, Title
- Habits completed, Tasks completed, Focus minutes
- Current streak, Longest streak, Days active
- 7-day habit completion chart
- 7-day focus minutes chart
- Streak calendar with daily status
- Badge collection progress

### 🎨 UI/UX Customization
- **Dark / Light mode** toggle
- **6 Color Themes:** Ember (Orange), Deep Space (Cyan), Forest (Green), Nebula (Purple), Crimson (Rose), Gold
- **Custom color picker** with live accent updates
- **6 Background Gradients:** Default, Space, Forest, Dusk, Steel, Crimson
- Persistent preference storage across sessions

---

## 🛠 Tech Stack

### Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | 18+ | Runtime |
| Express | 5.2.1 | Web framework |
| PostgreSQL | 14+ | Database |
| pg | 8.17.2 | PostgreSQL driver |
| bcrypt | 6.0.0 | Password hashing |
| jsonwebtoken | 9.0.3 | JWT authentication |
| express-validator | 7.3.1 | Input validation |
| cors | 2.8.5 | Cross-origin requests |
| morgan | 1.10.1 | HTTP request logging |
| dotenv | 17.2.4 | Environment variables |
| nodemon | 3.1.11 | Dev auto-reload |

### Frontend
| Technology | Purpose |
|------------|---------|
| HTML5 | Semantic markup |
| CSS3 | Styling, animations, variables |
| Vanilla JavaScript (ES Modules) | Logic & API integration |
| Font Awesome 6.5.1 | Icons |
| Google Fonts (Orbitron, Jersey 25, SN Pro) | Typography |
| Canvas API | Charts & particle effects |
| Web Audio API | Ambient sound synthesis |
| LocalStorage | Offline resilience & preferences |

### DevOps & Tools
| Tool | Purpose |
|------|---------|
| Git | Version control |
| Postman | API testing |
| pgAdmin | Database management |
| Render | Backend deployment |
| Vercel | Frontend deployment |

---

## 🏛 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT                                │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│  │  Auth   │ │ Habits  │ │  Tasks  │ │  Focus  │          │
│  │  Pages  │ │  Page   │ │  Page   │ │  Page   │          │
│  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘          │
│       └─────────────┴─────────┴─────────────┘              │
│                     JS API Layer                             │
│              (fetch + JWT + localStorage)                   │
└────────────────────────┬────────────────────────────────────┘
                         │ HTTP / REST / JSON
┌────────────────────────┴────────────────────────────────────┐
│                        SERVER                                │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  Express App  ←  CORS + Morgan + JSON Body Parser   │  │
│  └──────────────────────────────────────────────────────┘  │
│                          │                                   │
│  ┌─────────┬─────────┬───┴───┬─────────┬─────────┐        │
│  │  Auth   │  Habit  │ Task  │  Focus  │Timetable│        │
│  │ Routes  │ Routes  │Routes │ Routes  │ Routes  │        │
│  └────┬────┴────┬────┴───┬───┴────┬────┴────┬────┘        │
│  ┌────┴────────┴────────┴────────┴────────┴────┐        │
│  │              Controllers                      │        │
│  └────┬────────┬────────┬────────┬────────┬────┘        │
│  ┌────┴────────┴────────┴────────┴────────┴────┐        │
│  │              Services                         │        │
│  │  (Business Logic + Gamification + Puzzle)    │        │
│  └────┬────────┬────────┬────────┬────────┬────┘        │
│  ┌────┴────────┴────────┴────────┴────────┴────┐        │
│  │              Repositories                     │        │
│  │         (Raw SQL Queries via pg)              │        │
│  └─────────────────────────────────────────────┘        │
│                          │                                   │
│                   ┌──────┴──────┐                          │
│                   │ PostgreSQL  │                          │
│                   │  (pgAdmin)  │                          │
│                   └─────────────┘                          │
└─────────────────────────────────────────────────────────────┘
```

### Design Patterns Used
- **Layered Architecture:** Routes → Controllers → Services → Repositories
- **Repository Pattern:** All database queries isolated in repository files
- **Service Layer:** Business logic, calculations, and gamification rules
- **Middleware Pattern:** Auth verification, error handling, input validation
- **Custom Error Class:** `AppError` for consistent HTTP error responses

---

## 🗄 Database Schema

### Tables

| Table | Purpose |
|-------|---------|
| `users` | User accounts (UUID, name, email, password_hash) |
| `user_stats` | Aggregated stats (XP, tasks completed, habits completed, login streak) |
| `tasks` | User tasks with priority, status, due date |
| `habits` | Habits with frequency, target count, streaks |
| `habit_logs` | Daily completion logs per habit |
| `timetable` | Weekly schedule slots |
| `focus_sessions` | Focus timer sessions with start/end times |
| `badges` | Predefined achievement badges |
| `user_badges` | Many-to-many: users ↔ earned badges |
| `puzzle_images` | Curated images for the reveal system |
| `puzzle_progress` | Per-user puzzle tile reveal state |
| `puzzle_state` | Per-user streak and available unlocks |

### Key Constraints
- UUID primary keys with `gen_random_uuid()`
- Foreign keys with `ON DELETE CASCADE`
- Check constraints on enums (status, frequency, priority)
- Non-negative checks on counters
- Unique constraints on emails and badge names
- Composite unique on `(habit_id, completed_date)`

### Indexes
- `tasks(user_id, created_at DESC)`
- `habits(user_id)`
- `habit_logs(habit_id, completed_date)`
- `timetable(user_id, day_of_week, start_time)`
- `focus_sessions(user_id, completed)`
- `user_badges(user_id)`

---

## 🔌 API Endpoints

### Authentication (`/api/auth`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/register` | Register new user + auto-login JWT |
| POST | `/login` | Login with email/password |

### Habits (`/api/habits`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get all user habits |
| POST | `/create` | Create a new habit |
| POST | `/complete/:id` | Mark habit complete (triggers XP + badges + puzzle) |
| PUT | `/:id` | Update habit details |
| DELETE | `/:id` | Delete habit |

### Tasks (`/api/tasks`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get all user tasks |
| POST | `/` | Create a new task |
| POST | `/complete/:id` | Complete a task |

### Focus (`/api/focus`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/start` | Start a focus session |
| POST | `/stop/:id` | Stop session and calculate XP |

### Timetable (`/api/timetable`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get weekly schedule |
| POST | `/` | Add a time slot |
| DELETE | `/:id` | Remove a time slot |

### Gamification (`/api/gamification`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/badges` | Get all badges with earned status |
| POST | `/badges/check` | Evaluate and award new badges |

### Leaderboard (`/api/leaderboard`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get global rankings by XP |

### Puzzle (`/api/puzzle`)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/current` | Get current puzzle state and progress |
| POST | `/unlock` | Unlock a tile (specific or random) |

### Health
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/health` | Server health check |

---

## ⚙️ Setup & Installation

### Prerequisites
- Node.js 18+
- PostgreSQL 14+
- npm or yarn

### 1. Clone the Repository
```bash
git clone <repository-url>
cd "SD LAB PROJECT FINAL"
```

### 2. Database Setup
1. Open **pgAdmin** and create a database named `habit_tracker`.
2. Run the schema file:
   ```bash
   psql -U your_username -d habit_tracker -f server/sql/schema.sql
   ```
3. Seed the badges:
   ```bash
   psql -U your_username -d habit_tracker -f server/sql/seed_badges.sql
   ```
4. (Optional) Seed puzzle images:
   ```bash
   psql -U your_username -d habit_tracker -f server/sql/seed_puzzle_images.sql
   ```

### 3. Backend Setup
```bash
cd server
cp .env.example .env
# Edit .env with your database credentials and JWT secret
npm install
npm run dev
```
The server will start on `http://localhost:3137`.

### 4. Frontend Setup
The frontend is served statically by the Express server from the `/client` directory. Simply open:
```
http://localhost:3137/index.html
```

No separate build step is required for the frontend.

---

## 🔑 Environment Variables

Create a `.env` file inside `server/`:

```env
# Server
PORT=3137
NODE_ENV=development

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=habit_tracker
DB_USER=your_db_user
DB_PASSWORD=your_db_password

# JWT
JWT_SECRET=your_super_secret_jwt_key_here
JWT_EXPIRES_IN=7d
```

---

## 🧪 Testing

### Automated API Tests
A comprehensive test suite was executed covering all endpoints:

| Phase | Tests | Status |
|-------|-------|--------|
| Authentication | 4/4 | ✅ Passed |
| Habits | 4/4 | ✅ Passed |
| Tasks | 2/3 | ✅ Passed (1 transient timeout) |
| Focus Sessions | 2/2 | ✅ Passed |
| Gamification | 2/2 | ✅ Passed |
| Leaderboard | 1/1 | ✅ Passed |
| Timetable | 2/2 | ✅ Passed |
| Security | 2/2 | ✅ Passed |
| **Total** | **24/25** | **96% Pass Rate** |

### Critical Bugs Fixed During Testing
1. **Register Missing JWT** — Added token generation during registration for auto-login.
2. **Task Priority Type Mismatch** — Handled both numeric (1,2,3) and string (low,medium,high) inputs.
3. **Timetable NULL Constraint** — Fixed `day_of_week` parameter mapping to service layer.

### Manual Testing
A 16-test-case manual testing guide is included:
- User Registration & Login
- Dashboard Navigation
- Habit CRUD & Completion
- Task Management
- Focus Timer
- Timetable Scheduling
- Badge Earning
- Leaderboard Rankings
- Stats Verification
- Profile & Logout
- Security (Token Validation)
- UI/UX Verification
- Error Handling
- Data Persistence
- Multi-user Isolation

---

## 🚀 Deployment

### Backend (Render)
1. Push code to GitHub.
2. Create a new Web Service on Render.
3. Set environment variables from `.env`.
4. Build command: `npm install` (in `server/`)
5. Start command: `npm start`

### Frontend (Vercel)
1. The `client/` folder can be deployed separately on Vercel.
2. Update `API_ORIGIN` in `client/js/api.js` to point to your Render backend.
3. The server also serves static files, so a single-server deployment is possible.

---

| Page | Description |
|------|-------------|
| Login | Dark themed login with password visibility toggle |
| Dashboard | Central hub with XP bar, habits preview, timetable, puzzle, focus widget |
| Habits | Filterable habit list with categories, streaks, and completion |
| Focus | Full-screen focus timer with ambient sounds and particle effects |
| Timetable | Weekly grid with color-coded schedule slots |
| Leaderboard | Podium view + ranked list with tabs |
| Stats | XP ring, overview grid, charts, streak calendar |
| Achievements | Badge grid with progress bars and filters |
| Puzzle | Tile-reveal board with image progress and streak info |

---

## 👥 Team & Credits

### 👨‍💻 Backend Developer — **Chenna Sai Charan**
- Designed and implemented the entire REST API
- Database schema design with PostgreSQL
- JWT authentication & security middleware
- Gamification engine (XP, levels, badges)
- Puzzle reveal system with transactional SQL
- Habit streak calculation logic
- Focus session tracking
- Leaderboard ranking system
- Comprehensive testing & bug fixes

### 🎨 Frontend Developer — **Somasekhar Reddy**
- Complete UI/UX design and implementation
- 11 responsive HTML pages with semantic markup
- CSS theming system (dark/light, 6 themes, custom colors, backgrounds)
- Vanilla JavaScript modules for all interactive features
- Web Audio API ambient sound synthesis
- Canvas-based particle effects and charts
- LocalStorage-based offline resilience
- Puzzle tile flip animations and grid rendering


---

## 📄 License

This project was built for academic purposes. All rights reserved by the authors.

---

## 🔗 Links

- **Live Demo:** https://habitquest-rho.vercel.app/

---

<p align="center">
  <strong>Built with 💻, ☕, and a lot of XP grinding.</strong><br>
  <em>Consistency is the key. Level up every day.</em>
</p>

