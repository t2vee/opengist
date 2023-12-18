# Opengist

<img height="108px" src="https://raw.githubusercontent.com/thomiceli/opengist/a9dd531f676d01b93bb6bd70751a69382ca563b0/public/opengist.svg" alt="Opengist" align="right" />

Opengist but it has support for subpaths. 
Use this Nginx Config:
```
server {
    listen 443 ssl http2;
    server_name <domain>;

    ssl_certificate <ssl_pem file>;
    ssl_certificate_key <ssl_key file>;

    ssl_session_cache shared:SSL:1m;
    ssl_session_timeout 5m;

    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location /<YOUR_SUBPATH>/ {
        rewrite ^/opengist(/.*)$ $1 break;
        proxy_pass http://127.0.0.1:6157;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Prefix /<YOUR_SUBPATH>;

        sub_filter_once off;
        sub_filter_types *;
        sub_filter '</head>' '<base href="/<YOUR_SUBPATH>/"></head>';
    }
}
```

To install:
Clone this repo `https://github.com/t2vee/opengist`  
Use this docker-compose.yml file:  
```
version: "3"

services:
  opengist:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: opengist
    restart: unless-stopped
    ports:
      - "6157:6157" # HTTP port
      #- "2222:2222" # SSH port, can be removed if you don't use SSH
    volumes:
      - "./opengist:/opengist"
      - "./config.yml:/config.yml"
    environment:
      OG_EXTERNAL_URL: <YOUR_EXTERNAL_URL_WITH_SUBPATH>
```
`sudo docker compose up -d --build --remove-orphans`  

enjoy :3
