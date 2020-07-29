# Backpressure

Date: July 31, 2020

The back pressure feature in Zeebe is leading to user issues in the getting started experience.

Related GitHub Issues:
* [Remove single point of failure in the deployment process](https://github.com/zeebe-io/zeebe/issues/4765)
* [ResourceExhausted constantly appears during workflow deployment](https://github.com/zeebe-io/zeebe/issues/3367)

Examples: 

* Oct 19 [RESOURCE_EXHAUSTED: Reached maximum capacity of requests handled](https://forum.zeebe.io/t/resource-exhausted-reached-maximum-capacity-of-requests-handled/731)
* Nov 19 [ResourceExhausted constantly appears during workflow deployment](https://github.com/zeebe-io/zeebe/issues/3367)
* Jul 15 [Slack thread](https://zeebe-io.slack.com/archives/C6WGNHV2A/p1594798294300700)
* Jul 20 [Slack thread](https://zeebe-io.slack.com/archives/C6WGNHV2A/p1595930908388200)

Kristof Jozsa in the July 2020 Slack thread expressed the following:

> I understand you are implementing an existing algorithm, that sounds reasonable. However, the current behaviour results this:
> * someone starts using Zeebe for the first time
> * he/she starts like 3 simple workflows on a cluster of brokers with tons of available resources, gets shot immediately by backpressure on the first attempt
> * we help the newcomer here on Slack, then start discussing this topic again and again
> * goto 1
>
> Is there any value doing this over and over again? Wouldn't it be more reasonable to have a default behaviour which fills up all available resources and then start to crawl? Then if someone complains, you can show them the fantastic backpressure feature and all the fine-tuning options?

## Backpressure feature

The back pressure feature is intended to help regulate the overall performance of the system under high load. When the stream processor in a broker detects a certain level of latency (events queuing at a higher rate than they are being processed) it begins to return GRPC Error 8: RESOURCE EXHAUSTED to incoming requests.

Client applications need to handle this error and implement a back-off/retry or failure strategy. The Node client implements one in the library as a developer affordance, but the other libraries expose application programmers to this error.

## Getting Started Experience

We find that new users stand up a broker or a cluster, write a naive program that does not handle this error condition, and are then surprised and perplexed to encounter this error at relatively low volumes of throughput.

*Recommendation* 

For these users, we should make sure that the broker does not respond in this way. I'm not sure how the backpressure algorithm is implemented, but maybe it needs to only kick in when there is some absolute minimum of queued work, so that it never triggers at low volumes, even when there is latency.

Or, we turn off back-pressure by default - for the getting started experience - and make it an advanced configuration for production deployment.

## Performance profiling

Some users encounter this error condition when load-testing a cluster to map its performance envelope.

Examples:

* June 17 [Slack Thread](https://zeebe-io.slack.com/archives/C6WGNHV2A/p1592332635147000)

The [documentation on configuring backpressure](https://docs.zeebe.io/operations/backpressure.html) describes the various backpressure algorithms that are available.

*Recommendation* 

In interacting with these users, we need a clear pathway for them to follow:

* Set up a specific load scenario of instances starting per second / workers serving the jobs.
* Run the workload with backpressure off.
* Turn on backpressure (with instructions on how to do that).
* Use algorithm X.
* Rerun the workload.
* Try algorithm Y.
* Rerun the workload.
* Change backpressure parameter Z. If you see this, then try value V1, if you see this other thing, try value V2.
* ...etc




