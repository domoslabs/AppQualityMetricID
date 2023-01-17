---
title: "Requirements for a Network Quality Framework Useful for Applications, Users and Operators"
abbrev: "app-quality-metric-reqs"
category: info

docname: draft-teigen-ippm-app-quality-metric-reqs-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Transport"
workgroup: "IP Performance Measurement"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "IP Performance Measurement"
  type: "Working Group"
  mail: "ippm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ippm/"
  github: "domoslabs/AppQualityMetricRFC"
  latest: "https://domoslabs.github.io/AppQualityMetricRFC/draft-teigen-ippm-app-quality-metric-reqs.html"

author:
 -
    fullname: Bjørn Ivar Teigen
    organization: Domos
    email: bjorn@domos.no
    street: Gaustadalléen 21
    code: 0349
    country: NORWAY
 -
    fullname: Magnus Olden
    organization: Domos
    email: magnus@domos.no
    street: Gaustadalléen 21
    code: 0349
    country: NORWAY

normative:

informative:
  RFC6049: # Spatial Composition of Metrics
  #RFC8033: # PIE
  #RFC8290: # FQ_CoDel
  RFC9318: # IAB Workshop report
  SHARED:
    title: "The Internet is a Shared Network"
    author:
      name: "Stuart Cheshire"
    format:
      PDF: https://www.iab.org/wp-content/IAB-uploads/2021/09/draft-cheshire-internet-isshared-00b.pdf
  TR-452.1:
    title: "TR-452.1: Quality Attenuation Measurement Architecture and Requirements"
    author:
      org: Broadband Forum
    date: September 2020
    format:
      PDF: https://www.broadband-forum.org/download/TR-452.1.pdf
  BITAG:
    title: Latency Explained
    author:
      org: BITAG
    format:
      PDF: https://www.bitag.org/documents/BITAG_latency_explained.pdf
    date: October 2022
  RPM:
    title: Responsiveness under Working Conditions
    target: https://datatracker.ietf.org/doc/html/draft-ietf-ippm-responsiveness
    date: July 2022
  RRUL:
    title: Real-time response under load test specification
    target: https://www.bufferbloat.net/projects/bloat/wiki/RRUL_Spec/
  Bufferbloat:
    title: "Bufferbloat: Dark buffers in the Internet"
    target: https://queue.acm.org/detail.cfm?id=2071893
  Haeri22:
    title: "Mind Your Outcomes: The ΔQSD Paradigm for Quality-Centric Systems Development and Its Application to a Blockchain Case Study"
    target: https://www.mdpi.com/2073-431X/11/3/45

--- abstract

This document aims to describe the features and attributes a network quality framework must include to be useful for different stakeholders.
The stakeholders included are developers of Applications, End-Users, and Network Operators and Vendors.
At a high level, End-Users needs a network metric that is understandable.
Application developers need a network metric that allows them to evaluate how well their application is likely to perform given the measured network performance.
Network Operators and Vendors need a metric that facilitates troubleshooting and optimization of their networks.
Existing network quality metrics and frameworks typically address the needs of one or two of these stakeholders, but we have been unable to find one that bridges the needs of all three.

--- middle

# Introduction
The goal of this document is to describe the features a network performance framework must have to be understandable to end-users, useful for application developers, and actionable for network operators. We discuss the needs of each of these stakeholders in turn, and describe what it takes for a network quality framework to meet these needs.

A recent IAB workshop on measuring internet quality for end users identified an important point: Users mostly care about application performance (as opposed to network performance). Among the conclusions is the statement "A really meaningful metric for users is whether their application will work properly or fail because of a lack of a network with sufficient characteristics" {{RFC9318}}.
Because the Internet is a shared network {{SHARED}}, network metrics are seldom constant, nor are they simple functions of load. Their values change over time based on variations in load (amongst other things), and there is no feasible way to predict exactly how conditions will change. A promising way around this problem is to think of network performance metrics as random variables. Changing to a statisical world-view also has an added benefit. This new perspective gives us a way to attack the problem of whether an application is likely wo work properly of fail. When network performance is treated as a randomly distributed quantity it becomes possible to calculate what the *probability of application success* is. In our view *probability of application success* is the user-facing metric that makes the most sense. In the following we describe the necessary features a network quality metric and framework must have to support computing the probability of application success.

Our ultimate goal is to compute the probability of application success. This goal places some requirements on what we must measure. In general, application success depends on the delay of the network links and computational steps involved in making the application function. The delay in turn depends on how the application places load on the network. A suitable network performance metric must be able to desribe the delay characteristics of the network under realistic conditions and in enough detail to compute the probability of application success with satisfactory accuracy and precision. How to measure latency, and how to summarize latency measurements have been explored in several recent online reports {{BITAG}}, {{RRUL}}. Sufficiently comprehensive description of network performance is a necessary condition.

Different applications have different needs from the network, and they put different patterns of load on the network. To provide an answer to whether or not applications will work well or fail, a network quality framework must therefore be able to compare measurements of network performance to many different application requirements. Flexibility of application requirements is a necessary condition.

How can operators take action when measurements show that applications fail too often? We can answer this question if the measured metric(s) support composition {{RFC6049}}. Composition gives us the ability to divide results into sub-results, each measuring the performance of a required sub-milestone that must be reached in time or the application will likely fail. The most intuitive way to think about composition (in our opinion) is as addition and subtraction. For example, if we measure web-page load-times and find they are too often too slow, we may then separately measure DNS resolution time, TCP round-trip time and the time it takes to establish TLS connections to get a better idea of where the problem is. To be maximally useful for operators, a network quality framework should support this kind of analysis.

To summarize, the framework and "meaningful metric" we're looking for should have the following properties:

  1. Capture the information necessary to compute the probability that applications will work well. (Useful for End-users and Application developers)

  2. Compare meaningfully to different application requirements.

  3. Compose, so that the contributions of different sub-outcomes and sub-paths of the network can be quantified. (Useful for Operators and Vendors)

# Design Goal
The overall goal is to describe the requirements for an objective network quality framework and metric that is useful for end-users, application developers and network operators/vendors alike.

# Requirements for end-users
The quality framework should facilitate a metric that is objective, relatable and relatively understandable for an end-user.
We are looking for a middle ground between objective QoS metrics (Throughput, packet loss, jitter, average latency) and subjective but understandable QoE metrics (MOS, 5 star ratings).
The ideal framework should be objective like QoS metrics and understandable like QoE metrics.

If these requirements are met, the end-user can understand if a network can reliably deliver what they actually care about; the outcomes of applications.
Examples are how quickly a web-page loads, the smoothness of a video conference, or whether or not a video game has any lag.

# Requirements from Application and Platform Developers

The framework needs to give developers the ability to describe the network requirements of their application. The network requirements must include all relevant dimensions of network quality so that different applications which are senstive to different network quality dimensions can all evaluate the network accurately. We can’t expect all developers to have network expertise, so to make it easy for developers to use the framework it is key that network requirements can be specified approximately. Therefore, it must be possible to describe both simple and complex network requirements. The framework also needs to be flexible so it can be used with different kinds of traffic and so that extreme network requirements that far exceed the needs of today's applications can also be articulated.

If these requirements are met, developers of application or platforms can state or test their network requirements and evalutate if the network is sufficent for a great application outcome. Both the applications that have networking expertise and those who don't can make use of the framework.

# Requirements for Network Operators and Network Solution Vendors

From an operator perspective, the key is to have a framework that lets operators find the network quality bottlenecks and objectively compare different networks and technologies. To achieve this, the framework must support mathematically sound compositionality (‘addition’ and ‘subtraction’). Why? Network operators rarely manage network traffic end-to-end. If a test is purely end-to-end, the ability to find bottlenecks may be gone. If, however, we could measure end-to-end (e.g., a-b-c-d-e)  and not-end-to-end (e.g., b-c-d-e) and subtract, we can isolate the areas outside the influence of the network operator. In other words we could get the network quality of a-b and b-c-d-e separately. This is key for fault detection and accountability.

By having mathematically correct composition, a network operator can measure two segments separately, perhaps even with different approaches, and add them together to understand the end-to-end network quality. A network equipment vendor may use a different approach to figure out the network quality from the network equipment to the end–user device and to servers, having the possibility of adding these segments will help network operators better understand end-to-end performance.

The quality framework must be useful across lab testing, production network active testing, and monitoring. It must be useful on different time-scales and it can’t have a dependency on network technology or OSI Layer.

If these requirements are met, a network operator can monitor and test their network and understand where the true bottlenecks are, regardless of network technology.

# Discussion of other performance metrics
Many network performance metrics and frameworks for reasoning about them have been proposed, used, and abused througout the years. We present a brief description of some of the most relevant metrics.

For each of the metrics below we discuss, briefly, whether or not they meet each of the three criteria set out in the introduction.

*Average Peak Througphut*

Throughput relates to user-observable application outcomes in the sense that there must be *enough* bandwidth available. Adding extra bandwidth above a certain threshold will at best receive diminishing returns.

Throughput cannot be composed.

Throughput is relatively well understood amongst consumers.

*Average Latency*

TODO

*99th Percentile of Latency*

TODO

*Trimmed Mean of Latency*

TODO

*Round-trips Per Minute*

Round-trips per minute {{RPM}} is a metric and test procedure specifically designed to measure delays as experienced by application-layer protocol procedures such as HTTP GET, establishing a TLS connection and DNS lookups. It therefore measures something very close to the user-perceived application outcomes of HTTP-based applications. RPM loads the network before conducting latency measurements, and is therefore a measure of loaded latency (or working latency) well-suited to detecting bufferbloat {{Bufferbloat}}.

RPM is not composable.

RPM is understandable to end-users.

*Quality Attenuation*

Quality Attenuation is a network performance metric that combines latency and packet loss into a single variable {{TR-452.1}}.

Quality Attenuation relates to user-observable outcomes in the sense that user-observable outcomes can be measured using the Quality Attenuation metric directly, or the quality attenuation value describing the time-to-completion of a user-observable outcome can be computed if we know the quality attenuation of each sub-goal required to reach the desired outcome.

Quality Attenuation is composable because convolution of quality attenuation values allow us to compute the time it takes to reach specific outcomes given the quality attenuation of each sub-goal {{Haeri22}}.

Quality Attenuation is not easily understandable for end-users or application developers. More work is needed to make it useful for these groups.

*Summary of performance metrics*

| Metric                         | Capture probability of good/bad application outcome | Composable | Compare to a variety of application requirements | Understandable for end-users and App devs |
|--------------------------------|-----------------------------------------------------|------------|--------------------------------------------------|-------------------------------------------|
| Average latency                | No                                                  | Yes        | Yes                                              | Yes                                       |
| Average Peak Throughput        | No                                                  | No         | Yes                                              | Yes                                       |
| 99th Percentile of Latency     | No                                                  | No         | Yes                                              | Yes                                       |
| Trimmed mean of latency        | Yes (depending on what is measured)                 | No         | Yes                                              | Yes                                       |
| Round Trips Per Minute {{RPM}} | Yes                                                 | No         | Yes                                              | Yes                                       |
| Quality Attenuation            | Yes                                                 | Yes        | Yes                                              | No                                        |

# Conclusion
This work describes requirements for a new network quality framework that extends existing network quality metrics (i.e., RPM {{RPM}}, Quality Attenuation {{TR-452.1}}). We describe a framework which is useful for end-users, network operators, vendors and applications. RPM does a great job of improving visibility of network quality issues beyond throughput, but is inherently about end-to-end tests and is not designed to help network operators monitor, test, and understand their networks from within. Quality Attenuation {{TR-452.1}}, on the other hand, is a great tool for understanding the performance of a network from within, but is not easy to use or understand for end-users or application developers.

Quality attenuation is a network quality metric that meets the three criteria we set out in the introduction; it captures the probabilty of good or bad application outcomes, it is composable, and it can be compared to a variety of application requirements. The part that is still missing is how to present quality attenuation results to end-users and application developers in an understandable way. We believe a per-application (or per application-type) approach is appropriate here. The challenge lies in how to simplify. We must specify how and when it is apporpriate to throw away information without loosing too much precision and accuracy in the results. This depends on the needs and behaviours of different applications, and therefore the IETF seems like a good place to develop this. We need feedback from a wide range of application developers, protocol designers, and other users to make sure the resulting framework is as robust and general as possible.

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
