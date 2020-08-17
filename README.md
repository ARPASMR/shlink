## Introduction
This repository reports installation and configuration procedures, for the use in ARPA LOMBARDIA, of _Shlink_: a PHP-based self-hosted URL shortener that can be used to serve shortened URLs under personal/custom domain.

In this case, to easly set up Shlink, the official docker image [shlinkio/shlink:stable](https://hub.docker.com/r/shlinkio/shlink/tags) is used to generate a container.

## Official docs about Shlink

- Shink website: [Shlink - website](https://shlink.io)
- Official docs to install Shlink using a docker image: [Shlink Docker - GitHub page](https://github.com/shlinkio/shlink/tree/develop/docker)

## Usage
To read all about how to install a Shlink service read the official documentation on [Shlink Docker - GitHub page](https://github.com/shlinkio/shlink/tree/develop/docker).

------------------------------------------------------------------


## ARPA LOMBARDIA - Custom configuration settled to generate shorturls on domain host _http://shlink.docker.arpa.local/meteo/shlink/_
In the custom configuration shown below, Shlink is set up using an existing external PostgreSQL DB.
The service provides custom shorturls under the domain host _http://shlink.docker.arpa.local/meteo/shlink/_. 
Subsequently, thanks to a WAF (Web Application Firewall) solution adopted, the same domain was made accessible at: https://shorturl.arpalombardia.it/meteo/shlink/

**1. Generate a GeoLite2 License key**

_[ONLY FIRST TIME!!!!!]_

The _GEOLITE_LICENSE_KEY_ is the license key used to download new GeoLite2 database files. This is not mandatory, as a default license key is provided, but it is strongly recommended to provide your own. Go to [https://shlink.io/documentation/geolite-license-key](https://shlink.io/documentation/geolite-license-key) to know how to generate it.

In this case an account and a license key for idro@arpalombardia.it was created. To login in: https://www.maxmind.com/en/account/login


**2. Run the container linked using an external PostgreSQL DB**

This shlink instance interact with an external, already existing, PostgreSQL database: **_db_shlink_swarm_** (on _SINERGICO03_ Server)
```
docker run \
   shlinkio/shlink:stable \
   --name shlink \
   -p 8080:8080 \
   -e SHORT_DOMAIN_HOST=shlink.docker.arpa.local \
   -e SHORT_DOMAIN_SCHEMA=http \
   -e BASE_PATH= /meteo/shlink \
   -e GEOLITE_LICENSE_KEY=*LICENCE_KEY_GENERATA* \
   -e DB_DRIVER=postgres \
   -e DB_PORT=5432 \
   -e DB_NAME=db_shlink_swarm \
   -e DB_USER=*DB_USER* \
   -e DB_PASSWORD=*DB_PASSWORD* \
   -e DB_HOST=*DB_HOST* \
   -e no_proxy="localhost,.arpa.local,127.0.0.11,127.0.0.1"
   -e http_proxy="http://docker:Arpa2019!@proxy2.arpa.local:8080"
   -e https_proxy="https://docker:Arpa2019!@proxy2.arpa.local:8080"
```

**3. Generate the API-key**

_[ONLY FIRST TIME!!!!!]_

Once the shlink container is running, you can interact with the CLI tool by running `shlink`.
For example, if the container is called `shlink_container`, you can generate a new API key with:

```bash
docker exec -it shlink_container shlink api-key:generate
```

**4. Interact with the CLI tool**

Once the shlink container is running, you can interact with the CLI tool by running `shlink` with any of the supported commands.
Or you can list all tags with:

```bash
docker exec -it shlink_container shlink tag:list
```

Or locate remaining visits with:

```bash
docker exec -it shlink_container shlink visit:locate
```

All shlink commands will work the same way.

You can also list all available commands just by running this:

```bash
docker exec -it shlink_container shlink
```


