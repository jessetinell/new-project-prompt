# Hosting

<!-- Not deployed yet. Filled at the first deploy: see docs/STANDARDS.md → Local first, deploy on request. Remove this line then. -->

**Account:** {{account name}} ({{account_id}})

## Links

| | |
|---|---|
| App | {{live URL}} |
| Hosting project | https://dash.cloudflare.com/{{account_id}}/{{path}} |
| Database | https://dash.cloudflare.com/{{account_id}}/{{path}} |
| Storage | https://dash.cloudflare.com/{{account_id}}/{{path}} |
| Auth | https://one.dash.cloudflare.com/{{account_id}}/{{path}} |
| AI keys | {{link}} |
| AI spend limits | {{link}} |
| Logs / monitoring | https://dash.cloudflare.com/{{account_id}}/{{path}} |

## One-time setup

<!-- Numbered dashboard steps the human must do, per service. -->

1. **{{service}}:** {{step}}

## Deploy

Ask the user first.

```sh
{{migrate command, if any}}
{{deploy command}}
```

## Local

```sh
{{setup command}}
{{dev command}}
```
