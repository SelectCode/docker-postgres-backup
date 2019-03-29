# S3 Postgres Backup in Docker 🐳


### Usage:

docker-compose:
```yaml
pgbackups3:
    image: selectcode/postgres-backup
    container_name: backup
    restart: always
    environment:
      SCHEDULE: ${SCHEDULE}
      S3_REGION: region
      S3_ACCESS_KEY_ID: ${MINIO_ACCESS_KEY}
      S3_SECRET_ACCESS_KEY: ${MINIO_SECRET_KEY}
      S3_BUCKET: econap-telemetrics
      S3_PREFIX: backup
      S3_ENDPOINT: ${S3_URL}
      S3_S3V4: "yes"
      POSTGRES_HOST: pgdb
      POSTGRES_DATABASE: api_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      POSTGRES_EXTRA_OPTS: '--schema=api --blobs'
```

**.gitlab-ci.yml**
```yaml
before_script:
  - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY

// One-Time Run
prepare:backup:
  stage: prepare
  script:
    - docker-compose run --rm pgbackups3

// Daily Run
deploy:backup:
  stage: deploy
  variables:
    SCHEDULE: "@daily"
  script:
    - docker-compose run -d -e SCHEDULE="@daily" pgbackups3

```


### Automatic Periodic Backups

You can additionally set the `SCHEDULE` environment variable like `-e SCHEDULE="@daily"` to run the backup automatically.

More information about the scheduling can be found [here](http://godoc.org/github.com/robfig/cron#hdr-Predefined_schedules).

