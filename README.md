# ChatGPT Frontend

A React-based chat interface for a ChatGPT-style AI application. It connects to the [ChatGPT Backend](https://github.com/Sachin7137/ChatGPT_Backend), lets users create an account and sign in, start conversations, revisit recent chats, and send prompts to an AI model through the backend.

## Features

- **Sign up and log in** with client-side form validation.
- **Cookie-based sessions**: requests include credentials so the browser can send the backend's authentication cookie.
- **AI conversations**: start a new chat or continue an existing one.
- **Recent chats sidebar**: view the latest conversations and delete chats.
- **Message history**: fetch messages when a conversation is opened.
- **Token usage display**: show current usage and the configured limit when the backend returns usage information.
- **Responsive dark interface**.
- **Local state management** with Zustand for authentication state and cached messages.

## Tech stack

- React 19
- Vite
- React Router
- Axios
- Zustand
- React Hook Form and Zod
- Tailwind CSS 4

## How the frontend works

1. The app opens on `/`. The home page checks the session using `GET /user/profile`.
2. A new user can sign up at `/signup`, or an existing user can log in at `/login`. Successful authentication sets a cookie through the backend.
3. The chat sidebar requests recent conversations from `GET /chat/getRecentChat`.
4. When a chat is opened, `ChatRoom` fetches its messages from `GET /message/:chatId`.
5. When the user sends a prompt, the frontend posts to `POST /message` for a new conversation or `POST /message/:chatId` for an existing conversation.
6. The backend calls OpenRouter, saves the user message and AI response, and returns the new messages. The frontend reveals the returned response gradually for a typing effect.
7. Zustand keeps loaded messages in memory while the app is open. The database remains the source of truth, so the frontend can reload messages from the backend.

## Project structure

```text
src/
├── components/
│   ├── ChatRoom.jsx       # Conversation view, sending prompts, message history
│   └── ChatSidebar.jsx    # Recent chats and chat deletion
├── pages/
│   ├── Home.jsx           # Main layout, session restoration, usage display
│   ├── Login.jsx          # Login form
│   └── Signup.jsx         # Registration form
├── schemas/
│   └── authSchemas.js     # Zod validation schemas for auth forms
├── store/
│   ├── useAuthStore.js    # Shared authenticated-user state
│   └── useChatStore.js    # In-memory message cache by chat ID
├── api.js                 # Axios client and backend URL
├── App.jsx                # Frontend routes
├── main.jsx               # React entry point and BrowserRouter
└── index.css              # Global styles
```

## Requirements

- Node.js and npm
- The backend repository running locally
- A reachable MongoDB database, Redis instance, and valid OpenRouter API key configured on the backend

## Run locally

Clone and install the frontend:

```bash
git clone https://github.com/Sachin7137/ChatGPT_Frontend.git
cd ChatGPT_Frontend
npm install
```

Start the Vite development server:

```bash
npm run dev
```

Vite normally serves the app at `http://localhost:5173`. Open that URL in your browser.

Other available scripts:

```bash
npm run build    # Build for production
npm run preview  # Preview the production build
npm run lint     # Run ESLint
```

## Backend connection

The Axios client is configured in `src/api.js`:

```js
const api = axios.create({
  baseURL: "http://localhost:3000",
  withCredentials: true,
});
```

Therefore, run the backend on port `3000` for the default local setup, or update `baseURL` if your backend uses a different URL. The backend CORS configuration must allow the frontend origin and credentials. The current backend allows `http://localhost:5173`.

## AI model

For a new conversation, the frontend currently sends the model ID `aion-labs/aion-3.5` in `src/components/ChatRoom.jsx`. The backend uses the model stored on the chat when generating subsequent responses. Use a model ID supported by your OpenRouter account.

## Important notes

- Do not put the OpenRouter API key in the frontend. Keep provider credentials in the backend's environment variables.
- Authentication depends on the backend's HTTP-only cookie; keep `withCredentials: true` enabled when using cookie-based authentication.
- Chat messages are cached in Zustand only for the current app session; they are not a replacement for database persistence.
- The current message UI includes a delete-message action, but the backend must expose a matching message-delete route for that action to work. Deleting an entire chat uses the chat API.
- This project is a learning/development app. Review validation, security, and production deployment settings before exposing it publicly.

## Related repository

- [ChatGPT Backend](https://github.com/Sachin7137/ChatGPT_Backend) — authentication, chat/message APIs, database persistence, token tracking, and OpenRouter integration.
