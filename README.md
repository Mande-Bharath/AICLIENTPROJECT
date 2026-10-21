# EduReach

EduReach is a college information and student engagement platform. Visitors can
explore college information, courses, achievements, mentors, and testimonials.
Registered users can also access student-life and events content, placement
statistics, and the AI counselor.

The project contains a React and TypeScript frontend and a Node.js and
TypeScript API:

- `edureach-platform/client` — React 19, React Router, Vite, and Tailwind CSS.
- `edureach-platform/server` — Express 5, MongoDB/Mongoose, JWT authentication,
  and a LangChain retrieval-augmented generation (RAG) service using Google
  Gemini.

## Features

- Public college information and course pages.
- Sign-up, login, and authenticated user sessions.
- Content sections gated to signed-in users.
- AI chat backed by the college knowledge base in
  `edureach-platform/server/knowledge-base/edureach-knowledge.txt`.
- Optional AI counselor phone calls through Vapi.

## Requirements

- Node.js 24 (recommended; the backend uses Node's native TypeScript support).
- npm.
- A MongoDB Atlas database with Atlas Vector Search available.
- A Google AI Studio API key for Gemini chat and embeddings.
- Vapi credentials if you want to use the counselor phone-call feature.

## Setup

Clone the repository, then install dependencies for each application:

```bash
cd edureach-platform/client
npm install

cd ../server
npm install
```

Create `edureach-platform/server/.env` with the required server settings:

```dotenv
PORT=5000
CLIENT_URL=http://localhost:5173
MONGODB_URI=mongodb+srv://<username>:<password>@<cluster-url>/
JWT_SECRET=<long-random-secret>
JWT_EXPIRES_IN=7d
GOOGLE_API_KEY=<google-ai-studio-api-key>

# Optional: configure these to enable Vapi phone calls.
VAPI_API_KEY=<vapi-api-key>
VAPI_PHONE_NUMBER_ID=<vapi-phone-number-id>
VAPI_ASSISTANT_ID=<vapi-assistant-id>
```

Keep `.env` private; do not commit real credentials. The server requires
`MONGODB_URI`, `JWT_SECRET`, and `GOOGLE_API_KEY`. `PORT` defaults to `5000`,
`CLIENT_URL` defaults to `http://localhost:5173`, and token expiry defaults to
`7d`.

### MongoDB Atlas Vector Search

On first server startup, EduReach loads the bundled knowledge-base text, creates
embeddings with Google Gemini, and stores the chunks in the
`edureach_db.knowledge_docs` collection. The RAG service expects an Atlas Vector
Search index named `edureach_vector_index`, using the `embedding` field. Create
and configure this index in Atlas before using semantic chat retrieval. The
embedding model currently produces 3072-dimensional vectors; verify the
dimension in the server startup output if the embedding model changes.

## Run locally

Start the API in one terminal:

```bash
cd edureach-platform/server
npm run dev
```

The API listens on `http://localhost:5000` by default.

Start the frontend in another terminal:

```bash
cd edureach-platform/client
npm run dev
```

Open the local URL printed by Vite (normally `http://localhost:5173`). The
frontend currently sends API requests to `http://localhost:5000/api`.

## API routes

| Method | Route | Description |
| --- | --- | --- |
| `POST` | `/api/auth/register` | Register a user |
| `POST` | `/api/auth/login` | Log in |
| `GET` | `/api/auth/me` | Get the authenticated user |
| `POST` | `/api/chat/message` | Send a message to the AI counselor |
| `POST` | `/api/vapi/call` | Start a counselor call (authentication required) |

Authenticated routes expect a bearer token in the `Authorization` header.

## Useful commands

Run these from the relevant `client` or `server` directory:

| Command | Client | Server |
| --- | --- | --- |
| Development | `npm run dev` | `npm run dev` |
| Build | `npm run build` | `npm run build` |
| Lint | `npm run lint` | — |

The server package does not currently define an automated test suite.
