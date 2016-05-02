---
name: Backing up servers using Docker
---

# Backup Docker container's volumes to S3

```
docker run --rm --env-file taiga-back-dockup.txt --volumes-from taiga_taigaback_1 --name dockup tutum/dockup:latest
```

where taiga-back-dockup.txt contents are:

```
AWS_ACCESS_KEY_ID=accessKeyHere
AWS_SECRET_ACCESS_KEY=accessSecretHere
AWS_DEFAULT_REGION=us-east-1
BACKUP_NAME=taiga
PATHS_TO_BACKUP=/usr/local/taiga/media /usr/local/taiga/static
S3_BUCKET_NAME=taiga-backup
RESTORE=false
```

The `PATHS_TO_BACKUP` are found by the command: `docker inspect taiga_taigaback_1` and in the output you will find `Volumes`:

```
...
"Volumes": {
    "/usr/local/taiga/logs": {},
    "/usr/local/taiga/media": {},
    "/usr/local/taiga/static": {}
},
...
```

(I don't care to backup the logs directory)

If using an ubuntu based OS, create a cronjob. If using CoreOS, create a system timer. 
