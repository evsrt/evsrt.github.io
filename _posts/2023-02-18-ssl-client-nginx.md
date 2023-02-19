---
title: SSL client-based authentication on Nginx
date: 2023-02-18 11:12:00 +0300
categories: [Web, nginx]
tags: [nginx, ssl]     # TAG names should always be lowercase
---
To enable SSL client-based authentication on Nginx, you'll need to perform the following steps:

Generate SSL certificate and key pair: The first step is to generate a client certificate and key pair that will be used for authentication. You can use a tool like OpenSSL to generate the certificate and key pair.
```bash
openssl req -x509 -newkey rsa:4096 -keyout client.key -out client.crt -days 365
```
Configure Nginx to require client certificates: Once you have the client certificate and key pair, you need to configure Nginx to require client certificates for certain requests. To do this, add the following lines to your Nginx configuration file:
```bash
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /path/to/server.crt;
    ssl_certificate_key /path/to/server.key;

    ssl_client_certificate /path/to/client.crt;
    ssl_verify_client on;

    location /secure {
        # This location requires client certificates
    }
}
```
In the above configuration, the ssl_client_certificate directive specifies the path to the client certificate file, and the ssl_verify_client on directive enables client certificate verification.

Test client authentication: To test if the client authentication is working, you can use a web browser or a tool like OpenSSL to connect to the Nginx server and provide the client certificate. For example, using OpenSSL you can run the following command:
```bash
openssl s_client -connect example.com:443 -cert client.crt -key client.key
```
If everything is configured correctly, you should see a message indicating that the client certificate was accepted and you should be able to access the protected resources on the Nginx server.

That's it! With these steps, you should now have SSL client-based authentication enabled on your Nginx server.

[Module ngx_http_ssl_module](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_client_certificate)