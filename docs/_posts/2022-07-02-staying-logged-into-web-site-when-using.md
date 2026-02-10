---
layout: post
title: "Staying logged into a web site when using puppeteer"
date: 2022-07-02 12:00:00 -0600
---

Do you want to stay logged into a site after manually logging in with puppeteer?

This task is not as easy as just specifying the user data directory based on feedback from the following Github thread.

[https://github.com/puppeteer/puppeteer/issues/921](https://github.com/puppeteer/puppeteer/issues/921)

The work around is to save cookies after logging in and then load those cookies in subsequent sessions. Here is the answer from Github.

[https://stackoverflow.com/questions/56514877/how-to-save-cookies-and-load-it-in-another-puppeteer-session#56515357](https://stackoverflow.com/questions/56514877/how-to-save-cookies-and-load-it-in-another-puppeteer-session#56515357)
