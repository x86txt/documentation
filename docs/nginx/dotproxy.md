# nginx:dot:reverseproxy

instructions for running a DNS over HTTPS proxy in nginx

!!! note
    - I use a pair of <a href="https://github.com/AdguardTeam/AdGuardHome">adguard home</a> containers for my resolvers - 10.5.22.12 and 10.5.22.13 in the configs below.
    - if you get an error related to the stream module, you may need to compile the module or install it via your package manager: *sudo apt install libnginx-mod-stream*

``` nginx

$ sudo nano /etc/nginx/nginx.conf

# place this outside the default http block

# for our dns over tls proxy
stream {

# basic logging configuration for DoT
  log_format basic '$remote_addr [$time_local] '
                   '$protocol $status $bytes_sent $bytes_received '
                   '$session_time';

# DNS upstream pool
  upstream dns {
    zone dns 64k;
    server 10.5.22.12:853;
    server 10.5.22.13:853;
  }

# dns-over-tls server configuration
  server {
    listen              853 ssl;
    ssl_certificate     /etc/letsencrypt/live/doh.secunit.io/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/doh.secunit.io/privkey.pem;
    proxy_pass          dns;

    error_log     /var/log/nginx/dot_error.log    notice;
    access_log    /var/log/nginx/dot_access.log   basic;
  }

}


```

If you want to see a config for DNS over HTTPS (DoH), see here: <a href="./dohproxy.md">dohproxy.md</a>
