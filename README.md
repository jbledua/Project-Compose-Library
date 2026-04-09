# Project Compose Library

A simple Docker Compose stack for running Audiobookshelf with a Tailscale sidecar.

The stack has two services:

- `library-ts`: a Tailscale container that handles secure network access for the stack
- `library-app`: an Audiobookshelf container that shares the Tailscale network namespace

This setup lets Audiobookshelf run behind Tailscale without exposing the app directly to the public internet.

## Requirements

- Docker
- Docker Compose
- A Tailscale auth key with the required tags configured
- Local folders for your audiobook and podcast libraries

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/jbledua/Project-Compose-Library.git
cd Project-Compose-Library
````

### 2. Create your environment file

Copy the example environment file to `.env` and fill in your own values.

On Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

On macOS or Linux:

```bash
cp .env.example .env
```

Edit `.env` and update these values:

* `STACK_NAME`
* `TS_HOSTNAME`
* `TS_AUTHKEY`
* `PUBLIC_PORT` if you want to change the Audiobookshelf port
* `TIMEZONE`
* `AUDIOBOOKS_PATH`
* `PODCASTS_PATH`

To create `TS_AUTHKEY`, open the Tailscale admin console and create an auth key, or read the Tailscale auth key docs at [https://tailscale.com/kb/1085/auth-keys/](https://tailscale.com/kb/1085/auth-keys/). Copy the generated key into `.env` as `TS_AUTHKEY`. If you are using Tailscale tags, make sure the key is allowed to use the tag configured for this stack.

## Deploy the stack

Run the stack from the directory containing `docker-compose.yml`:

```bash
docker compose up -d
```

To check container status:

```bash
docker compose ps
```

To view logs:

```bash
docker compose logs -f
```

## Accessing Audiobookshelf

After the containers start, access Audiobookshelf through the configured port, usually:

```text
http://localhost:13378
```

If you changed `PUBLIC_PORT`, use that value instead.

If your client device is also connected to the same Tailscale tailnet, you can also access Audiobookshelf using the Tailscale DNS name or the Tailscale IP address for the stack.

## Volumes and Data

The stack uses three named volumes:

* `library-ts-state` stores Tailscale state
* `library-config` stores Audiobookshelf configuration
* `library-metadata` stores Audiobookshelf metadata and database

Your media folders are mounted into Audiobookshelf at:

* `/audiobooks`
* `/podcasts`

## Notes

* The Audiobookshelf container uses `network_mode: service:library-ts`, so it shares the Tailscale container's network stack.
* The Tailscale container is configured with `--advertise-tags=tag:container`.
* Make sure the paths in `.env` exist on your machine before starting the stack.