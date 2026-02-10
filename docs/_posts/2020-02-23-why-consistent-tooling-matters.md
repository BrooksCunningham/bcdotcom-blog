---
layout: post
title: "Why consistent tooling matters"
date: 2020-02-23 12:00:00 -0600
---

I have always known that there is a lot of tribal knowledge within the team that I am a part of. I also understand that the silo'ed knowledge is undesirable, but until recently I did not really see the extent where this can be problematic. We become too comfortable with going through inefficient workflows to solve problems because that is what we are used to. Tooling could be built to make the workflows more efficient, but the cost in time to build out the tools compared to going through the sub-optimal workflows was not worth paying in the moment. It was much easier to just train people on the nuance of how the systems worked and then demonstrate the sub-optimal way to complete the task. However, this methodology fell apart and was not scalable as it became necessary to aggressively on-board new engineers.

Once it became a requirement to on-board product support engineers to the product that I was helping maintain, I was essentially starting from the ground up with a bunch of new hires for a geographically dispersed team. The existing product support team members are tech savvy, but the technology that I had been supporting works very different from anything that they had worked with previously. As I was doing some of the training and explaining how to troubleshoot cases I would often get the question "How do you know to look at x system when y problem happens?". I was really bothered by these types of questions because these were largely undocumented Product quirks that the the existing Support team and myself would stumble upon and remember the pain of troubleshooting the low level mechanics of what could be causing x issue.

It is important for team members to all to use the same methodology when troubleshoot similar cases. The lack of tooling created a per-requisite to understand the low level details of systems before they could even begin to understand the approach to solving technical issues. Without consistent tooling there was a wide variety of process and methodology for solving cases. Everyone was left to their own devices to figure out the what they could see as the best way to solve cases. Useful tooling does a few things:

1. Provides mechanisms for troubleshooting specific issue
2. Provides a shared language for how issues have been troubleshot and what troubleshooting to do next.
3. Reduces on-boarding time

I will go through each of the 3 items above since they require their own more in-depth explanation.

## Provides mechanisms for troubleshooting specific issue

PKI is very large topic with many complex pieces. It is a common task for Support Engineers to check the validity of a certificate. My preferred way of checking the validity of a certificate is to use tooling in Openssl. Below is the function for reference:

```bash
function checkcert {
echo "---------------------------"
echo "Certificate Valid Dates"
echo "---------------------------"
true | openssl s_client -connect $1:443 2>/dev/null | openssl x509 -noout -dates
echo "---------------------------"
echo "Certificate CN and DNS Info"
echo "---------------------------"
true | openssl s_client -connect $1:443 2>/dev/null | openssl x509 -noout -text | grep DNS:
echo "Issuer"
echo "---------------------------"
true | openssl s_client -connect $1:443 2>/dev/null | openssl x509 -noout -issuer
echo "---------------------------"
echo "Check Hostname Validity"
echo "---------------------------"
true | openssl s_client -connect $1:443 2>/dev/null | grep 'Verify return code'
}
```

If a support engineer has to validate a certificate, then it is as easy as running **checkcert foo.bar** from the command line to check the validity. I am assuming that SNI is not a factor for this example. There is no need to open a browser to grab a screenshot of text. The output of the command already provides the most relevant information for identifying the validity of a certificate in an easy to consume and share format. Having information formatted in a way that is easy to consume is a critical part to the next point.

## Provides a shared language

IT systems are complex and the roles between organizations are highly specialized. Having a clear and consistent way of communicating between people will result in issues being fixed more quickly. Using similar tooling builds trust and confidence when transitioning work items from individual to another in a team. Also, there is less of a need to explain the steps that have been taken so far to troubleshoot a behavior when methodology that is followed is consistent among a team. Just like a real language, the shared and consistent follow through can feed into how well interaction go with external teams as well. For example, if it is expected that logs will be formatted a certain way when requests are sent to the Product team, then tooling can be built to accommodate this need. By building out the tooling to format logs in a more consumable way, this reduces the amount of time needed to manually do the formatting and also reduces the likelihood that the incorrect format will be used. This is especially important if using the incorrect format may cause delays by unnecessary back and forth on the tickets.

## Reduces on-boarding time

The previous points really feed into this last item, which I believe is the most important. Turnover is a part of any company and no one should attempt to completely eliminate turnover. Instead, focus on creating a great on-boarding program and reduce the amount of time necessary for someone to become proficient at their key responsibilities. For example, if I told an entry level new hire "Openssl is the **only** acceptable way to do certificate validation and if you have questions then check out the 'man' page", then I am failing as someone responsible for on-boarding. This is because the goal is to do certificate validation, not become an expert at Openssl (which is still a good skill to have). We have to think about how to abstract the low level details where it makes sense so that more time and energy may be spent on hire level tasks that add value to the company and customers.

I hope you enjoyed the post. Please leave questions and feedback in the comments below.
