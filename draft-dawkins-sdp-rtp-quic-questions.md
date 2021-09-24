---
title: SDP Offer/Answer for RTP using QUIC as Transport - Design Questions
abbrev: SDP O/A for RTP over QUIC
docname: draft-dawkins-sdp-rtp-quic-questions-latest
date:
category: std

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

  RFC2119:
  RFC3261:
  RFC3264:
  RFC3550:
  RFC3551:
  RFC3711:
  RFC4585:
  RFC5104:
  RFC5761:
  RFC5124:
  RFC8174:
  RFC8825:
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
  SDP-attribute-name:
    target: https://www.iana.org/assignments/sdp-parameters/sdp-parameters.xhtml#sdp-att-field
    title: "SDP Parameters - attribute-name"
    date: September 2021
  I-D.ietf-quic-datagram:
  I-D.engelbart-rtp-over-quic:
  I-D.hurst-quic-rtp-tunnelling:
  I-D.rtpfolks-quic-rtp-over-quic:

informative:

  I-D.dawkins-sdp-rtp-quic:
  RFC4145:

--- abstract

This document is a companion document to "SDP Offer/Answer for RTP using QUIC as Transport". That document focuses specifically on the description and registration of SDP "proto" attribute parameters with IANA to allow QUIC to be used as an encapsulation for RTP. In writing that document, it became obvious that decisions about an appropriate SDP description would depend on decisions and different proposals for those encapsulations  made different assumptions.

This document includes questions that have come up in efforts to describe a general-purpose set of "QUIC/RTP" IANA registrations, and (as discussions progress) suggested answers for those questions.

--- middle

# Introduction {#intro}

This document is a companion document to "SDP Offer/Answer for RTP using QUIC as Transport" ({{I-D.dawkins-sdp-rtp-quic}}). That document focuses specifically on the description and registration of SDP ({{RFC8866}}) "proto" attribute parameters with IANA ({{SDP-parameters}}) to allow QUIC to be used as an encapsulation for RTP ({{RFC3550}}). In writing that document, it became obvious that decisions about an appropriate SDP description would depend on decisions about RTP encapsulations onto the QUIC protocol ({{RFC9000}}), and different proposals for those encapsulations ({{I-D.engelbart-rtp-over-quic}}, {{I-D.hurst-quic-rtp-tunnelling}}, and {{I-D.rtpfolks-quic-rtp-over-quic}}) made different assumptions.

This document includes questions that have come up in efforts to describe a general-purpose set of "QUIC/RTP" IANA registrations, and (as discussions progress) suggested answers for those questions.

## Notes for Readers {#readernotes}

(Note to RFC Editor - if this document ever reaches you, please remove this section)

This document is intended for publiication as a standards-track RFC in the IETF stream, but has not been adopted by any IETF working group, and does not carry any special status within the IETF.

## Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 ({{RFC2119}}) ({{RFC8174}}) when, and only when, they appear in all capitals, as shown here.

##Scope of this document {#scope}

{{I-D.dawkins-sdp-rtp-quic}} will necessarily reflect questions and answers contained in this document, but that discussion material will not be appropriate for inclusion in a draft that focuses on SDP description and IANA registration. This document might be worth publishing on its own, or some of its contents might might be included in one or more documents that describe RTP encapsulation in the QUIC protocol.

# Questions (and, Eventually, Answers) {#questions}

## Useful AVP Profiles

{{SDP-parameters}} contains four classes of AVP profiles:

 * RTP/AVP ("RTP Profile for Audio and Video Conferences with Minimal Control"), as defined in {{RFC3551}},
 * RTP/SAVP ("The Secure Real-time Transport Protocol (SRTP)"), as defined in {{RFC3711}},
 * RTP/AVPF ("Extended RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/AVPF)"), as defined in {{RFC4585}}, and
 * RTP/SAVPF ("Extended Secure RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/SAVPF)"), as defined in {{RFC5124}}.

 We could register all four over QUIC, but if we can cut down on the number of options for implementers, we might achieve better interoperability.

### Can We Assume the Use of "Extended Profiles"?

We could register both AVP and AVPF profiles, but do we need to register both?

### Is "Secure RTP Encapsulated in UDP" Equivalent to "RTP Encapsulated in QUIC"?

RTP that is encapsulated in QUIC payloads will always be encrypted {{RFC9000}}. So, should we register (for example)

- QUIC/RTP/AVPF, knowing that any RTP payload using the QUIC protocol is encrypted, but is not SDES, or
- QUIC/RTP/SAVPF, because QUIC encryption provides at least an equivalent level of protection to SDES, or
- both QUIC/RTP/AVPF and QUIC/RTP/SAVPF, to minimize the changes necessary for existing RTP applications to add support for QUIC encapsulation?

### Encapsulations in Datagrams and Streams

We note that {{SDP-parameters}} contains registrations for both RTP encapsulated in UDP datagrams and RTP encapsulated in TCP streams.

If we wanted to allow the same level of flexibility for QUIC/RTP, we could register (for example) QUIC/DGRAM/RTP, mapped onto QUIC datagrams ({{I-D.ietf-quic-datagram}}), and QUIC/STREAM/RTP, mapped onto QUIC streams ({{RFC9000}}), reusing terminology from the Berkley Sockets API.

Should we do that? If so, starting out that way would be better than starting out with QUIC/RTP and then adding QUIC/STREAM/RTP later.

## Useful Support For Existing RTP Extensions

Although these questions may not directly impact the descriptions and IANA registrations in {{I-D.dawkins-sdp-rtp-quic}}, they are likely worth mentioning in that document.

It seems useful to confirm that we can assume support for "Multiplexing RTP Data and Control Packets on a Single Port", as described in {{RFC5761}}.

It seems useful to confirm that we can assume support for Media Multiplexing ("BUNDLE"), as described in {{RFC8843}}.

Are there other RTP extensions that we can assume support for?

## Feedback Mechanisms

RTP has relied on RTCP as its feedback mechanism for decades, but that mechanism has evolved over time, with the addition of AVPF feedback ({{RFC4585}}), and subsequent extensions (for example, the codec control messages defined in {{RFC5104}}).

Can we assume that RTP applications using QUIC as their transport encapsulation will continue to use AVPF as the basis for feedback mechanisms, largely unchanged?

- Perhaps some applications will do so.
- For example, {{I-D.engelbart-rtp-over-quic}} proposes that QUIC/RTP implementations may not need to support some RTCP messages, if QUIC itself provides equivalent functionality.
- Conversely, {{RIST-Simple-Prof}} extends the RTP/AVPF bitmasked-based retransmission request with its own range-based retransmission request.

## Potential Extensions To QUIC and QUIC-related Specifications

Because the topics in this section are speculative, it's not clear whether they would have any impact on SDP description and IANA registration in {{I-D.dawkins-sdp-rtp-quic}}.

### Support for NAT Traversal

From {{RFC9000}}, Section 8.2:

> Path validation is not designed as a NAT traversal mechanism. Though the mechanism described here might be effective for the creation of NAT bindings that support NAT traversal, the expectation is that one endpoint is able to receive packets without first having sent a packet on that path. Effective NAT traversal needs additional synchronization mechanisms that are not provided here.

It's worth noting that the driving use cases for the first version of IETF QUIC have been for HTTP-based web access, where the capability described in {{RFC9000}} was sufficient, while many existing applications using RTP also require support for NAT traversal. If we need NAT traversal for QUIC/RTP applications, we need to look at existing NAT traversal mechanisms, and determine whether they are sufficient, and can be included in SDP Offer/Answer for QUIC/RTP communication without modification.

### QUIC Datagram Multiplexing

{{I-D.ietf-quic-datagram}} does not provide support for a standardized datagram multiplexing identifier, for reasons described in Section 5.1, and at greater length in the Github issue about this (at https://github.com/quicwg/datagram/issues/6).

The high-level explanation is that there was considerable support for adding a datagram multiplexing identifier to the datagram frame type, but much less agreement about what effect that identifier would have, and whether the QUIC transport layer would be responsible for any particular processing, or whether this processing would necessarily be performed by the application.

In general, that's a fine plan. The question for this document is whether there is enough similarity for various applications using QUIC/RTP that specifying an RTP-specific datagram multiplier would provide better predictability and ability to multiplex datagrams from different applications without modifying those applications when they encounter each other for the first time.

# IANA Considerations

This document makes no requests of IANA.

# Security Considerations

This document is intended as the basis for discussion about protocol mechanisms that will be described in other documents. As a discussion paper, this document introduces no new security considerations, and any new security considerations resulting from those discussions should be included in the documents that actually describe protocol mechanisms.

# Acknowledgments

Thanks to thse folks for helping to improve this draft:

* Colin Perkins
* Richard Bradbury

(Your name also could appear here. Please comment and contribute, as described in "Contribution and Discussion Venues for this draft".).
