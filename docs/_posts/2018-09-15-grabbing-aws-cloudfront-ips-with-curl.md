---
layout: post
title: "Grabbing AWS CloudFront IPs with curl and jq"
date: 2018-09-15 12:40:00 -0600
---

There are occasions when restricting infrastructure access behind CloudFront becomes necessary, ensuring requests route through the CDN rather than directly to the origin. AWS publishes public IP ranges in JSON format at [https://ip-ranges.amazonaws.com/ip-ranges.json](https://ip-ranges.amazonaws.com/ip-ranges.json).

Rather than manually parsing the complete JSON file to locate CloudFront IPs, combining curl and jq command-line utilities provides an efficient solution. The following command extracts only CloudFront IP ranges:

```bash
curl https://ip-ranges.amazonaws.com/ip-ranges.json | jq '.prefixes | .[] | select(.service == "CLOUDFRONT") | .ip_prefix'
```

This approach filters the AWS IP ranges dataset to display exclusively CloudFront prefixes, enabling administrators to implement targeted access restrictions at their origin infrastructure.
