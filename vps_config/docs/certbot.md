## Managing Certbot

Certbot is a free, open source software tool for automatically using Let's Encrypt certificates on manually-administrated websites to enable HTTPS.

Install Certbot by running:

```sh
apt install certbot
```

For additional info run: `man certbot`

### Accruing New Certificates

_Make sure to stop any http service on port 80 before running the following command_

```sh
certbot certonly --standalone -d domain.com -d www.domain.com
```

You will find the certificates at `/etc/letsencrypt/live` directory. For renewing the certificates run `# certbot renew`

**IMPORTANT: After generating a new certificate or renewing, make sure you copy `chain.pem` file content and paste at the bottom of the `cert.pem` file. Some TLS library checks cert chain.**

```sh
cat /etc/letsencrypt/live/<domain>/chain.pem
nano /etc/letsencrypt/live/<domain>/cert.pem
```

**NOTE - Prior to template_actix4: Rustls library expect `privkey.pem` file to be start with `-----BEGIN RSA PRIVATE KEY-----` and end with `-----END RSA PRIVATE KEY-----`. Make sure to check on the file content in case of error.**
