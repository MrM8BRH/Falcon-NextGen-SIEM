### Overview

LogScale uses a **keystore** (holds your private key and certificate) and a **truststore** (holds certificates of CAs/nodes to trust). Both can be in PKCS12 (recommended) or JKS format. The key environment variables are `TLS_KEYSTORE_LOCATION`, `TLS_KEYSTORE_PASSWORD`, `TLS_TRUSTSTORE_LOCATION`, and `TLS_TRUSTSTORE_PASSWORD`. [LogScale](https://library.humio.com/falcon-logscale-self-hosted/configuration-tls.html)

---
I recommend using **one password for all three**:
```
TLS_KEYSTORE_PASSWORD=StrongPasswordHere
TLS_KEY_PASSWORD=StrongPasswordHere
TLS_TRUSTSTORE_PASSWORD=StrongPasswordHere
```

Create `certs` directory
```
mkdir -p /opt/humio/humio/certs
```

### Option 1 — Self-Signed Certificate

#### Step 1: Generate a Self-Signed Keystore

Use Java's `keytool` utility (included with the JDK):

```bash
keytool -genkeypair \
  -alias logscale \
  -keyalg RSA \
  -keysize 2048 \
  -validity 3650 \
  -storetype PKCS12 \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword \
  -keypass YourKeyPassword \
  -dname "CN=logscale.yourdomain.com, OU=IT, O=YourOrg, L=City, S=State, C=US" \
  -ext san=dns:logscale.yourdomain.com,ip:192.168.1.100
```

> Replace the CN, SAN, and paths with your actual FQDN and IP.

#### Step 2: Create a Truststore with the Self-Signed Certificate

Export the certificate and import it into a truststore so LogScale trusts itself (needed for cluster/internal traffic):

```bash
# Export the certificate
keytool -exportcert \
  -alias logscale \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword \
  -file /opt/humio/humio/certs/logscale.cer

# Import into a truststore
keytool -importcert \
  -alias logscale \
  -file /opt/humio/humio/certs/logscale.cer \
  -keystore /opt/humio/humio/certs/truststore.p12 \
  -storetype PKCS12 \
  -storepass YourTruststorePassword \
  -noprompt
```

#### Step 3: Configure LogScale

Add the following to your LogScale configuration (e.g., `/etc/humio/server.conf` or the relevant environment variable file): [LogScale](https://library.humio.com/falcon-logscale-self-hosted-1.88/configuration-tls.html)

```properties
# Keystore (private key + certificate)
TLS_KEYSTORE_LOCATION=/opt/humio/humio/certs/keystore.p12
TLS_KEYSTORE_PASSWORD=YourKeystorePassword
TLS_KEYSTORE_TYPE=PKCS12
TLS_KEY_PASSWORD=YourKeyPassword

# Truststore (trusted certificates)
#   TLS_TRUSTSTORE_LOCATION=/opt/humio/humio/certs/truststore.p12
#   TLS_TRUSTSTORE_PASSWORD=YourTruststorePassword
#   TLS_TRUSTSTORE_TYPE=PKCS12

# Enable TLS serving
TLS_SERVER=true

# Recommended protocols
TLS_PROTOCOLS=TLSv1.2,TLSv1.3
```

#### Step 4: Restart LogScale

```bash
systemctl restart humio
```

Clients connecting will see a browser warning since the certificate is self-signed. To suppress this, import `logscale.cer` into the client's trusted certificate store.

---

### Option 2 — Certificate from Active Directory (Windows CA / ADCS)

This is the recommended approach for enterprise environments, as all domain-joined machines will automatically trust the certificate.

#### Step 1: Generate a CSR (Certificate Signing Request)

```bash
keytool -genkeypair \
  -alias logscale \
  -keyalg RSA \
  -keysize 2048 \
  -storetype PKCS12 \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword \
  -keypass YourKeyPassword \
  -dname "CN=logscale.yourdomain.com, OU=IT, O=YourOrg, L=City, S=State, C=US"

keytool -certreq \
  -alias logscale \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword \
  -file /opt/humio/humio/certs/logscale.csr
```

#### Step 2: Submit the CSR to Your AD Certificate Authority

1. Open your AD CS web interface: `http://<YourCA>/certsrv`
2. Click **Request a Certificate → Advanced Certificate Request**
3. Paste the contents of `logscale.csr`
4. Select a certificate template (use **Web Server** or a custom one)
5. Download the issued certificate in **Base-64 encoded** format (e.g., `logscale.cer`)
6. Also download the **CA certificate chain** from the CA home page

#### Step 3: Build the Full Certificate Chain

Download the root CA (and intermediate CA if applicable) from `http://<YourCA>/certsrv` → "Download a CA certificate, certificate chain, or CRL".

#### Step 4: Import the Chain and Signed Certificate into the Keystore

```bash
# Import the Root CA into the keystore first
keytool -importcert \
  -alias rootca \
  -file /opt/humio/humio/certs/rootca.cer \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword \
  -trustcacerts -noprompt

# Import the signed certificate (must use the same alias as the keypair)
keytool -importcert \
  -alias logscale \
  -file /opt/humio/humio/certs/logscale.cer \
  -keystore /opt/humio/humio/certs/keystore.p12 \
  -storepass YourKeystorePassword
```

#### Step 5: Create a Truststore with the CA Certificate

```bash
keytool -importcert \
  -alias rootca \
  -file /opt/humio/humio/certs/rootca.cer \
  -keystore /opt/humio/humio/certs/truststore.p12 \
  -storetype PKCS12 \
  -storepass YourTruststorePassword \
  -noprompt
```

#### Step 6: Configure LogScale

Same configuration as Option 1 — just point to the same keystore/truststore paths:

```properties
TLS_KEYSTORE_LOCATION=/opt/humio/humio/certs/keystore.p12
TLS_KEYSTORE_PASSWORD=YourKeystorePassword
TLS_KEYSTORE_TYPE=PKCS12
TLS_KEY_PASSWORD=YourKeyPassword

#   TLS_TRUSTSTORE_LOCATION=/opt/humio/humio/certs/truststore.p12
#   TLS_TRUSTSTORE_PASSWORD=YourTruststorePassword
#   TLS_TRUSTSTORE_TYPE=PKCS12

TLS_SERVER=true
TLS_PROTOCOLS=TLSv1.2,TLSv1.3
```

#### Step 7: Restart LogScale

```bash
systemctl restart humio
```

Domain-joined clients and browsers that trust the AD CA will now connect without any certificate warnings.

---

### Key Differences Summary

| Item | Self-Signed | AD Certificate |
|---|---|---|
| **Trust** | Manual client trust needed | Auto-trusted on domain-joined clients |
| **Use case** | Testing / internal lab | Production / enterprise |
| **CSR to CA** | Not needed | Required |
| **Extended Key Usage** | Server Authentication should be present | Must include Server Authentication |
| **Renewal** | Manual | Via CA; can be automated |

### Additional Notes

- LogScale automatically reloads certificates when they change on disk, so you can rotate certificates without a full restart (HTTPS/keystore only — Kafka and ZooKeeper certificates still require a restart). [LogScale](https://library.humio.com/falcon-logscale-self-hosted/configuration-tls.html)
- LogScale supports SNI, meaning the alias of the key in the keystore must match the hostname in the SNI header for multi-certificate setups. [LogScale](https://library.humio.com/falcon-logscale-self-hosted/configuration-tls.html)
- If you're using LogScale in a **cluster**, each node needs its own keystore, and all nodes' certificates (or the shared CA) must be in the truststore.
