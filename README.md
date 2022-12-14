## **BRDC Project Team Redash**

#### **參考文檔**

> **說明** :以下是中國網站 照文檔內安裝的Redash會是簡中版，如果要安裝英文版git clone 要改成https://github.com/getredash/redash.git

https://www.dazdata.com/download/2.pdf

https://www.dazdata.com

https://www.dazdata.com/docs/download/open.html

> **說明** :英文Redash官方網站

https://github.com/getredash/redash

#### **Https(SSL)Setup**

sudo apt-get install nginx


nginx conf

```Python
upstream redash_servers {
  server 127.0.0.1:5000;
}

server {
  listen 80;

  # Allow accessing /ping without https. Useful when placing behind load balancer.
  location /ping {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass       http://redash_servers;
  }

  location / {
    # Enforce SSL.
    return 301 https://$host$request_uri;
  }
}

server {
  listen 443 ssl;

  # Make sure to set paths to your certificate .pem and .key files.
  ssl on;
  ssl_certificate /etc/nginx/cert.pem; # or crt
  ssl_certificate_key /etc/nginx/cert.key;

  # Specifies that we don't want to use SSLv2 (insecure) or SSLv3 (exploitable)
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  # Uses the server's ciphers rather than the client's
  ssl_prefer_server_ciphers on;
  # Specifies which ciphers are okay and which are not okay. List taken from https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
  ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:ECDHE-RSA-AES128-GCM-SHA256:AES256+EECDH:DHE-RSA-AES128-GCM-SHA256:AES256+EDH:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";

  access_log /var/log/nginx/redash.access.log;

  gzip on;
  gzip_types *;
  gzip_proxied any;

  location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass       http://redash_servers;
    proxy_redirect   off;
  }
}

```

sudo service nginx restart

#### SSO
