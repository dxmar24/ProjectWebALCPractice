# Render Deployment

This practice version deploys both the backend and frontend on Render from the same GitHub repository.

## Public Services

```text
Backend API: https://projectwebalcpractice-api.onrender.com
Frontend: https://projectwebalcpractice-frontend.onrender.com
```

Health check:

```text
https://projectwebalcpractice-api.onrender.com/api/health
```

## Render Blueprint

The root `render.yaml` defines:

- `projectwebalcpractice-api`: Docker web service using `06Code/Dockerfile`
- `projectwebalcpractice-frontend`: static site publishing `06Code/View`

Create a new Render Blueprint from the GitHub repo and Render will ask for the unsynced secret values.

## Backend Service

Use a Render **Web Service** with these settings:

```text
Repository: your-account/ProjectWebALCPractice
Branch: main
Runtime: Docker
Dockerfile: 06Code/Dockerfile
Docker context: 06Code
Plan: Free
```

The backend is built from `06Code/Dockerfile`, which installs PHP dependencies and serves the Slim application from `Controller/public`.

## Environment Variables

Configure these values in Render under **Environment**:

```env
APP_ENV=production
APP_DEBUG=false
APP_TIMEZONE=America/Bogota
APP_KEY=your_generated_64_character_key
DB_CONNECTION=pgsql
DB_HOST=your_pooler_or_database_host
DB_PORT=5432
DB_DATABASE=postgres
DB_USERNAME=postgres.your_project_ref
DB_PASSWORD=your_database_password
DB_SSLMODE=require
FRONTEND_ORIGINS=https://projectwebalcpractice-frontend.onrender.com,http://127.0.0.1:5173,http://localhost:5173,http://127.0.0.1:5500,http://localhost:5500
GOOGLE_CLIENT_ID=your_google_web_client_id.apps.googleusercontent.com
GOOGLE_AUTO_REGISTER_ROLE=student
GOOGLE_AUTO_REGISTER_BRANCH_ID=1
```

Do not commit `.env`, database passwords, API tokens, or Render secrets.

## Frontend Service

Use a Render **Static Site** with:

```text
Build command: sh 06Code/View/render-build.sh
Publish directory: 06Code/View
```

Environment:

```env
API_BASE_URL=https://projectwebalcpractice-api.onrender.com
GOOGLE_CLIENT_ID=your_google_web_client_id.apps.googleusercontent.com
SKIP_INSTALL_DEPS=true
```

## Verification

After every backend deployment, verify:

```text
GET https://projectwebalcpractice-api.onrender.com/api/health
GET https://projectwebalcpractice-api.onrender.com/api/branches
```

Both endpoints should return JSON. The health endpoint should show `database: connected`.

Then open the frontend and test:

```text
https://projectwebalcpractice-frontend.onrender.com/login.html
```

Sign in with Google, enter the director dashboard, sign out, and then use the browser back button. The app should show `session-ended.html` instead of the dashboard.
