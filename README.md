 # StreamVault 🎵

A music streaming backend built with **Node.js**, **Express.js**, and **MongoDB** — featuring a dual-role system with JWT-based authentication and role-based access control (RBAC).

---

## Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js |
| Framework | Express.js |
| Database | MongoDB (Mongoose) |
| Auth | JSON Web Tokens (JWT) |
| Password Hashing | bcrypt |
| Access Control | RBAC (User / Artist) |

---

## Features

### 🔐 Authentication
- **Register & Login** flows for both Users and Artists
- Passwords hashed with **bcrypt** before storage
- On successful login, a signed **JWT** is issued containing the user's role
- JWT middleware validates and decodes tokens on every protected route

### 🎭 Role-Based Access Control (RBAC)
Two roles are supported, each with scoped permissions:

| Permission | User | Artist |
|---|:---:|:---:|
| Browse tracks | ✅ | ✅ |
| Stream tracks | ✅ | ✅ |
| Upload tracks | ❌ | ✅ |
| Publish metadata | ❌ | ✅ |

Unauthorized role access returns a `403 Forbidden` response.

### 🎤 Artist Endpoints
- Upload tracks with associated metadata (title, genre, duration, etc.)
- Publish and manage their own catalog
- Blocked for regular users via role-verification middleware

### 🎧 User Endpoints
- Browse the full track catalog
- Stream tracks filtered by **artist** or **genre**
- Role-verified access on all streaming routes


---

## API Overview

### Auth Routes — `/api/auth`
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/register` | Register a new User or Artist |
| `POST` | `/login` | Login and receive a JWT |

### Artist Routes — `/api/artist` *(JWT + Artist role required)*
| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/upload` | Upload a new track with metadata |
| `GET` | `/my-tracks` | View all tracks uploaded by the artist |

### User Routes — `/api/tracks` *(JWT + User role required)*
| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/` | Browse all available tracks |
| `GET` | `/stream/:id` | Stream a specific track |
| `GET` | `/?artist=` | Filter tracks by artist |
| `GET` | `/?genre=` | Filter tracks by genre |

---

## Getting Started

### Prerequisites
- Node.js v18+
- MongoDB (local or Atlas)

### Installation

```bash
git clone https://github.com/your-username/streamvault.git
cd streamvault
npm install
```

### Environment Variables

Create a `.env` file in the root:

```env
PORT=5000
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret_key
JWT_EXPIRES_IN=7d
```

### Run the Server

```bash
# Development
npm run dev

# Production
npm start
```

---

## Authentication Flow

```
Client                        Server
  |                              |
  |--- POST /auth/register ----> |  Hash password, save user
  |--- POST /auth/login -------> |  Verify credentials, return JWT
  |                              |
  |--- GET /api/tracks --------> |  Verify JWT → check role → serve
  |--- POST /api/artist/upload-> |  Verify JWT → Artist only → 403 if User
```

---

## Security Highlights

- Passwords never stored in plain text — bcrypt with salt rounds
- JWT secrets stored in environment variables, never hardcoded
- Role claims embedded in token payload and re-verified server-side on every request
- Artist-only routes return `403` (not `404`) to clearly signal authorization failure

---
