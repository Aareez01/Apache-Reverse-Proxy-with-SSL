## Apache Reverse Proxy with SSL using Certbot

This guide will walk you through setting up an Apache reverse proxy with SSL on Ubuntu using **Certbot** for free Let's Encrypt SSL certificates.

### Prerequisites

- A domain name (e.g., `example.com`) pointed to your server's public IP address.
- A server running Apache.
- A valid email address for SSL certificate registration.
- `sudo` privileges on your server.

---

### Step 1: Install Apache and Enable Required Modules

Ensure Apache is installed and the necessary modules for proxying and SSL are enabled.

1. **Install Apache:**

    ```bash
    sudo apt update
    sudo apt install apache2
    ```

2. **Enable Required Apache Modules:**

    ```bash
    sudo a2enmod proxy
    sudo a2enmod proxy_http
    sudo a2enmod ssl
    sudo a2enmod headers
    ```

3. **Restart Apache to apply changes:**

    ```bash
    sudo systemctl restart apache2
    ```

---

### Step 2: Install Certbot for SSL Certificates

Certbot automates the process of obtaining and renewing SSL certificates from Let's Encrypt.

1. **Install Certbot:**

    ```bash
    sudo apt install certbot python3-certbot-apache
    ```

---

### Step 3: Configure Apache as a Reverse Proxy

1. **Create a New Virtual Host Configuration for Reverse Proxy:**

    Create a new configuration file for your site (e.g., `example.com`):

    ```bash
    sudo nano /etc/apache2/sites-available/example.com.conf
    ```

2. **Add the Reverse Proxy Configuration:**

    Replace `backend-server` with the IP address or domain of the backend server you want to proxy.

    ```apache
    <VirtualHost *:80>
        ServerName example.com
        ServerAlias www.example.com

        # Redirect HTTP to HTTPS
        RewriteEngine on
        RewriteCond %{SERVER_NAME} =example.com [OR]
        RewriteCond %{SERVER_NAME} =www.example.com
        RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
    </VirtualHost>

    <VirtualHost *:443>
        ServerName example.com
        ServerAlias www.example.com

        # Enable SSL
        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem

        # Reverse Proxy Setup
        ProxyPreserveHost On
        ProxyPass / http://backend-server/
        ProxyPassReverse / http://backend-server/

        # Optional: Ensure headers are set correctly
        RequestHeader set X-Forwarded-Proto "https"
        RequestHeader set X-Forwarded-Ssl on
    </VirtualHost>
    ```

3. **Enable the Virtual Host:**

    ```bash
    sudo a2ensite example.com.conf
    sudo systemctl reload apache2
    ```

---

### Step 4: Obtain SSL Certificates using Certbot

Certbot will automatically modify your Apache configuration to include the SSL certificates.

1. **Run Certbot for Apache:**

    ```bash
    sudo certbot --apache -d example.com -d www.example.com
    ```

2. **Certbot will:**
   - Automatically obtain and configure SSL certificates for your domain.
   - Update the Apache configuration to use the newly obtained SSL certificates.

3. **Follow the prompts:** 
   - Provide your email.
   - Agree to the Let's Encrypt terms.
   - Certbot will handle the rest.

---

### Step 5: Verify the SSL Setup

1. **Restart Apache:**

    ```bash
    sudo systemctl restart apache2
    ```

2. **Test the Configuration:**

   Visit `https://example.com` in your web browser. The site should load over HTTPS, and the SSL certificate should be valid (check the padlock icon in the browser's address bar).

---

### Step 6: Enable Auto-Renewal of SSL Certificates

Certbot automatically installs a cron job to renew the certificates before they expire. However, you can manually test the renewal process with:

```bash
sudo certbot renew --dry-run
```

---

### Step 7: (Optional) Adjust Firewall Settings

If you have a firewall running (e.g., `ufw`), make sure to allow HTTPS traffic:

```bash
sudo ufw allow 'Apache Full'
```

---

### Troubleshooting

- **Error: Failed to Obtain SSL Certificate:** 
  Ensure your domain's DNS settings point to your server and port 80 is accessible.
  
- **Backend Server Issues:** 
  If the reverse proxy doesn't work, ensure your backend server is correctly running and accessible from the Apache server.

---

You have successfully set up an Apache reverse proxy with SSL using Certbot. Your traffic is now securely routed through Apache, using an SSL certificate provided by Let's Encrypt.
