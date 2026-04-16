# Nextcloud Stack on Raspberry Pi

Personal self-hosted cloud stack built on Raspberry Pi with Docker Compose.

## Project goals

This project is designed to:

- build a personal cloud platform with Nextcloud
- learn DevOps fundamentals step by step
- manage services with Docker Compose
- separate application code from persistent data
- use Git with a clean and professional workflow
- understand storage, networking, configuration, and operations

## Planned architecture

The stack will include:

- Nextcloud
- PostgreSQL
- Redis

## Project structure

- `compose.yaml` → container orchestration
- `.env.example` → example environment variables
- `.gitignore` → ignored local and sensitive files
- `README.md` → project documentation
- `docs/` → notes and additional documentation

## Data layout

Project files live in:

- `/home/peppelomb17/nextcloud-stack`

Persistent service data lives on the external SSD in:

- `/mnt/ssd/nextcloud-data`

Planned persistent directories:

- `/mnt/ssd/nextcloud-data/postgres`
- `/mnt/ssd/nextcloud-data/redis`
- `/mnt/ssd/nextcloud-data/nextcloud`

## Learning approach

This project is being built as a guided DevOps exercise.

Each step is meant to explain:

- what we are doing
- why we are doing it
- where data lives
- how services communicate
- how to manage the project with Git

## Status

Current status:

- Git repository initialized
- main branch created
- feature branch `feat/project-scaffold` created
- initial scaffold committed
- persistent data directories created on SSD
