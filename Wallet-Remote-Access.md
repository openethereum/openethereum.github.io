---
title: Remote Access to the Parity Wallet
---

Although our main focus is for the Wallet to work locally, there are a couple of ways to access your remote Parity node's Wallet. SSH tunneling is the one we recommend.

All described methods will require to manually type Wallet Authorization Token and requires that the time on both machines is in sync. Going to http://time.is/ should render:

![your time is exact](images/time_is.jpg)


**NOTE: Described options vary in the degree of security, so make sure to understand possible consequences- in case of any hesitance reach us out on [Gitter](https://gitter.im/ethcore/parity).**


## SSH Tunneling - Recommended

1. Setup SSH server on the Host
1. On the Client connect to your Host with port forwarding and keep the session running

   ```
   $ ssh -L 8545:127.0.0.1:8545 -L 8546:127.0.0.1:8546 <user>@<host> -vv
   ```
1. Run Parity without any flags (default `interface`/`port`/`cors`/`hosts` settings are sufficient)
1. To access Parity Wallet on the Client open Parity UI
1. If Parity UI requests a new token, run on the Host:

   ```
   $ parity signer new-token
   ```
For older Parity versions (<=v1.9). Forwarding the port 8180 using `-L 8180:127.0.0.1:8180` is needed to connect via the browser.

## Exposing using Nginx

NOTE: It's recommended to setup authentication and SSL on your Nginx server.

1. Setup Nginx server on the Host.
1. Create Nginx configuration at `/etc/nginx/sites-enabled/parity.ui` (substitute `<external-ip>` with correct values):
    ```
    server {
      listen <external-ip>:8545;

       # Uncomment for SSL Server
       #listen <external-ip>:8545 ssl;
       #ssl on;
       #ssl_certificate /etc/nginx/parity.ui.crt;
       #ssl_certificate_key /etc/nginx/parity.ui.key;

       location / {
         proxy_pass http://127.0.0.1:8545;
       }
     }

    server {
      listen <external-ip>:8546;

      # Uncomment for SSL Server
      #listen <external-ip>:8546 ssl;
      #ssl on;
      #ssl_certificate /etc/nginx/parity.ui.crt;
      #ssl_certificate_key /etc/nginx/parity.ui.key;

      location / {
        proxy_pass http://127.0.0.1:8546;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_read_timeout 86400;
      }
    }
    ```

1. Restart nginx and run Parity without any flags (default `interface`/`port`/`cors`/`hosts` settings are sufficient)
1. On the Client, open Parity UI.
1. If Parity UI requests a new token, run on the Host:

   ```
   $ parity signer new-token
   ```
