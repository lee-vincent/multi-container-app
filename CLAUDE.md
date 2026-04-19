# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

```bash
# Start all services (detached)
docker compose up -d

# Start with Compose watch (hot-reload)
docker compose watch

# View logs
docker compose logs -f todo-app

# Stop and remove containers
docker compose down
```

The app runs at `http://localhost:3000`. MongoDB persists data to the `database` named volume.

## Architecture

Two services defined in `compose.yaml`:

- **todo-app**: Node.js/Express server (`app/`) on port 3000. Uses EJS templates, Mongoose ODM, and nodemon in dev mode. Livereload server on port 35729 auto-refreshes the browser on file changes.
- **todo-database**: MongoDB 6 (`mongo:6` image) on port 27017. Connection string uses the Docker service name as hostname: `mongodb://todo-database:27017/todoapp`.

The app container runs as a non-root `node` user. The Dockerfile uses npm cache mounts for fast rebuilds.

## Code Layout

```
app/
├── server.js          # Express + Mongoose init, livereload setup, mounts routes
├── config/keys.js     # MongoDB URI
├── models/Todo.js     # Mongoose schema (task: String, created_at: Date)
├── routes/front.js    # GET / (list), POST / (create), POST /todo/destroy (delete)
└── views/todos.ejs    # Bootstrap 4 UI with moment.js timestamps
```

## Development Notes

- `npm run dev` (runs inside container via nodemon) watches `*.js` and `*.ejs` files.
- Compose watch syncs local `app/` changes directly into the container; changes to `package.json` trigger a rebuild.
- `NODE_ENV` is set to `production` in `compose.yaml`, but the container CMD runs `npm run dev` (nodemon) regardless.
