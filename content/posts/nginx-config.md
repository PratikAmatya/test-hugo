+++
date = '2025-02-24T21:39:47+05:45'
draft = false
title = 'Nginx Config'
+++

### Frontend Nginx Sample Config

```nginx
server {

	root [PATH TO DIST FILE];

	index index.html index.htm index.nginx-debian.html index.php;

	server_name [DOMAIN NAME];

	location / {
     		 try_files $uri $uri/ /index.html;
	}


    location /vault {
        alias /usr/share/phpmyadmin;

        location ~\.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $request_filename;
            fastcgi_index index.php;
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        }
    }

    location /phpmyadmin/ {
        deny all;
    }

    location ~* \.env {
        deny all;
    }

    location ~* /\.git$ {
        deny all;
    }
}

```

### Backend Nginx Sample Config

```nginx
server {

    server_name [DOMAIN NAME];

    # Reverse Proxy for Node.js App
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # Optional: Add timeouts
        proxy_read_timeout 60s;
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;

        # Forward Real Client IP
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
	    proxy_set_header Host $host;
    }

    # Deny access to .env files
    location ~* \.env {
        deny all;
    }

    # Deny access to .git directories
    location ~* /\.git$ {
        deny all;
    }

    location ~* ^/(xmlrpc\.php|wp-admin|wp-includes|wlwmanifest\.xml|wp-login\.php) {
        deny all;
    }

    # Deny access to phpMyAdmin
    location /phpmyadmin/ {
        deny all;
    }

    # Optional: Add security headers
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
}
```
