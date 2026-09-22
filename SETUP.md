# Dev Intelligence — Setup & Integration Guide

## 1. What is included

This build is a React + Vite frontend with an Express + Socket.IO + MongoDB backend.

Implemented/fixed:

- Clean authentication flow with consistent JWT session storage.
- Email/password login + signup + OTP verification.
- Google OAuth (optional).
- **GitHub OAuth login/signup**.
- Authenticated GitHub account connection for existing users.
- Per-user GitHub token use for repository intelligence, with optional server-token fallback.
- Repository linking and project association.
- GitHub pull-request, issue and commit metrics.
- 30-day GitHub activity signal.
- Weighted project health score:
  - Task evidence: 40%
  - GitHub activity: 25%
  - Issue health: 20%
  - PR health: 15%
- Explainable risk register.
- Task tracker with status, priority, project, assignee and due date.
- Task-assignment notifications.
- Real-time Socket.IO group chat.
- Real-time peer-to-peer chat.
- `@mention` suggestions and notification creation.
- Notification center with read/read-all controls.
- Clickable profile from header/sidebar.
- Profile editing.
- Responsive layout for mobile, tablet, laptop and large screens.
- Fixed splash/logo animation so icon and full logo do not overlap.
- Analytics report download as JSON.
- Print-friendly report that can be saved as PDF from the browser.
- GitHub webhook event ingestion.
- Mobile navigation drawer.
- Consistent API session helpers.

---

## 2. Required tools

Install these on the development machine:

1. **Node.js 20+** (Node 22 is also supported by the application code).
2. **npm** (comes with Node.js).
3. **MongoDB Atlas** account, or a local MongoDB server.
4. **GitHub account** for OAuth and repository access.
5. **GitHub OAuth App** for GitHub login and account connection.
6. Optional:
   - Google Cloud OAuth credentials for Google login.
   - Gmail App Password / SMTP account for email OTP and invitations.
   - OpenRouter API key for the AI assistant.
   - GitHub Webhook secret if automatic webhook events are used.
7. A modern browser such as Chrome, Edge or Firefox.
8. VS Code is recommended for editing.

---

## 3. Project structure

```text
devintel-build/
├── backend/
│   ├── .env.example
│   ├── package.json
│   └── src/
│       ├── config/
│       ├── middleware/
│       ├── models/
│       ├── routes/
│       ├── services/
│       └── server.js
│
├── frontend/
│   ├── .env.example
│   ├── package.json
│   └── src/
│       ├── assets/
│       ├── components/
│       ├── pages/
│       ├── services/
│       └── App.jsx
│
└── SETUP.md
```

`node_modules` and real `.env` files are intentionally excluded from the clean delivery zip.

---

## 4. Install dependencies

Open PowerShell in the project folder.

### Backend

```powershell
cd backend
npm install
```

### Frontend

```powershell
cd ../frontend
npm install
```

---

## 5. Configure MongoDB

Copy:

```text
backend/.env.example
```

to:

```text
backend/.env
```

Set:

```env
MONGO_URI=mongodb+srv://USERNAME:PASSWORD@CLUSTER.mongodb.net/dev_intelligence?retryWrites=true&w=majority
JWT_SECRET=your_long_random_secret
CLIENT_URL=http://localhost:5173
```

Do not commit `.env`.

---

## 6. Configure frontend

Copy:

```text
frontend/.env.example
```

to:

```text
frontend/.env
```

Use:

```env
VITE_API_URL=http://localhost:5000/api
VITE_SOCKET_URL=http://localhost:5000
```

---

# 7. GitHub OAuth login/signup

Go to GitHub:

```text
GitHub → Settings → Developer settings → OAuth Apps → New OAuth App
```

Use:

```text
Application name:
Dev Intelligence

Homepage URL:
http://localhost:5173

Authorization callback URL:
http://localhost:5000/api/auth/github/callback
```

Create the application.

Copy the Client ID and Client Secret into:

```env
GITHUB_CLIENT_ID=...
GITHUB_CLIENT_SECRET=...
GITHUB_CALLBACK_URL=http://localhost:5000/api/auth/github/callback
```

Restart the backend.

### Login/signup flow

The authentication screen now has:

```text
Continue with GitHub
```

If the GitHub email already belongs to a Dev Intelligence account:

```text
GitHub
   ↓
Existing account
   ↓
Existing team
   ↓
JWT session
   ↓
Dashboard
```

If it is a new account:

```text
GitHub
   ↓
Create User
   ↓
Create Team
   ↓
User becomes Leader
   ↓
JWT session
   ↓
Dashboard
```

---

# 8. Connect GitHub after normal login

A user can also:

```text
Login with email/password
        ↓
GitHub page
        ↓
Connect GitHub
        ↓
GitHub authorization
        ↓
GitHub account linked
```

This is useful for existing users who did not initially use GitHub login.

The GitHub access token is stored server-side and is never sent to the React application.

For a production deployment, use an encrypted secret/token storage strategy.

---

# 9. GitHub project connectivity

After GitHub is connected:

```text
GitHub page
     ↓
Available repositories
     ↓
Select repository
     ↓
Enter owner + repository
     ↓
Select project
     ↓
Sync repository
```

The system stores:

- Stars
- Forks
- Open issues
- Total issues
- Open PRs
- Total PRs
- Merged PRs
- 30-day commits
- Default branch
- Language
- Last sync time

Project ↔ GitHub relationship is stored in MongoDB.

---

# 10. GitHub webhook

Create a webhook in the GitHub repository:

```text
Repository
→ Settings
→ Webhooks
→ Add webhook
```

Payload URL:

```text
https://YOUR-BACKEND-DOMAIN/api/webhooks/github
```

Content type:

```text
application/json
```

Secret:

```text
same value as GITHUB_WEBHOOK_SECRET
```

Useful events:

- Push
- Pull requests
- Issues
- Issue comments

For localhost, GitHub cannot directly reach `localhost`. Use a public tunnel such as Cloudflare Tunnel/ngrok for webhook testing.

---

# 11. Start the application

### Terminal 1 — backend

```powershell
cd backend
npm start
```

Expected:

```text
MongoDB connected
API: http://localhost:5000
Socket.IO: http://localhost:5000
```

### Terminal 2 — frontend

```powershell
cd frontend
npm run dev
```

Open:

```text
http://localhost:5173
```

---

# 12. Test authentication

Test normal signup:

```text
Signup
→ Create team
→ Enter name/email/password
→ Receive OTP
→ Verify
→ Dashboard
```

Test team joining:

```text
Signup
→ Join team
→ Team ID
→ Verify email
→ Dashboard
```

Test GitHub:

```text
Login screen
→ Continue with GitHub
→ Authorize
→ Dashboard
```

---

# 13. Test real-time group chat

Open two browser windows using two users from the same team.

```text
User A → Chat → #general
User B → Chat → #general
```

Send from A.

Expected:

```text
User A
   ↓
Socket.IO
   ↓
MongoDB Message
   ↓
message:new
   ↓
User B instantly receives message
```

No refresh should be required.

---

# 14. Test peer-to-peer chat

Open:

```text
Chat
→ People
→ Select teammate
```

The conversation becomes:

```text
User A ↔ User B
```

Private messages are stored with:

```text
type = direct
sender
recipient
team
```

---

# 15. Test @mentions

Inside chat type:

```text
@Rah
```

A teammate suggestion should appear.

Select the teammate and send:

```text
@Rahul please review the authentication task
```

The backend:

1. Saves the message.
2. Stores mentioned user IDs.
3. Creates a notification.
4. Sends a real-time notification event.

---

# 16. Test task assignment

Go to:

```text
Task Tracker
→ New task
```

Set:

```text
Title
Priority
Status
Assignee
Project
Due date
```

When another teammate is assigned:

```text
Task created
     ↓
Notification created
     ↓
Assignee sees notification
```

---

# 17. Health checker

The health model is:

```text
Health Score =
(Task Evidence × 40%)
+
(GitHub Activity × 25%)
+
(Issue Health × 20%)
+
(PR Health × 15%)
```

The dashboard displays every component separately.

### Task evidence

Based on task completion with an overdue penalty.

### GitHub activity

Uses a normalized 30-day activity target.

### Issue health

Open issues reduce the issue-health component.

### PR health

Merged PR ratio contributes to PR health.

The system also creates explainable signals for:

- Overdue tasks
- High/critical unfinished tasks
- Elevated open-issue ratio
- Growing PR queue
- No recent GitHub activity

---

# 18. Reports

Go to:

```text
Analytics & Reports
```

Available:

```text
JSON report
```

and:

```text
Print / PDF
```

For PDF:

```text
Print / PDF
→ Browser print dialog
→ Save as PDF
```

The report contains:

- Health score
- Task metrics
- GitHub metrics
- Health components
- Risk register
- Generation time

---

# 19. Profile

Profile can be opened by clicking:

```text
Top-right avatar
```

or:

```text
Sidebar → user profile
```

Profile includes:

- Name
- Email
- Role
- Team
- Team ID
- Verification status
- Avatar
- GitHub account

---

# 20. Responsive design

The UI has dedicated responsive layouts for:

```text
Mobile
Tablet
Laptop
Desktop
Large screen
```

Mobile:

```text
☰ menu
↓
navigation drawer
```

Chat automatically switches from:

```text
channels + conversation
```

to:

```text
conversation-focused mobile layout
```

---

# 21. Security checklist before deployment

Before deploying:

- Never upload `.env`.
- Never put MongoDB credentials in frontend files.
- Never put GitHub OAuth secrets in React.
- Never put AI keys in React.
- Use HTTPS.
- Use a strong JWT secret.
- Use separate production OAuth callback URLs.
- Rotate any credentials that have been exposed in old/local archives.
- Prefer a GitHub App or encrypted per-user token storage for production.
- Configure CORS to the exact production frontend domain.
- Use a production MongoDB user with minimum required permissions.

---

# 22. Production deployment

Recommended:

```text
Frontend
   ↓
Vercel / Netlify

Backend
   ↓
Render / Railway / Fly.io / VPS

Database
   ↓
MongoDB Atlas

GitHub
   ↓
OAuth + Webhooks

AI
   ↓
OpenRouter / chosen provider
```

Production environment:

```env
CLIENT_URL=https://your-frontend-domain.com
GITHUB_CALLBACK_URL=https://your-backend-domain.com/api/auth/github/callback
GOOGLE_CALLBACK_URL=https://your-backend-domain.com/api/auth/google/callback
VITE_API_URL=https://your-backend-domain.com/api
VITE_SOCKET_URL=https://your-backend-domain.com
```

Do not use localhost values in production.

---

# 23. Troubleshooting

### Frontend import error

Run:

```powershell
cd frontend
npm install
npm run build
```

### Socket unauthorized

Check:

```text
localStorage
devintel_token
```

and backend:

```text
JWT_SECRET
```

### GitHub says OAuth is not configured

Check:

```env
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
GITHUB_CALLBACK_URL=
```

Then restart backend.

### GitHub repository request fails

Check:

```text
GitHub page
→ Connected
```

Then reconnect GitHub if required.

### Chat does not update

Check both terminals and browser console.

Expected backend:

```text
Socket connected
```

Expected browser:

```text
Socket connected
```

### MongoDB connection fails

Check:

```env
MONGO_URI
```

and MongoDB Atlas:

```text
Network Access
Database Access
```

---

## Final architecture

```text
                 DEV INTELLIGENCE
                        │
        ┌───────────────┴───────────────┐
        │                               │
     React/Vite                    Express API
        │                               │
        │                         ┌─────┴─────┐
        │                         │           │
        │                     MongoDB      Socket.IO
        │                         │           │
        │                         │       Real-time
        │                         │         Chat
        │                         │
        └──────────────┬──────────┘
                       │
              GitHub OAuth/API
                       │
                 GitHub Webhooks
                       │
              Health + Risk Engine
                       │
                  AI Assistant
```
