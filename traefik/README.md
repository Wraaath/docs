# Traefik reverse-proxy configuration in docker-compose with Cloudflare and Let's Encrypt
Stolen from [TechnoTim's docs](https://github.com/techno-tim/techno-tim.github.io/tree/master/reference_files/traefik-portainer-ssl/traefik) \

* [Getting-started](#getting-started)
* [docker-compose.yml](#docker-compose)
* [traefik.yml](#traefik)
* [config.yml](#config)

## Getting-started
./traefik \
├── docker-compose.yml \
└── data \
‎..........├── acme.json \
‎..........├── config.yml \
....‎......└── traefik.yml

You need a Linux machine with Docker & docker-compose and a domain with Cloudflare DNS servers configured. \
You also need a way to configure local DNS resolving. You should probably use Pihole for this.

Remember to set permissions for `acme.json`
```bash
chmod 600 acme.json
```

**ALSO ALSO VERY ALSO** \
Make sure you don't redeploy this container too too much. Let's Encrypt will tempban your IP for 3 hours if you keep asking for certificates. This happend to me :)))

## docker-compose
Some stuff needs to be changed. Specifically the stuff like `<your_name>`. \

You need to this to the email you used on your Cloudflare account.
```yml
- CF_API_EMAIL=<cloudflare_email>
```

You need to this to an API-token from Cloudflare. The API-token needs [specific permissions](api-token.png).
```yml
- CF_DNS_API_TOKEN=<cloudflare_api_token>
```

Change the domain to the actual domain you own on Cloudflare. Otherwise no valid certificates can be aquired. \
Important to mention that you don't need to put `traefik.local.` in front of your domain, though you need something. Could be just `traefik.your_domain.tld`.
```yml
- "traefik.http.routers.traefik.rule=Host(`<traefik.local.your_domain.tld>`)"
```

Here you need to get a username for the dashboard. But it aint that simple. You first have to install `apache2-utils` and use the command `echo $(htpasswd -nb "<user>" "<password>") | sed -e s/\\$/\\$\\$/g`. The output is what you wanna paste instead of `<user:pass>`.
```yml
- "traefik.http.middlewares.traefik-auth.basicauth.users=<user:pass>"
```

Same story as the one before password. This is just for `https` and not `http`.
```yml
- "traefik.http.routers.traefik-secure.rule=Host(`<traefik.local.your_domain.tld>`)"
```

Same story as above except this is gonna be the main certificate Traefik is gonna get for your services.
```yml
- "traefik.http.routers.traefik-secure.tls.domains[0].main=<local.your_domain.tld>
```

And a subjectname alternative for Traefik. This is gonna be the certificates for most of your services. Like for `whateverservice.local.your_domain.tld` and all other services where you are gonna put the name in front of `.local`. The `*.` indicates it could be `anything.local.your_domain.tld`.
```yml
- "traefik.http.routers.traefik-secure.tls.domains[0].sans=<*.local.your_domain.tld>
```

## traefik
Really all you need to replace is this. With same email you used earlier.
```yml
email: <cloudflare_email>
```

## config
This is where you put everything for routing. Basically configurations for all services (except Traefik itself) that are not on the same machine as Traefik. \
These configurations are different for every service, so you should look [here](https://github.com/techno-tim/techno-tim.github.io/blob/master/reference_files/traefik-portainer-ssl/traefik/config.yml) for some templates for gettings started.
