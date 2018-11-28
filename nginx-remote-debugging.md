# Remote debugging ManageIQ with nginx reverse proxy

---------

## Note: I found a better way to do this, simply using the webpack-dev-server's built in proxy settings.

I'll leave this document here for posterity, and document the new process soon.

---------

This guide provides a method for running a local ManageIQ UI development environment which sends all its API requests to a remote appliance. We can use this to test new UI changes in V2V with real migrations.

This guide assumes you already have a working ManageIQ development environment, where you can run webpack-dev-server proxying to rails. It is written for developers using Mac OS, but it could be adapted easily for Linux.

# Setup

1. Install nginx with Homebrew:
    ```
    brew install nginx
    ```

2. By default, nginx will listen on port 8080. We need that port free for webpack-dev-server, so we want to change nginx's default port. Edit the default config file at `/usr/local/etc/nginx/nginx.conf` and replace the line:
    ```
    listen       8080;
    ```
    with:
    ```
    listen       8888;
    ```
    alternatively, you can comment out or delete that entire default `server { ... }` block, because we won't be using it.

3. Create a file at `/usr/local/etc/nginx/servers/manageiq-remote.conf` with the following contents:

    ```
    server {
        listen       3000;
        server_name  localhost 0.0.0.0;

        location /api/ {
            proxy_set_header Host      $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_hide_header Access-Control-Allow-Origin;
            add_header 'Access-Control-Allow-Origin' 'http://0.0.0.0:8080';
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Headers' 'X-CSRF-Token';
            proxy_hide_header Content-Security-Policy;
            proxy_redirect off;
            proxy_pass https://APPLIANCE_ADDRESS/api/;
        }

        location / {
            proxy_set_header Host      $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_redirect off;
            proxy_pass http://localhost:3030/;
        }
    }
    ```

    Replace `APPLIANCE_ADDRESS` with the IP address or domain name of the appliance whose API you want to use.

# Usage

1. Start the ManageIQ Rails server on port `3030`, instead of the default `3000`.
    ```
    cd manageiq
    bin/rails s -p 3030
    ```

2. Start nginx in the background by simply running:
    ```
    nginx
    ```

    To stop nginx later, you can use:

    ```
    nginx -s stop
    ```

    If you make changes to the nginx config files while it is running (e.g. to switch appliances), you can reload them:

    ```
    nginx -s reload
    ```

3. Start webpack-dev-server as you normally would, either with the `manageiq-ui-classic/bin/webpack-dev-server` script, or directly with:
    ```
    cd manageiq/plugins/manageiq-ui-classic
    env NODE_ENV=development WEBPACK_EXCLUDE_NODE_MODULES=1 \
        ./node_modules/.bin/webpack-dev-server --config config/webpack/development.js
    ```

4. Open `http://0.0.0.0:8080/` in your browser.

Your browser will make requests to webpack-dev-server on port 8080, which will proxy to nginx on port 3000. nginx will route most of your traffic to your local rails server on port 3030, but your API requests will be routed to the remote appliance!

To test it out, make a UI change and verify that you can see your local change with remote data.

# Known Issues

* The websocket connection (`/ws/notifications`) will not be forwarded to the appliance. You may see console errors about this connection, and websocket-based features like backend-driven notifications will not work.
