## Create Your Own Trusted Self-Signed SSL/TLS on Windows

Anyone can make their own certificates without help from a CA. The only difference is that certificates you make yourself won’t be trusted by anyone else. For local development, that’s fine.

The simplest way to generate a private key and self-signed certificate for localhost is with this openssl command from **WSL**:

```bash
openssl req -x509 -nodes -days 3650 -out localhost.crt -keyout localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

You can then configure your local web server with localhost.crt and localhost.key, and install localhost.crt in your list of locally trusted roots.

**NOTE: This certificate is only valid for 10 Years. For new projects make sure to generate new certificates if already expired.**

### Make Your Certificate Trusted on Chrome
- Open your chrome settings
- find the option `Privacy and security`
- In `Privacy and security` find the option `Security`
- scroll down to find the option `Manage certificates`
- after opening the manage certificate option, there should be a top bar containing a option named `Trusted Root Certification Authorities`
- click the option then click `import`
- click next and then browse the `localhost.cert` file prom your project and import
- leaves all the other options as it is and click next and finish
- click the 3 dot form the corner of your browser and find the option `Exit`, exit from clicking here
- restart your local host site and open your browser again

Everything should be okay!

### Make Your Certificate Trusted on windows

- find `Run` from the search box and run `mmc.exe`
- In the MMC-based console that opens, select `File > Add/Remove Snap-in`
- In the `Available snap-ins` list, select `Certificates`, and then click `Add`
- Select `Computer account`, and then click `Next`.
- In the `Select Computer` window that opens, click `Finish` (Local Computer will be selected by default).
- In the tree pane, select `Certificates (Local Computer) > Trusted Root Certification Authorities`, right-click `Certificates`, and then select `All Tasks > Import`.
- Open your `localhost.cert` file and import.
- Close and reopen your web browser if open. Reload the localhost site.

Everything should be okay!