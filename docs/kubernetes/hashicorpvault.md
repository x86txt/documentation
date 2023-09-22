# hashicorp:vault:ad:auth

This is how to configure [Hashicorp Vault](https://www.vaultproject.io/) for Active Directory Authentication.[^1]

[^1]: This article is based on this video from HashiCorp: [https://www.youtube.com/watch?v=aGp8pb7KRIo](https://www.youtube.com/watch?v=aGp8pb7KRIo)


``` shell title="enable the ldap authentication method"
$ vault auth enable ldap
Success! Enabled ldap auth method at: ldap/
```

``` shell title="send the ldap config to vault"
$ vault write auth/ldap/config \
   url="ldaps://dc1.secunit.lan:636" \
   userattr=sAMAccountName \
   userdn="CN=Users,DC=secunit,DC=lan" \
   groupdn="CN=Users,DC=secunit,DC=lan" \
   groupfilter="(&(objectClass=group)(member:1.2.840.113556.1.4.1941:={{.UserDN}}))" \
   groupattr="cn" \
   binddn="CN=vaultsvc,CN=Users,DC=secunit,DC=lan" \
   bindpass="niceTryHacker!@&#" \
   insecure_tls=false \
   certificate=@adca.pem \
   starttls=true
Success! Data written to: auth/ldap/config
```

``` shell title="now test a login to the vault"
$ vault login -method=ldap username=matt password='s00per D00p3r S7r0ng'

Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                    Value
---                    -----
token                  <redacted>
token_accessor         qlOERgrAeBbyA3GWRyz8kKMi
token_duration         768h
token_renewable        true
token_policies         ["default"]
identity_policies      []
policies               ["default"]
token_meta_username    matt
```
