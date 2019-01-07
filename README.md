# gitlab_composer_token_handler

The reasons for creating this tool is:

* gitlab deploy tokens can be generated per repository only (cannot be shared between more repositories)
* composer cannot declare repo-specific basic auth (at least for `gitlab`)

There are two approaches to mitigate the problem:
1. create deploy user for all repositories that will be given access to particular environment (production, staging, UAT, ...) and generate a token for him. Cons are gitlab service is paid per user which means: the more environments you need the more money you have to spend for deployment (this isn't a problem w/ comunity edition which is free of charge)
1. manage the tokens per environment AND repository

This tool was created in order to manage the tokens (point 2) automatically while maintaining the same level of security as of the default basic auth model.

`auth.json` file has to have a bit different structure. The contents of it should look like this (ie. to contain full repository URL instead of just a FQDN):
```
{
    "http-basic": {
        "gitlab.selfhosted.domain.tld/repository1.git": {
            "password": "AAAAAAAAAAAAAAAAAAAA",
            "username": "gitlab+deploy-token-11"
        },
        "gitlab.selfhosted.domain.tld/repository2.git": {
            "password": "BBBBBBBBBBBBBBBBBBBB",
            "username": "gitlab+deploy-token-16"
        },
        "gitlab.selfhosted.domain.tld/repository3.git": {
            "password": "CCCCCCCCCCCCCCCCCCCC",
            "username": "gitlab+deploy-token-15"
        },
        "gitlab.selfhosted.domain.tld/repository4.git": {
            "password": "DDDDDDDDDDDDDDDDDDDD",
            "username": "gitlab+deploy-token-12"
        },
        "gitlab.selfhosted.domain.tld/repository5.git": {
            "password": "EEEEEEEEEEEEEEEEEEEE",
            "username": "gitlab+deploy-token-13"
        },
        "gitlab.selfhosted.domain.tld/repository6.git": {
            "password": "FFFFFFFFFFFFFFFFFFFF",
            "username": "gitlab+deploy-token-14"
        }
    }
}
```

This simple OOP tool allows you to rewrite the composer json files in order to be able to download all dependencies while not storing sensitive data within your repository.

The tool was actually created for use w/ `docker` so that specific `COMPOSER` env var is set to the same value as the target file of this script. But works w/out the `docker` as well.

Example snippet from `docker-compose.yml` file:

```
version: '3'
services:
  composer:
    image: composer:1.4
    volumes:
      - htdocs:/app
    environment:
      - COMPOSER=/app/composer_gitignore.json
    command: >
      sh -c "/app/docker/composer_gitlab &&
      composer install --no-suggest --no-interaction"
```

This way, the script is run just before the composer starts and does all the necessary replacement.
