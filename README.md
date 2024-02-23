# Apache Reverse Proxy with SSL
# Reverse Proxy Setup Documentation

This documentation provides step-by-step instructions for setting up a reverse proxy using Apache on a server. The reverse proxy will listen for requests to a specific domain and forward them to another URL.

## Prerequisites

- SSH access to the reverse proxy server.
- Root or sudo privileges on the server.

## Steps

1. **SSH into the Reverse Proxy Server**: Connect to the reverse proxy server using SSH.

2. **Navigate to Apache Configuration Directory**: Once connected, navigate to the Apache `sites-enabled` directory:
    ```
    cd /etc/apache2/sites-enabled
    ```

3. **Copy Existing Configuration**: Duplicate an existing configuration file (without SSL) for the new domain:
    ```
    cp aetna.virtualmedicare.com.conf example.virtualmedicare.com.conf
    ```

4. **Edit the Configuration File**: Open the newly copied configuration file (`example.virtualmedicare.com.conf`) in a text editor and update the parameters.

5. **Save the File and Issue SSL Certificate**: Save the changes to the configuration file. Issue an SSL certificate for the new domain using Certbot:
    ```
    certbot -d example.virtualmedicare.com
    ```

6. **Configure SSL in SSL Configuration File**: Open the SSL configuration file (`example.virtualmediacare.com-le-ssl.conf`) and ensure the SSL parameters exist.

7. **Final Configuration**: Update the configuration file (`example.virtualmedicare.com.conf`) to include SSL parameters.

8. **Reload Apache**: After updating the configuration, reload Apache to apply the changes:
    ```
    systemctl reload apache2
    ```

9. **Access the New Domain**: Access the configured domain (`example.virtualmedicare.com`) in a web browser to verify the reverse proxy setup.

## Contributing

Contributions to improve this documentation are welcome. Please fork the repository, make your changes, and submit a pull request.

## License

This documentation is licensed under the [MIT License](LICENSE).
