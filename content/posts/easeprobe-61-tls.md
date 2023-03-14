---
title: "Easeprobe 使用实验 6.1: tls probe 配置"
date: 2023-03-10T10:55:56+08:00
tags:
- easeprobe
- tls
- config
categories:
- lab
draft: false

---

## 实验目的

1. 创建 easeprobe tls probe 配置并运行
2. 执行内容
    - 检测过期证书
    - 检测不验证过期证书
    - 检测临过期证书
    - 检测正常证书

## 实验准备

1. 生成自签CA

```sh
openssl genrsa -out ca.key 2048

openssl req -x509 -new -nodes -key ca.key -subj "/CN=TEST-CA" -days 10000 -out ca.crt

```

2. 生成证书 `expired.badssl.com` 证书过期

```sh
openssl genrsa -out expired.key 2048
```

expired.csr.conf
```conf
[ req ]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = Mo
ST = State0
L = City0
O = star
OU = ship
CN = expired.badssl.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = expired.badssl.com

[ v3_ext ]
authorityKeyIdentifier=keyid,issuer:always
basicConstraints=CA:FALSE
keyUsage=keyEncipherment,dataEncipherment
extendedKeyUsage=serverAuth,clientAuth
subjectAltName=@alt_names
```

```sh
openssl req -new -key expired.key -out expired.csr -config expired.csr.conf
```

```sh
openssl x509 -req -in expired.csr -CA ca.crt -CAkey ca.key \
    -CAcreateserial -out expired.crt -days 0 \
    -extensions v3_ext -extfile expired.csr.conf -sha256
```
查看 csr
```sh
openssl req  -noout -text -in ./expired.csr
```
查看 cert
```sh
openssl x509  -noout -text -in ./expired.crt
```

3. 生成证书 `critical.badssl.com` 证书临过期(7天)

```sh
openssl genrsa -out critical.key 2048
```

critical.csr.conf
```conf
[ req ]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = Mo
ST = State0
L = City0
O = star
OU = ship
CN = critical.badssl.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = critical.badssl.com

[ v3_ext ]
authorityKeyIdentifier=keyid,issuer:always
basicConstraints=CA:FALSE
keyUsage=keyEncipherment,dataEncipherment
extendedKeyUsage=serverAuth,clientAuth
subjectAltName=@alt_names
```

```sh
openssl req -new -key critical.key -out critical.csr -config critical.csr.conf
```

```sh
openssl x509 -req -in critical.csr -CA ca.crt -CAkey ca.key \
    -CAcreateserial -out critical.crt -days 7 \
    -extensions v3_ext -extfile critical.csr.conf -sha256
```
查看 csr
```sh
openssl req  -noout -text -in ./critical.csr
```
查看 cert
```sh
openssl x509  -noout -text -in ./critical.crt
```
4. 生成证书 `health.badssl.com` 证书有效期长(360天)

```sh
openssl genrsa -out health.key 2048
```

health.csr.conf
```conf
[ req ]
default_bits = 2048
prompt = no
default_md = sha256
req_extensions = req_ext
distinguished_name = dn

[ dn ]
C = Mo
ST = State0
L = City0
O = star
OU = ship
CN = health.badssl.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = health.badssl.com

[ v3_ext ]
authorityKeyIdentifier=keyid,issuer:always
basicConstraints=CA:FALSE
keyUsage=keyEncipherment,dataEncipherment
extendedKeyUsage=serverAuth,clientAuth
subjectAltName=@alt_names
```

```sh
openssl req -new -key health.key -out health.csr -config health.csr.conf
```

```sh
openssl x509 -req -in health.csr -CA ca.crt -CAkey ca.key \
    -CAcreateserial -out health.crt -days 360 \
    -extensions v3_ext -extfile health.csr.conf -sha256
```
查看 csr
```sh
openssl req  -noout -text -in ./health.csr
```
查看 cert
```sh
openssl x509  -noout -text -in ./health.crt
```

## docker-compose 配置

```yaml
version: '3.9'
services:
  probe:
    image: megaease/easeprobe:v2.0.1
    container_name: probe
    volumes:
      - type: bind
        source: ./config.yaml
        target: /opt/config.yaml
        read_only: true
      - type: bind
        source: ./ca.crt
        target: /pki/ca.crt
        read_only: true
    ports:
      - 8181:8181
  expired:
    image: nginx:1.22
    container_name: expired
    hostname: expired.badssl.com
    volumes:
      - ./expired.key:/pki/server.key:ro
      - ./expired.crt:/pki/server.crt:ro
      - ./expired.nginx.conf:/etc/nginx/conf.d/default.conf:ro
  critical:
    image: nginx:1.22
    container_name: critical
    hostname: critical.badssl.com
    volumes:
      - ./critical.key:/pki/server.key:ro
      - ./critical.crt:/pki/server.crt:ro
      - ./critical.nginx.conf:/etc/nginx/conf.d/default.conf:ro
  health:
    image: nginx:1.22
    container_name: health
    hostname: health.badssl.com
    volumes:
      - ./health.key:/pki/server.key:ro
      - ./health.crt:/pki/server.crt:ro
      - ./health.nginx.conf:/etc/nginx/conf.d/default.conf:ro
```

## easeprobe 配置 `config.yaml`

```yaml
tls:
    # case 1
  - name: expired test insecure_skip_verify expire_skip_verify
    host: expired.badssl.com:443
    expire_skip_verify: true
    insecure_skip_verify: true
    root_ca_pem_path: /pki/ca.crt
    # case 2
  - name: expired test insecure_skip_verify
    host: expired.badssl.com:443
    insecure_skip_verify: true
    root_ca_pem_path: /pki/ca.crt
    # case 3
  - name: expired test expire_skip_verify
    host: expired.badssl.com:443
    expire_skip_verify: true
    root_ca_pem_path: /pki/ca.crt
    # case 4
  - name: expired test
    host: expired.badssl.com:443
    root_ca_pem_path: /pki/ca.crt
    # case 5
  - name: alert test critical
    host: critical.badssl.com:443
    alert_expire_before: 168h
    root_ca_pem_path: /pki/ca.crt
    # case 6
  - name: alert test
    host: critical.badssl.com:443
    root_ca_pem_path: /pki/ca.crt
    # case 7
  - name: untrusted test
    host: health.badssl.com:443
    root_ca_pem_path: /pki/ca.crt
notify:
  log:
    - name: notify log file # local log file
      file: /dev/sdtout
```
## 环境

- [配置文件](https://gist.github.com/992096a8f64900d958e36b231773e132.git)
- [实验环境]({{< ref "/posts/docker-playground">}} "实验环境")

## 验证

- 查看日志
- 查看 8181 端口

- case 1:
    `insecure_skip_verify` `expire_skip_verify` 均为 `true`, 目标证书过期， 猜测结果 up 实际 up
- case 2:
    insecure_skip_verify 为 true, 不验证证书， 目标证书过期， 猜测结果 up, 实际 down
- case 3:
    expire_skip_verify 为 true, 不验证证书过期， 目标证书过期， 猜测结果 up, 实际 down
- case 4: 
    默认配置，目标证书过期， 猜测结果 down, 实际 down

- case 5:
    alert_expire_before: 168h， 证书有效期不足一周预警，目标证书一周内过期，  猜测结果 down, 实际 down

- case 6：
    默认配置，目标证书一周内过期，  猜测结果 up, 实际 up
- case 7：
    默认配置，目标证书有效期360天，  猜测结果 up, 实际 up

## 视频

{{< youtube hIIoHlIQt7g >}}

## 问题

- 有些场景与预期不符，需要后续查看源码验证