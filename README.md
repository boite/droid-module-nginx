# Droid Module: droid/nginx

Setup NGINX as a load balancing, SSL terminating reverse proxy. For more
information on Droid, please see [droidphp.com](http://droidphp.com).

The steps involved are:-

1. Update platform package lists.
2. Install the nginx package.
3. Write the configuration to `/etc/nginx/nginx.conf`.
4. Write an upstream configuration file, for each virtual host, to
   `/etc/nginx/conf.d/`.
5. Write a certificate authority file, for client authentication, for each
   applicable virtual host, to `/etc/ssl/certs/`.
6. Write a HTTPS private key file, for each applicable virtual host, to
   `/etc/ssl/private/`.
7. Write a virtual host configuration, for each virtual host, to
   `/etc/nginx/sites-available/`.
8. Enable each virtual host.
9. Instruct Nginx to reload its configuration.


## Assumptions

1. The platform is Debian-based.
2. Each of the virtual hosts is running on each of the upstream servers.


## Limitations

1. One virtual host configuration template is used as the basis for all of the
   virtual hosts.


## Information required by the module

1. Configuration values for the main nginx.conf (global, events and http
   contexts):-

        nginx:
            worker_processes: <integer>
            error_log: <string>
            events:
                worker_connections: <integer>
                multi_accept: <string> # "on" or "off"
            http:
                server_names_hash_bucket_size: <integer>
                client_max_body_size: <string> # e.g. "2m"
                access_log: <string>
                sendfile: <string> # "on" or "off"
                tcp_nopush: <string> # "on" or "off"
                tcp_nodelay: <string> # "on" or "off"
                keepalive_timeout: <integer>
                keepalive_requests: <integer>

2. A list of one or more virtual host configurations (server context) and
   non-config values, as follows:-

        nginx_vhosts:
            -
              listen: # list of one or more socket descriptions
                  - <string> # e.g. "443 ssl", "[::]:443 ssl"
              name: <string> # A name for the vhost
              servername: <string> # The server name
              index: <string> # default: "index.html index.htm"


3. A list of one or more upstream configurations, as follows:-

        nginx_upstreams:
            -
              name: <string> # name for the upstream
              servers: # A list of one or more upstream servers
                  - <string> # IP address (and opts)


## Optional information

1. Configuration values for the main nginx.conf (global and http contexts):-

        nginx:
            extra_options: <string>
            http:
                proxy_cache_path: <string>
                extra_options: <string>

2. The following may be added to the mandatory vhost configuration values
   (server context):-

        nginx_vhosts:
            -
              https_private_key: <string> # local path to private key (used by
                                          # droid to copy the key to the nginx
                                          # host)
              https_certificate: <string> # local path to server certificate
              auth_cacert: <string> # local path to Certificate Authority cert
                                    # for Client Authentication
              root: <string>
              error_page: <string>
              access_log: <string>
              error_log: <string>
              extra_options: <string>

3. The following may be added to the mandatory upstream configuration values,
   as follows:-

        nginx_upstreams:
            -
              strategy: <string> # "ip_hash", "round_robin", "least_conn", etc.
