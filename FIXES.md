# Fixes applied in this delivery

## Authentication
- Fixed missing `setSession` export from `services/api.js`.
- Standardized `devintel_token` + legacy `token` compatibility.
- Added authenticated `/auth/me` session refresh.
- Added GitHub OAuth login/signup.
- Added GitHub OAuth connection for existing users.
- Kept Google OAuth support.
- Added profile update endpoint.

## Splash / branding
- Fixed icon + full logo animation overlap.
- Added clean sequential splash transition.
- Added favicon.

## GitHub
- GitHub API can use the authenticated user's OAuth token.
- Optional `GITHUB_TOKEN` fallback remains available.
- Repository list, status and sync are connected to the authenticated account.
- Repository sync can attach a repository to a project.
- 30-day commit activity is tracked.
- Pull requests and issue metrics feed the health model.
- Webhook events create workspace notifications.

## Health / tracker
- Aggregated all linked repositories instead of only the first repository.
- Health score now uses:
  - Task evidence 40%
  - GitHub activity 25%
  - Issue health 20%
  - PR health 15%
- Added overdue/high-priority/open-issue/PR-queue/activity risk signals.
- Rebuilt Task Tracker with search, filters, priority, status, assignee, project and due date.
- Task assignment creates a notification.

## Chat
- Fixed Socket.IO authentication.
- Fixed silent `chatEnabled` rejection behavior.
- Added acknowledgements and error handling.
- Added group channels.
- Added peer-to-peer chat.
- Added reconnect status.
- Added duplicate protection.
- Added @mention suggestions.
- Mentions create notifications.
- Message history remains in MongoDB.

## Notifications
- Added Notification model and routes.
- Added read and mark-all-read behavior.
- Added real-time notification events.

## Profile
- Header avatar opens profile.
- Sidebar profile opens profile.
- Profile details and GitHub account status are visible.
- Name/avatar can be edited.

## Reports
- Added JSON report download.
- Added print-friendly report that can be saved as PDF.

## Responsive UI
- Added mobile navigation drawer.
- Added responsive tablet/laptop/desktop layouts.
- Reworked chat, tracker, GitHub, analytics and profile layouts for small screens.

## Clean delivery
- Real `.env` files are excluded.
- `node_modules` is excluded.
- `.env.example` files document required configuration.
