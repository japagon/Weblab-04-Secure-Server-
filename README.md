Weblab 04: Secure Web Server (HTTPS)
This project demonstrates how to configure a secure Apache web server using SSL/TLS with a self-signed certificate on a Debian Bullseye environment managed by Vagrant.



Objectives
Set up an Apache Virtual Host to listen on port 443.


Generate a self-signed SSL certificate using OpenSSL.



Verify secure access via HTTPS for the domain discovery.sistema.sol.


Step-by-Step Implementation
1. Prerequisites
This lab starts from the network and DNS configuration of the previous lab. The DNS service is essential to resolve discovery.sistema.sol before using the web server.



2. Enabling SSL Support
First, we must ensure that Apache is ready for encrypted traffic:


Install OpenSSL: Used to generate the security keys.


Enable SSL Module: Run a2enmod ssl to allow Apache to handle HTTPS connections.

3. Generating the Certificate
We created a self-signed certificate for our domain. Since it is self-signed, the browser will show a warning, but the traffic will be encrypted.




Key generation: A 2048-bit RSA key was created.


Certificate: A X.509 certificate valid for 365 days was issued for CN=discovery.sistema.sol.

4. Virtual Host Configuration
A new configuration file (discovery-ssl.conf) was created for port 443. It includes:


SSLEngine on: Activates SSL for the site.

SSLCertificateFile: Points to the generated certificate.

SSLCertificateKeyFile: Points to the private key.

5. Final Deployment
To apply the changes, the secure site was enabled using a2ensite discovery-ssl.conf and the Apache service was restarted.

How to Verify
Run vagrant up to build the infrastructure.

Open your browser and go to https://discovery.sistema.sol.



Accept the certificate: Click on "Advanced" and "Proceed" to bypass the self-signed warning.



Confirm the page loads correctly with the HTTPS protocol.


Technical Check (inside the VM)
You can verify the status using these commands:

sudo apache2ctl -M | grep ssl: Confirms the SSL module is loaded.


sudo ss -lntp | grep :443: Confirms Apache is listening on the secure port.
