---
title: SDP Offer/Answer for RTP using QUIC as Transport - Design Questions
abbrev: SDP O/A for RTP over QUIC
docname: draft-dawkins-sdp-rtp-quic-questions-latest
date:
category: info

ipr: trust200902
area: applications
workgroup: AVTCORE/MMUSIC Working Groups
keyword: Internet-Draft QUIC RTP SDP

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -
  ins: S. Dawkins
  name: Spencer Dawkins
  organization: Tencent America LLC
  country: United States of America
  email: spencerdawkins.ietf@gmail.com

normative:

  RFC3264:
  RFC3550:
  RFC3551:
  RFC3711:
  RFC4585:
  RFC4961:
  RFC5104:
  RFC5761:
  RFC5124:
  RFC7301:
  RFC7728:
  RFC8082:
  RFC8843:
  RFC8866:
  RFC9000:
  RFC9001:

  RIST-Simple-Prof:
    target: https://vsf.tv/download/technical_recommendations/VSF_TR-06-1_2020_06_25.pdf
    title: "Reliable Internet Stream Transport (RIST) Protocol Specification – Simple Profile"
    date: September 2021
  SDP-parameters:
    target: https://www.iana.org/assignments/sdp-parameters/sdp-parameters.xhtml#sdp-parameters-2
    title: "SDP Parameters - Proto"
    date: September 2021

  I-D.ietf-quic-datagram:
  I-D.ietf-quic-http:
  I-D.engelbart-rtp-over-quic:
  I-D.hurst-quic-rtp-tunnelling:
  I-D.rtpfolks-quic-rtp-over-quic:

informative:

  I-D.dawkins-sdp-rtp-quic:
  I-D.gruessing-moq-requirements:

--- abstract

This document is a companion document to "SDP Offer/Answer for RTP using QUIC as Transport". That document focuses on the description and registration of SDP "proto" attribute parameters with IANA, to allow applications that rely on SDP Offer/Answer to negotiate the QUIC protocol as an encapsulation for RTP.

In writing that document, it became obvious that decisions about an appropriate SDP description would depend on decisions about the way RTP would be encapsulated in QUIC, and different proposals for those encapsulations had made different assumptions. Given that none of these proposals have been adopted by an IETF working group yet, it's not appropriate to try to base a general-purpose set of "QUIC/RTP" IANA registrations on any one of them, so this document includes the questions that have come up, and (as discussions progress) suggested answers for those questions.

--- middle

# Introduction {#intro}

This document is a companion document to "SDP Offer/Answer for RTP using QUIC as Transport" ({{I-D.dawkins-sdp-rtp-quic}}). That document focuses on the description and registration of SDP ({{RFC8866}}) "proto" attribute parameters with IANA ({{SDP-parameters}}), to allow applications that rely on SDP Offer/Answer ({{RFC3264}}) to negotiate the QUIC protocol({{RFC9000}}) as an encapsulation for RTP ({{RFC3550}}).

In writing that document, it became obvious that decisions about an appropriate SDP description would depend on decisions about the way RTP would be encapsulated in QUIC, and different proposals for those encapsulations ({{I-D.engelbart-rtp-over-quic}}, {{I-D.hurst-quic-rtp-tunnelling}}, and {{I-D.rtpfolks-quic-rtp-over-quic}}) had made different assumptions. Given that none of these proposals have been adopted by an IETF working group yet, it's not appropriate to try to base a general-purpose set of "QUIC/RTP" IANA registrations on any one of them, so this document includes the questions that have come up, and (as discussions progress) suggested answers for those questions.

## Notes for Readers {#readernotes}

(Note to RFC Editor - if this document ever reaches you, please remove this section)

This document is intended to stimulate discussion about how proponents of "RTP over QUIC" expect that to work, recognizing that not everyone has the same goals in mind, but it understanding what the choices are will likely be helpful in making those choices, especially when the results of a choice provide direction that will allow implementers to agree on strategies and reuse as much code as possible.

## Relationship with other documents

Work on this draft and on {{I-D.gruessing-moq-requirements}} began in isolation from each other, and the authors of both drafts are still working out the scope of each draft, so there is some overlap between the drafts. At this time, it's worth saying that

* {{I-D.gruessing-moq-requirements}} is intended to capture requirements for the encapsulation of Media Transport Protocols in the QUIC protocol

* This document is intended to capture questiona that will affect the specification of Session Description Protocol description for these encapsulated Media Transport Protocols

Both specifications are open for issues and pull requests, and since there is a common author, assume that any contribution you make to either draft in GitHub will end up in the right place.

{{I-D.dawkins-sdp-rtp-quic}} will almost certainly reflect answers  to the questions contained in this document, but the discussion material in this document will not be appropriate for inclusion in a draft that focuses on SDP description and IANA registration. This document might be worth publishing on its own, but is primarily intended to guide discussion that will feed into {{I-D.dawkins-sdp-rtp-quic}}.

# Questions (and, Eventually, Answers) {#questions}

This version of this document is still very much a starting point for discussion, and additional questions are welcomed, even as we converge on answers.

## Useful AVP Profiles

{{SDP-parameters}} contains four classes of AVP profiles:

 * RTP/AVP ("RTP Profile for Audio and Video Conferences with Minimal Control"), as defined in {{RFC3551}},
 * RTP/SAVP ("The Secure Real-time Transport Protocol (SRTP)"), as defined in {{RFC3711}},
 * RTP/AVPF ("Extended RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/AVPF)"), as defined in {{RFC4585}}, and
 * RTP/SAVPF ("Extended Secure RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/SAVPF)"), as defined in {{RFC5124}}.

 We could register all four over QUIC, but if we can cut down on the number of options for implementers, we might achieve better interoperability.

### Can We Assume the Use of "Extended Audiovisual Profiles"?

We could register both AVP and AVPF profiles, but do we need to register both?

### Is "Secure RTP Encapsulated in UDP" Equivalent to "RTP Encapsulated in QUIC"?

RTP that is encapsulated in QUIC payloads will always be encrypted {{RFC9000}}. So we could register (for example)

- QUIC/RTP/AVPF, knowing that any RTP payload using the QUIC protocol is encrypted, but is not encrypted using SDES, or
- QUIC/RTP/SAVPF, because QUIC encryption provides at least an equivalent level of protection to SDES, or
- both QUIC/RTP/AVPF and QUIC/RTP/SAVPF, to minimize the changes necessary for existing RTP applications to add support for QUIC encapsulation?

#### Proposed Answer

We note that it is possible, and perhaps likely, that RTP-over-QUIC would be used in "mixed" environments, where one of the functions of an RTP mixer/translator is to connect RTP endpoints that are RTP-over-QUIC-enabled with RTP endpoints that are not.

In this case, the only way to ensure encryption over every hop between two endpoints is to use one of the RTP profiles that includes security.

### Encapsulations in Datagrams and Streams

We note that {{SDP-parameters}} contains registrations for both RTP encapsulated in UDP datagrams and RTP encapsulated in TCP streams.

If we wanted to allow the same level of flexibility for QUIC/RTP, we could register (for example) QUIC/DGRAM/RTP, mapped onto QUIC datagrams ({{I-D.ietf-quic-datagram}}), and QUIC/STREAM/RTP, mapped onto QUIC streams ({{RFC9000}}), reusing terminology from the Berkeley Sockets API.

Should we do that? If so, starting out that way would be better than starting out with QUIC/RTP and then adding QUIC/STREAM/RTP later.

## Useful Support For Existing RTP Extensions included in SDP Offer/Answer

At least one of the goals for QUIC/RTP encapsulation is that QUIC/RTP applications would not require more UDP ports than existing RTP applications.
For this reason,

- It seems useful to confirm that we can assume support for "Multiplexing RTP Data and Control Packets on a Single Port", as described in {{RFC5761}}.
- It seems useful to confirm that we can assume support for Media Multiplexing ("BUNDLE"), as described in {{RFC8843}}.

**Editor's Note** We recognize that the usage of RTP/RTCP ports in (for example) RTP/SAVPF, which runs over UDP, will be more nuanced in (for example) QUIC/RTP/SAVPF, which can use UDP ports in the same way as RTP/SAVPF, but can also use mechanisms such as Application-Layer Protocol Negotiation (ALPN) Protocol IDs to multiplex multiple applications on a single port, as further discussed in {{alpn}} below. This section should be read with {{alpn}} in mind. One possibility is that this discussion turns out to be about minimizing the need for more QUIC connections, which does translate directly to minimizing UDP ports.

Are there other RTP extensions that we should assume support for?

## Feedback Mechanisms

RTP has relied on RTCP as its feedback mechanism for decades, as that mechanism has evolved over time, with the addition of AVPF feedback ({{RFC4585}}), and subsequent extensions (for example, the codec control messages defined in {{RFC5104}} and extended in {{RFC7728}} and {{RFC8082}}).

Should we assume that RTP applications using QUIC as their transport encapsulation will continue to use AVPF as the basis for feedback mechanisms, largely unchanged?

It seems likely that many implementations that already utilize (S)AVPF as their feedback mechanisms will continue to do so for QUIC/RTP/AVPF sessions. These implementations already work, and continuing to use the same feedback mechanism for QUC/RTP/AVPF sessions will minimize the amount of new development and testing required for these implementations to add support for QUIC/RTP/AVPF.

We also recognize that {{RIST-Simple-Prof}} extends the RTP/AVPF bitmasked-based retransmission request with its own range-based retransmission request, as an indication that this feedback mechanism remains in the mainstream of RTP/RTCP protocol usage.

However, {{I-D.engelbart-rtp-over-quic}} proposes that QUIC/RTP implementations may not need to support some RTCP messages, if QUIC itself provides equivalent functionality.

If there's not one answer to the feedback mechanism question, the necessary feedback mechanism will need to be included in the SDP Offer/Answer exchange.

## Potential Extensions To QUIC and QUIC-related Specifications

Because the topics in this section are speculative, it's not clear whether they would have any impact on SDP description and IANA registration in {{I-D.dawkins-sdp-rtp-quic}}. They are included in this document for completeness.

### QUIC Datagram Multiplexing

{{I-D.ietf-quic-datagram}} does not provide support for a standardized datagram multiplexing identifier, for reasons described in Section 5.1, and at greater length in the Github issue about this (at https://github.com/quicwg/datagram/issues/6).

The high-level explanation is that there was considerable support for adding a datagram multiplexing identifier to the datagram frame type, but much less agreement about what effect that identifier would have, and whether the QUIC transport layer would be responsible for any particular processing, or whether this processing would necessarily be performed by the application. For example, some proponents of adding datagram multiplexing expected to use multiplexing identifiers as ways of distinguishing between datagrams of different priorities, and other propoents expected to used multiplexing identifiers to distinquish between datagrams that were part of multi-datagram conversations (more like streams, but using the datagram frame types).

Given that there was no agreement on the functionality that datagram multiplexer identifiers would be used for, or what requirements this addition to the protocol would place to QUIC transport processing, the best decision for the QUIC protocol seemed to be that any application that needed this capability could trivially add its own multiplexing identifiers at the beginning of the Datagram Data field ({{I-D.ietf-quic-datagram}}).

In general, that's a fine plan. The question for this document is whether there is enough similarity among various applications using QUIC/RTP that specifying an RTP-specific datagram multiplier would provide better predictability and ability to multiplex datagrams from different applications without modifying those applications when they encounter each other for the first time.

### RTP Destination Transport Addresses, Bundles, and QUIC Connection-IDs {#alpn}

RTP has more than one way to identify endpoints, whether {{RFC3550}}-style destination three-tuples, or {{RFC4961}}-style Symmetric RTP and RTCP, or {{RFC8843}}-style BUNDLE transport, but all are based on IP addresses and port addresses.

The QUIC protocol also starts out with five-tuple awareness as it establishes a connection and performs TLS 1.3 handshake between the client and server, as described in {{RFC9001}}, and performs path validation, as described in {{RFC9000}}, but can also create multiple connection identifiers using different transport addresses that will be associated with the same connection, and when another path has been validated and associated with a known connection identifier, the QUIC endpoint can begin receiving packets for the same connection from an entirely different transport address, with no other signaling. This change of transport addresses might be the result of QUIC-level "connection migration", but might also be the result of NAT rebinding.

Applications using QUIC/RTP will need some way of managing QUIC connection identifiers, rather than transport addresses. This points to at least one potential need for a mapping of RTP semantics over QUIC, equivalent to {{I-D.ietf-quic-http}}.

If QUIC/RTP applications will be making use of something like Application Layer Protocol Negotiation (ALPN) ({{RFC7301}}) identifiers to select QUIC/RTP processing, as HTTP/3 does, this may point to another potential need for a mapping.

### Support for NAT Traversal

It's worth noting that the driving use cases for the first version of the IETF QUIC protocol have been for HTTP-based web access, where the capability described in Section 8.2 of {{RFC9000}} was sufficient:

> Path validation is not designed as a NAT traversal mechanism. Though the mechanism described here might be effective for the creation of NAT bindings that support NAT traversal, the expectation is that one endpoint is able to receive packets without first having sent a packet on that path. Effective NAT traversal needs additional synchronization mechanisms that are not provided here.

Some existing RTP applications share this characteristic - at least one RTP endpoint can receive a packet without having previously sent packet on that path. For these applications, current QUIC functionality will be sufficient.

For other RTP applications, we may need a QUIC extension that provides NAT traversal, and we may need to include information about NAT traversal in SDP Offer/Answer to enable QUIC/RTP communication.

# IANA Considerations

This document makes no requests of IANA.

# Security Considerations

This document is intended as the basis for discussion about protocol mechanisms that will be described in other documents. As a discussion paper, this document introduces no new security considerations, and any new security considerations resulting from those discussions should be included in the documents that actually describe protocol mechanisms.

# Acknowledgments

Thanks to these folks, for authoring various "QUIC over RTP" drafts for specific use cases, which included their understanding of SDP aspects of their proposals:

* Jörg Ott, Roni Even, Colin Perkins, and Varun Singh, authors of {{I-D.rtpfolks-quic-rtp-over-quic}}
* Sam Hurst, author of {{I-D.hurst-quic-rtp-tunnelling}}
* Jörg Ott and Mathis Engelbart, authors of {{I-D.engelbart-rtp-over-quic}}

Thanks to these folks for helping to improve this draft by commenting, proposing text, or correcting confusion:

* Colin Perkins
* Richard Bradbury
* Stephan Wegner

(Your name also could appear here. Please comment and contribute, as described in "Contribution and Discussion Venues for this draft" above)
