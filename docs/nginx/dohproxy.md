# nginx:doh:reverseproxy

instructions for running a DNS over HTTPS proxy in nginx

!!! note
    I use a pair of <a href="https://github.com/AdguardTeam/AdGuardHome">adguard home</a> containers for my resolvers
    - doh1.secunit.io and doh2.secunit.io in the configs below.

``` nginx

# replace with your upstream DoH servers
upstream doh {
    server      doh1.secunit.io:443;
    server      doh2.secunit.io:443;
    keepalive   32;
}

server {
  listen                443 ssl;
  listen                443 quic; # we're going to enable http3 also
  http2                 on;       # let's enable http2
  quic_retry            on;       # for http3

  # I prefer separate log files for each of my virtual hosts
  error_log     /var/log/nginx/doh_error.log    notice;
  access_log    /var/log/nginx/doh_access.log   main;

  server_name  doh.secunit.io;is is the fqdn that will be used by DoH clients, https://doh.secunit.io/dns-query

  server_tokens off;

  ssl_protocols             TLSv1.3;                     # everything should support tls 1.3 at this point 
  ssl_prefer_server_ciphers on;
  ssl_dhparam               /etc/nginx/ssl/dhparam.pem;  # openssl dhparams -out <2048/4096> dhparam.pem
  ssl_ciphers               EECDH+AESGCM:EDH+AESGCM;
  ssl_ecdh_curve            secp521r1:secp384r1;               
  ssl_session_timeout       10m;
  ssl_session_cache         shared:SSL:10m;
  ssl_session_tickets       off;                
  ssl_early_data            on;                      

  # http headers
  add_header            X-Frame-Options DENY;
  add_header            X-Content-Type-Options nosniff;
  add_header            X-XSS-Protection "1; mode=block";
  add_header            Strict-Transport-Security "max-age=63072000";
  add_header            alt-svc 'h3=":443"; ma=86400';                      # let's allow http2 to upgrade to http3 if desired
  add_header            X-Powered-By "secunit, llc" always;                 # why not have some fun?
  add_header            X-Provided-By "amd, cloudflare, and nginx" always;  # ditto

  # you can use any ssl certificates here that the client trusts, these are the certs presented to them
  ssl_certificate       /etc/letsencrypt/live/doh.secunit.io/fullchain.pem;
  ssl_certificate_key   /etc/letsencrypt/live/doh.secunit.io/privkey.pem;

  # let's redirect any requests to root to /dns-query for broken DoH clients
  location = / {
    return 301 https://$host/dns-query;
  }

  location /dns-query {
    proxy_pass              https://doh;
    proxy_set_header        Host $host;
    proxy_set_header        X-Real-IP $remote_addr;
    proxy_ssl_session_reuse on;                      # this is important so subsequent requests don't have to set up a new http2/3 session each time
  }
}
```

If you want to see a config for DNS over TLS (DoT), see here: <a href="./dotproxy.md">dotproxy.md</a>
