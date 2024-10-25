
To Test Nginx Configuration: 

```bash
sudo nginx -t
```

## Context

A **context** is a block within the configuration file where specific directives are valid and applicable. Example: `http` , `server` , `location` , `mail` ... .  **Basically a block with curly parenthesis and can be nested**  

### Main Context:

Top-level context, outside of any blocks with Directives which apply to the entire server. Example directives: `user`, `worker_processes`, `error_log`, `pid`.

### Events Context:

The **`events`** context is responsible for handling the connection-level settings and configurations related to how `Nginx` manages connections for its worker processes.

```nginx
events {
   worker_connections 1024; # Maximum no. of simultaneous connections per worker
   use epoll;               # Event handling method (use 'epoll' for Linux)
}
```

### Stream Context:

The **`stream`** context is used for configuring TCP/UDP-based services in `Nginx`, such as load balancing or reverse proxying for non-HTTP traffic.

```nginx
stream {
    upstream backend {     # Defines a pool of backend servers
        server 192.168.1.10:3306;
        server 192.168.1.11:3306;
    }

    server {
        listen 3306;        # Listen on port 3306 for incoming MySQL connections
        proxy_pass backend; # Pass the traffic to the upstream servers
    }
}
```

### HTTP Context:

The **`http` context** is one of the most important and widely used contexts. It is where the configuration for handling HTTP and HTTPS traffic is defined.

The `http` context typically contains:

- **General HTTP configurations** (like timeouts, compression, etc.).
- **Server blocks** to define virtual hosts.
- **Location blocks** to define how specific paths are handled.

```nginx
http {
    # Global settings for HTTP requests
    include       mime.types;   # Include MIME type definitions
    default_type  application/octet-stream; # Default MIME type
    log_format    main '$remote_addr - $remote_user [$time_local] "$request" '
						'$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';
    access_log    /var/log/nginx/access.log main;  # Log format for access logs

    sendfile        on;         # Enable sendfile for efficient file transfers
    tcp_nopush      on;         # Optimize the sending of data packets
    keepalive_timeout  65;      # Timeout for keeping connections open
    gzip on;                    # Enable gzip compression for responses

    # Virtual host configuration (inside the server block)
    server {
        listen 80;               # Listen on port 80 for HTTP traffic
        server_name example.com; # Define the domain name

        root /var/www/html;      # Define the document root for the website
        index index.html;        # Define the default file to serve

        location / {
        # First try to serve request as file, then as directory,
        # then fall back to displaying a 404
            try_files $uri $uri/ =404;  
        }

        location /images/ {
            autoindex on;         # Enable directory listing for this path
        }
    }
}

```

#### default_server

When Nginx receives a request without a matching `Host` header or if no specific `server_name` matches the domain name in the request, Nginx will route the request to the `server` block marked with `default_server`.

Example for multiple `server_name`:

```nginx
server {
    listen 80 default_server;
    server_name _;
    root /var/www/default_site;
    index index.html;
}

server {
    listen 80;
    server_name example.com;
    root /var/www/example_site;
    index index.html;
}
```

> **NOTE:** A default server is a property of the listen port, and different default servers may be defined for different ports / listen address

### Reverse Proxy

A **reverse proxy** involves forwarding client requests from the Nginx server to one or more backend servers.

#### Basic Reverse Proxy Configuration

```nginx
server {
    listen 80;
    server_name example.com;  # Your domain or IP address

    location / {
	    # add_header Cache-Control no-store # Tell the browser to not cache req
        proxy_pass http://10.244.0.242:3000;  # Forward requests to the backend server (e.g., Node.js on port 3000)
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

### Load Balancing

To Use Load Balancing we use `upstream` block to configure list of servers to redirect to

```nginx
http {
    upstream backend {
        server 10.244.0.242:3000;  # First backend server
        server 10.244.0.242:3001;  # Second backend server
    }

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://backend;  # Use the upstream group for load balancing
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}

```

### SSL Certificate

To set up an HTTPS reverse proxy, you need to enable SSL

```nginx

upstream backend {
        server 10.244.0.242:3000;  # First backend server
        server 10.244.0.242:3001;  # Second backend server
    }
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;   # Path to your SSL certificate
    ssl_certificate_key /etc/nginx/ssl/example.com.key;  # Path to your SSL key

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;  # Redirect HTTP to HTTPS
}

```

