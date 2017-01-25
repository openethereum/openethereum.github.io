# Remote Access to the Parity UI

Although our main focus is for the UI to work locally, there are couple of ways to access your remote Parity node's UI.

All described methods will require to manually type UI Authorization Token and requires that the time on both machines is in sync. Going to http://time.is/ should render:

![image](https://cloud.githubusercontent.com/assets/138296/19265409/5e4a89ce-8fa5-11e6-8ec6-6c72c138ee48.png)
   

**NOTE: Described options varies in the degree of security, so make sure to understand possible consequences- in case of any hesitance reach us out on [Gitter](https://gitter.im/ethcore/parity).**


## Recommended: SSH Tunneling

1. Make sure the time is exact on Host (Node) and Client (Browser).
1. Setup SSH server on the Host
1. Run Parity without any flags (default `interface`/`port`/`cors`/`hosts` settings are sufficient)
1. Generate a new token on the Host:

   ```
   $ parity signer new-token
   ```
1. Connect to your Host with port forwarding and keep the session running

   ```
   $ ssh -L 8080:127.0.0.1:8080 -L 8180:127.0.0.1:8180 <user>@<host> -vv
   ```
1. To access Parity UI on the Client open a browser and type in authorization token, or open

   ```
   $ open http://127.0.0.1:8180/#/auth/<token>
   ```