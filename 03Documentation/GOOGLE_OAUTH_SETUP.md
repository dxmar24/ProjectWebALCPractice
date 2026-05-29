# Google OAuth Setup

This practice version uses Google Identity Services for Gmail login.

OAuth lets the app ask Google to confirm a user's identity without receiving the user's Gmail password. Google returns an ID token, the backend verifies the token signature and claims, and then the backend issues the normal ALC JWT used by the dashboard.

## Google Cloud

1. Open Google Cloud Console.
2. Create or choose a project.
3. Configure the OAuth consent screen.
4. Create an OAuth Client ID with application type **Web application**.
5. Add authorized JavaScript origins:

```text
https://projectwebalcpractice-frontend.onrender.com
http://127.0.0.1:5500
http://localhost:5500
http://127.0.0.1:5173
http://localhost:5173
```

This frontend uses the Google button flow, so no redirect URI is required for the current implementation.

## Backend Environment Variables

Set these in the Render API service:

```env
GOOGLE_CLIENT_ID=your_google_web_client_id.apps.googleusercontent.com
GOOGLE_AUTO_REGISTER_ROLE=student
GOOGLE_AUTO_REGISTER_BRANCH_ID=1
```

Optional:

```env
GOOGLE_ALLOWED_DOMAIN=your-school-domain.com
```

If `GOOGLE_ALLOWED_DOMAIN` is set, only Google accounts from that hosted domain can sign in.

## Frontend Environment Variables

Set this in the Render Static Site:

```env
GOOGLE_CLIENT_ID=your_google_web_client_id.apps.googleusercontent.com
API_BASE_URL=https://projectwebalcpractice-api.onrender.com
```

The static build runs `06Code/View/render-build.sh`, which writes these values into `script/config.js` during deployment.

## Database

For the shared class database, do not reset or recreate the schema. The Google flow uses the existing `users` and `students` tables:

- If the Google email already exists in `users`, the backend logs that user in.
- If the Google email exists as an active `students.email`, the backend creates the matching `users` row and logs in.
- If the email does not exist, the frontend asks the student to complete enrollment and the backend creates both `students` and `users`.

The optional `google_oauth_migration.sql` file is kept only for experiments that want to store Google account IDs. It is not required for the compatible shared-database flow.
