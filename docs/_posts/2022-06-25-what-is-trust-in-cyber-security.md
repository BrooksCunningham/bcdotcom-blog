---
layout: post
title: "What is trust in cyber security?"
date: 2022-06-25 10:26:00 -0600
---

**What is trust?** The philosophy of trust is fascinating. The definition of trust is "Assured reliance on the character, ability, strength, or truth of someone or something."

Where does the trustor's belief come from? My point of view is that the trustor must have a past experience or foundational belief on behalf of the trustor to inform the level of trust or distrust the trustor will have in the trustee. Without some prior knowledge to an interaction, there is likely to be no foundation for trust or distrust. Every adult human on the face of the earth will have a weighted perspective on how much they believe a trustee will act in a way that is beneficial to the trustor.

So far this post has been a very philosophical discussion. Why am I even talking about the concept of trust? In information security, trust is the bedrock for nearly every interaction (or at least it should be). When a person attempts to redeem a gift card online, the web application owner assumes that if valid card details are entered, then that must be the owner of the gift card. The online shop will then add the balance of the gift card to the person's account. It would be amazing if the world was this simple, but online fraudsters will take advantage of this inherent trust. Fraudsters can brute force gift card validation endpoints using automation (aka bots) to redeem balances on gift cards.

## How can a website owner distinguish between legitimate users and fraudsters for these cases?

There are a number of identifiers that are available with a given request including:

- Source of the traffic. (IP and Geo IP)
- Owner of the source traffic. IP ASN Owner and OrgName.
- HTTP request headers
- Rate of traffic

This is not an exhaustive list and there are many sub categories of these different fields that could be utilized as well. This is especially true with rate of traffic. If there are a million requests being sent within a 5 minute time frame from a single user, then that would likely be considered abusive or fraudulent by most web applications. These 5 million requests would not be considered trustworthy since it vastly exceeds the normal user usage. However, there are applications where this may be acceptable behavior. What qualifies as "normal" requires some prior knowledge or definition to define what "normal" really is.

We cannot infer trust in a vacuum. We must rely on prior knowledge to guide if something or someone is trustworthy. As we continue to progress in cyber security to fight fraud, it will be interesting to see how an individual's history is recorded for good or bad behavior.

## Questions for a later post

If my online persona violates the ToS for a site, then does that get recorded somewhere? Should it? When should it be a requirement for my real or true identity to be used for interacting with a site instead of having the ability to use a persona?
