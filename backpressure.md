# Backpressure

Date: July 31, 2020

The back pressure feature in Zeebe is leading to user issues in the getting started experience.

Examples: 

* Oct 19 [RESOURCE_EXHAUSTED: Reached maximum capacity of requests handled](https://forum.zeebe.io/t/resource-exhausted-reached-maximum-capacity-of-requests-handled/731)
* Nov 19 [ResourceExhausted constantly appears during workflow deployment](https://github.com/zeebe-io/zeebe/issues/3367)
* Jul 20 [Slack thread](https://zeebe-io.slack.com/archives/C6WGNHV2A/p1595930908388200)

Kristof Jozsa in the July 2020 Slack thread expressed the following:

> I understand you are implementing an existing algorithm, that sounds reasonable. However, the current behaviour results this:
* someone starts using Zeebe for the first time
* he/she starts like 3 simple workflows on a cluster of brokers with tons of available resources, gets shot immediately by backpressure on the first attempt
* we help the newcomer here on Slack, then start discussing this topic again and again
* goto 1
Is there any value doing this over and over again? Wouldn't it be more reasonable to have a default behaviour which fills up all available resources and then start to crawl? Then if someone complains, you can show them the fantastic backpressure feature and all the fine-tuning options?
