# Nextcloud Stack on Raspberry Pi

A self-hosted Nextcloud stack built step by step on Raspberry Pi 5 with Docker Compose, PostgreSQL, Redis, external SSD storage, and a future migration path to a tower server / mini datacenter.

## Project overview

This project is not just a basic Nextcloud install.

It is a practical DevOps-style build focused on:

- service separation
- persistent storage design
- Linux filesystem decisions
- container orchestration with Docker Compose
- healthchecks and startup dependencies
- Git-based project evolution
- future portability to a more robust server

The current milestone is a **working LAN deployment** of Nextcloud on Raspberry Pi.

## Current status

Current milestone:

- Nextcloud is running in LAN
- PostgreSQL is configured and persistent
- Redis is configured and integrated
- Nextcloud bootstrap completed successfully
- trusted domains configured for local access
- external SSD migrated from `exfat` to `ext4`
- Git history initialized and milestone tagged as `v0.1-working-lan`
- repository published on GitHub

## Why this project exists

This repository was built as a guided learning project to understand how to design and operate a self-hosted cloud stack properly.

The goal is not only to "make Nextcloud run", but to understand:

- why each service exists
- where data lives
- how containers depend on each other
- why filesystems matter
- how to validate a stack progressively
- how to move from a Raspberry Pi lab to a more serious server architecture

## Architecture

The stack is composed of 3 services:

- **Nextcloud** → application and web interface
- **PostgreSQL** → primary relational database for app metadata
- **Redis** → in-memory backend for sessions / cache / locking support

### High-level architecture

```text
Browser / Mobile App
        |
        v
   Nextcloud (Apache)
      /        \
     v          v
PostgreSQL    Redis
```

## Storage layout

Project files live in:

```text
/home/peppelomb17/nextcloud-stack
```

Persistent runtime data lives on the external SSD:

```text
/mnt/ssd/nextcloud-data
```

### Service data directories

```text
/mnt/ssd/nextcloud-data/postgres
/mnt/ssd/nextcloud-data/redis
/mnt/ssd/nextcloud-data/nextcloud/config
/mnt/ssd/nextcloud-data/nextcloud/data
/mnt/ssd/nextcloud-data/nextcloud/custom_apps
```

### Why data is separated this way

Instead of using one giant application volume, the stack separates:

- `config` → instance configuration
- `data` → user files
- `custom_apps` → optional custom extensions
- `postgres` → database storage
- `redis` → Redis runtime persistence area

This improves:

- clarity
- troubleshooting
- backup reasoning
- migration to future infrastructure

## Important infrastructure decision: exFAT to ext4

The external SSD originally used `exfat`.

That turned out to be a poor choice for a Linux server-style workload because:

- Unix ownership and permissions were not handled correctly
- `chown` failed
- container bind mounts became unreliable for a serious persistent setup

The SSD was therefore reformatted to:

```text
ext4
```

This was a critical architectural correction, not a cosmetic change.

## Docker Compose design choices

### Why PostgreSQL

PostgreSQL is used as the authoritative relational database backend.

Reasons:

- clear separation from the application container
- production-style architecture
- strong portability to future server environments
- better learning value than a one-container shortcut

### Why Redis

Redis is used as a support backend for in-memory operations.

It is not the primary data store, but it improves the application behavior by supporting:

- sessions
- cache
- locking-related behavior

### Why Nextcloud Apache image

The project currently uses:

```text
nextcloud:apache
```

This choice keeps the first milestone simpler because the web server is embedded in the application image.

A future evolution may move to:

- reverse proxy
- HTTPS
- dedicated front-end routing
- more production-oriented remote access

## Compose features implemented

The stack currently includes:

- explicit image versions
- bind mounts to persistent SSD storage
- `.env`-based configuration
- PostgreSQL healthcheck
- Redis healthcheck
- `depends_on` with `condition: service_healthy`
- exposed host port for Nextcloud
- trusted domains setup for LAN use

## Health and startup strategy

The services are not started blindly.

The stack uses:

- `healthcheck` on PostgreSQL
- `healthcheck` on Redis
- `depends_on` with `service_healthy` for Nextcloud

This means Nextcloud waits for backend readiness instead of relying only on raw container startup order.

## Local access

The current LAN deployment is accessed through:

```text
http://192.168.1.16:8080
```

Trusted domains were configured to allow local access from:

- `localhost`
- `192.168.1.16:8080`
- `raspberry.local:8080`

## Repository structure

```text
nextcloud-stack/
├── compose.yaml
├── .env.example
├── .gitignore
├── README.md
└── docs/
```

## Git history and milestones

The repository was built incrementally with multiple commits, not as a single dump.

Important milestone:

- `v0.1-working-lan` → first working LAN deployment

## What has been validated so far

Validated successfully:

- SSD mount and persistence
- `ext4` storage migration
- PostgreSQL container bootstrap
- Redis startup and healthcheck
- Redis host warning fix with `vm.overcommit_memory = 1`
- Nextcloud installation bootstrap
- local browser access
- admin login
- trusted domains configuration
- Git versioning and GitHub push

## Known limitations of the current milestone

This is a **LAN-first** deployment.

It is **not yet** a hardened remote/public cloud deployment.

Still missing:

- remote access architecture
- HTTPS
- reverse proxy or secure tunnel strategy
- router / port forwarding strategy
- final server hardening
- automated mobile photo upload workflow
- production-style backup strategy

## Future roadmap

Planned next steps:

1. Improve documentation and project usability
2. Add remote access strategy
3. Add HTTPS
4. Define backup and restore workflow
5. Configure automatic photo and image uploads from mobile devices
6. Prepare migration path to a tower server / mini datacenter
7. Introduce stronger hardening and operational practices

## Lessons learned

This project already highlighted several important real-world lessons:

- a working stack is not the same as a well-designed stack
- filesystems matter
- `exfat` is a poor base for Linux server persistence
- `healthcheck` is not optional if you want cleaner startup behavior
- `depends_on` without readiness checks is weak
- bind mounts must use the correct internal application paths
- Git history is part of infrastructure discipline

## Disclaimer

This repository represents a practical learning-oriented self-hosting project.

It is useful as:

- a Raspberry Pi lab
- a DevOps learning exercise
- a portable foundation for future infrastructure

It should not yet be treated as a final hardened Internet-exposed production deployment.

## Author

Built by Giuseppe Lombardia / PeppeLomb17 as a practical self-hosting and DevOps learning project.
