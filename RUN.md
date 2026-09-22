# Quick start

## 1. Configure backend

Open `backend/.env` and set at minimum:

```env
MONGO_URI=mongodb://127.0.0.1:27017/dev_intelligence
JWT_SECRET=change_this_to_a_long_random_secret
CLIENT_URL=http://localhost:5173
```

For email verification, add SMTP values. For AI, add `AI_API_KEY`. For GitHub, add `GITHUB_TOKEN`. For Google, add Google OAuth values.

## 2. Install

```powershell
npm install
npm --prefix backend install
npm --prefix frontend install
```

## 3. Start backend

```powershell
cd backend
npm run dev
```

## 4. Start frontend in a second terminal

```powershell
cd frontend
npm run dev
```

Open `http://localhost:5173`.

## First test

1. Create Team.
2. Enter a real email address.
3. Receive OTP.
4. Verify OTP.
5. Dashboard opens with your new empty workspace.
6. Create a project/task.
7. Open Team and invite a member.
8. Open Team Chat in two browser windows to test real-time messages.
9. Configure AI_API_KEY and ask the AI Assistant about the project.
10. Configure GITHUB_TOKEN and sync a repository from GitHub.
