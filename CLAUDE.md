# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a **GitHub Skills exercise repository** for teaching developers how to use **GitHub Copilot agent mode** to build full-stack applications. The exercise guides users through building "OctoFit Tracker," a fitness tracking application for Mergington High School.

The repository is designed to be used in **GitHub Codespaces** with GitHub Copilot agent mode enabled. The exercise follows a step-by-step tutorial structure with automated GitHub Actions workflows that check progress at each stage.

## Reference Architecture Approach

The exercise uses a **reference implementation pattern**:
- The `docs/mona-high-school-fitness-tracker.md` file contains complete reference code and implementation details for a similar app called "Monafit Tracker"
- Users are guided to adapt this reference implementation to create "OctoFit Tracker" for Mergington High School
- The `docs/octofit_story.md` provides the backstory and requirements for the OctoFit application

When working on this project, **always consult docs/mona-high-school-fitness-tracker.md** for:
- Directory structure patterns
- Sample code for models, serializers, views, URLs
- Database initialization commands
- React component examples
- Package dependencies

## Expected Application Structure

The exercise builds toward this directory structure:

```
octofit-tracker/
├── backend/
│   ├── venv/                          # Python virtual environment
│   ├── octofit_tracker/               # Django project/app
│   │   ├── __init__.py
│   │   ├── models.py                  # User, Team, Activity, Leaderboard, Workout models
│   │   ├── serializers.py             # DRF serializers with ObjectIdField handling
│   │   ├── settings.py                # Django settings with MongoDB config
│   │   ├── views.py                   # ViewSets + api_root endpoint
│   │   ├── urls.py                    # Root, admin, and /api/ endpoints
│   │   ├── admin.py
│   │   ├── tests.py
│   │   ├── wsgi.py
│   │   └── asgi.py
│   ├── manage.py
│   └── requirements.txt               # Must include Django==4.1, djongo, etc.
└── frontend/
    ├── src/
    │   ├── components/                # Activities, Teams, Users, Leaderboard, Workouts
    │   ├── App.js                     # Main app with React Router
    │   ├── index.js                   # Uses ReactDOM.createRoot (React 18)
    │   └── App.css
    ├── public/
    ├── package.json                   # react, react-router-dom, bootstrap
    └── node_modules/
```

## Development Commands

### Python Backend (Django + MongoDB)

**Activate virtual environment:**
```bash
source octofit-tracker/backend/venv/bin/activate
```

**Install Python dependencies:**
```bash
pip install -r octofit-tracker/backend/requirements.txt
```

**Database operations:**
```bash
# Initialize MongoDB database with collections
mongo --eval "db = db.getSiblingDB('octofit_db'); db.createCollection('users'); db.createCollection('teams'); db.createCollection('activity'); db.createCollection('leaderboard'); db.createCollection('workouts'); db.users.createIndex({ email: 1 }, { unique: true });"

# Verify collections
mongo --eval "db = db.getSiblingDB('octofit_db'); printjson(db.getCollectionNames());"

# Run migrations
python octofit-tracker/backend/manage.py makemigrations
python octofit-tracker/backend/manage.py migrate

# Populate database with test data (after creating populate_db.py management command)
python octofit-tracker/backend/manage.py populate_db
```

**Start Django development server:**
```bash
python octofit-tracker/backend/manage.py runserver
# Runs on port 8000 (auto-forwarded in Codespaces)
```

### React Frontend

**Install Node dependencies:**
```bash
npm install --prefix octofit-tracker/frontend
```

**Start React development server:**
```bash
cd octofit-tracker/frontend
npm start
# Runs on port 3000 (auto-forwarded in Codespaces)
```

### MongoDB

**Start MongoDB service:**
```bash
sudo service mongodb start
```

**Check MongoDB status:**
```bash
sudo service mongodb status
```

## Key Technical Details

### Django Configuration
- **Database Engine**: `djongo` (Django + MongoDB)
- **Database Name**: `octofit_db`
- **Collections**: users, teams, activity, leaderboard, workouts
- **CORS**: Enabled via django-cors-headers with `CORS_ALLOW_ALL_ORIGINS = True`
- **Allowed Hosts**: Must include `localhost`, `127.0.0.1`, and the Codespace URL pattern

### Django Models
All models use `djongo.models` with `ObjectIdField` for MongoDB `_id` fields. The serializers include a custom `ObjectIdField` to handle BSON ObjectId serialization.

### API Endpoints Structure
- Root: `/` - Returns JSON with links to all API endpoints
- Admin: `/admin/` - Django admin interface
- API: `/api/users/`, `/api/teams/`, `/api/activities/`, `/api/leaderboard/`, `/api/workouts/`

### Frontend (React)
- Uses **React 18** with `ReactDOM.createRoot()` API (not legacy `ReactDOM.render()`)
- **React Router v6** for navigation
- **Bootstrap 5** for styling (imported in index.js)
- Components fetch data from Django REST API using Codespace URL

### Codespace Configuration
The `.devcontainer/devcontainer.json` configures:
- Forwarded ports: 3000 (React), 8000 (Django), 27017 (MongoDB)
- Port 3000 and 8000 set to public visibility on startup via `post_start.sh`
- Required VS Code extensions: GitHub Copilot (insiders), Python, Pylance

## Exercise Workflow

The exercise follows a multi-step structure managed by GitHub Actions:

1. **Step 1**: Create and publish `build-octofit-app` branch
2. **Step 2**: Create directory structure, requirements.txt, install MongoDB
3. **Step 3**: Initialize MongoDB database, create Django project, configure models/serializers/views/urls
4. **Step 4**: Set up Django REST Framework endpoints
5. **Step 5**: Create React frontend with components
6. **Step 6**: Use GitHub Copilot on GitHub.com

Progress is automatically checked via GitHub Actions workflows in `.github/workflows/`. Each step has corresponding instructions in `.github/steps/`.

## Important Context for AI Assistance

When helping users with this exercise:

1. **Reference the docs**: Always check `docs/mona-high-school-fitness-tracker.md` for complete examples and patterns
2. **Naming convention**: Replace "monafit" with "octofit" and "Mona High School" with "Mergington High School"
3. **Follow the steps**: This is a guided tutorial - respect the step-by-step progression
4. **Codespace URLs**: API endpoints in views.py and frontend components should use the Codespace URL pattern (auto-generated domain)
5. **Testing expectations**: The GitHub Actions workflows validate specific files exist with specific content (e.g., `requirements.txt` must contain `Django==4.1`)
6. **Agent mode focus**: The exercise emphasizes using GitHub Copilot's agent mode, not just chat/autocomplete

## Common Pitfalls

- **React 18 API**: Must use `ReactDOM.createRoot()`, not legacy `ReactDOM.render()`
- **MongoDB with Django**: Requires `djongo` package and proper ObjectId handling in serializers
- **Port forwarding**: Codespace ports must be set to public for external access
- **Virtual environment**: Always activate venv before running pip/Django commands
- **CORS configuration**: Frontend and backend run on different ports, CORS must be configured
