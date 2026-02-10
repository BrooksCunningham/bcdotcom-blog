---
layout: post
title: "Considerations on MSS when using a GRE tunnel from a CDN to a CDN origin"
date: 2024-01-18 16:22:00 -0600
---

When a GRE (Generic Routing Encapsulation) tunnel is used between the CDN and the CDN origin, it can impact the Maximum Segment Size (MSS) of the TCP connections due to the additional overhead introduced by the GRE encapsulation.

## GRE Tunnel Overhead

GRE encapsulation adds additional headers to packets being transmitted. A GRE header typically adds 24 bytes (20 bytes for the GRE header and 4 bytes for the GRE key, if used). This extra overhead reduces the amount of space available in each packet for the actual payload data.

## Impact on MSS

The MSS in a TCP connection signifies the largest amount of data, in bytes, that a computer or communications device can receive in a single TCP segment. It does not include the TCP header or IP header.

Under normal circumstances without a GRE tunnel, the MSS is calculated based on the Maximum Transmission Unit (MTU) of the network path, typically 1500 bytes for Ethernet networks. From this, the IP header (20 bytes) and TCP header (typically 20 bytes) are subtracted, resulting in a default MSS of 1460 bytes.

With a GRE tunnel, the additional GRE header needs to be accounted for. If the network path's MTU remains at 1500 bytes, the MSS must be reduced accordingly to accommodate the GRE header. For example, with a 24-byte GRE header, the MSS would need to be reduced to 1436 bytes (1500 - 20 for IP header - 20 for TCP header - 24 for GRE header).

## Consequences

**1. Fragmentation:**
If the MSS is not adjusted for the GRE overhead, packets might exceed the MTU, leading to fragmentation. Fragmentation can reduce performance and increase the likelihood of packet loss.

**2. Reduced Efficiency:**
A smaller MSS means that more packets are required to send the same amount of data. This can lead to increased overhead and reduced efficiency in data transmission.

**3. TCP Performance:**
TCP performance might be impacted due to the reduced MSS. TCP throughput can be less efficient with smaller packet sizes, especially over long-distance links where latency is a factor.

## Adjusting MSS

Network administrators can adjust the MSS using TCP MSS clamping, a technique used in routers and firewalls to modify the MSS value within the TCP SYN packets. This ensures that the end devices establish connections using an MSS that takes into account the GRE overhead, preventing issues like fragmentation.

## Summary

Using a GRE tunnel between the CDN and CDN origin impacts the MSS due to the additional header overhead of the GRE encapsulation. This necessitates an adjustment of the MSS to avoid fragmentation and maintain efficient TCP performance. Proper network configuration, including MSS clamping, is essential to handle this change effectively.
