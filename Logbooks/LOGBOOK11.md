# Public-Key Infrastructure

## Setup

Numa fase inicial executamos os containers fornecidos no lab:

```bash
docker-compose build # Build the container images
docker-compose up # Start the containers
sudo nano etc/hosts # Colocar '10.9.0.80 www.bank32.com & www.torres2024.com'
```
Podemos pingar 10.9.0.80 www.bank32.com & www.torres2024.com para verificar se a implementação foi correta.

## Task 1 -  Becoming a Certificate Authority (CA)

Copiamos o ficheiro de certificado default (presente em `/usr/lib/ssl/openssl.cnf`) para a pasta de trabalho local. Depois descomentamos a linha "unique_subject", e criamos um ficheiro index.txt vazio e um ficheiro serial com o numero 1000: 

```bash
mkdir -p demoCA/newcerts
touch demoCA/index.txt
echo 1000 > demoCA/serial
```

Depois fizemos setup da CA:

```bash
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 \
-keyout ca.key -out ca.crt \
-subj "/CN=www.modelCA.com/O=Model CA LTD./C=US" \
-passout pass:dees
```



Para ver o conteúdo dos ficheiros gerados, decodificamos o certificado X509 e a chave RSA:

```bash
openssl x509 -in ca.crt -text -noout
openssl rsa -in ca.key -text -noout
```

Podemos ver que é um certificado CA uma vez que existe na secção *basic constraints* um atributo *certificate authority* verdadeiro:

![1](/docs/images/logbook11-1.png)

Este certificado é *self-signed* pois o campo *issuer* e o campo *subject* é o mesmo:

![2](/docs/images/logbook11-2.png)

O conteúdo do ficheiro gerado referente à criptografia é este: 
```bash
RSA Private-Key: (4096 bit, 2 primes)
modulus:
    00:cd:3c:3f:24:24:18:a1:4a:64:65:95:88:67:ed:
    e5:8d:3d:1c:be:b2:bb:59:2f:4c:42:3a:e2:85:e3:
    45:15:fb:df:74:7d:b7:e1:f5:64:b8:b5:9c:e4:a5:
    05:00:9b:ed:74:9e:35:90:ab:5d:d0:e0:41:28:f4:
    4b:b3:7e:bd:1d:26:16:0a:8c:c9:2e:8a:a5:01:01:
    14:f1:2f:b7:30:2e:1a:69:ba:de:64:69:36:fa:82:
    c6:b2:35:bf:79:d6:db:f6:e6:3e:67:26:d3:71:91:
    bc:b5:f3:ad:f8:d6:ac:9c:35:17:b9:64:e2:b5:29:
    53:41:ee:48:39:94:14:fb:7f:1b:9e:56:05:5f:c6:
    a2:c9:b6:77:59:0b:39:68:eb:f5:a3:fb:7f:db:85:
    95:18:e3:14:f8:8b:93:11:82:3a:01:71:34:ec:2a:
    c9:c1:4d:3c:34:1a:fc:0c:30:35:6f:b7:cc:33:b8:
    88:6b:fa:5f:5a:8d:15:fb:8b:e3:df:6a:51:a0:6a:
    a2:bd:ab:37:eb:1e:7e:4c:27:17:85:ef:bf:71:95:
    6d:15:82:d2:df:40:24:0d:67:16:a0:7e:b8:d0:31:
    7f:75:3f:cd:ac:dd:c2:8b:d4:19:27:3f:20:6d:aa:
    ae:32:0a:6b:df:5b:e2:2d:1a:51:73:67:fa:fa:80:
    92:dc:3d:20:05:ef:63:76:53:9c:b7:b0:f7:c7:37:
    4c:96:f8:db:3b:56:f0:dd:27:f5:67:25:e0:30:40:
    a2:aa:1b:26:88:ee:07:29:34:92:74:87:e8:c7:c6:
    86:59:83:44:fe:3c:01:ec:42:53:46:82:f5:72:14:
    fb:2a:de:bd:cb:e0:2c:d1:68:8f:a8:e7:f1:bc:9a:
    aa:ab:e0:2c:e2:2c:70:9a:b1:5b:38:95:05:c6:b7:
    0a:2b:32:67:5a:c9:ed:04:2d:65:34:d2:f1:b5:5a:
    cc:61:2d:0f:bc:7b:f6:d0:4e:c6:6b:d6:b0:fb:03:
    4d:3c:e2:28:37:e6:8e:8e:30:8c:26:74:84:09:34:
    e7:00:42:61:54:9f:d8:87:15:14:87:a7:e4:18:a7:
    e8:18:13:6e:5b:bb:77:40:05:60:44:1a:58:ea:93:
    92:bc:7a:47:86:da:cf:22:79:48:8b:a5:42:bf:2f:
    ad:ea:92:8c:06:5c:31:9f:cb:80:ae:72:25:e1:df:
    42:8e:ff:ee:79:cd:d5:ea:8b:47:2e:28:b2:d0:81:
    fb:81:c4:e9:9b:fd:30:a1:4f:54:3c:31:01:a4:04:
    f8:67:d0:d6:2a:ca:66:80:d0:17:e1:41:61:a2:12:
    86:0b:ae:87:a1:98:40:02:15:6a:6c:8d:b1:f9:44:
    14:24:b9
publicExponent: 65537 (0x10001)
privateExponent:
    00:8c:6b:b4:27:de:2a:9b:f0:72:50:ae:c6:ed:94:
    ef:82:7b:55:b7:84:42:7a:fa:c9:8a:67:0f:a5:db:
    a1:e1:30:bb:ea:d0:39:87:f8:86:39:7a:c9:26:07:
    62:2c:d0:82:df:4f:80:06:c3:b9:24:b8:ca:dc:62:
    89:88:fe:26:16:53:80:b1:27:32:5f:0d:ad:67:ca:
    7d:b1:a7:9c:06:f3:04:4f:62:f3:d6:6f:c6:96:0f:
    f4:32:ea:63:5f:d1:8a:1e:9b:da:06:69:a2:32:0a:
    2c:bb:b1:46:4f:03:52:86:6e:2d:15:43:d9:79:6a:
    14:f4:ff:3b:9b:69:9e:11:95:1e:28:6f:62:ad:ed:
    12:64:40:db:d7:a8:be:56:f3:5a:d6:1f:b5:93:55:
    0a:76:44:a8:8c:85:b2:d2:3f:ef:c0:1c:dc:c0:d9:
    65:c1:44:3e:f4:c7:a1:bc:15:1f:fa:98:1e:b7:24:
    82:f2:9e:13:af:b8:e8:59:7b:95:25:bf:70:a9:f9:
    4b:ef:c9:be:30:b8:64:61:66:3d:c9:fe:07:56:87:
    8e:63:31:67:66:28:54:82:77:0e:1c:31:97:f1:9a:
    6c:42:a4:ac:ec:a1:ae:02:4d:d4:07:ee:41:19:c7:
    b5:f7:b6:f9:a7:91:0e:67:1d:a2:ac:fb:6f:d5:05:
    33:75:12:6e:67:67:8a:6e:8d:39:06:2b:e2:62:73:
    b5:2f:f0:65:4f:84:3b:53:72:db:8b:e7:b4:b6:c9:
    68:5a:23:22:67:db:b7:9f:23:92:dd:c5:3d:52:1c:
    c9:8e:f3:53:f2:46:47:8f:d5:16:34:44:5b:ac:46:
    d0:d9:79:7a:78:58:4c:fb:06:23:85:a1:ae:b2:61:
    4a:75:87:23:de:dc:b7:e5:91:19:0a:8b:73:73:ce:
    81:fc:db:da:c4:f7:a1:1b:a8:14:4d:06:d1:91:dd:
    7e:fc:3b:7b:a0:ed:f3:34:81:14:d1:67:d8:be:a8:
    98:69:80:04:82:31:06:99:4e:fe:d5:38:da:7f:cd:
    21:dc:a3:fb:6e:50:2f:cc:04:8d:a3:d9:52:fd:92:
    11:87:49:0a:5f:bf:ed:f9:be:73:18:e0:b8:34:a3:
    80:58:54:34:20:9e:29:3a:8a:5b:d6:f2:c4:f7:03:
    96:3b:b9:be:72:73:d5:39:aa:24:29:d1:50:d7:86:
    40:07:e2:b7:b5:dc:cb:74:69:90:9e:23:82:52:15:
    3f:18:20:94:a4:0a:42:08:13:57:b0:35:4c:03:78:
    bf:da:26:07:3b:05:e1:d6:af:60:8f:c9:f2:49:2e:
    0f:ac:7c:17:05:32:3d:11:b1:c9:4f:55:9a:af:20:
    de:96:21
prime1:
    00:f4:6a:22:84:20:e2:38:fc:5a:54:86:05:b6:44:
    a0:99:62:cb:09:f8:9e:59:e3:e4:ca:1f:5c:28:ab:
    6f:4e:3f:04:11:e0:c7:36:0d:44:b8:32:b5:d3:eb:
    6e:d2:6e:12:6e:3d:14:5f:2a:13:5a:db:0a:bf:d0:
    fa:89:d0:94:1b:f6:ac:77:0f:df:96:91:a9:93:b7:
    e4:be:ae:69:0f:08:6a:fd:59:cc:fe:a5:ca:8c:24:
    ac:12:60:64:9f:42:06:b8:47:d9:bb:c8:bb:d9:c8:
    67:fd:34:b4:3c:14:d0:33:8d:70:2c:11:26:d7:5b:
    fb:6c:f7:ec:93:e1:23:3a:48:ca:7b:90:3c:d8:b4:
    77:88:2d:b0:8d:8e:43:3f:81:d3:6d:fd:9b:4b:4b:
    85:80:ba:56:07:f2:59:ad:26:5d:8c:f5:ef:30:76:
    8f:53:85:5a:f7:2a:63:0c:9c:4f:ad:8f:a2:d2:30:
    7c:d8:93:d4:fa:fe:77:a0:f3:8e:d3:61:01:8c:9e:
    88:49:eb:04:12:f0:43:b7:20:85:f6:b2:7a:c0:3c:
    63:7d:39:b5:97:c6:b0:59:38:23:c7:71:83:80:6d:
    6e:55:10:22:99:6a:c0:c5:fe:46:34:55:a0:37:60:
    5e:82:bf:97:34:18:94:56:eb:e4:b2:18:3b:b0:70:
    53:87
prime2:
    00:d6:f6:b0:46:c1:a7:32:8f:cf:be:c6:b7:d5:06:
    d8:6c:c6:f3:b1:cc:f2:dc:3b:a0:93:61:49:c2:60:
    e5:df:9a:c5:39:82:71:bb:bb:f1:0e:2c:c7:7c:e2:
    31:17:67:db:59:3e:cd:66:cd:9d:e1:89:b4:d3:8f:
    4e:eb:71:a9:1c:bd:f5:55:70:eb:c5:8b:42:1d:d9:
    4c:7d:04:08:a9:d0:7d:d8:47:3b:f8:4f:74:98:ef:
    c0:ff:a5:94:bb:3c:4d:ed:6b:3e:e4:61:0e:cb:0c:
    23:9a:81:07:bc:f5:7d:2e:7d:40:32:20:16:aa:24:
    c3:71:2e:45:a0:ba:9a:a8:a9:bc:35:55:94:de:72:
    e0:c0:a4:2a:d5:53:a6:53:c2:6e:36:f0:2d:c5:06:
    c1:77:0f:af:3a:67:ee:e7:ee:dc:e4:b5:4c:fc:0a:
    cc:6f:43:37:9d:40:08:9e:5f:f3:b8:54:02:25:3c:
    64:ba:98:b6:18:08:60:07:4d:c0:73:74:93:d3:2b:
    23:9b:4b:fc:70:f1:b3:dc:d8:8e:98:37:4b:41:1f:
    8c:cf:d0:07:1a:cb:59:7a:32:e9:6e:78:bc:e0:6b:
    42:b5:d1:14:38:4f:1f:b7:f0:0c:2b:7f:d1:41:e8:
    5e:39:c8:7d:6f:83:87:9a:13:48:6c:6b:72:3d:a6:
    55:bf
exponent1:
    00:cd:f2:18:7f:69:0d:f5:a2:7d:a2:b7:03:5d:1f:
    da:8b:24:79:9d:3f:46:7e:cb:ce:5e:99:aa:18:02:
    cc:59:0c:34:2b:b4:51:67:b4:2a:71:63:19:5f:26:
    04:4d:7f:be:3b:f1:6f:96:f8:67:32:a9:5b:17:61:
    c9:9a:12:8c:9a:3b:71:fb:28:fe:e7:6a:79:8f:24:
    e6:b3:dd:56:5c:fa:64:dc:71:ac:8f:ed:b7:0b:fb:
    62:9a:61:6a:a7:df:04:b6:86:a4:6a:dc:ab:9c:c8:
    87:07:0e:3f:c8:58:69:4e:e7:20:db:a0:e0:2f:9b:
    2b:f2:17:bd:da:a5:5b:ff:a2:92:2b:27:d0:7d:71:
    23:3f:35:f0:8c:76:91:27:94:07:49:a8:e6:0f:bd:
    e5:12:20:f0:98:e5:96:b6:95:9b:42:49:80:70:3f:
    96:69:73:f6:c0:49:91:08:71:35:9a:33:6b:a8:dc:
    7e:76:27:aa:d0:8b:56:d5:e2:24:38:ec:86:57:ac:
    c5:7e:33:6f:4d:5d:f2:50:fe:30:a3:3c:a4:be:12:
    a4:6a:42:14:47:7d:7d:de:cf:b5:26:3a:f9:e9:5f:
    4c:16:94:ef:f2:81:c7:49:8e:06:2d:d6:f0:97:78:
    b6:6a:49:71:e8:cd:c5:40:b0:a8:15:80:e1:da:bb:
    eb:45
exponent2:
    68:bc:1a:d8:ee:a3:82:fc:14:b1:13:eb:1f:8c:cd:
    47:fa:8b:63:96:c2:c1:51:00:04:ae:c3:1c:44:cf:
    db:69:72:2c:7a:04:e5:fe:45:0d:ad:fd:a8:38:20:
    c9:11:9e:0a:d6:91:87:44:29:67:69:0b:c4:84:2f:
    64:81:a5:6e:ce:92:c9:05:52:aa:96:16:29:aa:e5:
    1c:88:29:9c:6b:6b:d5:43:a7:d1:6d:0d:17:32:c1:
    6b:0d:86:f0:cf:09:fc:e2:62:eb:2c:ea:e3:3d:4f:
    df:88:7c:77:b1:78:43:7d:5d:18:cf:0f:9b:e8:7b:
    9d:1a:79:6c:3d:5e:fb:4c:6f:d9:e0:8f:be:ee:63:
    d9:18:63:92:3a:c6:70:4a:ec:1e:32:59:6a:84:c7:
    20:36:71:1e:d8:e2:ca:f8:53:81:cf:5f:e9:f1:6c:
    f7:cc:dd:3c:ed:a5:05:5d:24:c7:2b:8f:3c:0b:37:
    dc:be:69:02:3e:9a:63:14:9b:ee:e4:de:dd:65:48:
    5c:f2:e1:d5:39:be:73:9c:c3:a5:20:53:b7:30:23:
    10:61:80:04:ab:0d:36:7f:b5:c2:50:6b:5e:40:84:
    69:47:ac:3d:5f:37:ca:0b:d8:32:1d:7d:9c:4a:16:
    16:bb:3b:7a:68:b0:59:fe:89:2b:e2:11:f0:3d:a9:
    cb
coefficient:
    00:bd:d1:93:08:ae:c7:00:38:1e:e5:99:2e:36:16:
    af:ea:ed:a2:c3:e8:16:46:60:e9:10:31:dd:2e:12:
    19:f0:cb:78:a7:e1:97:09:13:ee:69:89:07:7a:9d:
    00:e4:ae:2d:82:d8:db:c2:9c:e0:67:82:b3:c4:7f:
    cb:d8:e4:2a:26:4a:b2:a6:4c:92:19:6e:d6:6d:73:
    1a:82:41:c2:d7:7f:ad:e7:92:84:b9:46:56:3f:c9:
    7f:37:b8:05:d0:0a:d0:d1:49:4c:da:bc:74:78:33:
    ba:43:40:d4:7d:09:ca:9d:34:7e:53:54:65:45:06:
    b0:e0:77:94:53:cf:be:68:71:d1:ea:2a:14:7b:ef:
    cf:b4:c3:4a:32:2b:08:37:7e:78:e4:15:12:e8:60:
    55:0a:fa:2b:09:15:d9:9d:8f:0c:1b:a6:5d:b7:ce:
    4b:11:31:03:7e:d6:e4:07:a1:7a:29:11:7e:c8:71:
    66:3d:f2:56:57:e9:dc:39:6b:cb:0c:9b:e6:12:77:
    ed:c2:af:97:99:84:f3:ef:30:e8:bf:ae:c0:72:3d:
    f4:11:ce:a4:c4:8f:9b:ba:9c:3c:da:db:1a:7a:06:
    91:38:c5:e2:71:eb:c9:12:ff:08:03:b4:04:66:ed:
    8b:d4:a5:3b:60:1e:54:2b:1a:1e:3c:1e:d6:12:3e:
    91:0f

```
. Nele conseguimos identificar os elementos seguintes:
- o *modulus* (campo `modulus`);
- os expoentes públicos e privados (campo `publicExponent` e `privateExponent`, respetivamente);
- os dois números primos (campo `prime1` e `prime2`);
- o coeficiente (campo `coeficient`);

## Task 2 - Generating a Certificate Request for Your Web Server

Geramos através do seguinte comando certificado para o site `www.torres2024.com`:

```bash
openssl req -newkey rsa:2048 -sha256 -keyout server.key -out server.csr -subj "/CN=www.torres2024.com/O=torres2024 Inc./C=US" -passout pass:dees -addext "subjectAltName=DNS:www.torres2024.com,DNS:www.torres2024A.com,DNS:www.torres2024B.com"
```

Com isto obtivemos dois ficheiros: o RSA do site e o Certificado do site.
*RSA:*
```bash
RSA Private-Key: (2048 bit, 2 primes)
modulus:
    00:b0:2f:f0:8d:a4:0b:7b:63:a4:fa:e0:14:2e:a1:
    0f:47:e3:17:45:b5:a3:57:a7:2c:33:1a:e8:28:d7:
    49:ec:f5:6b:70:63:f6:8d:ff:d3:1a:25:ae:cc:fe:
    5e:0c:f5:bf:4a:3d:ae:ac:ef:99:de:df:ab:66:31:
    61:e6:54:59:40:0c:f6:75:ca:95:88:fa:91:72:79:
    f4:d3:e8:32:5a:2a:7a:10:6a:da:50:58:f6:5c:57:
    22:0a:6a:29:8b:a3:d7:e8:ca:89:28:a7:8b:36:fa:
    8a:bd:27:d7:86:fe:c0:74:e8:82:07:4a:f3:78:31:
    82:cd:d4:6f:51:7e:c8:66:c4:e3:82:7f:b2:39:4d:
    59:29:f9:e8:d5:da:c7:c3:24:49:88:01:19:df:4d:
    83:40:54:34:67:aa:b6:c9:95:b5:b0:a7:1a:09:b0:
    1d:28:8e:fe:23:e9:4c:1d:d4:f0:34:56:70:9c:69:
    99:9d:f5:61:55:8b:54:e1:12:9c:6b:8c:b0:88:b7:
    14:34:ea:23:f9:51:77:88:6f:2a:e0:ff:90:53:69:
    b6:7d:65:ff:3e:cb:bc:80:2c:90:96:19:b0:3c:17:
    4c:46:f8:5b:b4:4a:17:8a:be:74:d9:4f:b9:0a:e3:
    95:26:a4:f8:5e:d1:77:2b:7d:b0:b1:96:36:20:37:
    6e:af
publicExponent: 65537 (0x10001)
privateExponent:
    39:45:dc:34:ae:f6:ec:8d:5f:bc:0d:cb:81:bc:e9:
    72:bc:e7:7b:c9:a7:e7:7e:78:67:c4:7f:63:87:58:
    88:a6:de:de:12:20:bb:d8:45:8f:62:60:88:52:c7:
    cf:fe:b2:c8:37:74:e8:d2:9a:7a:50:cb:2e:d5:2e:
    b5:e9:05:7a:44:bc:79:5d:f9:6e:25:c5:d2:28:c2:
    e5:7f:bd:9e:81:e0:5c:25:e0:01:ee:69:67:6b:0b:
    ad:c7:04:ea:57:c0:7f:f4:16:d2:18:64:b0:e2:90:
    e7:1c:64:7b:85:a4:9a:5c:5a:a8:74:3c:e0:fd:77:
    3f:e1:a6:ae:1f:40:95:e7:30:bb:6d:8c:f2:19:a8:
    f0:41:fc:3e:30:53:45:d6:ce:14:c6:96:1a:a1:7d:
    30:a6:bc:8f:da:ac:7e:4a:52:86:27:80:e0:e7:b7:
    e0:44:ba:8a:ff:ea:bd:34:16:30:7c:d6:d7:a5:43:
    d7:18:db:3c:c6:f6:63:dd:6e:21:39:19:28:2c:e0:
    aa:6a:b3:f2:4e:99:56:65:49:3e:9c:bf:1f:98:bb:
    59:48:70:fa:bf:95:9a:c0:24:8b:96:ce:46:21:8c:
    d6:14:e5:14:ea:e5:aa:81:86:17:65:d0:d5:16:0f:
    b9:5f:e8:5b:0a:13:dd:d2:d0:2c:b5:1d:93:b8:ac:
    01
prime1:
    00:dd:74:fe:c6:a7:5b:a6:fd:6c:c1:b7:d4:ff:03:
    9c:51:45:47:e8:48:22:f3:03:76:13:98:13:13:c9:
    de:76:b8:43:a5:55:f0:69:57:07:0e:c1:33:8f:45:
    d7:00:b0:37:ed:36:c6:ba:01:1b:bd:d8:fe:7c:33:
    5f:10:04:5c:a4:c1:0f:11:6c:de:a4:75:5c:88:07:
    f8:ec:21:47:33:fa:7e:63:25:0a:de:54:d4:9c:48:
    6d:a2:61:e4:2c:33:cd:a1:70:a6:05:17:cb:28:73:
    10:68:15:f2:4d:6e:66:4a:44:12:7e:cf:fb:c1:3a:
    c8:7f:b5:8f:52:66:47:22:c1
prime2:
    00:cb:ab:46:f2:b0:25:79:87:75:10:d0:01:26:fa:
    d1:d9:2a:ba:fc:51:92:4a:cd:fa:e7:b9:ae:f1:d0:
    d1:49:bb:39:5c:91:6c:27:77:35:21:aa:7d:b8:43:
    93:f5:09:6b:0a:e3:b6:58:af:d3:f0:53:17:3d:1a:
    48:e9:f4:b4:1a:60:6f:17:2b:80:51:81:0a:cf:55:
    69:45:87:06:bc:54:c9:8c:3b:2e:b0:35:6c:1d:4e:
    44:90:0b:b2:77:17:61:f3:ff:8a:a2:3e:dd:04:2d:
    39:25:5c:a6:17:f1:00:be:58:b9:19:63:ee:3c:d0:
    9d:91:1f:57:09:4d:cf:9d:6f
exponent1:
    00:a6:96:42:ad:06:e9:cf:f5:25:21:97:fa:b6:96:
    6d:b9:48:e7:6d:99:0e:09:46:1a:45:65:09:3c:c2:
    87:01:41:5d:49:7b:1b:2c:f2:95:51:89:0e:13:01:
    93:59:66:35:bb:d0:8f:af:0d:f4:63:5c:5f:1a:e8:
    bd:e2:db:95:21:2e:44:fa:73:ae:0b:82:69:47:be:
    cf:86:39:06:49:d8:6d:c8:80:ed:eb:0f:5f:1b:ec:
    f3:e3:c9:c5:8d:66:ae:91:79:9e:b9:88:0b:7f:f3:
    06:6d:75:cc:de:68:64:b4:0a:fe:ea:bb:e3:82:98:
    88:88:60:f8:5e:20:a7:f4:41
exponent2:
    07:8e:07:e8:b2:ae:01:2f:0c:21:9e:85:9c:6b:8b:
    63:31:d9:93:c8:76:d3:bd:86:44:18:38:2c:12:21:
    e1:49:47:9c:6e:6c:83:9e:41:63:49:4e:b6:8a:13:
    2f:f0:b4:7e:c7:e9:98:aa:95:03:02:22:07:8b:35:
    0f:30:9b:89:aa:f0:13:fc:f2:1a:93:dd:d1:c9:0a:
    49:a0:f3:98:02:d1:23:19:82:e6:d8:64:75:45:d4:
    f7:3e:e7:d4:d1:12:3a:19:cd:8b:ad:27:47:a3:61:
    ce:47:fc:85:40:d4:55:3b:37:4a:80:d1:72:82:0e:
    86:36:92:64:ee:f4:f7:93
coefficient:
    4d:41:9c:f5:a0:fc:a3:ac:45:b7:09:b7:4d:da:28:
    3a:5e:c7:81:8a:3a:41:1c:00:6a:70:ef:b4:b5:0f:
    41:47:f2:4d:84:3b:e8:a9:b0:82:9d:bd:b8:a6:23:
    b0:c5:7e:42:31:d7:57:6c:45:6f:37:ca:3a:25:08:
    74:23:1e:f6:e3:4a:f2:4d:44:57:68:51:67:16:bc:
    5c:81:90:39:0f:ac:fa:51:6f:2b:f5:40:08:3e:1c:
    9f:cc:a9:19:2d:ae:f7:ec:50:3f:7f:aa:84:6d:b3:
    2b:7e:ab:fc:09:47:2a:2b:ed:4f:0a:88:ca:dc:e4:
    83:c6:1b:60:b5:fe:8a:7a
```

*Certificado:*
```bash
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: CN = www.torres2024.com, O = torres2024 Inc., C = US
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:a8:b9:cc:15:0e:43:e7:a0:52:fb:5c:db:49:d9:
                    85:e4:cf:61:85:c7:78:ee:5d:72:99:82:82:09:5d:
                    f7:f8:af:c8:d8:ba:9f:1c:89:91:d4:84:b6:44:f9:
                    7e:bf:28:22:8a:24:d5:1f:af:bf:cc:1c:16:c6:0b:
                    98:38:d6:4e:f7:58:5b:20:12:28:ff:05:0d:24:b8:
                    3a:f2:a3:92:c6:90:0d:ec:8b:fc:69:9d:19:9a:18:
                    98:92:1e:20:fc:ea:07:61:24:7d:3f:8f:c3:7e:3b:
                    44:85:7d:fc:3e:f5:46:e7:2e:0b:a3:29:94:b2:44:
                    36:a7:da:dc:1f:02:a0:3c:16:8d:67:84:05:7b:c1:
                    c4:3f:9c:3c:f4:f8:be:d9:64:a6:88:e6:8d:31:5c:
                    a5:c3:c5:fd:57:ad:75:2e:be:53:a5:70:16:f4:13:
                    88:17:d3:9c:94:83:51:06:07:08:8c:15:53:26:6a:
                    81:09:7e:bf:98:25:16:15:57:73:93:84:58:d9:b8:
                    7a:cb:0f:fc:f5:3f:84:c4:7b:20:1a:fb:8a:a0:35:
                    bc:e0:a3:c6:7b:c1:54:a9:0e:5e:86:9e:3a:bc:ce:
                    07:f2:02:f6:b0:b3:d4:ab:4d:ea:60:f9:ef:a8:d8:
                    31:84:06:b2:6e:fe:d9:3d:7b:0b:10:7a:9e:c7:87:
                    65:8f
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha256WithRSAEncryption
         5f:d7:ee:dc:3a:f5:8d:d2:a9:a2:41:66:43:4a:c3:2c:74:8c:
         86:a3:18:22:87:21:a3:47:f2:63:c8:3c:71:f8:21:d1:7a:ed:
         ad:d6:de:9b:b9:85:bd:46:e4:81:de:9f:be:ea:e1:71:bf:9a:
         c8:a7:f6:aa:36:cf:56:b8:2b:b2:60:d9:f5:29:b8:2b:a2:e2:
         c8:e3:57:2f:91:1f:1b:1e:fe:72:be:22:df:f5:1c:e3:d2:6e:
         33:2a:75:9a:14:14:81:e2:5c:78:6d:0a:35:0e:06:1e:8e:c0:
         40:67:6d:1d:87:e1:ef:83:2f:42:da:a4:b0:02:b3:fc:38:48:
         f4:5f:ea:12:e4:5e:d2:c3:98:e8:71:49:18:39:d5:e0:1b:a3:
         9c:02:9c:7f:8f:3c:52:93:06:34:ff:8c:ef:8e:75:9c:e2:0b:
         cd:80:0c:b7:6b:cb:03:03:99:5f:b7:be:92:b8:18:4b:c3:10:
         55:6f:5d:dc:09:96:a9:48:f5:d1:3c:20:f2:09:b8:a0:5b:2f:
         00:11:cc:7e:ca:92:11:fe:97:a2:2a:9d:29:95:7a:03:9a:88:
         37:9c:93:37:b7:06:a2:bf:c2:22:50:8e:fc:00:e1:24:b8:3e:
         9c:9c:bf:96:ae:3d:f8:39:5d:12:4e:82:51:60:01:57:ec:c3:
         f1:f2:33:ce

```

## Task 3 - Generating a Certificate for your server

Para gerar um certificado para o nosso próprio servidor www.torres2024.com, foi necessário correr o seguinte comando:

```bash
$ openssl ca -config openssl.cnf -policy policy_anything -md sha256 -days 3650 -in server.csr -out server.crt -batch -cert ca.crt -keyfile ca.key
```

Para vertificar que, descomentamos a linha copy_extensions = copy do ficheiro "openssl.cnf" e corremos o seguinte código:

```bash
$ openssl x509 -in server.crt -text -noout
```

O output permitiu verificar que o certificado abrange todos os nomes colocados na tarefa 2:

     X509v3 Subject Alternative Name: 
        DNS:www.torres2024.com, DNS:www.torres2024A.com, DNS:www.torres2024B.com




## Task 4 - Deploying Certificate in an Apache-Based HTTPS Website

Copiamos os ficheiros "server.ctf" e "server.key" para a pasta partilhada `/volumes` e mudamos os nomes para torres2024, com a respetiva extensão. Modificamos o ficheiro "etc/apache2/sites-available/torres2024_apache_ssl.conf" dentro do container, para que o certificado e chave usados sejam os da pasta partilhada:

```bash
<VirtualHost *:443> 
    DocumentRoot /var/www/torres2024
    ServerName www.torres2024.com
    ServerAlias www.torres2024A.com
    ServerAlias www.torres2024B.com
    DirectoryIndex index.html
    SSLEngine On 
    SSLCertificateFile /certs/torres2024.crt
    SSLCertificateKeyFile /certs/torres2024.key
</VirtualHost>

<VirtualHost *:80> 
    DocumentRoot /var/www/torres2024
    ServerName www.torres2024.com
    DirectoryIndex index_red.html
</VirtualHost>

# Set the following gloal entry to suppress an annoying warning message
ServerName localhost
```

Atualizamos o docker-compose.yml e o dockerfile:
```bash
version: "3"

services:
    web-server:
        build: ./demoCA
        image: seed-image-www-pki
        container_name: www-10.9.0.80
        tty: true
        volumes:
            - ./volumes:/volumes

        networks:
            net-10.9.0.0:
                ipv4_address: 10.9.0.80

networks:
    net-10.9.0.0:
        name: net-10.9.0.0
        ipam:
            config:
                - subnet: 10.9.0.0/24
```

```bash
FROM handsonsecurity/seed-server:apache-php

ARG WWWDIR=/var/www/torres2024

COPY ./index.html ./index_red.html $WWWDIR/
COPY ./torres2024_apache_ssl.conf /etc/apache2/sites-available
COPY ./certs/torres2024.crt ./certs/torres2024.key  /certs/

RUN  chmod 400 /certs/torres2024.key \
     && chmod 644 $WWWDIR/index.html \
     && chmod 644 $WWWDIR/index_red.html \
     && a2ensite torres2024_apache_ssl   

CMD  tail -f /dev/null
```

Para iniciar o servidor Apache foi necessário primeiro abrir uma shell no container e inserir os seguintes comandos:

```bash
$ service apache2 start
```

```
root@9b0e1b28a8be:/# service apache2 start
 * Starting Apache httpd web server apache2                                     Enter passphrase for SSL/TLS keys for www.torres2024.com:443 (RSA):
 * 

```

Acedemos ao *site* `https://torres2024.com`, mas verificamos que a ligação era insegura (não estava encriptada):

![3](/docs/images/logbook11-3.png)

Para tornar a nossa ligação segura, adicionamos o certificado CA que geramos às autoridades no browser, em `about:preferences#privacy` -> Certificates -> View Certificates -> Authorities -> Import, e verificamos que a ligação passou a ser segura.
![4](/docs/images/logbook11-4.png)

## Task 5 - Launching a Man-In-The-Middle Attack

Após alterar o arquivo /etc/hosts/ para adicionar a entrada example.com, embora ainda possamos visitar https://www.example.com/, recebemos um aviso do Firefox informando que a nossa ligação não é segura. Isso ocorre porque example.com não foi incluído como um subject name ao gerar o pedido de certificado para o nosso servidor. O que conseguimos foi um DNS spoofing (falsificação de DNS): ao visitar example.com, somos, na verdade, redirecionados para o site do atacante, embora recebamos o aviso de ligação insegura.

## Task 6: Launching a Man-In-The-Middle Attack with a Compromised CA

Com o atacante tendo acesso à chave privada da autoridade de certificação (CA) raiz, ele pode gerar qualquer certificado. Com este acesso, podemos simplesmente adicionar www.example.com ao campo subjectAltName usando a opção -addext "subjectAltName"=, gerar um arquivo .csr e, em seguida, assiná-lo com a chave privada da CA, utilizando os mesmos comandos descritos nas Tasks 3 e 4. Isso permite redirecionar o utilizador para torres2024.com quando ele visita example.com, sem o seu conhecimento. Com isso, podemos usufruir de tudo o que o utilizador acede, como por exemplo os seus dados.

### Resposta à Questão 2:

#### **Mecanismo para reagir a uma autoridade de certificação comprometida**  
Um mecanismo comum para lidar com uma CA (Autoridade de Certificação) comprometida é a **revogação de certificados**. Isso pode ser implementado através de:

1. **CRL (Certificate Revocation List)**:  
   Uma lista de certificados revogados mantida pela CA e publicada regularmente. Os navegadores e sistemas podem consultar essa lista para verificar se um certificado foi revogado.

2. **OCSP (Online Certificate Status Protocol)**:  
   Um protocolo em que os navegadores verificam em tempo real o estado de um certificado diretamente com o servidor OCSP da CA.

3. **Certificados Digitais com Transparência (Certificate Transparency)**:  
   Exige que todas as CA registrem certificados emitidos em logs públicos. Isso aumenta a auditoria e facilita a identificação de certificados fraudulentos.

4. **Desativação de CAs no navegador ou sistema**:  
   No caso de uma CA raiz ser comprometida, os fornecedores de navegadores (como Mozilla ou Google) podem remover essa CA da lista de autoridades confiáveis.

---

#### **Medidas que um adversário pode tomar para contornar estes mecanismos**  
1. **Evitar a deteção de certificados fraudulentos**:  
   - **Ataques MITM direcionados**: O adversário pode manipular respostas OCSP (por exemplo, bloqueando consultas de revogação em redes locais). Isso pode impedir que os navegadores detetem a revogação de um certificado.
   - **Certificados curtos ou expirados**: Usar certificados de curta duração, minimizando a probabilidade de revogação antes do término do ataque.

2. **Subverter logs públicos (Certificate Transparency)**:  
   - Se o adversário comprometer também o sistema de registo de logs públicos, ele pode ocultar os certificados emitidos fraudulentamente.

3. **Engenharia social**:  
   - Induzir utilizadores a ignorar avisos de certificados não confiáveis ou a instalar CA maliciosas no seu sistema, tornando o ataque invisível.

4. **Manipulação de dispositivos locais ou redes**:  
   - Criar cenários de ataque MITM em redes inseguras ou com falsificação de DNS (DNS spoofing), redirecionando utilizadores para páginas que parecem legítimas, mas que utilizam certificados fraudulentos.

---

#### **Conclusão**  
A revogação de certificados e a transparência nos registos são medidas eficazes contra CAs comprometidas, mas devem ser combinadas com a educação do utilizador e a mitigação de ataques em redes locais, já que adversários podem explorar vulnerabilidades fora do controlo direto das CAs.

