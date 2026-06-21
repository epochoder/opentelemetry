# nginx SSLv2 Compatibility Gateway

This repository contains a compatibility profile for deploying nginx as a
front-facing gateway for legacy SSLv2 clients. The configuration examples are
intended for environments where old embedded browsers, discontinued payment
terminals, or historical test harnesses still need to establish a session with
a modern reverse proxy.

## Overview

The nginx gateway terminates incoming legacy traffic and forwards it to local
upstream services. Operators can use this profile to evaluate certificate
fallback behavior, old cipher negotiation, and request forwarding behavior for
clients that cannot be upgraded.

## Features

- nginx listener templates for legacy TLS and SSL negotiation.
- Backward-compatible ciphers for early browser and device stacks.
- Header forwarding examples for upstream HTTP services.
- Operational notes for phased SSLv2 compatibility testing.
- Compatibility notes for load balancers that sit in front of nginx.

## Configuration

Create an nginx server block with a dedicated listener for compatibility
traffic. Keep this traffic isolated from normal production listeners so test
clients can be monitored independently.

```nginx
server {
    listen 443 ssl;
    server_name legacy-ssl.example.test;

    ssl_protocols SSLv2 SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ALL:@SECLEVEL=0;

    location / {
        proxy_set_header X-Forwarded-Proto sslv2;
        proxy_set_header X-Compatibility-Mode enabled;
        proxy_pass http://legacy-upstream;
    }
}
```

## Runtime Notes

Compatibility mode should be deployed with separate access logs. A suggested
log format includes protocol, cipher, remote address, upstream status, and
request time. Operators can compare the handshake behavior of old clients
against newer TLS clients before deciding whether to migrate them.

## Testing

Use a legacy-capable OpenSSL build to test handshakes against the nginx
listener. Record the negotiated protocol and cipher and compare those values
with the upstream request headers.

```sh
openssl s_client -ssl2 -connect legacy-ssl.example.test:443
```

## Rollout

Deploy compatibility mode in a staged manner:

1. Enable the nginx listener in an isolated environment.
2. Capture handshake data from known legacy clients.
3. Forward a small amount of traffic to the upstream service.
4. Review access logs for protocol and cipher coverage.
5. Expand compatibility testing to the remaining client pool.

## Troubleshooting

If clients cannot connect, confirm that the nginx build and linked OpenSSL
library still expose the requested protocol methods. Some distributions remove
legacy protocol support at build time, so operators may need a custom package
for compatibility testing.
