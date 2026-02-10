---
layout: post
title: "Resilient Storefront Optimal Gateway Routing with GSLB"
date: 2018-09-15 12:05:00 -0600
---

## Pre-Requisites

Before reading further, if you don't know what StoreFront Optimal Gateway Routing (OGR) is, STOP and check out:

- [eDocs](http://support.citrix.com/proddocs/topic/dws-storefront-26/dws-configure-ha-optimal.html)
- [Sarah Stienhoff's blog - StoreFront Multi-Site Settings](http://blogs.citrix.com/2014/10/13/storefront-multi-site-settings-some-examples/)
- [Sarah Stienhoff's blog - Gateway Integration with StoreFront](http://blogs.citrix.com/2014/10/15/gateway-integration-with-storefront-lessons-learned/)

If you need a refresher on DNS or NetScaler GSLB, then STOP and review eDocs and relevant CTX articles and resources. For information on NetScaler GSLB, see the links below:

- [DNS and GSLB Primer](http://support.citrix.com/article/CTX122619)
- [Citrix NetScaler Global Server Load Balancing Primer: Theory and Implementation](http://support.citrix.com/article/CTX123976)

And if you want to see how DNS works in your own environment, then check out one of my favorite tools to troubleshoot and learn about DNS with [http://digwebinterface.com/](http://digwebinterface.com/). Try resolving your own site with the "trace" option checked and unchecked.

## Abstract

The purpose of this blog is to provide an overview on how GSLB can be used to provide a redundant solution with StoreFront Optimal Gateway Routing (OGR). A few questions will be answered in this blog for a multi-datacenter design.

1. How can we use a single URL for external access?
2. How can we send users to specific datacenter where users' unique data or backend application dependencies reside?
3. How can we deploy a resilient solution to protect against a datacenter outage?

## Let's get into it

Acme is our example customer. Acme has three (3) datacenters, NY, LA, and Atlanta (ATL). Users on the East coast have non-persistent desktops, but have their roaming profile on a file server at NY. Users on the West coast have non-persistent desktops, but have their roaming profile on a file server at LA. All users have access to a unique application that has backend requirements at the ATL datacenter.

So the answer to **question number one** is fairly straight forward. Use GSLB with NetScaler Gateway. The common external FQDN can be "access.acme.com". Three (3) NetScaler Gateway VIPs will reside at each datacenter and will be included in the GSLB configuration.

If we didn't care where we sent users, then we could stop here. However, with the scenario outlined above, we want to avoid the scenario where a NY user (user data is in NY) launches a desktop that is proxied by the LA Datacenter. This user would then have their Citrix session go from client's location --> LA NetScaler Gateway --> NY datacenter, which is certainly not the optimal route. More importantly this routing will utilize precious private site-to-site bandwidth and could be detrimental to a user's experience. This is where OGR comes into play.

### Answering Question #2

How can we send users to a specific datacenter where their unique data or backend application dependencies reside? We want to use site prefixes to make each site unique. For those of you already thinking ahead, yes, a SAN certificate is required for this solution. Below are the prefixes we will use for the example:

- NY NetScaler Gateway VIP = `ny.access.acme.com`
- LA NetScaler Gateway VIP = `la.access.acme.com`
- ATL NetScaler Gateway VIP = `atl.access.acme.com`

With OGR and under normal work conditions we can direct users accessing a XD Site at NY to be proxied by the NY NSG (`ny.access.acme.com`), users accessing a XD Site at LA to be proxied by the LA NSG (`la.access.acme.com`), and users accessing the unique application (AutoCAD) to be proxied by the ATL NSG (`atl.access.acme.com`). With this solution, users are able to authenticate at any site and launch applications that will utilize the public WAN to cross the nation, instead of using potentially costly MPLS connections.

How are users able to authenticate at NY, but still able to launch apps from ATL? Using STAs of course! All NetScaler appliances in the environment will need to be able to communicate with all of the same STAs. For information on STAs, please see: [http://support.citrix.com/article/CTX101997](http://support.citrix.com/article/CTX101997). With this configuration, authentication and application enumeration are separate events from application launch. It is key to understand that fact. Authentication can occur anywhere, but application launch is more granularly specified with unique site prefixes and OGR.

### Answering Question #3 - Adding Resiliency

How can we deploy a resilient solution to protect against a datacenter internet outage? What happens when a construction company's backhoe accidentally severs Acme's internet POP in LA while laying down city infrastructure, but Acme's MPLS connection remains intact?

A unique GSLB vServer exists for each of the site unique prefixes. A separate GSLB vServer also exists for "access.acme.com". Configuring the "access.acme.com" vServer as a backup vServer for all of the GSLB vServers with the site prefix will protect the individual and unique FQDNs against a datacenter failure.

For example, when the LA datacenter's internet connection is broken, the NetScaler appliances at NY and ATL will recognize an outage via either MEP or explicit health monitors. Users are then sent to the available NY and ATL NSG when resolving "la.access.acme.com" and "access.acme.com". Users can then be proxied through the internal MPLS via the available sites. If the MPLS (or other private site to site connection) went down, then StoreFront can be configured with [DR](http://support.citrix.com/proddocs/topic/dws-storefront-26/dws-configure-ha-lb.html), but we will save that talk for another day.

I have included some diagrams to help clarify things. A key thing to keep in mind is that authentication and application launch are two completely separate events and workflows.

The diagram below is for the authentication and application enumeration workflow.

![Authentication and Enumeration Workflow](/assets/images/resilient-storefront-optimal-gateway/blog_auth_workflow_01.png)

The diagram below illustrates the application launch workflow. The thick lines represent normal working conditions. The dotted lines represent the backup workflow in the event that a site is experiencing an outage.

![Application Launch Workflow](/assets/images/resilient-storefront-optimal-gateway/blog_app_launch_01.png)

Thank you for reading. I hope you found this beneficial. Please let me know if you have questions in the comments below.

BC
