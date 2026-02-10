---
layout: post
title: "Basecamp 2 RSS Feed and Slack Integration"
date: 2018-09-15 12:00:00 -0600
---

## Abstract

The purpose of this post is to demonstrate how Basecamp updates can be automatically pulled into a Slack channel.

## Pre-reqs

Before going any further, the following assumptions must be satisfied.

1. IFTTT must be integrated into your Slack team.
2. The Slack channel that will receive the Basecamp updates must be a public Slack channel.

## Identify the Problem

Slack and Basecamp are both awesome tools in their own right, and both have a distinct purpose for successful project execution. Slack is great for real time troubleshooting and communication when a conference call is not necessary. Whereas Basecamp is great for task management and big picture tracking. What I have found while using both tools independently is that Basecamp can quickly be forgotten in favor of strictly Slack and email communication. This is typically a non-issue for small projects with very few moving parts, but as projects become larger with more teams involved it becomes even more important to keep track of tasks independently through Basecamp. To keep everyone on track and focused on their respective tasks the two tools need to be merged.

## Fix the Problem

The solution to this problem is to pull Basecamp updates into Slack by using [IFTTT](https://ifttt.com/). Basecamp 2 supports RSS feeds that are automatically updated when something new happens within a Basecamp project. See the link below for details.

[https://basecamp.com/help/2/guides/projects/progress#rss-feeds](https://basecamp.com/help/2/guides/projects/progress#rss-feeds)

IFTTT can be used to pull updates from RSS feeds and post new updates into Basecamp. The link below will take you right to the _If This_ portion of the IFTTT applet.

[https://ifttt.com/create/if-new-feed-item?sid=3](https://ifttt.com/create/if-new-feed-item?sid=3)

Now here is where authentication comes into play and things are a bit trickier as the following links from StackOverflow will articulate.

- [http://stackoverflow.com/questions/2100979/how-to-authenticate-an-rss-feed](http://stackoverflow.com/questions/2100979/how-to-authenticate-an-rss-feed)
- [http://stackoverflow.com/questions/920003/is-it-possible-to-use-authentication-in-rss-feeds-using-php](http://stackoverflow.com/questions/920003/is-it-possible-to-use-authentication-in-rss-feeds-using-php)

A lot of RSS feeds are accessed via an unauthenticated means. However, Basecamp (thankfully) protects project RSS feeds so that not just anybody can view your project details. To authenticate against an RSS feed, the URL must be constructed in the following manner.

```
https://username:password@basecamp.com/<account_id>/projects/<project_id>.atom
```

The `<account_id>` and `<project_id>` pieces of the above URL will be specific to your specific Basecamp identifiers. The username and password will be your username and password that you can use to access Basecamp. Since this is a URL that is used to access the RSS feed, then your username may need to be modified. I'll use the following email as an example.

```
zergrush@allyourbase.com
```

The ampersand (`@`) must be URL encoded when used for the RSS feed. The following is an example of a properly constructed Basecamp URL.

```
https://zergrush%40allyourbase.com:password1234@basecamp.com/9999999/projects/99999999.atom
```

You can validate that the URL should work by copy/pasting it in your browser. If you do not see an RSS feed, then check to make sure that any other special characters in your username or password are encoded properly. Below is my favorite site for URL encoding and decoding.

[http://meyerweb.com/eric/tools/dencoder/](http://meyerweb.com/eric/tools/dencoder/)

If IFTTT accepts the RSS feed URL, then congrats! The hard part is over. You can then select Slack for the _Then That_ action and use the Post to Channel option. One thing to note, is that the Slack channel must be a public channel for this integration to work. You can also customize how the RSS message is sent to Slack within the IFTTT settings. That's all there is to it. Test by doing anything on the Basecamp project associated with the RSS feed you configured, and then Slack should reflect the update in about 5 - 10 min. I hope anyone reading has found this article beneficial. Let me know in the comments below if you have any questions! Thanks, Brooks
