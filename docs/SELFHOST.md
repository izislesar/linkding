# linkding selfhost notes

Runs on sqlite, ~60mb ram. Nothing starts on boot — everything manual.

## up

```bash
cp selfhost/.env.example selfhost/.env
# edit passwords
docker compose -f selfhost/docker-compose.yml --env-file selfhost/.env up -d
```

open http://localhost:9090, login with superuser creds from .env.

## backup

```bash
docker compose -f selfhost/docker-compose.yml stop
docker run --rm -v linkding-data:/data -v $(pwd)/backups:/b alpine \
  tar czf /b/linkding-$(date +%F).tar.gz /data
docker compose -f selfhost/docker-compose.yml start
```

restore is the reverse (stop, rm volume contents, untar, start).

## update

bump the tag in selfhost/docker-compose.yml, then:

```bash
docker compose -f selfhost/docker-compose.yml --env-file selfhost/.env pull
docker compose -f selfhost/docker-compose.yml --env-file selfhost/.env up -d
```

if the changelog mentions migrations, check `docker logs linkding` after update.
