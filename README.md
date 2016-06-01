# sentry-stack

Running Sentry with Docker.

All images are official. You can find them in [Docker Hub](https://hub.docker.com/).


## Copy .env-example to .env and set these variables:

```
SENTRY_POSTGRES_HOST=database
SENTRY_REDIS_HOST=redis
SENTRY_SECRET_KEY=<some-nice-secret-key-here>
```

## Downloading Images:

```bash
docker pull sentry:onbuild
docker pull sentry:8.3
docker pull redis:latest
docker pull postgres:latest
```
or
```bash
docker-compose build .
```

## Mounting and Running Images:

#### Start Base Services
```bash
docker-compose up -d redis database sentry
```

#### Initial Sentry Setup
```bash
docker exec -it [container-name] sentry upgrade
```

#### Removing Sentry's Old Data
```bash
docker exec -it [container-name] sentry cleanup --days=30
```

#### Run Remaining Containers (Celery)
```bash
docker-compose up -d
```

#### Run Shell (Bash) in Sentry as Root
```bash
docker exec -it --user=root [container-name] /bin/bash
```

#### Queue Monitoring
```bash
docker exec -it --user=root [container-name] sentry queues list
```

### Full Running Example:

```bash
$ docker-compose up -d redis database sentry
$ docker exec -it sentrystack_sentry_1 sentry upgrade # container-name = sentrystack_sentry_1
$ docker-compose up -d
$ docker restart sentrystack_sentry_1 # restart is required after installing plugins.
```

> Check http://docker-machine-ip:9000/_health/?full after that.

## Plugins

To activate plugins go to:

```
http://docker-machine-ip:9000/sentry/<project-name>/settings/plugins/
```

> Plugins are activated per project.

#### Github


```bash
docker exec -it --user=root [container-name] pip install sentry-github
```

![github](https://dl.dropboxusercontent.com/u/26562227/Github/Sentry/sentry-github.png)

First, you must configure your webhook and get api keys on Github.
```
<URL_TO_SENTRY>/account/settings/social/complete/github/
```

Then set the variables below:

```
GITHUB_APP_ID = 'GitHub Application Client ID'
GITHUB_API_SECRET = 'GitHub Application Client Secret'
GITHUB_EXTENDED_PERMISSIONS = ['repo']
```

Use HTTPS?

```
SOCIAL_AUTH_REDIRECT_IS_HTTPS = True
```

Behind a proxy?

```
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
USE_X_FORWARDED_HOST = True
```

#### Slack

```bash
docker exec -it --user=root [container-name] pip install sentry-slack
```

![slack](https://dl.dropboxusercontent.com/u/26562227/Github/Sentry/sentry-slack.png)

#### Riemann

```bash
docker exec -it --user=root [container-name] pip install sentry-riemann
```

![riemann](https://dl.dropboxusercontent.com/u/26562227/Github/Sentry/sentry-riemann.png)

## Sentry Web

```
Open 127.0.0.1:9000 (Sentry Web UI)
```
