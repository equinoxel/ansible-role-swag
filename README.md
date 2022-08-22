Role Name
=========

SWAG installer for linux

Role Variables
--------------
This role uses the variables listed below, along with default values (see defaults/main.yml).

### Docker

You can specify the docker container name and version:

```yml
swag_container_name: "swag"
swag_image_version: "latest"
```

Swag will run by default as user *1000:1000*:

```yml
swag_uid: 1000
swag_gid: 1000
```

You will need to define volumes to store data permanently. `swag_data` defines the location where SWAG stores its files (default site, configuration, certs...). You can define a separate `swag_config` volume to store its configuration data (e.g. for backup).

```yml
swag_data: "/mnt/data/swag"
swag_config: "{{ swag_data }}/config"
```

**Note**: This role supports path-defined volumes (not named volumes) right now.

You will need to specify the HTTP and HTTPS ports:

```yml
swag_port_http: 3080
swag_port_https: 3443
```

You can also customise the timezone:

```yml
swag_tz: "Europe/Brussels"
```

### Domains and certificates

You need to specify your main domain and subdomains (if any):

```yml
swag_url: "example.com"
swag_validation: "dns"
swag_subdomains: 
swag_onlysubdomains: "false"
```

If you use a provider for certificates, you need to specify a set of common values:

```yml
swag_certprovider: # implicitly letsencrypt
swag_propagation:
swag_duckdnstoken:
swag_email: "user@example.com"
swag_extra_domains: 
swag_staging: "false"
```
Please use `swag_staging: "true"` while setting up and change it to `"false"` once you're ready. This way, you'll avoid hitting letsencrypt's query limits.

### DNS

This role allows you to set up the DNS configuration/plugin via a template. You can define the template via the `swag_dns_config` parameter, where `src` specifies the location of the template and `dst` defines the destination name like so:

```yml
swag_dnsplugin: "cloudflare"
swag_dns_config:
  src: "cloudflare.ini.j2"
  dst: "cloudflare.ini"
```

This template replicates the one defined in SWAG:
```jinja
# Instructions: https://github.com/certbot/certbot/blob/master/certbot-dns-cloudflare/certbot_dns_cloudflare/__init__.p># Replace with your values

{% if not cloudflare_api_token %}
# With global api key:
dns_cloudflare_email = {{ swag_email }}
dns_cloudflare_api_key = {{ cloudflare_api_key }}
{% else %}

# With token (comment out both lines above and uncomment below):
dns_cloudflare_api_token = {{ cloudflare_api_token }}
{% endif %}
```

In this case, you'll need to define the cloudflare credentials:

```yml
cloudflare_api_token:
cloudflare_api_key:
```

At the moment of writing this role, I use [cloudflare](https://www.cloudflare.com). You can define your own plugin and template. 

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yml
- hosts: servers
  roles:
      - laurivan.swag
```

License
-------

MIT

Author Information
------------------

This role was created in 2022 by [Laur Ivan](https://www.laurivan.com)
