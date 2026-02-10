---
layout: post
title: "Demystifying the NGINX Real IP Module"
date: 2018-09-15 12:39:00 -0600
---

The Real IP module within NGINX is very strict. The purpose of this post is to go over how the NGINX's `real_ip_from` works by walking through a few examples. Below is the official NGINX document: [http://nginx.org/en/docs/http/ngx_http_realip_module.html](http://nginx.org/en/docs/http/ngx_http_realip_module.html)

## Example 1

**NGINX configuration:**

```nginx
set_real_ip_from 0.0.0.0/0 ;
real_ip_recursive on ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 10.0.0.2
dst IP = 10.0.0.3
```

**Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

The IP 1.1.1.1 would be utilized for the real client IP based on the above request.

## Example 2

**NGINX configuration.** Same configuration as above.

**Source and Destination IP:**

```
src IP = 10.0.0.2
dst IP = 10.0.0.3
```

**Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunnningham.com
```

The X-Forwarded-For header is missing. NGINX will utilize the layer 3 source IP as the client IP. In this case NGINX will utilize the IP 10.0.0.2 as the real IP.

## Example 3

Now lets get tricky and lock down the Real IP Module to a subset of IP's.

**NGINX Config:**

```nginx
set_real_ip_from 10.0.0.0/8 ;
real_ip_recursive on ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 10.0.0.2
dst IP = 10.0.0.3
```

**HTTP Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

NGINX would use the IP 4.4.4.4 as the real client IP in the above request. The reason for this is that NGINX will trust the last IP in the chain of trusted IP's in the designated real IP header.

## Example 4

**NGINX Config:**

```nginx
set_real_ip_from 10.0.0.0/8 ;
set_real_ip_from 4.4.4.4 ;
real_ip_recursive on ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 10.0.0.2
dst IP = 10.0.0.3
```

**HTTP Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

NGINX would use the IP 3.3.3.3 as the real IP since that is 10.0.0.0/8 is trusted and 4.4.4.4 is the last IP in the chain of trusted IP's.

## Example 5

**NGINX Config:**

```nginx
set_real_ip_from 10.0.0.0/8 ;
set_real_ip_from 4.4.4.4 ;
real_ip_recursive off ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 10.0.0.2
dst IP = 10.0.0.3
```

**HTTP Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

NGINX would use the IP 4.4.4.4 as the real IP since the `real_ip_recursive` is set to off. Only the last IP in the chain of X-Forwarded-For would be utilized for the client IP.

## Example 6 - Internet IPs as source IP

**NGINX Config:**

```nginx
set_real_ip_from 10.0.0.0/8 ;
set_real_ip_from 4.4.4.4 ;
real_ip_recursive off ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 55.55.55.55
dst IP = 10.0.0.3
```

**HTTP Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

NGINX would use 55.55.55.55 as the real client IP. The reason for this is because the source IP address is not defined as trusted within the `set_real_ip_from`.

## Example 7

**NGINX Config:**

```nginx
set_real_ip_from 10.0.0.0/8 ;
set_real_ip_from 4.4.4.4 ;
set_real_ip_from 55.55.55.55 ;
real_ip_recursive on ;
real_ip_header x-forwarded-for ;
```

**Source and Destination IP:**

```
src IP = 55.55.55.55
dst IP = 10.0.0.3
```

**HTTP Request:**

```http
GET /someurl.html HTTP/1.1
host: brookscunningham.com
X-Forwarded-For: 1.1.1.1, 2.2.2.2, 3.3.3.3, 4.4.4.4
```

NGINX would use 3.3.3.3 as the real client IP. The reason for this is because `real_ip_recursive` is set to on and the source IP address is now defined as trusted within the `set_real_ip_from` up to 4.4.4.4.
