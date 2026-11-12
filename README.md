# Career Agent: Standalone Docker Deployment

This folder contains everything needed to run **Career Agent** without requiring access to the source code repository.

---

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) (version 20.10 or later)
- [Docker Compose](https://docs.docker.com/compose/) (v2 or later, built into Docker Desktop / `docker compose`)

---

## Quick Start

### 1. Configure Environment Variables

Make a copy of `.env.example` named `.env`:

```bash
cp .env.example .env
```

Open `.env` in any text editor and configure your LLM provider credentials:

```dotenv
PORT=12000
OPENAI_API_KEY=your-actual-api-key-here
OPENAI_MODEL_NAME=deepseek-chat
OPENAI_BASE_URL=https://api.deepseek.com
```

> **Note:** The application supports any OpenAI-compatible API, including DeepSeek, OpenAI, Groq, Ollama, and OpenRouter.

### 2. Launch the Application

Start the container in detached mode:

```bash
docker compose up -d
```

Docker will automatically pull the image (`ghcr.io/regisx001/career-agent:latest`), create persistent volumes, initialize the database schema, and launch the service.

### 3. Access the Application

Open your browser and navigate to:

- **Web Application & UI:** http://localhost:12000
- **Interactive API Documentation:** http://localhost:12000/docs
- **Health Check Endpoint:** http://localhost:12000/health

For remote deployments, replace `localhost` with your server's public IP address or domain name.

---

## Management Commands

| Action | Command |
|---|---|
| View real-time logs | `docker compose logs -f` |
| Check service health | `docker compose ps` |
| Stop application | `docker compose stop` |
| Stop and remove container | `docker compose down` |
| Update to latest image | `docker compose pull && docker compose up -d` |
| Restart application | `docker compose restart` |

---

## Data Persistence & Backups

All application state (SQLite database, imported CVs, generated documents, and LaTeX compilation cache) is persisted across container restarts using Docker volumes:

- `career-agent-data`: Contains `/app/data/career_agent.db` and `/app/data/uploads`.
- `career-agent-latex-cache`: Contains XeLaTeX compilation cache for faster PDF rendering.

### Backing Up Your Data

To create a backup archive of your application data:

```bash
docker run --rm \
  -v career-agent-data:/data \
  -v "$(pwd)":/backup \
  alpine tar czf /backup/career_agent_backup_$(date +%Y%m%d).tar.gz -C /data .
```

---

## GitHub Container Registry (GHCR) Access

If the image is hosted in a private GitHub Container Registry repository, authenticate before pulling:

```bash
echo "YOUR_GITHUB_PERSONAL_ACCESS_TOKEN" | docker login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin
```
