## About

**LeetBuddy** is a free, open-source Chrome extension that provides AI-powered assistance for LeetCode problems. It helps developers improve their coding interview skills by offering contextual hints, explanations, edge case generation, and an interactive whiteboard - all without leaving the LeetCode page.

### Why LeetBuddy?

- **Learn, Don't Cheat**: Get hints and guidance without spoiling the solution
- **Context-Aware**: Automatically understands the problem, your code, and your progress
- **100% Free**: No paywalls, subscriptions, or hidden costs
- **Open Source**: Transparent, community-driven development
- **Privacy-Focused**: No tracking or data collection

---

## Features

### 🤖 Smart AI Assistant
- **Natural Language Chat**: Ask questions in plain English
- **Context Detection**: Automatically reads problem statements, constraints, and your code
- **Streaming Responses**: Real-time AI responses for better UX

### 💡 Intelligent Hints
- **Progressive Hints**: Get just enough help without revealing the solution
- **Edge Case Generator**: Discover test cases you might have missed
- **Complexity Analysis**: Understand time/space complexity of your approach

### ✏️ Interactive Whiteboard
- **Visual Diagrams**: Draw trees, graphs, arrays, and data structures
- **Image Recognition**: AI analyzes your drawings to provide explanations
- **Persistent Sessions**: Chat history saved per problem

### 🎨 Developer Experience
- **Multiple Modes**: Chat and Whiteboard interfaces
- **Syntax Highlighting**: Beautiful code rendering with Prism.js
- **Markdown Support**: Rich text formatting in responses
- **Session Management**: Conversations persist across page refreshes

---

## Architecture

LeetBuddy uses a microservices architecture with Docker containerization:

```
┌─────────────────────────────────────────────────────────────┐
│                         NGINX (Port 80)                     │
│  ┌────────────┬──────────────────┬───────────────────────┐  │
│  │ Static     │ /health          │ /api/LLM              │  │
│  │ Files      │ (Health Check)   │ (Extension API)       │  │
│  └────────────┴──────────────────┴───────────────────────┘  │
└──────┬───────────────┬────────────────────┬─────────────────┘
       │               │                    │
       ▼               ▼                    ▼
┌──────────────┐ ┌──────────────┐   ┌──────────────┐
│   Website    │ │   Website    │   │  Extension   │
│   Frontend   │ │   Backend    │   │   Backend    │
│              │ │              │   │              │
│ React + Vite │ │ Express.js   │   │ Express.js   │
│ TailwindCSS  │ │ (Port 3001)  │   │ (Port 3002)  │
└──────────────┘ └──────────────┘   └──────┬───────┘
                                           │
                                           ▼
                                    ┌──────────────┐
                                    │    Redis     │
                                    │  (Upstash)   │
                                    │              │
                                    │ Chat Session │
                                    │  Management  │
                                    └──────────────┘
                                           │
                                           ▼
                                    ┌──────────────┐
                                    │ Google Gemini│
                                    │     API      │
                                    │              │
                                    │  AI Model    │
                                    └──────────────┘
```

## Development

### Project Structure

```
LeetBuddy/
├── backend/
│   ├── extension/           # Extension backend service
│   │   ├── controllers/     # Request handlers
│   │   ├── routes/          # API routes
│   │   └── services/        # Business logic (LLM, Redis)
│   ├── website/             # Website backend service
│   │   ├── controllers/     # Health check controllers
│   │   └── routes/          # Health routes
│   ├── shared/              # Shared utilities
│   │   ├── cors.js          # CORS configuration
│   │   ├── errorHandlers.js # Error handling middleware
│   │   ├── logger.js        # Logging utilities
│   │   ├── rateLimiter.js   # Rate limiting
│   │   ├── redis.js         # Redis client wrapper
│   │   └── security.js      # Helmet security config
│   └── index.js             # Backend entry point
├── frontend/
│   ├── extension/           # Chrome extension
│   │   ├── public/          # Extension manifest & assets
│   │   └── src/
│   │       ├── background/  # Background script
│   │       ├── components/  # React components
│   │       ├── tabs/        # Chat & Whiteboard tabs
│   │       └── utils/       # Helper functions
│   └── website/             # Marketing website
│       └── src/
│           ├── components/  # React components
│           ├── pages/       # Page components
│           ├── hooks/       # Custom React hooks
│           └── data/        # Static data & SEO
├── nginx/
│   ├── default.conf         # Production nginx config
│   └── default.conf.dev     # Development nginx config
├── docker-compose.yml       # Docker orchestration
├── Dockerfile               # Production Docker image
├── Dockerfile.dev           # Development Docker image
├── render.yaml              # Render.com deployment config
└── .env                     # Environment variables
```

### Available Scripts

#### Root Level Scripts

```bash
# Development
npm run dev              # Start backend in development mode
npm run dev:website      # Start website frontend dev server
npm run dev:extension    # Start extension frontend dev server

# Building
npm run build            # Build website frontend
npm run build:website    # Build website frontend
npm run build:extension  # Build extension frontend
npm run build:all        # Build both frontends

# Installation
npm run install:all      # Install all dependencies
npm run install:backend  # Install backend dependencies only
npm run install:website  # Install website frontend dependencies
npm run install:extension # Install extension frontend dependencies

# Cleaning
npm run clean            # Remove node_modules, dist, lock files
npm run clean:modules    # Remove all node_modules
npm run clean:dist       # Remove all dist directories
npm run clean:locks      # Remove all package-lock files

# Reset & Rebuild
npm run reset            # Clean, reinstall, rebuild everything
npm run reset:hard       # Hard reset with cache clean
npm run fresh            # Hard reset + Docker rebuild (recommended)
npm run nuke             # Nuclear option: delete everything

# Docker
npm run docker:clean     # Remove Docker containers, volumes, images
npm run docker:reset     # Clean Docker and rebuild from scratch

# Testing
npm test                 # Run tests (placeholder)
```

#### Backend Scripts

```bash
cd backend
npm start               # Start production server
npm run dev             # Start with nodemon (auto-reload)
npm test                # Run backend tests
```

#### Frontend Website Scripts

```bash
cd frontend/website
npm run dev             # Start Vite dev server
npm run build           # Build for production
npm run preview         # Preview production build
npm run lint            # Run ESLint
```

#### Frontend Extension Scripts

```bash
cd frontend/extension
npm run dev             # Start Vite dev server with HMR
npm run build           # Build for production (creates dist/)
npm run preview         # Preview production build
npm run lint            # Run ESLint
```

### Tech Stack

#### Frontend (Website)
- **Framework**: React 19
- **Build Tool**: Vite 6
- **Styling**: TailwindCSS 4
- **Routing**: React Router 7
- **Icons**: Lucide React
- **SEO**: Custom meta tags + JSON-LD structured data

#### Frontend (Extension)
- **Framework**: React 19
- **Build Tool**: Vite 6
- **Styling**: TailwindCSS 4
- **Whiteboard**: tldraw
- **Markdown**: react-markdown + remark-gfm
- **Syntax Highlighting**: Prism.js + prism-react-renderer
- **Icons**: React Icons
- **State Management**: React Context API
- **UUID**: uuid v11

#### Backend
- **Runtime**: Node.js 22+
- **Framework**: Express.js 5
- **AI Model**: Google Gemini 2.5 Flash (@google/genai)
- **Database**: Redis (ioredis) via Upstash
- **Security**:
  - Helmet (security headers)
  - CORS with origin validation
  - Rate limiting (express-rate-limit)
- **Utilities**:
  - Compression
  - dotenv (environment variables)
  - Custom error handlers
  - Structured logging

#### Infrastructure
- **Containerization**: Docker + Docker Compose
- **Reverse Proxy**: NGINX
- **Deployment**: Render.com
- **CDN**: Cloudflare (for production)
- **External Services**:
  - Upstash Redis (session management)
  - Google Gemini API (AI responses)

#### Development Tools
- **Linting**: ESLint 9
- **Hot Reload**: Vite HMR, Nodemon
- **Environment Management**: dotenv

---

## Deployment

### Website & Backend

The website and backend are deployed on [Render.com](https://render.com) with automatic deployment from the `main` branch.

#### Production Deployment

1. **Push to main branch**
   ```bash
   git push origin main
   ```

2. Render.com automatically:
   - Detects changes
   - Builds Docker image using `Dockerfile`
   - Runs database migrations (if any)
   - Deploys to production
   - Runs health checks

#### Manual Deployment (Render.com)

1. **Configure Render service** using `render.yaml`
2. **Set environment variables** in Render dashboard (not synced from repo)
3. **Deploy manually** from Render dashboard if needed

#### Environment Variables (Production)

Set these in Render.com dashboard:

```bash
NODE_ENV=production
PORT=10000
REDIS_URL=<your-upstash-redis-url>
GEMINI_API_KEY=<your-gemini-key>
CHROME_EXTENSION_ORIGIN=chrome-extension://nlemdecocfoaimdbfgpilfgdmcllhphn
MODEL=gemini-2.5-flash
INSTRUCTIONS="<your-custom-instructions>"
VITE_SITE_URL=https://leetbuddy.app
VITE_VIDEO_URL=<your-cdn-video-url>
VITE_CHROME_STORE_URL=https://chromewebstore.google.com/detail/nlemdecocfoaimdbfgpilfgdmcllhphn
```

### Chrome Extension

#### Building for Production

1. **Build the extension**
   ```bash
   npm run build:extension
   ```

2. **Create distribution package**
   ```bash
   cd frontend/extension/dist
   zip -r ../extension.zip .
   cd ..
   ```

   This creates `frontend/extension/extension.zip`

#### Extension Manifest

Key settings in `frontend/extension/public/manifest.json`:

```json
{
  "manifest_version": 3,
  "name": "LeetBuddy",
  "version": "1.1.2",
  "permissions": ["activeTab", "storage"],
  "host_permissions": ["https://leetcode.com/*"],
  "content_security_policy": {
    "extension_pages": "script-src 'self'; object-src 'self'"
  }
}
```
## Acknowledgments

### Built With

- [Google Gemini](https://ai.google.dev/) - AI model powering intelligent responses
- [Upstash](https://upstash.com/) - Redis database for session management
- [Render](https://render.com/) - Cloud hosting platform
- [React](https://react.dev/) - UI framework
- [Vite](https://vitejs.dev/) - Build tool
- [TailwindCSS](https://tailwindcss.com/) - CSS framework
- [tldraw](https://tldraw.dev/) - Whiteboard library
- [Express.js](https://expressjs.com/) - Backend framework
---

