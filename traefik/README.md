# Traefik reverse-proxy configuration in docker-compose with Cloudflare and Let's Encrypt
Taken primarily from [TechnoTim's docs](https://github.com/techno-tim/techno-tim.github.io/tree/master/reference_files/traefik-portainer-ssl/traefik) \
You need a Linux machine with Docker & docker-compose and a domain with Cloudflare DNS servers configured.

## Folders 'n files
./traefik \
├── docker-compose.yml \
└── data \
    ├── acme.json \
    ├── config.yml \
    └── traefik.yml

Remember to set permissions for `acme.json`
```bash
chmod 600 acme.json
```

## docker-compose.yml
Some stuff needs to be changed. Specifically the stuff like `<your_name>`.
`- CF_API_EMAIL=<cloudflare_email>` to the email you used on your Cloudflare account.
`- CF_DNS_API_TOKEN=` to an API-token from Cloudflare. The API-token [needs specific permissions](api-token.png).
