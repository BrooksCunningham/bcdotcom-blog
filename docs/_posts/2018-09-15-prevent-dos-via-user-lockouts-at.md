---
layout: post
title: "Prevent a DOS via user lockouts at NetScaler Gateway"
date: 2018-09-15 00:00:00 -0600
---

Before we begin let me first say... **All NetScaler Gateway landing page customizations are unsupported. Changing the NetScaler Gateway landing page will cause you to have an unsupported environment. I do not condone malicious attempts to lockout user accounts. The purpose of this article is to highlight a current risk and mitigation steps.**

The current recommended configuration for two-factor authentication at NetScaler is available here: [http://support.citrix.com/article/CTX125364](http://support.citrix.com/article/CTX125364)

With the configuration highlighted in the article above, web-based users that authenticate are hitting AD first. Ideally, we would want to follow the authentication workflow that is configured for the Native Receiver. The Native Receiver evaluates RADIUS first, and if this is successful, then the LDAP policy is invoked.

## What is the risk of leaving the configuration exactly how the article has outlined the configuration?

If Bob, a malicious user, knows Alice's username, then Bob could enter a bogus password 3 times and lock Alice's account. Bob could do this as often as he liked until some measure went into place to stop Bob. If Bob knew a lot of usernames and had some knowledge of scripting tools such as JMeter, then he could lockout a large number of user accounts effectively acting as a DOS.

## So what can we do to mitigate such a risk?

The quick and easy way to do it is to reverse the web authentication policies so that they match up with the Native Receiver (RADIUS as primary, LDAP as secondary). However, this will force users to enter their RADIUS passcode before entering their AD username. Most organizations want to have the dynamic pin as the 2nd password for users to enter.

So how can we mitigate the risk AND have the dynamic token as the second password users need to enter? Like in the quick and easy method, we would need to make the RADIUS authentication primary and the LDAP authentication secondary. Now we need to customize some JavaScript on the NetScaler. The file `/vpn/login.js` is what we need to customize. This file can be found under `"/netscaler/ns_gui/vpn/login.js"`.

### Original JavaScript

```javascript
function ns_showpwd_default() {
  var pwc = ns_getcookie("pwcount");
  document.write('' + _("Password"));
  if ( pwc == 2 ) {
    document.write(' 1');
  }
  document.write(':');
  document.write('passwd" size="30" maxlength="127" style="width:100%;">');
  if ( pwc == 2 ) {
    document.write('' + _("Password2") + ' passwd1" size="30" maxlength="127" style="width:100%;">');
  }
  UnsetCookie("pwcount");
}
```

### Revised JavaScript

```javascript
function ns_showpwd_default() {
  var pwc = ns_getcookie("pwcount");
  document.write('' + _("Password"));
  if ( pwc == 2 ) {
    document.write(' 1');
  }
  document.write(':');
  document.write('passwd1" size="30" maxlength="127" style="width:100%;">');
  if ( pwc == 2 ) {
    document.write('' + _("Password2") + ' passwd" size="30" maxlength="127" style="width:100%;">');
  }
  UnsetCookie("pwcount");
}
```

With this configuration, the values `passwd1` and `passwd` are swapped so that when a user POSTs their credentials, NetScaler will evaluate RADIUS before attempting to contact AD.

With this configuration, organizations can remove an avenue for would-be attackers who intend to lockout users.

## Relevant Resources

- [http://support.citrix.com/article/CTX125364](http://support.citrix.com/article/CTX125364)
- [http://support.citrix.com/article/CTX126206](http://support.citrix.com/article/CTX126206)
- [http://support.citrix.com/proddocs/topic/netscaler-gateway-101/ng-connect-custom-theme-page-tsk.html](http://support.citrix.com/proddocs/topic/netscaler-gateway-101/ng-connect-custom-theme-page-tsk.html)

Have you worked at an organization that has come under attack from user lockouts? What have you done to mitigate the threat? Let me know in the comments below and feel free to ask questions! Thanks for reading, BC
