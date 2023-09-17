# certbot:cloudflare

How to use certbot with cloudflare token dns validation to generate TLS certificates.


``` shell title="install prerequisites"

$ sudo apt install certbot && python3-certbot-dns-cloudflare

```

Obtain a cloudflare API token [from here](https://dash.cloudflare.com/profile/api-tokens) with Zone:DNS:Edit permissions.

- [x] Create Custom Token
- [x] Zone:DNS:Edit
- [x] Include:Specific zone:*your domain*
    - If you have a static IP that the request will come from, add it via:
          - [x] Is in:your.static.ip
- [x] Continue to summary
- [x] Create Token

<figure markdown>
![Cloudflare API Token](/assets/images/cloudflareapitoken.png){ loading=lazy }
<figcaption>example of my api token</figcaption>
</figure>

``` shell title="create cloudflare credentials file, protect it"
$ cat << EOF | sudo tee /root/.cloudflare
# cloudflare token used by certbot
dns_cloudflare_api_token = 0123456789abcdef0123456789abcdef01234567
EOF

$ sudo chmod 400 /root/.cloudflare
```

[^1]: https://developers.cloudflare.com/fundamentals/api/get-started/keys/
[^2]: https://developers.cloudflare.com/fundamentals/api/get-started/create-token/

!!! bug 

    If you have an older version of certbot that doesn't support tokens[^1], you can use your global API key[^2] + login email address, **but this is considered very insecure.**


``` shell title="generate the certificate request"
$ sudo certbot certonly --dns-cloudflare \
--dns-cloudflare-credentials /root/.cloudflare \
--dns-cloudflare-propagation-seconds 30 \
-d www.domain.com -d host2.domain.com -d host3.domain.com \

```
[^3]: https://certbot-dns-cloudflare.readthedocs.io/en/stable/

!!! note

    Your certificates will be located in: 
    
        /etc/letsencrypt/live/<your.domain>/{cert.pem,chain.pem,fullchain.pem,privkey.pem}

    The two you'll probably want to use most often are cert.pem and privkey.pem.