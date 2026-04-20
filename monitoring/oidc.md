# OIDC on Grafana 

Config via UI with admin user only

## Generic OAuth
1. Go to Administrator --> Authentication.
2. Select Generic OAuth.
3. Fill information
    - Display name : `Oauth`
    - ClientID : from oauth
    - scopes
        - read
    - Auth URL: uri authorize eg `https://oauth.data.storemesh.com/o/authorize/`
    - Token URL : uri token eg `http://oauth-loadbalancer.dataplatform:18000/o/token/`
    - API URL : uri get user info eg `http://oauth-loadbalancer.dataplatform:18000/api/v1/account/me/`
    - Allow sign up : `true`
4. Save and enable.

# Note

If the redirect url is localhost add these in grafana.ini.

```ini
[server]
protocol = http
root_url = https://your-domain.com
```


After config restart the grafana pod