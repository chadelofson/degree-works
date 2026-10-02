# Create a Keystore and generate Certificate Request

To have certificates for the site you need to create a keystore.  In this article we will go through creating a keystore and generating a request.

## Creating the Keystore
1. Once you are logged into the server create a new login session with dwadmin:

```bash
sudo su - dwadmin
```

2. In the home directory for dwadmin go to the keystores location (i.e. keystores)
```bash
cd keystores
```

3. Create the Keystore and Certificate request:
```bash
keytool -genkeypair \
 -alias <alias> \
 -keyalg RSA \
 -keysize 3072 \
 -sigalg SHA256withRSA \
 -validity 365 \
 -storetype JKS \
 -keystore /path/to/keystore/<keystore_name>.jks
 -ext "SAN=dns:server.example.edu"
```

Note: Change <alias>, <keystore_name> and server.example.edu to the values you need.

4. Generate the CSR
bash```
keytool -certreq \
  -alias <alias> \
  -sigalg SHA256withRSA \
  -keystore /path/to/keystore/<keystore>.jks \
  -file /path/to/certrequest.csr \
  -ext "SAN=dns:server.example.edu"
```

5. Send the request to the security team

6. Once you have the certificate, upload it to the server.

7. Change into the extracted certificate folder and run the following command to import into the certificate root into the keystore:
```bash
keytool -importcert \
  -alias digicert-root
  -file /path/to/certificate/root/TrustedRoot.crt
  -keystore /path/to/keystore/<keystore>.jks
```

8. Import the imtermediate CA certificate
```bash
keytool -importcert \
  -alias digicert-intermediate \
  -file /path/to/certificate/DigiCertCA.crt \
  -keystore /path/to/keystore/<keystore>.jks
```

9. Import the certificate
```bash
keytool -importcert \
  -alias <alias> \
  -file /path/to/cert.crt \
  -keystore /path/to/keystore.jks
```

10. Finally you can optionally confirm it was imported:
```bash
keytool -list -v \
  -alias <alias> \
  -keystore /path/to/keystore.jks
```