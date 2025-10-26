# SSL/TLS Certificates

## cacert.pem

If you run LibreELEC behind an inline proxy that intercepts TLS communications or access content from an TLS encrypted source that requires a self-signed certificate to be trusted, you need to add the private CA certificate to the OS trust chain.

This is done by creating `/storage/.config/cacert.pem` with the private CA certificate details and rebooting. On restart the `ssl-config` systemd service will append the private certificate to the OS default certificate file to create a combined file `/run/libreelec/cacert.pem` which is the symlink target for `/etc/ssl/cacert.pem` referenced by Kodi, curl, etc.
