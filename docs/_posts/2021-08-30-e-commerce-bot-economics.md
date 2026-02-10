---
layout: post
title: "E-commerce Bot Economics"
date: 2021-08-30 12:00:00 -0600
---

## What does supply and demand have to do with bots?

For this post, I am not talking about bots that are performing attacks such as SQLi or Account Takeover (ATO). This post will strictly explore the grey area of web scrapping and cart checkout based bots. For folks that have studied economics, you are likely familiar with tried and true supply and demand curve. A quick refresher can be found on [wikipedia](https://en.wikipedia.org/wiki/Supply_and_demand).

## Xbox and PS5 bots

There are a number of tools that exist for going through a checkout process to buy products. The bot operators typically fall into a few different buckets:

- An organization that is trying to buy up as much inventory as possible to resell the product at a mark-up. See, [PS5 resell scalper units sold 2021](https://screenrant.com/ps5-stockx-resell-scalper-units-sold-2021/)
- An individual or organization that sells the tooling to go through a checkout process very quickly. See, [mostadvancedbot.com](https://mostadvancedbot.com/products/target-bot)

The motivation for an organization to buy thousands or even hundreds of thousands of PS5s is because... drumroll.... MONEY! There is an enormous opportunity to buy low and sell high for this highly sought after product.

## Into the economics

There is a substantial delta between what customers are willing to pay for a product and what the product is initially sold for. In a free marketplace, the high demand would be a signal to charge more to customers for the product. Charging a higher sticker price allows demand to go down to meet supply and ultimately result in higher profits for a business. More importantly, this removes the profit margin that can exist in secondary markets. A bot management solution can be a helpful tool for raising the barrier to entry for a bot operator. However, if there is enough of a margin between the price of the product that the primary seller and the price that the end consumer is willing to pay, then bot operators will find a way around any bot management solution. The ultimate solution is to close the gap between the primary market and any secondary or end consumer tooling markets.

## Primary market sellers cannot always raise prices

There are a number of marketplaces where the primary sellers cannot raise prices. For example, with ticketing platforms the ticket prices are often set by the artists that will be performing. This results in many artists that will set ticket prices substantially below market value with the hope that "real fans" will be able to buy the tickets. When scalpers purchase the tickets and then sell them at the actual market value, then the fans get frustrated with the ticket selling vendor. The bot operators then resell the tickets at a market rate on a secondary market with a healthy profit. The artists are then able to deflect any criticism about ticket prices onto the ticket selling vendor for not implementing sufficient ticket scalping measures.

## What is the solution?

The real solution to this problem is an economics solution. There are 2 options. Either increase supply or reduce demand. To continue with the venue ticketing example, increasing supply is a simple concept. If artists do substantially more shows so that more seats are available, then supply goes up. This is a simple concept, but not easy or desirable to implement since it requires much more work for the artists and supporting staff for less of a return. This is the approach that Kid Rock took. I would encourage everyone to listen to [this podcast for details on that use-case](https://www.npr.org/transcripts/671583061). The other approach is to reduce demand, which is also an easy concept. Taylor Swift did this by raising her ticket prices. The following link contains those details: [Higher Concert Ticket Prices](https://econlife.com/2019/12/higher-concert-ticket-prices/).

These solutions are great if the seller has the ability and will to make these changes, but often the seller has a deficit in one of these areas. As a consequence, sellers are forced to examine how to increase the barriers to entry to make it less profitable for secondary markets to operate. An effective bot management solution and identity management solution can be a critical piece in increasing the costs for secondary markets. However, if the gap between the price customers are willing to pay and the available supply remains wide, then secondary markets will remain profitable.

## Where can this mindset be applied?

I would argue that the true customer for companies that sell products at below market value is the supplier. The initial sellers need the ability to charge more for these high demand products, or they will be forced to add more friction in the buying process to try to combat the secondary market. This economics approach may be considered for really any highly sought after product. This includes physical products like shoes (sneaker bots), baby clothes, GPUs, airline tickets, and PS5.

## Bot Operator Tooling

The tooling that is used to buy the product from the primary marketplace can vary from basic scripts using items like the following:

- Browser plug-ins
- Python Requests and Beautiful Soup
- Selenium
- Anti-captcha solutions to defeat captchas
- Residential and mobile network proxies

## Appendix

- [https://www.npr.org/transcripts/671583061](https://www.npr.org/transcripts/671583061)
- [https://econlife.com/2019/12/higher-concert-ticket-prices/](https://econlife.com/2019/12/higher-concert-ticket-prices/)
- [https://screenrant.com/ps5-stockx-resell-scalper-units-sold-2021/](https://screenrant.com/ps5-stockx-resell-scalper-units-sold-2021/)
- [https://mostadvancedbot.com/products/target-bot](https://mostadvancedbot.com/products/target-bot)
- [https://anti-captcha.com/](https://anti-captcha.com/)
- [https://lp.brightdata.com/proxy-network](https://lp.brightdata.com/proxy-network)
- [https://infare.com/products/data-feeds/](https://infare.com/products/data-feeds/)
