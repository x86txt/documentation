# cloudflare:nohost:redirect

If you want to perform a redirect for a domain, but don't have an actual server hosted anywhere, you can do this at Cloudflare by creating an A record for the domain, with an IP of 192.0.2.1.

For example, let's say I want to host a privacy policy using one of the free privacy policy generation services, like [privacypolicies.com](https://www.privacypolicies.com).

1. Create your policy and make a note of the URL provided, in my case that url is https://www.privacypolicies.com/live/d148ad59-eb7d-4e21-90d2-5724356eeac5
2. I want privacy.secunit.io to redirect to that privacypolicy URL.
3. I create an A record for hostname 'privacy' with an IP of 192.0.2.1.

<figure markdown>
![example of output](/assets/images/cloudflare/privacypolicyredirect.png){ loading=lazy }
  <figcaption>create an A record like this</figcaption>
</figure>

Save your record and and go to your Cloudflare Redirect Rules under 'Rules -> Redirect Rules'.

1. Create Rule
2. Choose 'Custom Filter Expression'
3. Field -> Hostname -> equals -> privacy.secunit.io
4. Then -> Static -> URL -> https://www.privacypolicies.com/live/d148ad59-eb7d-4e21-90d2-5724356eeac5
5. Deploy

<figure markdown>
![example of output](/assets/images/cloudflare/privacypolicycfredirect.png){ loading=lazy }
  <figcaption>create a redirect record like this</figcaption>
</figure>

Now when you visit [https://privacy.secunit.io](https://privacy.secunit.io) you will be redirected to the other URL!
