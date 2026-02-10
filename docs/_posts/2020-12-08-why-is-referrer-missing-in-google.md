---
layout: post
title: "Why is referrer missing in Google Analytics?"
date: 2020-12-08 14:44:00 -0600
---

Learn from my mistakes and save some time. I spent way more time on this than needed. If your referrer is missing from your Google Analytics and you use Google Tag Manager, then make sure you set the field **alwaysSendReferrer** to **true** per the screenshot below.

![Google Tag Manager alwaysSendReferrer setting](/assets/images/why-is-referrer-missing-in-google/gtm_always_send_referrer.png)

This field is necessary to always send the referrer as the **dr** parameter in web requests to Google Analytics. For more information on the referrer, see the following link.

[Google Analytics Field Reference - Referrer](https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#referrer)

Have a great day!

Brooks
