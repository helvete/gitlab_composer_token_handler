# gitlab_composer_token_handler
gitlab tokens are per repository; composer cannot declare repo-specific basic auth

Contents auth.json should look like this (ie. to contain full protocollles repository URL):
```
    "http-basic": {                                                             
        "gitlab.selfhosted.domain.tld/gitlab_composer_token_handler.git": {                     
            "password": "secret-passwordish-token",                                 
            "username": "gitlab+deploy-token-01"                                
        },
```
