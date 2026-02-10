---
layout: post
title: "A Different Approach to a Single FQDN for StoreFront and NetScaler Gateway"
date: 2018-09-15 00:00:00 -0600
---

The purpose of this post is to show how users can be educated to use a single URL, while still using having a StoreFront base URL that is different from the NetScaler Gateway URL. Please keep in mind this solution works best for Receiver for Web. This solution does work with the Native Receiver, but the Provisioning file would be the easiest way to configure the Native Receiver in my opinion.

For this scenario, I will use connect.example.com for external access to the Citrix environment. Int-connect.example.com will be used for internal access to the Citrix environment.

## Requirements

Below are an overview of the requirements for the scenario to get us started.

1. SAN certificate for int-connect.example.com and connect.example.com.
2. Connect.example.com will resolve to the publicly accessible NetScaler Gateway VIPs.
3. Int-connect.example.com will resolve to the internal StoreFront Load Balanced VIPs.
4. CNAME on the internal DNS. connect.example.com --> int-connect.example.com.
5. Responder Policy to redirect from connect.example.com to int-connect.example.com.

## Implementation

Now for the magic of creating the single FQDN that users need to know. In this example, the "single URL" for users is connect.example.com. On the internal DNS infrastructure, create a CNAME for connect.example.com to point to int-connect.example.com. Then, on the NetScaler appliance, create a Responder Policy that redirects traffic with the HTTP Host header of "connect.example.com" to "int-connect.example.com". Bind this policy to the StoreFront LB VIP on NetScaler.

## Expected User Behavior

So what is the expected user behavior? A user on the internal network types connect.example.com into their browser. Connect.example.com resolves as a CNAME for int-connect.example.com. The user will resolve int-connect.example.com. After obtaining the IP address for int-connect.example.com, the user connects to the SF LB VIP using the IP address and the HTTP host header connect.example.com. The Responder policy redirects the user to int-connect.example.com. The user's browser follows the redirect and is able to access the StoreFront LB VIP. By using a SAN certificate with the names we need, the user will not receive a certificate warning.

The workflow above is all seamless to the user. From their perspective, they type connect.example.com, and that takes them to the resources they need to focus on their job.

## Important Note for Native Receiver

**Please keep in mind that this workflow is unique to Receiver for Web.** Users that manually configure Receiver on the internal network will need to type out "int-connect.example.com" to connect to the StoreFront VIP and avoid a redirect. Again, I recommend using the provisioning file from StoreFront to configure the Native Receiver.

Let me know if you have questions in the comments below! BC
