# Flask on Docker

A simple Flask application with PostgreSQL, Nginx, and Docker Compose for development and production.

## Features
- Flask web framework
- PostgreSQL database
- Nginx reverse proxy for production
- Separate Docker configurations for dev and prod
- Static and media file handling
- Database initialization and seeding commands

## Prerequisites
- Docker
- Docker Compose

## Development

1. Create `.env.dev` in the root directory:
   ```
   FLASK_APP=project/__init__.py
   FLASK_DEBUG=1
   DATABASE_URL=postgresql://hello_flask:hello_flask@db:5432/hello_flask_dev
   SQL_HOST=db
   SQL_PORT=5432
   DATABASE=postgres
   APP_FOLDER=/usr/src/app
   ```

2. Build and run:
   ```bash
   docker-compose up -d --build
   ```

3. Create and seed the database:
   ```bash
   docker-compose exec web python manage.py create_db
   docker-compose exec web python manage.py seed_db
   ```

4. Access at http://localhost:1147

## Production

1. Create `.env.prod` and `.env.prod.db` in the root directory.

   `.env.prod`:
   ```
   FLASK_APP=project/__init__.py
   FLASK_DEBUG=0
   DATABASE_URL=postgresql://hello_flask:hello_flask@db:5432/hello_flask_prod
   SQL_HOST=db
   SQL_PORT=5432
   DATABASE=postgres
   APP_FOLDER=/home/app/web
   ```

   `.env.prod.db`:
   ```
   POSTGRES_USER=hello_flask
   POSTGRES_PASSWORD=hello_flask
   POSTGRES_DB=hello_flask_prod
   ```

2. Build and run:
   ```bash
   docker-compose -f docker-compose.prod.yml up -d --build
   ```

3. Create the database (if needed):
   ```bash
   docker-compose -f docker-compose.prod.yml exec web python manage.py create_db
   ```

4. Access at http://localhost:1147

## Directory Structure
```
.
├── docker-compose.prod.yml
├── docker-compose.yml
├── README.md
└── services
    ├── nginx
    │   ├── Dockerfile
    │   └── nginx.conf
    └── web
        ├── Dockerfile
        ├── Dockerfile.prod
        ├── entrypoint.prod.sh
        ├── entrypoint.sh
        ├── manage.py
        ├── project
        │   ├── config.py
        │   ├── __init__.py
        │   ├── media
        │   ├── static
        │   └── ...
        └── requirements.txt
```

## Common Commands
- Stop containers: `docker-compose down` (add `-v` to remove volumes)
- View logs: `docker-compose logs -f`
- Flask shell: `docker-compose exec web flask shell`
- Rebuild after changes: `docker-compose up -d --build`

## Notes
- Development mounts `services/web` for live code reloading.
- Production uses Gunicorn and Nginx for serving static/media files.
- Default database credentials are for demonstration; change them in production.
