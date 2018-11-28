# Remote debugging ManageIQ with webpack-dev-server proxy settings

This guide provides a method for running a local ManageIQ UI development environment which sends all its API requests to a remote appliance. We can use this to test new UI changes in V2V with real migrations.

This guide assumes you already have a working ManageIQ development environment, where you can run webpack-dev-server proxying to rails.

# Setup

1. In `manageiq-ui-classic`, make a copy of the file `config/webpack/development.js` at `config/webpack/development-remote.js`.
2. Edit the file with the following changes (replacing APPLIANCE_ADDRESS with the IP address or domain name of the appliance whose API you want to use).

    ```diff
    --- development.js
    +++ development-remote.js
    @@ -19,7 +19,7 @@

       devServer: {
         clientLogLevel: 'none',
    -    https: settings.dev_server && settings.dev_server.https,
    +    https: true,
         host: settings.dev_server && settings.dev_server.host,
         port: settings.dev_server && settings.dev_server.port,
         contentBase: output.path,
    @@ -31,6 +31,10 @@
           ignored: /node_modules/
         },
         proxy: {
    +      '/api': {
    +        target: 'https://APPLIANCE_ADDRESS',
    +        secure: false,
    +      },
           '/': {
             target: `http://${settings.dev_server.host}:${env.PORT || '3000'}`,
             secure: false,
    ```

# Usage

1. Start your ManageIQ Rails server on port 3000 as you normally would for local development.

2. In `manageiq-ui-classic`, run webpack-dev-server with your new config file, passing the additional environment flag `NODE_TLS_REJECT_UNAUTHORIZED=0`. For example:

    ```
    env NODE_ENV=development NODE_TLS_REJECT_UNAUTHORIZED=0 WEBPACK_EXCLUDE_NODE_MODULES=1 \
        ./node_modules/.bin/webpack-dev-server --config config/webpack/development-remote.js
    ```
    
 3. Open a browser at https://0.0.0.0:8080/ and try logging in. You should be seeing UI assets from your local environment, and API resources from the appliance!
 
