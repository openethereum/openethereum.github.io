---
title: Remote Access to the Parity Wallet
---

Although our main focus is for the Wallet to work locally, there are couple of ways to access your remote Parity node's Wallet.

All described methods will require to manually type Wallet Authorization Token and requires that the time on both machines is in sync. Going to http://time.is/ should render:

![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)


**NOTE: Described options varies in the degree of security, so make sure to understand possible consequences- in case of any hesitance reach us out on [Gitter](https://gitter.im/ethcore/parity).**


## Recommended: SSH Tunneling

1. Make sure the time is exact on the Host (Node) and the Client (Browser).
1. Setup SSH server on the Host
1. Run Parity without any flags (default `interface`/`port`/`cors`/`hosts` settings are sufficient)
1. On the Client connect to your Host with port forwarding and keep the session running

   ```
   $ ssh -L 8545:127.0.0.1:8545 -L 8546:127.0.0.1:8546 -L 8180:127.0.0.1:8180 <user>@<host> -vv
   ```
1. To access Parity Wallet on the Client open a browser and type in authorization token, or open

   ```
   $ open http://127.0.0.1:8180/#/auth/<token>
   ```

1. To generate a new token on the Host:

   ```
   $ parity signer new-token
   ```

## Exposing using Nginx

NOTE: It's recommended to setup authentication and SSL on your nginx server.

1. Make sure the time is exact on the Host (Node) and the Client (Browser).
1. Setup Nginx server on the Host.
1. Create nginx configuration at `/etc/nginx/sites-enabled/parity.ui` (substitute `<external-ip>` with correct values):
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

    server {
      listen <external-ip>:8180;

      # Uncomment for SSL Server
      #listen <external-ip>:8180 ssl;
      #ssl on;
      #ssl_certificate /etc/nginx/parity.ui.crt;
      #ssl_certificate_key /etc/nginx/parity.ui.key;

      location / {
        proxy_pass http://127.0.0.1:8180;
      }
    }
    ```

1. Restart nginx and run Parity without any flags (default `interface`/`port`/`cors`/`hosts` settings are sufficient)
1. On the Client, open your browser and go to:

   ```
   $ open http://<external-ip>:8180/#/auth/<token>
   ```

1. To generate a new token on the Host:

   ```
   $ parity signer new-token
   ```

## Directly exposing to external connections

This method is the least secure, but doesn't require SSH tunnels. It exposes your node RPC to external connections and if you really need to use it we highly advise you to configure very strict firewall rules and use it read-only, otherwise your password are being sent unencrypted over the network. This method is not recommended in general anyway, be warned.

1. Make sure the time is exact on the Host (Node) and the Client (Browser).
1. Figure out interface address you will use to access the Wallet-IP of the server (if you want the Wallet to be available on multiple interfaces see the note below)
1. Run Parity on the Host, with the following flags:

   ```
   $ parity --dapps-interface <IP> --ws-interface <IP> --ui-interface <IP>
   ```

1. On the Client, open your browser and go to:

   ```
   $ open http://<IP>:8180/#/auth/<token>
   ```

1. To generate a new token on the Host:

   ```
   $ parity signer new-token
   ```

NOTE: Parity will accept only connections coming to correct interface (IP), if you want to listen on multiple interfaces (IP=`0.0.0.0`; again - not recommended) you need to run with additional flags like: `--unsafe-expose`
