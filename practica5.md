# Transmisiones Web Seguras (TLS)

## INTRODUCCION 

Internet es un canal no seguro, cualquier persona podría obtener nuestros datos, para evitar que esto suceda, ciertas comunicaciones con información confidencial deben ser cifradas. Para ello, vamos a generar un certificado que esté autofirmado para un servidor web Apache.

### INSTALACION


### Paso 1: Instalar Easy-RSA

```bash
$ sudo sudo apt install easy-rsa
```

### Paso 2: Preparar un directorio para la infraestructura de clave pública

```bash
$ mkdir ~/easy-rsa
```
Cree los enlaces simbólicos con el comando ln
```bash
$ ln -s /usr/share/easy-rsa/* ~/easy-rsa/
```
Para restringir el acceso a su nuevo directorio de la PKI, asegúrese de que solo el propietario pueda acceder a él usando el comando chmod
```bash
$ chmod 700 /home/user/easy-rsa
```
Por último, inicie la PKI dentro del directorio easy-rsa:
```bash
$ cd ~/easy-rsa
$ ./easyrsa init-pki
```

### Paso 3 - Crear una entidad de certificación

```bash
$ cd ~/easy-rsa
$ nano vars
```
Una vez que se abra el archivo, pegue las siguientes líneas y sustituya cada valor resaltado por la información de su propia organización. 
```bash
set_var EASYRSA_REQ_COUNTRY    "ES"
set_var EASYRSA_REQ_PROVINCE   "Valencia"
set_var EASYRSA_REQ_CITY       "Valencia"
set_var EASYRSA_REQ_ORG        "ASIR"
set_var EASYRSA_REQ_EMAIL      "sancarbur@alu.edu.gva.es"
set_var EASYRSA_REQ_OU         "ASIR"
set_var EASYRSA_ALGO           "ec"
set_var EASYRSA_DIGEST         "sha512"
```
Para crear el certificado root público y el par de claves privadas para su entidad de certificación, vuelva a ejecutar el comando ./easy-rsa, aunque esta vez con la opción build-ca:

```bash
$ ./easyrsa build-ca
```

Nos quedaran dos archivos importantes, `~/easy-rsa/pki/ca.crt` y `~/easy-rsa/pki/private/ca.key`.

### Paso 4: Distribuir el certificado público de su entidad de certificación

Hacemos un cat en el archivo ~/easy-rsa/pki/ca.crt y el resultado será similar al siguiente:

```bash
-----BEGIN CERTIFICATE-----

MIIDNjCCAh6gAwIBAgIUA1HZbG9qmZAs9cVXVgOmnSDwMA4wDQYJKoZIhvcNAQEL

BQAwDzENMAsGA1UEAwwEQVNJUjAeFw0yMzExMTMxODM3MDBaFw0zMzExMTAxODM3

MDBaMA8xDTALBgNVBAMMBEFTSVIwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEK

AoIBAQCza4vyjEagcDQARg5bI009kfotHzabeFXqpmP7vqOII8l2Wy1FIlpzihsw

oP0lJEx2xMIjf5C85tBFmHpZ0Mkf9TKzqqM9Che/rrfIlooS7TraL6BE8QHqZQmv

tRPALXLtkioUgQVbDKQJ6CAHqfkY0h2NJ+tmihorJNMPIePNJmNATsVrENYTig8a

fnkSt46ZlKQt3vKDsAhA1U/iLt2epcYRLXEMEmFjEKpAawzwHZU93Ch9HXvGDI2e

ZgkSuow09vE79nhEysN8ALWDR+ylnAPquazPBomdI8trQLV30S3/Z8BZORKzih/K

KIvDWEY5HaPTWtpCBZCD8PGCdDW/AgMBAAGjgYkwgYYwHQYDVR0OBBYEFHVb2DMG

Qn9pBXigSt1q6eOiXf5AMEoGA1UdIwRDMEGAFHVb2DMGQn9pBXigSt1q6eOiXf5A

oROkETAPMQ0wCwYDVQQDDARBU0lSghQDUdlsb2qZkCz1xVdWA6adIPAwDjAMBgNV

HRMEBTADAQH/MAsGA1UdDwQEAwIBBjANBgkqhkiG9w0BAQsFAAOCAQEAS9SM6996

sL95Gyo7RYHBbgyhhrufKMsqHmIleYxE4bWvBxweri8iS93elzXz4pBOBhB2wcth

FVKFAkiRB6Iz5jiztrQ8wl+HtB7EYA2QlXr/4SjGc6tKeVi3clX3xPsNMqdaoWXk

dH7F6SLTSDSMJVBsP+qWYWugFcecO2i5COYIdGDBU/j4H4hqG35oiQidntdPSiCH

S2PZRfdfzou+HRaEasp8eCUr5rZnKQyOtVWxCQEVuBfcBSQ7ZpfjrL/cnyAcEow8

mu/nCU1kv154IJ4BAyUzxh/jepz7xG/sKnxTeHpPbO9xy01CmQFZq0kycZLJlKMs

JAbaMtIGD6kCJg==

-----END CERTIFICATE-----
```
Creamos un archivo llamado ca.crt en el directorio /tmp y copiamos el resultado del cat.

Importamos el certificado
```bash
$ sudo cp /tmp/ca.crt /usr/local/share/ca-certificates/
$ sudo update-ca-certificates
```

### Crear CSR 

Primero instalamos openssl 

El primer paso para crear una CSR es generar una clave privada. Para crear una clave privada usando openssl, cree un directorio practice-csr y luego genere una clave dentro de este. En vez de crear un certificado para identificar usuarios u otras CA, crearemos esta solicitud para un servidor ficticio llamado santi-server.

```bash
$ mkdir ~/practice-csr
$ cd ~/practice-csr
$ openssl genrsa -out santi-server.key
```

Ahora que tiene una clave privada, puede crear una CSR correspondiente y volver a usar la utilidad openssl.
```bash
$ openssl req -new -key santi-server.key -out santi-server.req
```
Ahora copiamos al directorio /tmp

```bash
$ cp santi-server.req /tmp/santi-server.req
```

### Firmar CSR

El primer paso para firmar la CSR ficticia es importar la solicitud de certificado usando la secuencia de comandos easy-rsa:

```bash
$ cd ~/easy-rsa
$ ./easyrsa import-req /tmp/santi-server.req santi-server
```
Ahora, puede firmar la solicitud ejecutando la secuencia de comandos easyrsa con la opción sign-req:
```bash
$ ./easyrsa sign-req server santi-server
```

### Configurar el sitio de apache
Editamos la siguiente linea del archivo /etc/apache2/sites-available/default-ssl.conf.
```bash
SSLCertificateFile      /etc/ssl/certs/santi-server.crt     
SSLCertificateKeyFile /etc/ssl/private/santi-server.key
```
Activamos el sitio:
```bash
$ sudo a2ensite default-ssl
$ sudo systemctl restart apache2
```

Editamos el fichero `etc/hosts` para añadir nuestra ip y nuestros nombre de certificado:

```bash
10.0.2.15       santi-server
```

### COMPROBACION
Comprobamos en el navegador que nuestra entidad es de confianza:

Por último entramos en el navegador un ponemos https://santi-server y podremos observar que ya lo reconoce como sitio seguro.
