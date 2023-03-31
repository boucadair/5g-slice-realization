---
title: "A  Realization of IETF Network Slices for 5G Networks Using Current IP/ MPLS Technologies"
abbrev: "Implementing 5G Transport Slices"
category: info

docname: draft-srld-teas-5g-slicing-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Routing"
workgroup: "TEAS"
keyword:
 - L3VPN
 - L2VPN
 - Slice Service

author:

 -
   fullname: Krzysztof G. Szarkowicz
   role: editor
   organization: Juniper Networks
   city: Wien
   country: Austria
   email: kszarkowicz@juniper.net

 -
   fullname: Richard Roberts
   role: editor
   organization: Juniper Networks
   city: Rennes
   country: France
   email: rroberts@juniper.net

 -
   fullname: Julian Lucek
   organization: Juniper Networks
   city: London
   country: United Kingdom
   email: jlucek@juniper.net

 -
    fullname: Mohamed Boucadair
    role: editor
    organization: Orange
    country: France
    email: mohamed.boucadair@orange.com

 -
   fullname: Luis M. Contreras
   organization: Telefonica
   street: Ronda de la Comunicacion, s/n
   city: Madrid
   country: Spain
   email: luismiguel.contrerasmurillo@telefonica.com
   uri:   http://lmcontreras.com/


contributor:

 -
   fullname: John Drake
   organization: Juniper Networks
   city: Sunnyvale
   country: United States of America
   email: jdrake@juniper.net

 -
   fullname: Ivan Bykov
   organization: Ribbon Communications
   city: Tel Aviv
   country: Israel
   email: ivan.bykov@rbbn.com

 -
   fullname:  Reza Rokui
   organization: Ciena
   city: Ottawa
   country: Canada
   email: rrokui@ciena.com

 -
   fullname: Luay Jalil
   organization: Verizon
   city: Dallas, TX
   country: United States of America
   email: luay.jalil@verizon.com

 -
   fullname: Beny Dwi Setyawan
   organization: XL Axiata
   city: Jakarta
   country: Indonesia
   email: benyds@xl.co.id

 -
   fullname: Amit Dhamija
   organization: Rakuten
   city: Bangalore
   country: India
   email: amit.dhamija@rakuten.com

 -
   fullname: Mojdeh Amani
   organization: British Telecom
   city: London
   country: United Kingdom
   email: mojdeh.amani@bt.com

normative:

informative:

   TR-GSTR-TN5G:
              title: "Technical Report GSTR-TN5G"
              author:
                org: ITU-T
              date: 9 February 2018
              target: https://www.itu.int/dms_pub/itu-t/opb/tut/T-TUT-HOME-2018-PDF-E.pdf

   TS-23.501:
              title: "TS 23.501: System architecture for the 5G System (5GS)"
              date: 2021
              author:
                org: 3GPP
              target: https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3144

   TS-28.530:
              title: "TS 23.530: Management and orchestration; Concepts, use cases and requirements)"
              author:
               org: 3GPP
              date: 2021
              target: https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3273

   O-RAN.WG9.XPSAAS:
              title: "O-RAN.WG9.XPSAAS: O-RAN WG9 Xhaul Packet Switched Architectures and Solutions Version 03.00"
              author:
               org: O-RAN Alliance
              date: 27 February 2022
              target: https://www.o-ran.org/specifications

   NG.113:
          title: "NG.113: 5GS Roaming Guidelines Version 4.0"
          author:
           org: GSMA
          date: 28 May 2021
          target: https://www.gsma.com/newsroom/wp-content/uploads//NG.113-v4.0.pdf

--- abstract

   5G slicing is a feature that was introduced by the 3rd Generation
   Partnership Project (3GPP) in mobile networks. This feature covers slicing
   requirements for all mobile domains, including the Radio Access
   Network (RAN), Core Network (CN), and Transport Network (TN).

   This document describes a basic IETF Network Slice realization model
   in IP/MPLS networks with a focus on the Transport Network fulfilling
   5G slicing connectivity requirements. This realization model reuses many building blocks currently commonly used
   in service provider networks.

--- middle


#  Introduction

   {{!I-D.ietf-teas-ietf-network-slices}} defines a framework for
   network slicing in the context of networks built using IETF
   technologies.  The IETF network slicing framework introduces the
   concept of a Network Resource Partition (NRP), which is simply a
   collection of resources identified in the underlay network.  There
   could be multiple realizations of high-level IETF Network Slice and
   NRP concepts, where each realization might be optimized for the
   different network slicing use cases that are listed in
   {{!I-D.ietf-teas-ietf-network-slices}}.

   This document describes a basic - using only single NRP - IETF
   Network Slice realization model in IP/MPLS networks, with a focus on
   fulfilling 5G slicing connectivity requirements.  This IETF Network
   Slice realization model leverages many building blocks currently
   commonly used in service provider networks.

   The reader may refer to {{?I-D.ietf-teas-ns-ip-mpls}} for more advanced
   realization models.

   A brief 5G overview is provided in {{sec-5g-intro}} for readers' convenience. The reader may refer to {{?RFC6459}} and {{TS-23.501}} for more
   details about 3GPP network architectures.
# Conventions and Definitions {#sec-conv-def}

{::boilerplate bcp14-tagged}

The document uses the terms defined in {{!I-D.ietf-teas-ietf-network-slices}} and additional terms:

Transport Network (TN):
: The term Transport Network is commonly used in mobile networking to represent the connectivity between mobile Network Functions. 

Customer:
: A Customer is an entity that relies on the Provider Network (e.g. WAN) for interconnecting customer sites. In the context of this document, the customer manages and orchestates the 5G Mobile Network. The Customer network hosts notably 5G Network Functions for RAN and CORE Networks.

Provider:
: A provider is responsible for Orchestrating and managing the Provider Network to interconnect customer sites. As per {{!I-D.ietf-teas-ietf-network-slices}}:
* The interconnection service relies on IETF Network Slices (INS).
* The IETF Network Slice Controller (NSC) orchestrates the IETF Network Slices.

Customer Edge (CE):
: The CE is a device managed by the customer that provides logical connectivity to the Provider Network. The logical connectivity is enforced at Layer 2 and/or Layer 3 and is denominated an Attachment Circuit. In the context of this document, examples of CEs include Routers, Switches Firewall, Servers or any Network Functions (CU, DU, UPF…). This document generalizes the definition of a CE with the introduction of Distributed CEs introduced in {{sec-distributed}}.

Provider Edge (PE):
: The PE is a device managed by the Provider that is connected to the CE. The connectivity between the CE and the PE is achieved thanks to an Attachment Circuit. The PE function usually binds ACs to VPN services. This document generalizes the definition of a PE with the introduction of Distributed PEs introduced in {{sec-distributed}}.

Attachment Circuit (AC):

: The attachment circuit is the logical connection that attaches a CE to a PE in the Provider Network. A CE is connected to the PE thanks to one or multiple ACs. ACs are usually bound to a VPN service within the Provider Network. An AC is technology-specific. For consistency with data model terminology (insert ref ???), we assume that an AC is configured on a “bearer”, which represents the underlying connectivity. Examples of ACs are VLANs (AC) configured on a physical interface (bearer) or an Overlay VXLAN EVI (AC) configured on IP underlay (bearer).

5G Network Slice Orchestrator (5GNSO):
: The entity responsible for the orchestration of the End-to-End 5G Slice made up of RAN, Core and Transport Network Domain. The details of the 5GNSO are outside the scope of this document. The 5GNSO interfaces with the IETF NSC to orchestrate the Transport Network.

An extended list of abbreviations used in this document is provided in {{ext-abbr}}.

# 5G Network Slicing Integration in Transport Networks

## Forewords: scope of the Transport Network

Appendix {{sec-5g-intro}} provides an  overview of 5G Networking. It is advised that the reader with limited background in 5G networking consult . It notably introduces the main building blocks of a 5G Network such as the RAN, CORE and Transport Network. The 3GPP specifications loosely define the Transport Network and its integration in RAN and CN domains. Tt is a non 3GPP-management system that interconnects Mobile Network Functions (NFs). Practically, this interconnection (e.g. the TN) may not map with a monolithic architecture and management domain. It is frequently segmented, non-uniform and managed by different entities. For Example, {{fig-1}} depicts a NF deployed in an Edge Data Centers connected to  a NF  in a Public Cloud thanks to a WAN network (e.g. MPLS-VPN service). Here, the TN can be interpreted as an abstraction representing an end-to-end connectivity based on 3 distinct IP networking domains: DC, WAN and Public Cloud. A model for the Transport Network based on Orchestration domains is introduced later in this document. This model permits to define more precisely where IETF Network Slice applies. Additionally, the term Transport Network is used to disambiguate 5G Networking (i.e. RAN and CORE NF Orchestration) with the NF interconnection (e.g. IP, packet-based forwarding). By extension, the disambiguation applies to Transport Network Slicing wrt End-to-End 5G Network Slicing (see section ???) as well the Management domains: RAN, Core and TN domains.

~~~ aasvg
     ┌──────────────────────────────────┐     
  ┌──│      5G RAN or CORE Network      │──┐  
  │  └──────────────────────────────────┘  │  
  │                                        │  
  ▼                                        ▼  
┌──┐  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  ┌──┐ 
│NF├ ─ ─      Transport Network        ├ ┤NF│ 
└──┘  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  └──┘ 
          │             │            │        
          ▼             ▼            ▼        
                                              
  ┌───Data Center──┐ ┌─MPLS-VPN─┐  ┌─Public─┐ 
  │                │ │ Backbone │  │ Cloud  │ 
  │   ┌───┐┌───┐   │┌┴─┐      ┌─┴┐┌┴─┐      │ 
  │   └───┘└───┘   │└┬─┘      └─┬┘└┬─┘      │ 
  │┌──┐┌──┐┌──┐┌──┐│┌┴─┐      ┌─┴┐ │        │ 
  │└──┘└──┘└──┘└──┘│└┬─┘      └─┬┘ │        │ 
  └────────────────┘ └──────────┘  └────────┘ 
~~~
{: #fig-1 title="Transport Network vs RAN and CORE Network" artwork-align="center"}


##  5G Network Slicing versus Transport Network Slicing

##  Segmentation of the Transport Network


[insert ??? FIX]
(e.g. customer sites or public/private cloud  managed by the customer), usually thanks to VPN services. The TN extends up the PE (Provider Edge) routers, which connects to adjacent Customer Networks. We also assume that the Transport Network is based on IP, MPLS or SRv6 technologies.


### Transport Network ???


To start with, we assume that a 5G Network Slice Orchestrator is responsible for the orchestration of end-to-end 5G Slice. This includes the configuration of slices for the RAN and CORE domains  (i.e. 5G  Network Functions) and the Transport domain (i.e. NF-NF datapath). (??? add ref to framework for customer/provider naming) This document splits the orchestration Transport domain into two main building blocks:
- Customer Site: the customer site contains the 5G Network Functions and optionally additional routing devices.
- Provider Network: the Provider Network interconnects customer sites (i.e. WAN).

The RAN and CORE Networks NFs are deployed  
This includes the orchestration  Customer and Transport Network
This document defines the Transport Network with a service provider scope (i.e. WAN ).
The role of the TN is to interconnect Cutomer Networks (e.g. 5G sites with 5G Network Functions).

This document makes of the terms defined in {{!I-D.ietf-teas-ietf-network-slices}}:
* The TN extends up to the Provider Edge (PE).
* The PE is logically connected to the Customer Network via an Attachment Circuit.
* The CE is a device managed by the customer (i.e. 5G Network Orchestration) that provides logical connectivity to the Transport Network via the Attachment Circuit.
We also assume that the Transport Network is based on IP, MPLS or SRv6 technologies. Additionally, the orchestration of the TN is based on the IETF NSC. The CE can be 

In parrallel, a 5G Network Slice Orchestrator is responsible for orchestating the end-to-end 5G Slice logic. This includes the orchestration of the Customer Network and the Transport Network. including Network Functions and the Transport Network. The Orchestration of the TN is enforced via the IETF NSC.

~~~~
{::include ./drawings/end-to-end-TN.fig}
~~~~
{: #fig-end-to-end title="TN integration in 5G E2E Slicing" artwork-align="center"}

Deployments may not strictly fit with the generic PE/CE definition above. For example, the provider can mandate the use of a  Managed CE (aka Managed CPE) when providing VPNs services. Conversely, a CE (i.e. managed by the customer) may implement an MPLS/SRv6 PE function when connected to the Provider network via inter-AS MPLS/SRv6 techniques (e.g. Option B as per [??? REF]). These common Use-Cases introduce confusion in the existing terminology (PE or CE) and the actual location of the Attachment Circuit. The following sections clarifies these aspects.

??? Define the Customer Network Orchestration logic.

####  Distributed PE and CE {#sec-distributed}

This document introduces the concept of distributed CEs and PEs. This approach provides a generic definition of CE/PE/AC that is consistent with the orchestration perimeters. The CEs and PEs delimit respectively the Customer and Provider Orchestration domains, while the AC interconnects these domains.

{{fig-2}} represents the generic model for CE and PE together with examples of distributed CE and PE use-cases.
* Distributed CE: the logical connectivity is realized thanks to the configuration of multiple devices in the Customer Domain.  The CE function is distributed. An example of such a distribution is the realization of an interconnection with an L3 VPN service based on a distributed CE composed of an L2 switch and an L3 router (example ii).
* Distributed PE: the logical connectivity is realized thanks to the configuration of multiple devices in the Transport Network (provider Orchestration domain). The PE function is distributed. An example of a distributed PE is the “Managed CE service” as it is commonly named in the industry. In this case, a provider supplies VPN services based on CE and PE which are both managed by the provider (example iii). The “Managed CE” use case is a frequent source of confusion, since the actual Edge (Customer vs Provider) does not map with the Orchestration perimeters. For this purpose, these two elements are considered as distributed PE. The managed CE can also be a Data Center Gateway as depicted in example iv).

In subsequent sections of this document, the terms CE and PE are used for both a single device and a distributed device.
~~~ aasvg
┌ ─ ─ ─ ─ ─ ─ ─                       ┌ ─ ─ ─ ─ ─ ─ ─ ┐
    Customer   │                          Provider     
│     Site ┌────┐                  ┌──┴─┐  Network    │
           │    ├──────────────────┤    │              
│          │ CE ├ ─ ─ ─ ─AC ─ ─ ─ ─│ PE │             │
           │    ├──────────────────┤    │              
│          └────┘                  └──┬─┘             │
 ─ ─ ─ ─ ─ ─ ─ ┘                       ─ ─ ─ ─ ─ ─ ─ ─ 
                  i) Generic Model                     
┌ ─ ─ ─ ─ ─ ─ ─                       ┌ ─ ─ ─ ─ ─ ─ ─ ┐
    Customer   │                          Provider     
│     Site                            │    Network    │
 ┏━━━━━━━━━━━━━━━┓                                     
│┃ ┌─────┐ ┌────┐┃                 ┌──┴─┐             │
 ┃ │     │ │    ├┃─────────────────┤    │              
│┃ │     ├ ┼ ─ ─│┃ ─ ─ ─ AC─ ─ ─ ─ ┤ PE │             │
 ┃ │ RTR │ │ SW ├┃─────────────────┤    │              
│┃ └─────┘ └────┘┃                 └──┬─┘             │
 ┗━━Distributed━━┛                                     
│       CE                            │               │
 ─ ─ ─ ─ ─ ─ ─ ┘                       ─ ─ ─ ─ ─ ─ ─ ─ 
                  ii) Distributed CE                   
┌ ─ ─ ─ ─ ─ ─ ─                       ┌ ─ ─ ─ ─ ─ ─ ─ ┐
    Customer   │                          Provider     
│     Site                            │    Network    │
               │                  ┏━━━━━━━━━━━━━━━┓    
│          ┌────┐                 ┃┌──┴─┐   ┌────┐┃   │
           │    ├─────────────────┃┤Mngd│   │    │┃    
│          │ CE ├ ─ ─ ─ ─AC ─ ─ ─ ┃│ CE ├───┤ PE │┃   │
           │    ├─────────────────┃┤    │   │    │┃    
│          └────┘                 ┃└──┬─┘   └────┘┃   │
               │                  ┗━━Distributed━━┛    
│                                     │  PE           │
 ─ ─ ─ ─ ─ ─ ─ ┘                       ─ ─ ─ ─ ─ ─ ─ ─ 
                  iii) Distributed PE                  
                                                       
┌ ─ ─ ─ ─ ─ ─ ─                       ┌ ─ ─ ─ ─ ─ ─ ─ ┐
    Customer   │                          Provider     
│     Site                            │    Network    │
   ┏━━━━━━━━━━━━━━━━┓             ┏━━━━━━━━━━━━━━━━┓   
│  ┃    IP Fabric   ┃             ┃┌──┴─┐   ┌────┐ ┃  │
   ┃   ┌───┐┌───┐   ┃─────────────╋┤ DC │   │    │ ┃   
│  ┃   └───┘└───┘   ┃ ─ ─ ─AC ─ ─ ╋│ GW ├───┤ PE │ ┃  │
   ┃┌──┐┌──┐┌──┐┌──┐┃─────────────╋┤    │   │    │ ┃   
│  ┃└──┘└──┘└──┘└──┘┃             ┃└──┬─┘   └────┘ ┃  │
   ┗━━━Distributed━━┛             ┗━━Distributed━━━┛   
│          CE                         │  PE           │
               │                                       
└ ─Data Center─                       └ ─ ─ ─ ─ ─ ─ ─ ┘
                  iv) Distributed PE                   
                        and CE                         
~~~
{: #fig-2 title="Generic Model vs Distributed CE and PE" artwork-align="center"}

####  MPLS/SRv6 Attachment Circuit

In some cases, the CE router connects with the Provider thanks to Inter-AS Option B/C (FIXREF ???). In this case, the configuration of VRFs together with Control Plane identifiers such as route-targets/route-distinguishers happens on the CE. This is a source of confusion since these are typical enforced on PE devices. The definition based on Orchestration Scope prevails: the CE is managed by the Customer and the AC is based on MPLS or SRv6 dataplane technologies.

####  Co-Managed CE / PE

Another variation is a co-Managed CE/PE device which is orchestrated by both the Customer and the Provider. In this case, Customer and Provider usually have control on distinct device configuration perimeters (e.g. Customer is responsible for the LAN interface, Provider is responsible for the WAN interface and Routing). Considering the generic model, the device has both PE and CE function and there is no strict AC connection, although we may consider that the AC stitching logic happens internally within the box. (??? worth discussion ???)

###  Segmentation of the NF-to-NF Datapath

   The datapath between NFs can be decomposed into 3 main types of sections based on Orchestration
   domains and logic:

   *  Customer Network Section: the Customer Network section is deployed within the Customer Network. This section either connects two NFs located on the same Customer Network (??? insert REF} or it connects a NF to a CE. This section may not exist if the NF is the CE: in this case the AC connects the NF to the PE. The realization of this section is driven the 5G Network  Orchestration and potentially Customer Network Orchestration (e.g. Fabric Manager, Element Management System, VIM...). The realization of this section does not involve the Transport Network Orchestration.
   *  Transport Network Section: The TN section provides connectivity between Customer Networks. This section represents the connectivity between two PEs (e.g. VRF).The realization of this section is controlled by the NSC.
   *  Attachment Circuit section: As mentionned in previous sections, the Attachment Circuit connects the Customer Network to the Transport Network. In other words, the AC section represents the connectivity between a CE and PE.  The orchestration of this section relies partially the NSC for the configuration of the AC on the PE interface and the Customer Network Orchestration logic.

      The Local segment either connects two NFs within a Customer Network or connects a NF to
      the TN. In the first case, the realization of the segment is  driven by the 5G Network Slice Orchestrator (5GNSO) without any
      involvement of the IETF NSC. In the second case, the realization of this segment partially relies on the IETF NSC for the configuration of the AC on a PE interface.  Generally, the Local Segment is a datapath local to a site with a potential extension to reach the TN. A Customer Network can be (but not limited to): a Data Center (DC), a Point of Presence (PoP), a
      Central Office (CO), or a virtualized infrastructure in a Public
      Cloud.
   *  
   Note that more complex scenarios can be considered (for example, adding an extra
   segmentation of TN or Local Segments).  Additionally, sites can be of
   different types (such as Edge, Data Center, or Public Cloud), each with
   specific network design, hardware dependencies, management interface,
   and diverse networking technologies (e.g., MPLS, SRv6, VXLAN, or L2VPN vs.
   L3VPN).  The objective of this section is to clarify the scope
   of the Transport Network rather than to cover random technology or
   design combination.

   The realization of IETF Network Slices (i.e., connectivity with
   performance commitments) applies to the TN Segments.  We
   consider Local Segments as an extension of the connectivity of the
   RAN/CN domain without slice-specific performances requirements by
   assuming that the local infrastructure is overprovisioned and
   implements traditional QoS/Scheduling logic.

   Also, since the TN domain can extend either to the CE or to
   the PE, we introduce the term Edge Transport Node (ETN) to denote
   this boundary.  The ETN is, therefore, a Transport node that stitches
   Local Segments and TN Segments.  Note that depending on the design,
   the placement of the Service Demarcation Point (SDP)
   {{!I-D.ietf-teas-ietf-network-slices}} may or may not be enforced on the
   ETN itself.

   {{figure-1}} is a representation of the end-to-end datapath between NFs including Segments and ETNs (in
   practice PE or a managed CE), where applicable.

~~~ aasvg
      SMO/Site           TN           SMO/Site
    Orchestration   Orchestration   Orchestration
          │               │               │
          │               │               │
┌ ─ ─ ─ ─ ┼ ┐      ┌ ─ ─ ─│─ ─ ─ ┐      ┌ ┼ ─ ─ ─ ─ ┐
          │               │               │
│   ┌──┐  ▼ │    ┌─┴─┐    ▼    ┌─┴─┐    │ ▼  ┌──┐   │
    │NF├─────────┤ETN├─────────┤ETN├─────────┤NF│
│   └──┘    │    └─┬─┘Transport└─┬─┘    │    └──┘   │
  5G Site 1            Network            5G Site 2
└ ─ ─ ─ ─ ─ ┘      └ ─ ─ ─ ─ ─ ─ ┘      └ ─ ─ ─ ─ ─ ┘
       └─────────┘   └─────────┘   └─────────┘
          Local          TN           Local
         Segment       Segment       Segment

                   ■─────────────■
                 IETF Network Slice

       ◀─────────────────────────────────────▶
           End-to-end datapath between NFs
~~~
{: #figure-1 title=" Segmentation of the NF-NF Datapath" artwork-align="center"}

   NFs may also be placed in the same site and interconnected via a
   Local Segment.  In such a case, there is no TN Segment (i.e., no
   Transport Network Node is present in the datapath).

~~~ aasvg
                      SMO/Site
                    Orchestration
                          │
                          │
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
                          │
│   ┌──┐                  ▼                  ┌──┐   │
    │NF├─────────────────────────────────────┤NF│
│   └──┘                                     └──┘   │
                       5G Site
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
       └─────────────────────────────────────┘
                    Local Segment

       ◀─────────────────────────────────────▶
           End-to-end datapath between NFs
~~~
{: #figure-2 title="NF-to-NF Datapath within the Same Site" artwork-align="center"}

   {{figure-3}} provides samples to illustrate the  different
   realizations of Local and TN Segments, as well the SDPs:

   *  Layer 2 vs. Layer 3 Local Segment: The Local Segment can interconnect the NF
      and the ETN thanks to a unique VLAN/LAN with no intermediate
      routing hop (the simplest example is an NF directly connected to a
      PE): A1, A2, A3, and A4.  Alternatively, the NF interfaces may be
      attached in a different VLAN/LAN than the ETN interface assuming some additional local routing capabilities between the ETN and the NF (e.g., CE, IP
      Fabric): B1, B2, B3, and B4.

   *  ETN: It can be either a PE (A3, A4, B3, and B4) or a CE if it
      is managed by the TN Orchestration (A1, A2, B1, and B2).

   *  SDP: It can be located in many places as per
      Section b 4.2 of {{!I-D.ietf-teas-ietf-network-slices}}: A1/B1 for case
      (1), A2/B2 for case (2), A3/B3 for case (3), and A4/B4 for case
      (4).

   *  Redundancy/Scale-out: No example of redundancy/multihoming/scale-
      out is provided for the sake of simplicification.  Nonetheless,
      each node/NF can be represented by multiple instances (e.g.,
      multiple containers in a cloud architecture).

~~~ aasvg
       Local Segment              Transport Network
◀───────────────────────────▶◀─────────────────── ─ ─ ─ ─

┌─────────────────────┐      ┌─────────────────── ─ ─ ─ ┐
│ Site Type A1        │      │
│ ┌────┐              │    ┌─■──┐    ┌────┐      .      │
│ │ NF ├───────────────────┤ CE ├────┤ PE ├─────╱ ╲
│ └────┘              │    └─┬──┘    └────┘    ;   :    │
└─────────────────────┘      │                 ;   :
┌─────────────────────┐      │                ;     :   │
│ Site Type A2        │      │                │     │
│ ┌────┐              │ ┌────┤       ┌────┐   │     │   │
│ │ NF ├────────────────┤ CE ■───────┤ PE ├───│     │
│ └────┘              │ └────┤       └────┘   ;     :   │
└─────────────────────┘      │               ; ┌───┐ :
┌─────────────────────┐      │               │ │ P │ │  │
│ Site Type A3        │      │               │ └───┘ │
│ ┌────┐              │      ├────┐          │       │  │
│ │ NF ├─────────────────────■ PE ├──────────│       │
│ └────┘              │      ├────┘          │       │  │
└─────────────────────┘      │               │       │
┌─────────────────────┐      │               │       │  │
│ Site Type A4        │      │               ;       :
│ ┌────┐              │    ┌─■──┐           ;         : │
│ │ NF ├───────────────────┤ PE ├───────────│  ┌───┐  │
│ └────┘              │    └─┬──┘           │  │ P │  │ │
└─────────────────────┘      │              │  └───┘  │
┌─────────────────────┐      │              │         │ │
│ Site Type B1.───.   │      │              │         │
│           ,'     `. │      │              │         │ │
│          ;  Local  :│      │              │         │
│ ┌────┐   │ Routing ││    ┌─■──┐    ┌────┐ │         │ │
│ │ NF ├───┤ managed ├─────┤ CE ├────┤ PE ├─┤         │
│ └────┘   : by SMO  ;│    └─┬──┘    └────┘ │         │ │
│           ╲       ╱ │      │              │         │
│            `.   ,'  │      │              │  ┌───┐  │ │
│              `─'    │      │              │  │ P │  │
└─────────────────────┘      │              │  └───┘  │ │
┌─────────────────────┐      │              │         │
│ Site Type B2.───.   │      │              │         │ │
│           ,'     `. │      │              │         │
│          ;  Local  :│      │              │         │ │
│ ┌────┐   │ Routing ││ ┌────┤       ┌────┐ │         │
│ │ NF ├───┤ managed ├──┤ CE ■───────┤ PE ├─┤         │ │
│ └────┘   : by SMO  ;│ └────┤       └────┘ │         │
│           ╲       ╱ │      │              │         │ │
│            `.   ,'  │      │              │  ┌───┐  │
│              `─'    │      │              │  │ P │  │ │
└─────────────────────┘      │              │  └───┘  │
┌─────────────────────┐      │              │         │ │
│ Site Type B3.───.   │      │              :         ;
│           ,'     `. │      │               :       ;  │
│          ;  Local  :│      │               │       │
│ ┌────┐   │ Routing ││      ├────┐          │       │  │
│ │ NF ├───┤ managed ├───────■ PE ├──────────│       │
│ └────┘   : by SMO  ;│      ├────┘          │       │  │
│           ╲       ╱ │      │               │       │
│            `.   ,'  │      │               │ ┌───┐ │  │
│              `─'    │      │               │ │ P │ │
└─────────────────────┘      │               : └───┘ ;  │
┌─────────────────────┐      │                :     ;
│ Site Type B4.───.   │      │                │     │   │
│           ,'     `. │      │                │     │
│          ;  Local  :│      │                │     │   │
│ ┌────┐   │ Routing ││    ┌─■──┐             :     ;
│ │ NF │───┤ managed ├─────┤ PE ├──────────────:   ;    │
│ └────┘   : by SMO  ;│    └─┬──┘              │   │
│           ╲       ╱ │      │                 :   ;    │
│            `.   ,'  │      │                  ╲ ╱
│              `─'    │      │                   '      │
└─────────────────────┘      └──────────────────── ─ ─ ─

                          ├───────────────┤
                                 ETN

             ■ Service Demarcation Point
~~~
{: #figure-3 title="Examples of various combinations of Local Segments, ETN, and SDP" artwork-align="center"}

###  Orchestration of Local Segment Terminations at ETNs

   The interconnection between a 5G site and the Transport Network is
   made up of shared networking resources.  More precisely, the Local
   Segment terminates to an interface of the ETN, which must be
   configured with consistent dataplane network information (e.g.,  VLAN-
   ID and IP addresses/subnets).  Hence, the realization of this
   interconnection requires a coordination between the Service
   Management and Orchestration (SMO) and the Transport Orchestration (IETF
   NSC).  In this document, and aligned with {{?RFC8969}}, we assume that this coordination is based upon
   standard YANG data models and APIs (more details in further sections).

   {{figure-4}} is a basic example of a Layer 3 CE-PE link realization
   with shared network resources, such as VLAN-ID and IP prefixes, which
   must be passed between Orchestrators via the Network Slice Service Interface ({{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}) or an Attachement Circuit Service Interface ({{?I-D.boro-opsawg-teas-attachment-circuit}}).

~~~ aasvg
     Datapath network resources (e.g., VLAN-IDs or IP
    prefixes) exchanged via SMO-NSC interface (NSI)

      ┌ ─ ─ ─ ─ ─ ─ ┐                ┌ ─ ─ ─ ─ ─ ─ ┐
                                           TN
      │             │                │Orchestration│
        SMO / Site     IETF APIs/DM
      │Orchestration│ ◀────────────▶ │  IETF NSC   │
       ─ ─ ─ ─ ─ ─ ─                  ─ ─ ─ ─ ─ ─ ─
                │                        │
                │                        │
┌ ─ ─ ─ ─ ─ ─ ─ ┼ ┐                    ┌ ┼ ─ ─ ─ ─ ─ ─ ─ ┐
                ▼                        ▼
│ ┌──┐      ┌──┐.1│    192.0.2.0/31    │.0┌──┐           │
  │NF├──────┤CE├──────────────────────────┤PE│
│ └──┘      └──┘  │      VLAN 100      │  └──┘           │
       Site
│                 │                    │        TN       │
 ─ ─ ─ ─ ─ ─ ─ ─ ─                      ─ ─ ─ ─ ─ ─ ─ ─ ─

    └────────────────────────────────────┘
                 Local Segment
~~~
{: #figure-4 title="An Example of Data Exchange" artwork-align="center"}

   Note that the allocation of these resources (e.g.,  VLAN-IDs or IP prefixes)
   can be either managed by the SMO or the Transport Network.  In other
   words, the initial SMO request for the creation of a new IETF Network
   Slice on a given 5G site may or may not include all network
   resources.  In the latter case, this information is exchanged in a
   second step.

## 5G Slice to IETF Network Slice Mapping

   There are multiple options to map a 5G network slice to IETF Network
   Slices:

   * 1 to N:
      A single 5G Network Slice can map to multiple IETF Network
      Slices (1 to N).  One example of such a case is the separation of
      the 5G Control Plane and User Plane: this use case is represented
      in {{figure-5}} where a slice (EMBB) is deployed with a separation of
      User Plane and Control Plane at the TN.

   * N to 1:
      Multiple 5G Network Slices may rely upon the same IETF Network
      Slice (i.e., in {{TS-28.530}} semantic, two RAN/CN NSSes uses a
      shared TN NSS).  In such a case, the Service Level Agreement (SLA) differentiation of slices
      would be entirely controlled at 5G Control Plane, for example, with
      appropriate placement strategies: this use case is represented in
      {{figure-6}}, where a User Plane Function (UPF) for the URLLC slice is
      instantiated at the edge cloud close to the gNB CU-UP User Plane for
      better latency/jitter control, while the 5G Control Plane and the UPF
      for EMBB slice are instantiated in the regional cloud.

   * N to M:
      The 5G to IETF Network Slice mapping combines both
      approaches with a mix of shared and dedicated associations.

~~~ aasvg
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐

│                        5G Slice eMBB                          │

│            ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐            │
  ┌─────┐ N3   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐   N3 ┌─────┐
│ │CU-UP├───────   IETF Network Slice UP_eMBB    ───────┤ UPF │ │
  └─────┘      └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘      └─────┘
│            │                                     │            │
  ┌─────┐ N2   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐   N2 ┌─────┐
│ │CU-CP├───────      IETF Network Slice CP      ───────┤ AMF │ │
  └─────┘      └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘      └─────┘
└ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ┘

             │                                     │
                       Transport Network
             │                                     │

             └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
~~~
{: #figure-5 title="1 (5G Slice) to N (IETF Network Slice) Mapping" artwork-align="center"}

~~~ aasvg
                  ┌ ─ ─ ─ ─ ─ ─ ┐
                     Edge Cloud
                  │             │
                    ┌─────────┐
                  │ │UPF_URLLC│ │
                    └─────┬───┘
                  └ ─ ─ ─ │ ─ ─ ┘
┌ ─ ─ ─ ─ ─ ─ ─ ┐ ┌ ─ ─ ─ │ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                    ┌ ─ ─ ┴ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │ ┌ ─ ─ ─ ─ ─ ─ ─
│   Cell Site   │ │                            │                  │
                    │                            │ │   Regional
│ ┌───────────┐ │ │                            │         Cloud    │
  │CU-UP_URLLC├─────┤                            │ │ ┌──────────┐
│ └───────────┘ │ │         IETF Network       ├─────┤  5GC CP  │ │
                    │        Slice ALL           │ │ └──────────┘
│ ┌───────────┐ │ │                            │                  │
  │CU-UP_eMBB ├─────┤                            │ │ ┌──────────┐
│ └───────────┘ │ │                            ├─────┤ UPF_eMBB │ │
 ─ ─ ─ ─ ─ ─ ─ ─    │                            │ │ └──────────┘
                  │  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘                  │
                                                 │ └ ─ ─ ─ ─ ─ ─ ─
                  │      Transport Network
                   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
~~~
{: #figure-6 title="N (5G Slice) to 1 (IETF Network Slice) Mapping" artwork-align="center"}

   Note that the actual realization of the mapping depends on several
   factors, such as the actual business cases, the NF vendor
   capabilities, the NF vendor reference designs, as well as service
   provider or even legal requirements.

Specifically, the actual mapping is a design choice of service operators that may be a function of, e.g., the number of instantiated slices, requested services, or local engineering capabilities and guidelines. However, operators should carefully consider means to ease slice migration strategies (e.g., move from 1-to-1 mapping to N-to-1).

##  First 5G Slice versus Subsequent Slices

   A 5G Network Slice is fully functional with both 5G Control Plane and
   User Plane capabilities (i.e., dedicated NF functions or contexts).
   In this regard, the creation of the "first slice" is subject to a
   specific logic since it must deploy both CP and UP.  This is not the
   case for the deployment of subsequent slices because they can share
   the same CP of the first slice, while instantiating dedicated UP.  An
   example of an incremental deployment is depicted in {{figure-7}}.

   At the time of writing (2023), Section 6.2 of {{NG.113}} specifies that the
   eMBB slice (SST=1 and no SD) should be supported globally.  This 5G
   slice would be the first slice in any 5G deployment.

   Note that the actual realization of the mapping depends on several
   factors such as the actual business cases, the NF vendor
   capabilities, the NF vendor reference designs, as well as service
   providers or even legal requirements.

~~~ aasvg
   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
                      ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │  1    ┌─────┐      ┌──────────────────────────┐ │    ┌─────┐  │
      s S  │NF-CP├──────┤  CP IETF NS (IETF-NS-1)  ├──────┤NF-CP│
   │  t l  └─────┘      └──────────────────────────┘ │    └─────┘  │
        i             │
   │  5 c  ┌─────┐      ┌──────────────────────────┐ │    ┌─────┐  │
      G e  │NF-UP├──────┤  UP IETF NS (IETF-NS-2)  ├──────┤NF-UP│
   │       └─────┘      └──────────────────────────┘ │    └─────┘  │
    ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                                                     │
                      │      Transport Network
                                                     │
                      └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                         Deployment of first 5G slice
                                     │ │
                                     │ │
                                     │ │
                                    ─┘ └─
                                    ╲   ╱
                                     ╲ ╱
                                      V
   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
                      ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │  1    ┌─────┐      ┌──────────────────────────┐ │    ┌─────┐  │
      s S  │NF-CP├──────┤  CP IETF NS (IETF-NS-1)  ├──────┤NF-CP│
   │  t l  └─────┘      └──────────────────────────┘ │    └─────┘  │
        i             │
   │  5 c  ┌─────┐      ┌──────────────────────────┐ │    ┌─────┐  │
      G e  │NF-UP├──────┤  UP IETF NS (IETF-NS-2)  ├──────┤NF-UP│
   │       └─────┘      └──────────────────────────┘ │    └─────┘  │
    ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                                                     │
   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
      2                                              │
   │  n S  ┌──────┐   │ ┌──────────────────────────┐     ┌──────┐  │
      d l  │NF-UP2├─────┤   UP2 IETF NS (IETF-NS-3)├─────┤NF-UP2│
   │    i  └──────┘   │ └──────────────────────────┘     └──────┘  │
      5 c                                            │
   │  G e             │                                            │
    ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─
                      │
                              Transport Network      │
                      │
                       ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
       Deployment of additional 5G slice with shared Control Plane
~~~
{: #figure-7 title="First and Subsequent Slice Deployment" artwork-align="center"}

#  High-Level Overview of the Realization Model

   {{!I-D.ietf-teas-ietf-network-slices}} introduces the concept of the
   Network Resource Partition (NRP), which is defined as a collection of
   resources identified in the underlay network.  In the basic
   realization model described in this document, a single NRP is used
   with the following characteristics:

   *  L2VPN/L3VPN service instances for logical separation:

      This realization model of transport for 5G slices assumes Layer-3
      delivery for midhaul and backhaul transport connections, and a
      Layer 2 or Layer 3 for
      fronthaul connections. eCPRI supports both delivery models. L2VPN/L3VPN service instances might be
      used as a basic form of logical slice separation.  Furthermore, using
      service instances results in an additional outer header (as packets
      are encapsulated/decapsulated at the nodes performing PE
      functions) providing clean discrimantion between 5G QoS and TN
      QoS, as explained in {{sec-qos-map}}.

   *  Fine-grained resource control at the ETN:

      This is sometimes called 'admission control' or 'traffic
      conditioning'.  The main purpose is the enforcement of the
      bandwidth contract for the slice right at the edge of the
      transport domain where the traffic is handed-off between the
      transport domain and the 5G domains (i.e., RAN/Core).

      The toolset used here is granular ingress policing (rate limiting)
      to enforce contracted bandwidths per slice and, potentially, per
      traffic class within the slice.  Out-of-contract traffic might be
      immediately dropped, or marked as high drop-probability traffic,
      which is more likely to be dropped somewhere in transit if
      congestion occurs.  In the egress direction at the edge of the
      transport domain, hierarchical schedulers/shapers can be deployed,
      providing guaranteed rates per slice, as well as guarantees per
      traffic class within each slice.

      In the managed CE use cases (use cases A1, A2, B1, and B2 depicted in
      {{figure-7}}), edge admission control could be distributed between CE
      and PE, where one part of the edge admission control is
      implemented on the CE, and another part of the edge admission control
      is implemented on the PE.

   *  Coarse resource control at the TN transit (non-attachment
      circuits) links of the transport domain, using a single NRP, spanning the entire TN domain.
      Transit nodes do not maintain any state of individual slices.
      Instead, only a flat (non-hierarchical) QoS model is used on
      transit links, with up to 8 traffic classes.  At the transport
      domain edge, traffic-flows from multiple slice services are mapped
      to the limited number of traffic classes used on transit links.

   *  Capacity planning/management for efficient usage of TN edge and TN
      transit resources:

      The role of capacity management is to ensure the transport
      capacity can be utilized without causing any bottlenecks.  The
      toolset used here can range from careful network planning, to
      ensure more less equal traffic distribution (i.e., equal cost load
      balancing), to advanced traffic engineering techniques, with or
      without bandwidth reservations, to force more consistent load
      distribution even in non-ECMP friendly network topologies.

~~~ aasvg
           ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
     ┌──────────┐               base NRP               ┌──────────┐
     │   ETN    │                                      │   ETN    │
   ┌ ┼ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─│─
     ■◀───┐│    │         IETF Network Slice 1         │   │┌────▶■ │
   │ │    │     │        ┌─────┐        ┌─────┐        │    │     │
     ■◀───┤│    │        │  P  │        │  P  │        │   │├────▶■ │
   ├ ┼ ─ ─├────▶□◀──────▶□◀───▶□◀──────▶□────▶□◀──────▶□◀───┤─ ─ ─│─
     ■◀───┤│    │        │     │        │     │        │   │├────▶■ │
   │ │    │     │        └─────┘        └─────┘        │    │     │
     ■◀───┘│    │         IETF Network Slice 2         │   │└────▶■ │
   └ ┼ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─│─
     │     │    │                                      │   │      │
     └──────────┘                                      └──────────┘
           └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
    ■ fine-grained QoS (dedicated resources per IETF NS)
    □ coarse QoS, with resources shared by all IETF NSes
~~~
{: #figure-8 title="Resource Allocation in with single NRP Slicing Model" artwork-align="center"}

   The 5G control plane relies upon the S-NSSAI (Single Network Slice
   Selection Assistance Information: 32-bit slice identifier) for slice
   identification.  The S-NSSAI is not visible to the transport domain,
   so instead 5G functions can expose the 5G slices to the transport
   domain by mapping to explicit L2/L3 identifiers such as VLAN-ID, IP
   addresses, or Differentiated Services Code Point (DSCP) as documented in {{?I-D.gcdrb-teas-5g-network-slice-application}}.

##  VLAN Hand-off {#sec-vlan-handoff}

   In this option, the IETF Network Slice, fulfilling connectivity
   requirements between NFs of some 5G slice, is represented at the SDP
   by a VLAN, or double VLANs (commonly known as QinQ).  Each VLAN can
   represent a distinct logical interface on the attachment circuits,
   hence it provides the possibility to place these logical interfaces
   in distinct L2 or L3 service instances and implement separation
   between slices via service instances.  Since the 5G interfaces are IP
   based interfaces (the only exception could be the F2 fronthaul-
   interface, where eCPRI with Ethernet encapsulation is used), this
   VLAN is typically not transported across the TN domain.  Typically,
   it has only local significance at a particular SDP.  For
   simplification it is recommended to rely on the same VLAN identifier
   for all ACs, when possible.  However, SDPs for a same slice at
   different locations may also use different VLAN values.  Therefore, a
   VLAN to IETF Network Slice mapping table MUST be maintained for each
   AC, and the VLAN allocation MUST be coordinated between TN orchestration and
   local segment orchestration.  Thus, while VLAN hand-off is simple from
   the NF point of view, it adds complexity due to the requirement of
   maintaining mapping tables for each SDP.

~~~ aasvg
VLANs representing slices           VLANs representing slices

           │     ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─      │             │
           │                        │     │             │
┌──────┐   ▼   ┌─┴───┐ Transport┌─────┐   ▼   ┌─────┐   ▼   ┌──────┐
│      ●───────●■    │          │    ■●───────●     ●───────●      │
│ NF   ●───────●■ ETN│          │ETN ■●───────●L2/L3●───────●   NF │
│      ●───────●■    │          │    ■●───────●     ●───────●      │
└──────┘       └─┬───┘  Network └─────┘       └─────┘       └──────┘
                                    │
                 └ ─ ─ ─ ─ ─ ─ ─ ─ ─
      └────────┘└────────────────────┘└─────────────────────┘
         Local             TN                   Local
        Segment          Segment               Segment

 ● – logical interface represented by VLAN on physical interface
 ■ - Service Demarcation Point
~~~
{: #figure-9 title="5G Slice with VLAN Hand-off" artwork-align="center"}

##  IP Hand-off

   In this option, the slices in the transport domain are instantiated
   by IP tunnels (for example, IPsec or GTP-U tunnels) established between
   NFs.  The transport for a single 5G slice is constructed with
   multiple such tunnels, since a typical 5G slice contains many NFs -
   especially DUs and CUs.  If a shared NF (i.e., an NF that serves
   multiple slices, for example a shared DU) is deployed, multiple
   tunnels from shared NF are established, each tunnel representing a
   single slice.  As opposed to the VLAN hand-off case, there is no
   logical interface representing a slice on the PE, hence all slices are
   handled within single service instance.  On the other hand, similarly
   to the VLAN hand-off case, a mapping table tracking IP to IETF
   Network Slice mapping is required.

~~~ aasvg
                                        Tunnels representing slices

                  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ┐                   │
                                                        │
┌──────┐       ┌──┴──┐ Transport┌───┴─┐       ┌─────┐   ▼   ┌──────┐
│    ○════════════■════════════════■══════════════════════════○    │
│ NF   ├───────┤ ETN │          │ ETN ├───────┤L2/L3├───────┤   NF │
│    ○════════════■════════════════■══════════════════════════○    │
└──────┘       └──┬──┘  Network └───┬─┘       └─────┘       └──────┘

                  └ ─ ─ ─ ─ ─ ─ ─ ─ ┘
      └────────┘└────────────────────┘└─────────────────────┘
         Local             TN                   Local
        Segment          Segment               Segment

          ○ – tunnel (IPsec, GTP-U, ...) termination point
          ■ - Service Demarcation Point
~~~
{: #figure-10 title="5G Slice with IP Hand-off" artwork-align="center"}

   The mapping table can be simplified if, for example, IPv6 addressing is used
   to address NFs.  An IPv6 address is a 128-bit long field, while the
   S-NSSAI is a 32-bit field: Slice/Service Type (SST): 8 bits, Slice
   Differentiator (SD): 24 bits. 32 bits, out of 128 bits of the IPv6
   address, may be used to encode the S-NSSAI, which makes an IP to
   Slice mapping table unnecessary. This mapping is simply a local allocation method
   to allocate IPv6 addresses to NF loopbacks, without redefining IPv6
   semantics. Different IPv6 address allocation schemes following this
   mapping approach may be used, with one example allocation showed in {{figure-11}}.

   Note that this addressing scheme is local to a node; intermediary nodes are not
   required to associate any additional semantic with IPv6 address.

   One
   benefit of embedding the S-NSSAI in the IPv6 address is that it provides
   a very easy way of identifying the packet as belonging to given
   S-NSSAI at any place in the transport domain.  This might be
   used, for example, to selectively enable per S-NSSAI monitoring, or
   any other per S-NSSAI handling, if required.

~~~ aasvg
             NF specific          reserved
        (not slice specific)     for S-NSSAI
    ◀───────────────────────────▶ ◀───────▶
   ┌────┬────┬────┬────┬────┬────┬────┬────┐
   │2001:0db8:xxxx:xxxx:xxxx:xxxx:ttdd:dddd│
   └─────────┴─────────┴─────────┴─────────┘
    tt     - SST (8 bits)
    dddddd - SD (24 bits)
~~~
{: #figure-11 title="An Example of S-NSSAI embedded into IPv6" artwork-align="center"}

   In the example shown in {{figure-11}}, the most significant 96 bits of the IPv6 address are
   unique to the NF, but do not carry any slice-specific information, while
   the least significant 32 bits are used to embed the S-NSSAI information.
   The 96-bit part of the address may be further divided based, for
   example, on the geographical location or the DC identification.

   {{figure-12}} shows an example of a slicing deployment, where the S-NSSAI is
   embedded into IPv6 addresses used by NFs.  NF-A has a set of tunnel
   termination points, with unique per-slice IP addresses allocated from
   the 2001:db8::a:0:0/96 prefix, while NF-B uses a set of tunnel
   termination points with per-slice IP addresses allocated from
   2001:db8::b:0:0/96.  This example shows two slices: eMBB (SST=1) and
   MIoT (SST=3).  Therefore, for eMBB the tunnel IP addresses are auto-
   derived (without the need for a mapping table) as {2001:db8::a:100:0,
   2001:db8::b:100:0}, while for MIoT (SST=3) tunnel uses
   {2001:db8::a:300:0, 2001:db8::b:300:0}.

~~~ aasvg
 2001:db8::a:0:0/96 (NF-A)                2001:db8::b:0:0/96 (NF-B)

 2001:db8::a:100:0/128  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─   2001:db8::b:100:0/128
     │                                     │                  │
┌────▼─┐ eMBB (SST=1)   │     Transport                     ┌─▼────┐
│    ○═══════════════════■════════════════■═══════════════════○    │
│ NF-A │                │                  │                │ NF-B │
│    ○═══════════════════■════════════════■═══════════════════○    │
└────▲─┘ MIoT (SST=3)   │      Network                      └─▲────┘
     │                                     │                  │
 2001:db8::a:300:0/128  └ ─ ─ ─ ─ ─ ─ ─ ─ ─   2001:db8::b:300:0/128

     └──────────────────┘└────────────────┘└──────────────────┘
        Local Segment        TN Segment        Local Segment

          ○ – tunnel (IPsec, GTP-U, ...) termination point
          ■ - Service Demarcation Point
~~~
{: #figure-12 title="Deployment example with S-NSSAI embedded into IPv6" artwork-align="center"}

##  MPLS Label Hand-off

   In this option, the service instances representing different slices
   are created directly on the NF, or within the cloud infrastructure
   hosting the NF, and attached to the TN domain.  Therefore, the packet
   is MPLS encapsulated outside the TN domain with native MPLS
   encapsulation, or MPLSoUDP encapsulation, depending on the capability
   of the NF or cloud infrastructure, with the service label depicting
   the slice.

   There are three major methods (based upon Section 10 of {{!RFC4364}}) for interconnecting multiple service domains:

   *  Option 10A ({{sec-10a}}): VRF-to-VRF connections.
   *  Option 10B ({{sec-10b}}): redistribution of labeled VPN routes with next-hop
      change at domain boundaries.
   *  Option 10C ({{sec-10c}}): redistribution of labeled VPN routes without next-hop
      change + redistribution of labeled transport routes with next-hop
      change at domain boundaries.

###  Option 10A {#sec-10a}

   In this option, MPLS is not used in VRF-to-VRF hand-offs,
   since services are terminated at the boundary of each domain, and
   VLAN hand-off is in place between the domains.  Thus, this option is
   the same as VLAN hand-off, described in {{sec-vlan-handoff}}.

###  Option 10B {#sec-10b}

   In this option, L3VPN service instances for different IETF
   Network Slice Services are instantiated outside the TN domain.  These L3VPN service instances
   could be instantiated either on the compute, hosting mobile network
   functions ({{figure-13}}, left hand side), or within the cloud
   infrastructure itself (e.g., on the top of the rack or leaf switch
   within cloud IP fabric ({{figure-13}}, right hand side)). On the local segment connected to ETN, packets are already MPLS
   encapsulated (or MPLSoUDP encapsulated, if cloud or compute
   infrastructure doesn't support native MPLS encapsulation). Therefore,
   the PE uses neither a VLAN nor an IP address for slice
   identification at the SDP, but instead uses the MPLS label.

~~~ aasvg
     ◁──────        ◁──────        ◁──────
     BGP VPN        BGP VPN        BGP VPN
       COM=1, L=A"    COM=1, L=A'    COM=1, L=A
       COM=2, L=B"    COM=2, L=B'    COM=2, L=B
       COM=3, L=C"    COM=3, L=C'    COM=3, L=C
    ◁─────────────▷◁────────────▷◁─────────────▷
               nhs  nhs      nhs  nhs
                                                        VLANs
 service instances                service instances  representing
representing slices              representing slices    slices
     │          ┌ ─ ─ ─ ─ ─ ─ ─ ─            │           │
     │               Transport   │           │           │
┌────▼─┐       ┌┴────┐       ┌─────┐       ┌─▼──────┐    ▼  ┌──────┐
│    ◙ │       │■    │       │    ■│       │ ◙………………●───────●      │
│ NF ◙ ├───────┤■ ETN│       │ETN ■├───────┤ ◙………………●───────●   NF │
│    ◙ │       │■    │       │    ■│       │ ◙………………●───────●      │
└──────┘       └┬────┘       └─────┘       └────────┘       └──────┘
                      Network    │            L2/L3
                └ ─ ─ ─ ─ ─ ─ ─ ─
      └────────┘└──────────────────┘└───────────────────────┘
         Local            TN                    Local
        Segment         Segment                Segment

  ● – logical interface represented by VLAN on physical interface
  ◙ - service instances (with unique MPLS label)
  ■ - Service Demarcation Point
~~~
{: #figure-13 title="MPLS Hand-off: Option B" artwork-align="center"}

   MPLS labels are allocated dynamically, especially in Option 10B
   deployments, where at the domain boundaries service prefixes are
   reflected with next-hop self, and new label is dynamically allocated,
   as visible in {{figure-13}} (e.g., labels A, A' and A" for the first depicted slice).  Therefore, for any slice-specific per hop
   behavior at the TN domain edge, the ETN must be able to determine
   which label represents which slice.  In the BGP control plane, when
   exchanging service prefixes over local segment, each slice might be represented by a unique BGP community, so
   tracking label assignment to the slice is possible.  For example, in
   {{figure-13}}, for the slice identified with COM=1, ETN advertises a
   dynamically allocated label A".  Since, based on the community, the
   label to slice association is known, ETN can use this dynamically
   allocated label A" to identify incoming packets as belonging to slice
   1, and execute appropriate edge per hop behavior.

   It is worth noting that slice identification in the BGP control plane
   might be with per-prefix granularity.  In extreme case, each prefix can have
   different community representing a different slice.  Depending on the
   business requirements, each slice could be represented by a different
   service instance, as outlined in {{figure-13}}.  In that case, the route
   target extended community might be used as slice differentiator.  In
   another deployment, all prefixes (representing different slices)
   might be handled by single 'mobile' service instance, and some other
   BGP attribute (e.g., a standard community) might be used for slice
   differentiation.  Or there could be a deployment that groups multiple
   slices together into a single service instance, resulting in a
   handful of service instances.  In any case, fine-grained per-hop
   behavior at the edge of TN domain is possible.

###  Option 10C {#sec-10c}

   ***for further study***

#  QoS Mapping Models {#sec-qos-map}

   The resources are managed via various QoS policies deployed in the
   network.  QoS mapping models to support 5G slicing connectivity
   implemented over packet switched transport uses two layers of QoS that are discussed in the following subsections.

## 5G QoS Layer

   QoS treatment is indicated in the 5G QoS layer by the 5QI (5G QoS
   indicator), as defined in {{TS-23.501}}. A 5QI is an identifier (ID) that is
   used as a reference to 5G QoS characteristics (e.g., scheduling
   weights, admission thresholds, queue management thresholds, and link
   layer protocol configuration) in the RAN domain.  Given that
   5QI applies to the RAN domain, it is not visible to the
   TN domain.  Therefore, if 5QI-aware treatment is desired in the TN
   domain as well, 5G network functions might set DSCP with a value
   representing 5QI so that differentiated treatment can implemented in TN domain
   as well.  Based on these DSCP values, at SDP of each TN segment
   used to construct transport for given 5G slice, very granular QoS
   enforcement might be implemented.

   The mapping between 5QI and
   DSCP is out of scope for this document.  Mapping recommendations
   are documented, e.g., in {{?I-D.henry-tsvwg-diffserv-to-qci}}.

   Each slice service might have flows with multiple 5QIs, thus there could be many
   different 5QIs being deployed. 5QIs (or, more precisely,
   corresponding DSCP values) are visible to the TN domain at SDP
   (i.e., at the edge of the TN domain).

   In this document, this layer of QoS will be referred as '5G QoS
   Class' ('5G QoS' in short), or '5G DSCP'.

## TN QoS Layer

   Control of the TN resources on transit links, as well as traffic
   scheduling/prioritization on transit links, is based on a flat
   (non-hierarchical) QoS model in this IETF Network Slice
   realization.  That is, IETF Network Slices are assigned dedicated
   resources (e.g., QoS queues) at the edge of the TN domain (at
   SDPs), while all IETF Network Slices are sharing resources (sharing
   QoS queues) on the transit links of the TN domain.  Typical router
   hardware can support up to 8 traffic queues per port, therefore
   the architecture assumes 8 traffic queues per port support in
   general.

   At this layer, QoS treatment is indicated by QoS indicator
   specific to the encapsulation used in the TN domain, and it could
   be DSCP or MPLS Traffic Class (TC).  This layer of QoS will be referred as 'TN QoS
   Class', or 'TN QoS' for short, in this document.

## QoS Realization Models

   While 5QI might be exposed to the TN domain, via the DSCP value
   (corresponding to specific 5QI value) set in the IP packet generated
   by NFs, some 5G deployments might use 5QI in the RAN domain only,
   without requesting per 5QI differentiated treatment from the TN
   domain.  This can be due to an NF limitation (e.g., no capability to set
   DSCP), or it might simply depend on the overall slicing deployment
   model.  The O-RAN Alliance, for example, defines a phased approach to
   the slicing, with initial phases utilizing only per slice, but not
   per 5QI, differentiated treatment in the TN domain
   (Annex F of {{O-RAN.WG9.XPSAAS}}).

   Therefore, from a QoS perspective, the 5G slicing connectivity
   realization architecture defines two high-level realization models
   for slicing in the transport domain: a 5QI-unaware model and a 5QI-
   aware model.  Both slicing models in the transport domain could be
   used concurrently within the same 5G slice.  For example, the TN
   segment for 5G midhaul (F1-U interface) might be 5QI-aware, while
   at the same time the TN segment for 5G backhaul (N3 interface) might
   follow the 5QI-unaware model.

   These models are further elaborated in the following two subsections.

##  5QI-unaware Model {#sec-5QI-unaware}

   In 5QI-unaware mode, the DSCP values in the packets received from NF
   at SDP are ignored.  In the TN domain, there is no QoS
   differentiation at the 5G QoS Class level.  The entire IETF Network
   Slice is mapped to single TN QoS Class, and, therefore, to a single
   QoS queue on the routers in the TN domain.  With a small number of
   deployed 5G slices (for example only two 5G slices: eMBB and MIoT),
   it is possible to dedicate a separate QoS queue for each slice on
   transit routers.  However, with introduction of private/enterprises
   slices, as the number of 5G slices (and thus corresponding IETF
   Network Slices) increases, a single QoS queue on transit links serves
   multiple slices with similar characteristics.  QoS enforcement on
   transit links is fully coarse (single NRP, sharing resources among
   all IETF Network Slices), as displayed in {{figure-14}}.

~~~ aasvg
   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   ┏━━━━━━━━━━━━━━━━━┓         ETN                              │
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃
   ┃   SDP           ┃              ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
   ┃│  ┌──────────┐ │┃              ┃       Transit link        ┃
   ┃   │IETF NS 1 ├────────────┐    ┃┌────────────────────────┐ ┃
   ┃│  └──────────┘ │┃         ├─────▶     TN QoS Class 1     │ ┃
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃         │    ┃└────────────────────────┘ ┃
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃         │    ┃┌────────────────────────┐ ┃
   ┃   SDP           ┃         │    ┃│     TN QoS Class 2     │ ┃
   ┃│  ┌──────────┐ │┃         │    ┃└────────────────────────┘ ┃
   ┃   │IETF NS 2 ├────────┐   │    ┃┌────────────────────────┐ ┃
   ┃│  └──────────┘ │┃     │   │    ┃│     TN QoS Class 3     │ ┃
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃     │   │    ┃└────────────────────────┘ ┃
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃     │   │    ┃┌────────────────────────┐ ┃
   ┃   SDP           ┃     └─────────▶     TN QoS Class 4     │ ┃
   ┃│  ┌──────────┐ │┃         │    ┃└────────────────────────┘ ┃
   ┃   │IETF NS 3 ├────────────┘    ┃┌────────────────────────┐ ┃
   ┃│  └──────────┘ │┃     ┌─────────▶     TN QoS Class 5     │ ┃
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃     │        ┃└────────────────────────┘ ┃
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃     │        ┃┌────────────────────────┐ ┃
   ┃   SDP           ┃     │        ┃│     TN QoS Class 6     │ ┃
   ┃│  ┌──────────┐ │┃     │        ┃└────────────────────────┘ ┃
   ┃   │IETF NS 4 ├────────┤        ┃┌────────────────────────┐ ┃
   ┃│  └──────────┘ │┃     │        ┃│     TN QoS Class 7     │ ┃
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃     │        ┃└────────────────────────┘ ┃
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃     │        ┃┌────────────────────────┐ ┃
   ┃   SDP           ┃     │        ┃│     TN QoS Class 8     │ ┃
   ┃│  ┌──────────┐ │┃     │        ┃└────────────────────────┘ ┃
   ┃   │IETF NS 5 ├────────┘        ┃     Max 8 TN Classes      ┃
   ┃│  └──────────┘ │┃              ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃                                          │
   ┣━━━━━━━━━━━━━━━━━┛
    ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
   Fine-grained QoS enforcement         Coarse QoS enforcement
     (dedicated resources per            (resources shared by
       IETF Network Slice)                multiple IETF NSs)
~~~
{: #figure-14 title="Slice to TN QoS Mapping (5QI-unaware Model)" artwork-align="center"}

   When the IP traffic is handed over at the SDP from the local segment to the TN domain, the PE encapsulates the
   traffic into MPLS (if MPLS transport is used in the TN domain), or
   IPv6 - optionally with some additional headers (if SRv6 transport is
   used in the TN domain), and sends out the packets on the TN transit
   link.

   The original IP header retains the DCSP marking (which is ignored in
   5QI-unaware mode), while the new header (MPLS or IPv6) carries QoS
   marking (MPLS Traffic Class bits for MPLS encapsulation, or DSCP for
   SRv6/IPv6 encapsulation) related to TN CoS.  Based on TN QoS Class
   marking, per hop behavior for all IETF Network Slices is executed on
   TN links.  TN domain transit routers do not evaluate the original IP
   header for QoS-related decisions.  This model is outlined in
   {{figure-15}} for MPLS encapsulation, and in {{figure-16}} for SRv6
   encapsulation.

~~~ aasvg
                                 ┌──────────────┐
                                 │ MPLS Header  │
                                 ├─────┬─────┐  │
                                 │Label│TN TC│  │
┌──────────────┐ ─ ─ ─ ─ ─ ─ ─ ─ ├─────┴─────┴──┤
│  IP Header   │         │╲      │  IP Header   │
│      ┌───────┤         │ ╲     │      ┌───────┤
│      │5G DSCP│ ────────┘  ╲    │      │5G DSCP│
├──────┴───────┤             ╲   ├──────┴───────┤
│              │              ╲  │              │
│              │               ╲ │              │
│              │                ▏│              │
│   Payload    │               ╱ │   Payload    │
│(GTP-U/IPsec) │              ╱  │(GTP-U/IPsec) │
│              │             ╱   │              │
│              │ ────────┐  ╱    │              │
│              │         │ ╱     │              │
│              │         │╱      │              │
└──────────────┘ ─ ─ ─ ─ ─ ─ ─ ─ └──────────────┘
~~~
{: #figure-15 title="QoS with MPLS Encapsulation" artwork-align="center"}

~~~ aasvg
                                 ┌──────────────┐
                                 │ IPv6 Header  │
                                 │      ┌───────┤
                                 │      │TN DSCP│
                                 ├──────┴───────┤
                                     optional
                                 │     IPv6     │
                                      headers
┌──────────────┐ ─ ─ ─ ─ ─ ─ ─ ─ ├──────────────┤
│  IP Header   │         │╲      │  IP Header   │
│      ┌───────┤         │ ╲     │      ┌───────┤
│      │5G DSCP│ ────────┘  ╲    │      │5G DSCP│
├──────┴───────┤             ╲   ├──────┴───────┤
│              │              ╲  │              │
│              │               ╲ │              │
│              │                ▏│              │
│   Payload    │               ╱ │   Payload    │
│(GTP-U/IPsec) │              ╱  │(GTP-U/IPsec) │
│              │             ╱   │              │
│              │ ────────┐  ╱    │              │
│              │         │ ╱     │              │
│              │         │╱      │              │
└──────────────┘ ─ ─ ─ ─ ─ ─ ─ ─ └──────────────┘
~~~
{: #figure-16 title="QoS with IPv6 Encapsulation" artwork-align="center"}

   From the QoS perspective, both options are similar.  However, there
   is one difference between the two options.  The MPLS TC is only 3
   bits (8 possible combinations), while DSCP is 6 bits (64 possible
   combinations).  Hence, SRv6 {{?RFC8754}} provides more flexibility for TN CoS
   design, especially in combination with soft policing with in-profile/
   out-profile traffic, as discussed in {{sec-inbound-edge-resource-control}}.

   Edge resources are controlled in a granular, fine-grained
   manner, with dedicated resource allocation for each IETF Network
   Slice.  The resource control/enforcement happens at each SDP in two
   directions: inbound and outbound.


###  Inbound Edge Resource Control {#sec-inbound-edge-resource-control}

   The main aspect of inbound edge resource control is per-slice traffic
   capacity enforcement.  This kind of enforcement is often called
   'admission control' or 'traffic conditioning'.  The goal of this
   inbound enforcement is to ensure that the traffic above the
   contracted rate is dropped or deprioritized, depending on the
   business rules, right at the edge of TN domain.  This, combined with
   appropriate network capacity planning/management ({{sec-capacity-planning}}) is required to ensure proper isolation between slices in
   a scalable manner.  As a result, traffic of one slice has no influence
   on the traffic of other slices, even if the slice is misbehaving
   (e.g., DDoS attacks or node/link failures) and generates traffic
   volumes above the contracted rates.

   The slice rates can be characterized with following parameters
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}:

   *  CIR: Committed Information Rate (i.e., guaranteed bandwidth)

   *  PIR: Peak Information Rate (i.e., maximum bandwidth)

   These parameters define the traffic characteristics of the slice and
   are part of SLO parameter set provided by the SMO to IETF NSC.  Based
   on these parameters the inbound policy can be implemented using one
   of following options:

   *  1r2c (single-rate two-color) rate limiter

      This is the most basic rate limiter, which meters at the SDP a
      traffic stream of given slice and marks its packets as in-contract
      (below contracted CIR) or out-of-contract (above contracted CIR).
      In-contract packets are accepted and forwarded.  Out-of contract
      packets are either dropped right at the SDP (hard rate limiting),
      or remarked (with different MPLS TC or DSCP TN markings) to
      signify 'this packet should be dropped in the first place, if
      there is a congestion' (soft rate limiting), depending on the
      business policy of the operator.  In the second case, while
      packets above CIR are forwarded at the SDP, they are subject to being
      dropped during any congestion event at any place in the TN domain.

   *  2r3c (two-rate three-color) rate limiter

      This was initially defined in {{!RFC2698}}, and its improved version
      in {{!RFC4115}}.  In essence, the traffic is assigned to one of the these three
      categories:

        -  Green, for traffic under CIR

        -  Yellow, for traffic between CIR and PIR

        -  Red, for traffic above PIR


      An inbound 2r3c meter implemented with {{!RFC4115}}, compared to
      {{?RFC2698}}, is more 'customer friendly' as it doesn't impose
      outbound peak-rate shaping requirements on customer edge (CE)
      devices. 2r3c meters in general give greater flexibility for edge
      enforcement regarding accepting the traffic (green), de-
      prioritizing and potentially dropping the traffic during
      congestion (yellow), or hard dropping the traffic (red).

   Inbound edge enforcement model for 5QI-unaware model, where all packets
   belonging to the slice are treated the same way in the TN domain (no
   5Q QoS Class differentiation in the TN domain) is outlined in
   {{figure-17}}.

~~~ aasvg
            Slice
           policer     ┌─────────┐
              ║    ┌───┴──┐      │
              ║    │      │      │
              ║    │    S │      │
              ║    │    l │      │
              ▼    │    i │      │
──────────────◇────┼──▶ c │      │
                   │    e │  A   │
                   │      │  t   │
                   │    1 │  t   │
                   │      │  a   │
                   ├──────┤  c   │
                   │      │  h   │
                   │    S │  m   │
                   │    l │  e   │
                   │    i │  n   │
──────────────◇────┼──▶ c │  t   │
                   │    e │      │
                   │      │  C   │
                   │    2 │  i   │
                   │      │  r   │
                   ├──────┤  c   │
                   │      │  u   │
                   │    S │  i   │
                   │    l │  t   │
                   │    i │      │
──────────────◇────┼──▶ c │      │
                   │    e │      │
                   │      │      │
                   │    3 │      │
                   │      │      │
                   └───┬──┘      │
                       └─────────┘
~~~
{: #figure-17 title="Ingress Slice Admission Control (5QI-unware Model)" artwork-align="center"}

###  Outbound Edge Resource Control

   While inbound slice admission control at the transport edge is
   mandatory in the model, outbound edge resource control might not be
   required in all use cases.  Use cases that specifically call for
   outbound edge resource control are:

   *  Slices use both CIR and PIR parameters, and transport edge links
      (attachment circuits) are dimensioned to fulfil the aggregate of
      slice CIRs.  If at any given time, some slices send the traffic
      above CIR, congestion in outbound direction on the transport edge
      link might happen.  Therefore, fine-grained resource control to
      guarantee at least CIR for each slice is required.

   *  Any-to-Any (A2A) connectivity constructs are deployed, again
      resulting in potential congestion in outbound direction on the
      transport edge links, even if only slice CIR parameters are used.
      This again requires fine-grained resource control per slice in
      outbound direction at transport edge links.

   As opposed to inbound edge resource control, typically implemented
   with rate-limiters/policers, outbound resource control is typically
   implemented with a weighted/priority queuing, potentially combined
   with optional shapers (per slice).  A detailed analysis of different
   queuing mechanisms is out of scope for this document, but is provided
   in {{?RFC7806}}.

   {{figure-18}} outlines the outbound edge resource control model at the
   transport network layer for 5QI-unaware slices.  Each slice is
   assigned a single egress queue.  The sum of slice CIRs, used as the
   weight in weighted queueing model, MUST NOT exceed the physical
   capacity of the attachment circuit.  Slice requests above this limit
   MUST be rejected by the NSC, unless an already established slice with
   lower priority, if such exists, is preempted.

~~~ aasvg
      ┌─────────┐        QoS output queues
      │     ┌───┴──┐─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
      │     │ S    │                            ╲│╱
      │     │ l    │                             │
      │     │ i    │                             │
      │  A  │ c    │                             │  weight=Slice-1-CIR
      │  t  │ e  ┌─┴──────────────────────────┐  │ shaping=Slice-1-PIR
   ───┼──t──┼────▶                            │  │
      │  a  │ 1  └─┬──────────────────────────┘ ╱│╲
      │  c  ├──────┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
      │  h  │ S    │                            ╲│╱
      │  m  │ l    │                             │
      │  e  │ i    │                             │
      │  n  │ c    │                             │  weight=Slice-2-CIR
      │  t  │ e  ┌─┴──────────────────────────┐  │ shaping=Slice-2-PIR
   ───┼─────┼────▶                            │  │
      │  C  │ 2  └─┬──────────────────────────┘ ╱│╲
      │  i  ├──────┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
      │  r  │ S    │                            ╲│╱
      │  c  │ l    │                             │
      │  u  │ i    │                             │
      │  i  │ c    │                             │  weight=Slice-3-CIR
      │  t  │ e  ┌─┴──────────────────────────┐  │ shaping=Slice-3-PIR
   ───┼─────┼────▶                            │  │
      │     │ 3  └─┬──────────────────────────┘ ╱│╲
      │     └───┬──┘─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
      └─────────┘
~~~
{: #figure-18 title="Ingress Slice Admission control (5QI-unaware Model)" artwork-align="center"}

##  5QI-aware Model

   In the 5QI-aware model, potentially a large number of 5G QoS Classes, represented via DSCP set by NFs
   (the architecture scales to thousands of 5G slices) is mapped
   (multiplexed) to up to 8 TN QoS Classes used in transport transit
   equipment, as outlined in {{figure-19}}.

~~~ aasvg
  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
  ┏━━━━━━━━━━━━━━━━━┓         ETN                              │
  ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃
  ┃   SDP           ┃              ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
  ┃│  ┌──────────┐ │┃              ┃       Transit link        ┃
  ┃   │5G DSCP A ├───────────────┐ ┃┌────────────────────────┐ ┃
I ┃│  └──────────┘ │┃            ├──▶     TN QoS Class 1     │ ┃
E ┃   ┌──────────┐  ┃            │ ┃└────────────────────────┘ ┃
T ┃│  │5G DSCP B ├───────────┐   │ ┃┌────────────────────────┐ ┃
F ┃   └──────────┘  ┃        │   │ ┃│     TN QoS Class 2     │ ┃
  ┃│  ┌──────────┐ │┃        │   │ ┃└────────────────────────┘ ┃
N ┃   │5G DSCP C ├──╋─────┐  │   │ ┃┌────────────────────────┐ ┃
S ┃│  └──────────┘ │┃     │  │   │ ┃│     TN QoS Class 3     │ ┃
  ┃   ┌──────────┐  ┃     │  │   │ ┃└────────────────────────┘ ┃
1 ┃│  │5G DSCP D ├─────┐  │  │   │ ┃┌────────────────────────┐ ┃
  ┃   └──────────┘  ┃  │  │  ├──────▶     TN QoS Class 4     │ ┃
  ┃└ ─ ─ ─ ─ ─ ─ ─ ┘┃  │  │  │   │ ┃└────────────────────────┘ ┃
  ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃  │  │  │   │ ┃┌────────────────────────┐ ┃
  ┃   ┌──────────┐  ┃  │  ├─────────▶     TN QoS Class 5     │ ┃
  ┃│  │5G DSCP A ├─────│──│──│───┘ ┃└────────────────────────┘ ┃
I ┃   └──────────┘  ┃  │  │  │     ┃┌────────────────────────┐ ┃
E ┃│  ┌──────────┐ │┃  │  │  │     ┃│     TN QoS Class 6     │ ┃
T ┃   │5G DSCP E ├─────│──│──┘     ┃└────────────────────────┘ ┃
F ┃│  └──────────┘ │┃  │  │        ┃┌────────────────────────┐ ┃
  ┃   ┌──────────┐  ┃  │  │        ┃│     TN QoS Class 7     │ ┃
N ┃│  │5G DSCP F ├─────│──┘        ┃└────────────────────────┘ ┃
S ┃   └──────────┘  ┃  │           ┃┌────────────────────────┐ ┃
  ┃│  ┌──────────┐ │┃  ├────────────▶     TN QoS Class 8     │ ┃
2 ┃   │5G DSCP G ├─────┘           ┃└────────────────────────┘ ┃
  ┃│  └──────────┘ │┃              ┃     Max 8 TN Classes      ┃
  ┃   SDP           ┃              ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
  ┃└ ─ ─ ─ ─ ─ ─ ─ ┘┃                                          │
  ┣━━━━━━━━━━━━━━━━━┛
   ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
  Fine-grained QoS enforcement         Coarse QoS enforcement
    (dedicated resources per            (resources shared by
      IETF Network Slice)                multiple IETF NSs)
~~~
{: #figure-19 title="Slice 5Q QoS to TN QoS Mapping (5QI-aware Model)" artwork-align="center"}

   Given that in large scale deployments (large number of 5G
   slices), the number of potential 5G QoS Classes is much higher than
   the number of TN QoS Classes, multiple 5G QoS Classes with similar
   characteristics - potentially from different slices -
   would be grouped with common operator-defined TN logic and mapped to a same TN QoS Class when transported in the TN
   domain.  That is, common per hop behavior (PHB) is executed on
   transit TN routers for all packets grouped together. An example of this
   approach is outlined in {{figure-34}}.

    > Please note that the numbers indicated in {{figure-34}} (S-NSSAI, 5QI, DSCP, queue, etc.) are provided for illustration purposes only and shoudl not be considered as deployment guidance.

~~~ aasvg
                      ┌───────────── ETN  ─────────────────┐
┌────── NF-A ──────┐  │                                    │
│                  │  │ ┌ ─ ─ ─ ─ ┐                        │
│ 3GPP S-NSSAI 100 │  │     SDP                            │
│┌──────┐ ┌───────┐│  │ │┌───────┐│                        │
││5QI=1 ├─▶DSCP=46├──────▶DSCP=46├───┐                     │
│└──────┘ └───────┘│  │ │└───────┘│  │                     │
│┌──────┐ ┌───────┐│  │  ┌───────┐   │                     │
││5QI=65├─▶DSCP=46├──────▶DSCP=46├┼──┤                     │
│└──────┘ └───────┘│  │  └───────┘   │                     │
│┌──────┐ ┌───────┐│  │ │┌───────┐│  │                     │
││5QI=7 ├─▶DSCP=10├──────▶DSCP=10──────┐  ┌──────────────┐ │
│└──────┘ └───────┘│  │ │└───────┘│  │ │  │TN QoS Class 5│ │
└──────────────────┘  │  ─ ─ ─ ─ ─   ├─│──▶   Queue 5    │ │
                      │              │ │  └──────────────┘ │
┌────── NF-B ──────┐  │              │ │                   │
│                  │  │ ┌ ─ ─ ─ ─ ┐  │ │                   │
│ 3GPP S-NSSAI 200 │  │     SDP      │ │                   │
│┌──────┐ ┌───────┐│  │ │┌───────┐│  │ │                   │
││5QI=1 ├─▶DSCP=46├──────▶DSCP=46├───┤ │  ┌──────────────┐ │
│└──────┘ └───────┘│  │ │└───────┘│  │ │  │TN QoS Class 1│ │
│┌──────┐ ┌───────┐│  │  ┌───────┐   │ ├──▶   Queue 1    │ │
││5QI=65├─▶DSCP=46├──────▶DSCP=46├┼──┘ │  └──────────────┘ │
│└──────┘ └───────┘│  │  └───────┘     │                   │
│┌──────┐ ┌───────┐│  │ │┌───────┐│    │                   │
││5QI=7 ├─▶DSCP=10├──────▶DSCP=10├─────┘                   │
│└──────┘ └───────┘│  │ │└───────┘│                        │
└──────────────────┘  │  ─ ─ ─ ─ ─                         │
                      └────────────────────────────────────┘
~~~
{: #figure-34 title="Example of 3GPP QoS Mapped to TN QoS" artwork-align="center"}

In current SDO progress of 3GPP (Rel.17) and O-RAN the mapping of 5QI to
DSCP is not expected in per-slice fashion, where 5QI to DSCP mapping may
vary from 3GPP slice to 3GPP slice, hence the mapping of 5G QoS DSCP values
to TN QoS Classes may be rather common.

   Like in 5QI-unaware model, the original IP header retains the DCSP
   marking corresponding to 5QI (5G QoS Class), while the new header
   (MPLS or IPv6) carries QoS marking related to TN QoS Class.  Based on
   TN QoS Class marking, per hop behavior for all aggregated 5G QoS
   Classes from all IETF Network Slices is executed on TN links.  TN
   domain transit routers do not evaluate original IP header for QoS
   related decisions.  The original DSCP marking retained in the
   original IP header is used at the PE for fine-grained per slice and
   per 5G QoS Class inbound/outbound enforcement on the AC.

   In 5QI-aware model, compared to 5QI-unware model, edge resources are controlled in an even more
   granular, fine-grained manner, with dedicated resource allocation for
   each IETF Network Slice and dedicated resource allocation for number
   of traffic classes (most commonly up 4 or 8 traffic classes,
   depending on the HW capability of the equipment) within each IETF
   Network Slice.

###  Inbound Edge Resource Control

   Compared to the 5QI-unware model, admission control (traffic
   conditioning) in the 5QI-aware model is more granular, as it enforces
   not only per slice capacity constraints, but may as well enforce the
   constraints per 5G QoS Class within each slice.

   5G slice using multiple 5QIs can potentially specify rates in one of
   the following ways:

   *  Rates per traffic class (CIR or CIR+PIR), no rate per slice (sum
      of rates per class gives the rate per slice).

   *  Rate per slice (CIR or CIR+PIR), and rates per prioritized
      (premium) traffic classes (CIR only).  Best effort traffic class
      uses the bandwidth (within slice CIR/PIR) not consumed by
      prioritized classes.

   In the first option, the slice admission control is executed with
   traffic class granularity, as outlined in {{figure-20}}.  In this model,
   if a premium class doesn't consume all available class capacity, it
   cannot be reused by non-premium (i.e., Best Effort) class.

~~~ aasvg
                     Class             ┌─────────┐
                    policer         ┌──┴───┐     │
                                    │      │     │
5Q-QoS-A: CIR-1A ──────◇────────────┼──▶ S │     │
5Q-QoS-B: CIR-1B ──────◇────────────┼──▶ l │     │
5Q-QoS-C: CIR-1C ──────◇────────────┼──▶ i │     │
                                    │    c │     │
                                    │    e │     │
   BE CIR/PIR-1D ──────◇────────────┼──▶   │  A  │
                                    │    1 │  t  │
                                    │      │  t  │
                                    ├──────┤  a  │
                                    │      │  c  │
5Q-QoS-A: CIR-2A ──────◇────────────┼─▶  S │  h  │
5Q-QoS-B: CIR-2B ──────◇────────────┼─▶  l │  m  │
5Q-QoS-C: CIR-2C ──────◇────────────┼─▶  i │  e  │
                                    │    c │  n  │
                                    │    e │  t  │
   BE CIR/PIR-2D ──────◇────────────┼─▶    │     │
                                    │    2 │  C  │
                                    │      │  i  │
                                    ├──────┤  r  │
                                    │      │  c  │
5Q-QoS-A: CIR-3A ──────◇────────────┼─▶  S │  u  │
5Q-QoS-B: CIR-3B ──────◇────────────┼─▶  l │  i  │
5Q-QoS-C: CIR-3C ──────◇────────────┼─▶  i │  t  │
                                    │    c │     │
                                    │    e │     │
   BE CIR/PIR-3D───────◇────────────┼─▶    │     │
                                    │    3 │     │
                                    │      │     │
                                    └──┬───┘     │
                                       └─────────┘
~~~
{: #figure-20 title="Ingress Slice Admission Control (5QI-aware Model)" artwork-align="center"}

   The second model combines the advantages of 5QI-unaware model (per
   slice admission control) with the per traffic class admission
   control, as outlined in {{figure-20}}.  Ingress admission control is at
   class granularity for premium classes (CIR only).  Non-premium class
   (i.e.,  Best Effort) has no separate class admission control policy,
   but it is allowed to use the entire slice capacity, which is available at
   any given moment.  I.e., slice capacity, which is not consumed by
   premium classes.  It is a hierarchical model, as depicted in
   {{figure-21}}.

~~~ aasvg
                              Slice
                             policer   ┌─────────┐
                   Class        .   ┌──┴───┐     │
                  policer      ; :  │      │     │
5Q-QoS-A: CIR-1A ────◇─────────┤─┼──┼──▶ S │     │
5Q-QoS-B: CIR-1B ────◇─────────┤─┼──┼──▶ l │     │
5Q-QoS-C: CIR-1C ────◇─────────┤─┼──┼──▶ i │     │
                               │ │  │    c │     │
                               │ │  │    e │     │
   BE CIR/PIR-1D ──────────────┤─┼──┼──▶   │  A  │
                               │ │  │    1 │  t  │
                               : ;  │      │  t  │
                                .   ├──────┤  a  │
                               ; :  │      │  c  │
5Q-QoS-A: CIR-2A ────◇─────────┤─┼──┼──▶ S │  h  │
5Q-QoS-B: CIR-2B ────◇─────────┤─┼──┼──▶ l │  m  │
5Q-QoS-C: CIR-2C ────◇─────────┤─┼──┼──▶ i │  e  │
                               │ │  │    c │  n  │
                               │ │  │    e │  t  │
   BE CIR/PIR-2D ──────────────┤─┼──┼──▶   │     │
                               │ │  │    2 │  C  │
                               : ;  │      │  i  │
                                .   ├──────┤  r  │
                               ; :  │      │  c  │
5Q-QoS-A: CIR-3A ────◇─────────┤─┼──┼──▶ S │  u  │
5Q-QoS-B: CIR-3B ────◇─────────┤─┼──┼──▶ l │  i  │
5Q-QoS-C: CIR-3C ────◇─────────┤─┼──┼──▶ i │  t  │
                               │ │  │    c │     │
                               │ │  │    e │     │
   BE CIR/PIR-3D ──────────────┤─┼──┼──▶   │     │
                               │ │  │    3 │     │
                               : ;  │      │     │
                                '   └──┬───┘     │
                                       └─────────┘
~~~
{: #figure-21 title="Ingress Slice Admission Control (5QI-aware) - Hierarchical" artwork-align="center"}

###  Outbound Edge Resource Control

   {{figure-22}} outlines the outbound edge resource control model at the
   transport network layer for 5QI-aware slices.  Each slice is assigned
   multiple egress queues.  The sum of queue weights (equal to 5Q QoS
   CIRs within the slice) CIRs MUST NOT exceed the CIR of the slice
   itself.  And, similarly to the 5QI-aware model, the sum of slice CIRs
   MUST NOT exceed the physical capacity of the attachment circuit.

~~~ aasvg
   ┌─────────┐        QoS output queues
   │     ┌───┴──┐─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │     │    ┌─┴──────────────────────────┐ ╲│╱
───┼─────┼────▶ 5Q-QoS-A: w=5Q-QoS-A-CIR   │  │
   │     │ S  └─┬──────────────────────────┘  │
   │     │ l  ┌─┴──────────────────────────┐  │
───┼─────┼─i──▶ 5Q-QoS-B: w=5Q-QoS-B-CIR   │  │
   │     │ c  └─┬──────────────────────────┘  │  weight=Slice-1-CIR
   │     │ e  ┌─┴──────────────────────────┐  │ shaping=Slice-1-PIR
───┼─────┼────▶ 5Q-QoS-C: w=5Q-QoS-C-CIR   │  │
   │     │ 1  └─┬──────────────────────────┘  │
   │     │    ┌─┴──────────────────────────┐  │
───┼─────┼────▶ Best Effort (remainder)    │  │
   │     │    └─┬──────────────────────────┘ ╱│╲
   │  A  ├──────┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │  t  │    ┌─┴──────────────────────────┐ ╲│╱
   │  t  │    │                            │  │
   │  a  │    └─┬──────────────────────────┘  │
   │  c  │ S  ┌─┴──────────────────────────┐  │
   │  h  │ l  │                            │  │
   │  m  │ i  └─┬──────────────────────────┘  │  weight=Slice-2-CIR
   │  e  │ c  ┌─┴──────────────────────────┐  │ shaping=Slice-2-PIR
   │  n  │ e  │                            │  │
   │  t  │    └─┬──────────────────────────┘  │
   │     │ 2  ┌─┴──────────────────────────┐  │
   │  C  │    │                            │  │
   │  i  │    └─┬──────────────────────────┘ ╱│╲
   │  r  ├──────┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │  c  │    ┌─┴──────────────────────────┐ ╲│╱
   │  u  │    │                            │  │
   │  i  │ S  └─┬──────────────────────────┘  │
   │  t  │ l  ┌─┴──────────────────────────┐  │
   │     │ i  │                            │  │
   │     │ c  └─┬──────────────────────────┘  │  weight=Slice-3-CIR
   │     │ e  ┌─┴──────────────────────────┐  │ shaping=Slice-3-PIR
   │     │    │                            │  │
   │     │ 3  └─┬──────────────────────────┘  │
   │     │    ┌─┴──────────────────────────┐  │
   │     │    │                            │  │
   │     │    └─┬──────────────────────────┘ ╱│╲
   │     └───┬──┘─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   └─────────┘
~~~
{: #figure-22 title="Egress Slice Admission Control (5QI-aware)" artwork-align="center"}

##  Transit Resource Control

   Transit resource control is much simpler than Edge resource control.
   As outlined in {{figure-19}}, at the edge, 5Q QoS Class marking
   (represented by DSCP related to 5QI set by mobile network functions
   in the packets handed off to the TN) is mapped to the TN QoS Class.
   Based in TN QoS Class, when the packet is encapsulated with outer
   header (MPLS or IPv6), TN QoS Class marking (MPLS TC or IPv6 DSCP in
   outer header, as depicted in {{figure-15}} and {{figure-16}}) is set in the
   outer header.  PHB on transit is based exclusively on that TN QoS
   Class marking, i.e., original 5G QoS Class DSCP is not taken into
   consideration on transit.

   Transit resource control does not use any inbound interface policy,
   but only outbound interface policy, which is based on priority queue
   combined with weighted or deficit queuing model, without any shaper.
   The main purpose of transit resource control is to ensure that during
   network congestion events, for example caused by network failures and
   temporary rerouting, premium classes are prioritized, and any drops
   only occur in traffic that was de-prioritized by ingress admission control {{sec-inbound-edge-resource-control}} or in non-premium (best-effort) classes.  Capacity planning and management, as described in {{sec-capacity-planning}}, ensures that enough
   capacity is available to fulfill all approved slice requests.

#  Transport Planes Mapping Models

   A network operator might define various tunnel groups, where each
   tunnel group is created with specific optimization criteria and
   constraints.  This document refers to such tunnel groups as
   'transport planes'.  For example, a transport plane "A" might represent
   tunnels optimized for latency, and transport plane "B" represent tunnels optimized for high capacity.

   {{figure-23}} depicts an example of a simple network with two transport
   planes.  These transport planes might be realized via various IP/MPLS
   techniques, for example Flex-Algo or RSVP/SR traffic engineering
   tunnels with or without PCE, and with or without bandwidth
   reservations.

   {{sec-capacity-planning}} discusses in detail different bandwidth
   models that can be deployed in the transport network.  However,
   discussion about how to realize or orchestrate transport planes is
   out of scope for this document.

~~~ aasvg
┌───────────────┐                                    ┌──────┐
│  Ingress PE   │   ╔═══════════════════════════════▶│ PE-A │
│               │   ║   ╔═══════════════════════════▷│      │
│  ┌ ─ ─ ─ ─ ┐  │   ║   ╚═════════════════════╗      └──────┘
│            ●══════╝   ╔═════════════════════╝
│  │Transport●════════════════════════════════╗      ┌──────┐
│    Plane A ●═════════════╗                  ╚═════▶│ PE-B │
│  │         ●═══════╗  ║  ║  ╔═══╗   ╔═══╗   ╔═════▷│      │
│   ─ ─ ─ ─ ─   │    ║  ║  ║  ║   ║   ║   ║   ║      └──────┘
│               │    ║  ║  ║  ║   ╚═══╝   ╚═══╝
│  ┌ ─ ─ ─ ─ ┐  │    ║  ║  ║  ║                      ┌──────┐
│            ○═══════║══╝  ╚════════════════════════▶│ PE-C │
│  │Transport○═══════║════════╝               ╔═════▷│      │
│    Plane B ○═══════║═════════════════╗      ║      └──────┘
│  │         ○═════╗ ╚═══════════════╗ ║      ║
│   ─ ─ ─ ─ ─   │  ║ ╔═╗ ╔═╗ ╔═╗ ╔═╗ ║ ╚══════╝      ┌──────┐
│               │  ║ ║ ║ ║ ║ ║ ║ ║ ║ ╚══════════════▶│ PE-D │
└───────────────┘  ╚═╝ ╚═╝ ╚═╝ ╚═╝ ╚════════════════▷│      │
                                                     └──────┘
         ●════════▶  Tunnels of Transport Plane A
         ○════════▷  Tunnels of Transport Plane B
~~~
{: #figure-23 title="Transport Planes" artwork-align="center"}

   Note that there could be multiple tunnels within a single transport plane
   between any pair of PEs. For readibility, {{figure-23}} shows only single
   tunnel per transport plane for [ingress PE, egress PE] pair.

   Similar to the QoS mapping models discussed in {{sec-qos-map}}, for mapping
   to transport planes at the ingress PE, both 5QI-unaware and 5QI-aware
   modes are defined.  In essence, entire slices can be mapped to
   transport planes without 5G QoS consideration (5QI-unaware mode), or
   flows with different 5G QoS Classes, even if they are from the same
   slice, might be mapped to different transport planes (5QI-aware
   mode).

##  5QI-unaware Model

   As discussed in {{sec-5QI-unaware}}, in the 5QI-unware model, the TN domain
   doesn't take into account 5G QoS during execution of per-hop
   behavior.  The entire slice is mapped to single TN QoS Class,
   therefore the entire slice is subject to the same per-hop behavior.
   Similarly, in 5QI-unaware transport plane mapping model, the entire
   slice is mapped to a single transport plane, as depicted in
   {{figure-24}}.

~~~ aasvg
   ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   ┏━━━━━━━━━━━━━━━━━┓                        │
   ┃ Attach. Circuit ┃      PE router
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃                        │
   ┃   SDP           ┃
   ┃│  ┌──────────┐ │┃                        │
   ┃   │IETF NS 1 ├──────────┐
   ┃│  └──────────┘ │┃       │                │
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃       │
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃       │   ┌─────────┐  │
   ┃   SDP           ┃       │   │         │
   ┃│  ┌──────────┐ │┃       │   │Transport│  │
   ┃   │IETF NS 2 ├──────┐   ├───▶  Plane  │
   ┃│  └──────────┘ │┃   │   │   │    A    │  │
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃   │   │   │         │
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃   │   │   └─────────┘  │
   ┃   SDP           ┃   │   │
   ┃│  ┌──────────┐ │┃   │   │                │
   ┃   │IETF NS 3 ├──────┤   │
   ┃│  └──────────┘ │┃   │   │   ┌─────────┐  │
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃   │   │   │         │
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃   │   │   │Transport│  │
   ┃   SDP           ┃   ├───│───▶  Plane  │
   ┃│  ┌──────────┐ │┃   │   │   │    B    │  │
   ┃   │IETF NS 4 ├──────┘   │   │         │
   ┃│  └──────────┘ │┃       │   └─────────┘  │
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃       │
   ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃       │                │
   ┃   SDP           ┃       │
   ┃│  ┌──────────┐ │┃       │                │
   ┃   │IETF NS 5 ├──────────┘
   ┃│  └──────────┘ │┃                        │
   ┃ ─ ─ ─ ─ ─ ─ ─ ─ ┃
   ┗━━━━━━━━━━━━━━━━━┛                        │
   └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
~~~
{: #figure-24 title="Slice to Transport Plane Mapping (5QI-unaware Model)" artwork-align="center"}

   It is worth noting that there is no strict correlation between TN QoS
   Classes and Transport Planes.  The TN domain can be operated with
   e.g., 8 TN QoS Classes (representing 8 hardware queues in the
   routers), and 2 Transport Planes (e.g., latency optimized transport
   plane using link latency metrics for path calculation, and transport
   plane following IGP metrics).  TN QoS Class determines the per-hop
   behavior when the packets are transiting through the TN domain, while
   Transport Plane determines the path, optimized or constrained based
   on operator's business criteria, that the packets use to transit
   through the TN domain.


##  5QI-aware Model

   In 5QI-aware model, the traffic can be mapped to transport planes at
   the granularity of 5G QoS Class.  Given that the potential number of
   transport planes is limited, packets from multiple 5G QoS Classes
   with similar characteristics are mapped to a common transport plane,
   as depicted in {{figure-25}}.

~~~ aasvg
     ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
     ┏━━━━━━━━━━━━━━━━━┓
     ┃ Attach. Circuit ┃                         │
     ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃        PE router
     ┃   SDP           ┃                         │
     ┃│  ┌──────────┐ │┃
     ┃   │ 5G QoS A ├──────┐                     │
   I ┃│  └──────────┘ │┃   │
   E ┃   ┌──────────┐  ┃   │                     │
   T ┃│  │ 5G QoS B ├──────┤
   F ┃   └──────────┘  ┃   │         ┌─────────┐ │
     ┃│  ┌──────────┐ │┃   │         │         │
   N ┃   │ 5G QoS C ├───────────┐    │Transport│ │
   S ┃│  └──────────┘ │┃   ├────│────▶  Plane  │
     ┃   ┌──────────┐  ┃   │    │    │    A    │ │
   1 ┃│  │ 5G QoS D ├───────────┤    │         │
     ┃   └──────────┘  ┃   │    │    └─────────┘ │
     ┃└ ─ ─ ─ ─ ─ ─ ─ ┘┃   │    │
     ┃┌ ─ ─ ─ ─ ─ ─ ─ ┐┃   │    │                │
     ┃   ┌──────────┐  ┃   │    │
     ┃│  │ 5G QoS A ├──────┤    │    ┌─────────┐ │
   I ┃   └──────────┘  ┃   │    │    │         │
   E ┃│  ┌──────────┐ │┃   │    │    │Transport│ │
   T ┃   │ 5G QoS E ├──────┘    ├────▶  Plane  │
   F ┃│  └──────────┘ │┃        │    │    B    │ │
     ┃   ┌──────────┐  ┃        │    │         │
   N ┃│  │ 5G QoS F ├───────────┤    └─────────┘ │
   S ┃   └──────────┘  ┃        │
     ┃│  ┌──────────┐ │┃        │                │
   2 ┃   │ 5G QoS G ├───────────┘
     ┃│  └──────────┘ │┃                         │
     ┃   SDP           ┃
     ┃└ ─ ─ ─ ─ ─ ─ ─ ┘┃                         │
     ┗━━━━━━━━━━━━━━━━━┛
     └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
~~~
{: #figure-25 title="Slice to Transport Plane mapping (5QI-aware Model)" artwork-align="center"}

#  Capacity Planning/Management {#sec-capacity-planning}

## Bandwidth Requirements

   This section describes the information conveyed by the SMO to the
   transport controller with respect to slice bandwidth requirements.

   {{figure-26}} shows three DCs that contain instances of network
   functions.  Also shown are PEs that have links to the DCs.  The PEs
   belong to the transport network.  Other details of the transport
   network, such as P-routers and transit links are not shown.  Also
   details of the DC infrastructure such as switches and routers are not
   shown.

   The SMO is aware of the existence of the network functions and their
   locations.  However, it is not aware of the details of the transport
   network.  The transport controller has the opposite view - it is
   aware of the transport infrastructure and the links between the PEs
   and the DCs, but is not aware of the individual network functions.

~~~ aasvg
┌ ─ ─ ─ ─ DC 1─ ─ ─ ─    ┌ ─ ─ ─ ─ ─ ─ ─ ─ ┐   ┌ ─ ─ ─ ─ DC 2─ ─ ─ ─
  ┌──────┐           │  ┌────┐         ┌────┐              ┌──────┐ │
│ │ NF1A │           ───■PE1A│         │PE2A■──┤           │ NF2A │
  └──────┘           │  └────┘         └────┘              └──────┘ │
│ ┌──────┐               │                 │   │           ┌──────┐
  │ NF1B │           │                                     │ NF2B │ │
│ └──────┘               │                 │   │           └──────┘
  ┌──────┐           │  ┌────┐         ┌────┐              ┌──────┐ │
│ │ NF1C │           ───■PE1B│         │PE2B■──┤           │ NF2C │
  └──────┘           │  └────┘         └────┘              └──────┘ │
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─    │    Transport    │   └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─

                         │     Network     │   ┌ ─ ─ ─ ─ DC 3─ ─ ─ ─
                                       ┌────┐              ┌──────┐ │
                         │             │PE3A■──┤           │ NF3A │
                                       └────┘              └──────┘ │
                         │                 │   │           ┌──────┐
                                                           │ NF3B │ │
                         │                 │   │           └──────┘
                                       ┌────┐              ┌──────┐ │
                         │             │PE3B■──┤           │ NF3C │
                                       └────┘              └──────┘ │
                         └ ─ ─ ─ ─ ─ ─ ─ ─ ┘   └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─

  ■ - SDP, with fine-grained QoS (dedicated resources per IETF NS)
~~~
{: #figure-26 title="An Example of Multi-DC Architecture" artwork-align="center"}

   Let us consider 5G Slice "X" that uses some of the network functions in
   the three DCs.  If this slice has latency requirements, the SMO will
   have taken those into account when deciding which NF instances
   in which DC is to be invoked for this slice.  As a result of such a
   placement decision, the three DCs shown are involved in 5G Slice "X",
   rather than other DCs.  For its decision-making, the SMO
   needs information from the NSC about the observed latency between DCs.
   Preferably, the NSC would present the topology in an abstracted form,
   consisting of point-to-point abstracted links between pairs of DCs
   and associated latency and optionally delay variation and link loss
   values.  It would be valuable to have a mechanism for the SMO to
   inform the NSC which DC-pairs are of interest for these metrics -
   there may be of order thousands of DCs, but the SMO will only be
   interested in these metrics for a small fraction of all the possible
   DC-pairs, i.e. those in the same region of the network.  The
   mechanism for conveying the information will be discussed in a future
   version of this document.

   {{figure-27}} shows the matrix of bandwidth demands for 5G slice "X".
   Within the slice, multiple network function instances might be
   sending traffic from DCi to DCj.  However, the SMO sums the
   associated demands into one value.  For example, NF1A and NF1B in DC1
   might be sending traffic to multiple NFs in DC2, but this is
   expressed as one value in the traffic matrix: the total bandwidth
   required for 5G Slice X from DC1 to DC2 (8 units).  Each row in the
   right-most column in the traffic matrix shows the total amount of
   traffic going from a given DC into the transport network, regardless
   of the destination DC.  Note that this number can be less than the
   sum of DC-to-DC demands in the same row, on the basis that not all
   the network functions are likely to be sending at their maximum rate
   simultaneously.  For example, the total traffic from DC1 for Slice X
   is 11 units, which is less than the sum of the DC-to-DC demands in
   the same row (13 units).  Note, as described in {{sec-qos-map}}, a slice
   may have per-QoS class bandwidth requirements, and may have CIR and
   PIR limits.  This is not included in the example, but the same
   principles apply in such cases.

~~~ aasvg
      To┌──────┬──────┬──────┬──────────────┐
From    │ DC 1 │ DC 2 │ DC 3 │Total from DC │
 ┌──────┼──────┼──────┼──────┼──────────────┤
 │ DC 1 │ n/a  │  8   │  5   │     11.0     │
 ├──────┼──────┼──────┼──────┼──────────────┤
 │ DC 2 │  1   │ n/a  │  2   │      2.5     │
 ├──────┼──────┼──────┼──────┼──────────────┤
 │ DC 3 │  4   │  7   │ n/a  │     10.0     │
 └──────┴──────┴──────┴──────┴──────────────┘
                    Slice X

      To┌──────┬──────┬──────┬──────────────┐
From    │ DC 1 │ DC 2 │ DC 3 │Total from DC │
 ┌──────┼──────┼──────┼──────┼──────────────┤
 │ DC 1 │ n/a  │  4   │ 2.5  │     6.0      │
 ├──────┼──────┼──────┼──────┼──────────────┤
 │ DC 2 │ 0.5  │ n/a  │ 0.8  │     1.0      │
 ├──────┼──────┼──────┼──────┼──────────────┤
 │ DC 3 │ 2.6  │  3   │ n/a  │     5.1      │
 └──────┴──────┴──────┴──────┴──────────────┘
                    Slice Y
~~~
{: #figure-27 title="Inter-DC Traffic Demand Matrix" artwork-align="center"}

   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} can be used to convey all
   of the information in the traffic matrix to the IETF NSC.  The IETF
   NSC applies policers corresponding to the last column in the traffic
   matrix to the appropriate PE routers, in order to enforce the
   bandwidth contract.  For example, it applies a policer of 11 units to
   PE1A and PE1B that face DC1, as this is the total bandwidth that DC1
   sends into the transport network corresponding to Slice X.  Also, the
   controller may apply shapers in the direction from the TN to the DC,
   if otherwise there is the possibility of a link in the DC being
   oversubscribed.  Note that a peer NF endpoint of an AC can be
   identified using 'peer-sap-id' as defined in {{?I-D.ietf-opsawg-sap}}.

   Depending on the bandwidth model used in the network ({{sec-bw}}),
   the other values in the matrix, i.e., the DC-to-DC demands, may not
   be directly applied to the transport network.  Even so, the
   information may be useful to the IETF NSC for capacity planning and
   failure simulation purposes.  If, on the other hand, the DC-to-DC
   demand information is not used by the IETF NSC, the IETF YANG Data
   Model for L3VPN Service Delivery {{?RFC8299}} or the IETF YANG Data
   Model for L2VPN Service Delivery {{?RFC8466}} could be used instead of
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}, as they support
   conveying the bandwidth information in the right-most column of the
   traffic matrix.

   The transport network may be implemented in such a way that it has
   various types of paths, for example low-latency traffic might be
   mapped onto a different transport path to other traffic (for example
   a particular flex-algo or a particular set of TE LSPs), as discussed
   in {{sec-qos-map}}.  The SMO can use
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} to request low-latency
   transport for a given slice if required.  However, {{?RFC8299}} or
   {{!RFC8466}} do not support requesting a particular transport-type,
   e.g., low-latency.  One option is to augment these models to convey
   this information.  This can be achieved by reusing the 'underlay-
   transport' construct defined in {{!RFC9182}} and {{!RFC9291}}.

##  Bandwidth Models {#sec-bw}

   This section describes three bandwidth management schemes that could
   be employed in the transport network.  Many variations are possible,
   but each example describes the salient points of the corresponding
   scheme.  Schemes 2 and 3 use TE; other variations on TE are possible
   as described in {{?I-D.ietf-teas-rfc3272bis}}.

### Scheme 1: Shortest Path Forwarding (SPF)

   Shortest path forwarding is used according to the IGP metric.  Given
   that some slices are likely to have latency SLOs, the IGP metric on
   each link can be set to be in proportion to the latency of the link.
   In this way, all traffic follows the minimum latency path between
   endpoints.

   In Scheme 1, although the operator provides bandwidth guarantees to
   the slice customers, there is no explicit end-to-end underpinning of
   the bandwidth SLO, in the form of bandwidth reservations across the
   transport network.  Rather, the expected performance is achieved via
   capacity planning, based on traffic growth trends and anticipated
   future demands, in order to ensure that network links are not over-
   subscribed.  This scheme is analogous to that used in many existing
   business VPN deployments, in that bandwidth guarantees are provided
   to the customers but are not explicitly underpinned end to end across
   the transport network.

   A variation on the scheme is that Flex-Algo, defined in
   {{?I-D.ietf-lsr-flex-algo}}, is used, for example one Flex-Algo could
   use latency-based metrics and another Flex-Algo could use the IGP
   metric.  There would be a many-to-one mapping of slices to Flex-
   Algos.

   While Scheme 1 is technically feasible, it is vulnerable to
   unexpected changes in traffic patterns and/or network element
   failures resulting in congestion.  This is because, unlike Schemes 2
   and 3 that employ TE, traffic cannot be diverted from the shortest
   path.

###  Scheme 2: TE LSPs with Fixed Bandwidth Reservations

   Scheme 2 uses RSVP-TE or SR-TE LSPs with fixed bandwidth
   reservations.  By "fixed", we mean a value that stays constant over
   time, unless the SMO communicates a change in slice bandwidth
   requirements, due to the creation or modification of a slice.  Note
   that the "reservations" would be in the mind of the transport
   controller - it is not necessary (or indeed possible for SR-TE) to
   reserve bandwidth at the network layer.  The bandwidth requirement
   acts as a constraint whenever the controller (re)computes the path of
   an LSP.  There could be a single mesh of LSPs between endpoints that
   carry all of the traffic types, or there could be a small handful of
   meshes, for example one mesh for low-latency traffic that follows the
   minimum latency path and another mesh for the other traffic that
   follows the minimum IGP metric path, as described in {{sec-qos-map}}.
   There would be a many-to-one mapping of slices to LSPs.

   The bandwidth requirement from DCi to DCj is the sum of the DCi-DCj
   demands of the individual slices.  For example, if only Slice X and
   Slice Y are present, then the bandwidth requirement from DC1 to DC2
   is 12 units (8 units for Slice X and 4 units for Slice Y).  When the
   SMO requests a new slice, the transport controller, in its mind,
   increments the bandwidth requirement according to the requirements of
   the new slice.  For example, in {{figure-26}}, suppose a new slice is
   instantiated that needs 0.8 Gbps from DC1 to DC2.  The transport
   controller would increase its notion of the bandwidth requirement
   from DC1 to DC2 from 12 Gbps to 12.8 Gbps to accommodate the
   additional expected traffic.

   In the example, each DC has two PEs facing it for reasons of
   resilience.  The transport controller needs to determine how to map
   the DC1 to DC2 bandwidth requirement to bandwidth reservations of TE
   LSPs from DC1 to DC2.  For example, if the routing configuration is
   arranged such that in the absence of any network failure, traffic
   from DC1 to DC2 always enters PE1A and goes to PE2A, the controller
   reserves 12.8 Gbps of bandwidth on the LSP from PE1A to PE2A.  If, on
   the other hand, the routing configuration is arranged such that in
   the absence of any network failure, traffic from DC1 to DC2 always
   enters PE1A and is load-balanced across PE2A and PE2B, the controller
   reserves 6.4 Gbps of bandwidth on the LSP from PE1A to PE2A and 6.4
   Gbps of bandwidth on the LSP from PE1A to PE2B.  It might be tricky
   for the transport controller to be aware of all conditions that
   change the way traffic lands on the various PEs, and therefore know
   that it needs to change bandwidth reservations of LSPs accordingly.
   For example, there might be an internal failure within DC1 that
   causes traffic from DC1 to land on PE1B, rather than PE1A.  The
   transport controller may not be aware of the failure and therefore
   may not know that it now needs to apply bandwidth reservations to
   LSPs from PE1B to PE2A/PE2B.

### Scheme 3: TE LSPs without Bandwidth Reservation

   Like Scheme 2, Scheme 3 uses RSVP-TE or SR-TE LSPs.  There could be a
   single mesh of LSPs between endpoints that carry all of the traffic
   types, or there could be a small handful of meshes, for example one
   mesh for low-latency traffic that follows the minimum latency path
   and another mesh for the other traffic that follows the minimum IGP
   metric path, as described in {{sec-qos-map}}.  There would be a many-to-one
   mapping of slices to LSPs.

   The difference between Scheme 2 and Scheme 3 is that Scheme 3 does
   not have fixed bandwidth reservations for the LSPs.  Instead, actual
   measured data-plane traffic volumes are used to influence the
   placement of TE LSPs.  One way of achieving this is to use
   distributed RSVP-TE with auto-bandwidth.  Alternatively, the
   transport controller can use telemetry-driven automatic congestion
   avoidance.  In this approach, when the actual traffic volume in the
   data plane on given link exceeds a threshold, the controller, knowing
   how much actual data plane traffic is currently travelling along each
   RSVP or SR-TE LSP, can tune the paths of one or more LSPs using the
   link such that they avoid that link.

   It would be undesirable to move a minimum-latency LSP rather than
   another type of LSP in order to ease the congestion, as the new path
   will typically have a higher latency, if the minimum-latency LSP is
   currently following the minimum-latency path.  This can be avoided by
   designing the algorithms described in the previous paragraph such
   that they avoid moving minimum-latency LSPs unless there is no
   alternative.

# IANA Considerations

   This document does not make any IANA request.

#  Security Considerations

   IETF Network Slices considerations are discussed in Section 6 of
   {{!I-D.ietf-teas-ietf-network-slices}}.

   TBC.

--- back


# Acronyms and Abbreviations {#ext-abbr}

   3GPP: 3rd Generation Partnership Project

   5GC: 5G Core

   5QI: 5G QoS Indicator

   A2A: Any-to-Any

   AC: Attachment Circuit

   AMF: Access and Mobility Management Function

   AUSF: Authentication Server Function

   BBU: Baseband Unit

   BH: Backhaul

   BS: Base Station

   CE: Customer Edge

   CIR: Committed Information Rate

   CN: Core Network

   CoS: Class of Service

   CP: Control Plane

   CSP: Communication Service Provider

   CU: Centralized Unit

   CU-CP: Centralized Unit Control Plane

   CU-UP: Centralized Unit User Plane

   DC: Data Center

   DDoS: Distributed Denial of Services

   DN: Data Network

   DSCP: Differentiated Services Code Point

   DU: Distributed Unit

   eCPRI: enhanced Common Public Radio Interface

   FH: Fronthaul

   FIB: Forwarding Information Base

   GPRS: Generic Packet Radio Service

   gNB: gNodeB

   GTP: GPRS Tunneling Protocol

   GTP-U: GPRS Tunneling Protocol User plane

   HW: Hardware

   ID: Identifier

   IGP: Interior Gateway Protocol

   IP: Internet Protocol

   L2VPN: Layer 2 Virtual Private Network

   L3VPN: Layer 3 Virtual Private Network

   LSP: Label Switched Path

   MH: Midhaul

   MIoT: Massive Internet of Things

   MPLS: Multiprotocol Label Switching

   NF: Network Function

   NR: New Radio

   NRF: Network Function Repository

   NRP: Network Resource Partition

   NSC: Network Slice Controller

   NSS: Network Slice Subnet

   PE: Provider Edge

   PIR: Peak Information Rate

   PLMN: Public Land Mobile Network

   PSTN: Public Switched Telephony Network

   QoS: Quality of Service

   RAN: Radio Access Network

   RF: Radio Frequency

   RIB: Routing Information Base

   RSVP: Resource Reservation Protocol

   RU: Radio Unit

   SD: Slice Differentiator

   SDP: Service Demarcation Point

   SLA: Service Level Agreement

   SLO: Service Level Objective

   SMF: Session Management Function

   SMO: Service Management and Orchestration

   S-NSSAI: Single Network Slice Selection Assistance Information

   SST: Slice/Service Type

   SR: Segment Routing

   SRv6: Segment Routing version 6

   TC: Traffic Class

   TE: Traffic Engineering

   TN: Transport Network

   TS: Technical Specification

   UDM: Unified Data Management

   UE: User Equipment

   UP: User Plane

   UPF: User Plane Function

   URLLC: Ultra Reliable Low Latency Communication

   VLAN: Virtual Local Area Network

   VNF: Virtual Network Function

   VPN: Virtual Private Network

   VRF: Virtual Routing and Forwarding

   VXLAN: Virtual Extensible Local Area Network

#  An Overview of 5G Networking {#sec-5g-intro}

   This section provides a brief introduction to 5G mobile networking
   with a perspective on the Transport Network.  This section does not
   intend to replace or define 3GPP architecure, it just provides a
   brief overview for readers that do not have a mobile background.  For
   more comprehensive information, refer to {{TS-23.501}}.

##  Key Building Blocks

   {{TS-23.501}} defines the Network Functions (UPF, AMF, etc.) that
   compose the 5G System (5GS) Architecture together with related
   interfaces (e.g., N1, N2).  This architecture has native Control
   and User Plane separation, and the Control Plane leverages a service-
   based architecture.  {{figure-28}} outlines an example 5GS architecture
   with a subset of possible network functions and network interfaces.

~~~ aasvg

  ┌─────┐  ┌─────┐  ┌─────┐    ┌─────┐  ┌─────┐  ┌─────┐
  │NSSF │  │ NEF │  │ NRF │    │ PCF │  │ UDM │  │ AF  │
  └──┬──┘  └──┬──┘  └──┬──┘    └──┬──┘  └──┬──┘  └──┬──┘
Nnssf│    Nnef│    Nnrf│      Npcf│    Nudm│        │Naf
  ───┴────────┴──┬─────┴──┬───────┴───┬────┴────────┴────
            Nausf│    Namf│       Nsmf│
              ┌──┴──┐  ┌──┴──┐     ┌──┴──────┐
              │AUSR │  │ AMF │     │   SMF   │
              └─────┘  └──┬──┘     └──┬──────┘
                       ╱  │           │      ╲
Control Plane      N1 ╱   │N2         │N4     ╲N4
════════════════════════════════════════════════════════════
User Plane          ╱     │           │         ╲
                ┌───┐  ┌──┴──┐  N3 ┌──┴──┐ N9 ┌─────┐ N6  .───.
                │UE ├──┤(R)AN├─────┤ UPF ├────┤ UPF ├────( DN  )
                └───┘  └─────┘     └─────┘    └─────┘     `───'
~~~
{: #figure-28 title="5GS Architecture and Service-based Interfaces" artwork-align="center"}

   Similar to previous versions of 3GPP mobile networks {{?RFC6459}}, a 5G mobile network is split
   into the following four major domains ({{figure-29}}):

   *  UE, MS, MN, and Mobile:

      The terms UE (User Equipment), MS (Mobile Station), MN (Mobile
      Node), and mobile refer to the devices that are hosts with the
      ability to obtain Internet connectivity via a 3GPP network.  An MS
      is comprised of the Terminal Equipment (TE) and a Mobile Terminal
      (MT).  The terms UE, MS, MN, and mobile are used interchangeably
      within this document.

   *  Radio Access Network (RAN):

      Provides wireless connectivity to the UE devices via radio.  It is
      made up of the Antenna that transmits and receives signals to the
      UE and the Base Station that digitizes the signal and converts the
      RF data stream to IP packets.

   *  Core Network (CN):

      Controls the CP of the RAN and provides connectivity to the Data
      Network (e.g., the Internet or a private VPN).  The Core Network
      hosts dozens of services such as authentication, phone registry,
      charging, access to PSTN and handover.

   *  Transport Network (TN):

      Provides connectivity between sites where 5G Network Functions are
      located.  The TN may connect sites from the RAN to the Core
      Network, as well as sites within the RAN or within the CN.  This
      connectivity is achieved using IP.

~~~ aasvg
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
                                               │
│             ┌────────────┐    ┌────────────┐
              │            │    │            │ │
│ ┌────┐      │            │    │            │     .───────.
  │ UE ├──────┤    RAN     │    │     CN     ├────(    DN   )
│ └────┘      │            │    │            │     `───────'
              │            │    │            │ │
│             └──────┬─────┘    └──────┬─────┘
                     │                 │       │
│                    │                 │
                     │                 │       │
│              ┌─────┴─────────────────┴────┐
               │                            │  │
│              │                            │
               │     Transport Network      │  │
│              │                            │
               │                            │  │
│              └────────────────────────────┘
                                               │
│                    5G System
                                               │
└ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
~~~
{: #figure-29 title="Building Blocks of 5G Architecture (A High-Level Representation)" artwork-align="center"}

##  Core Network (CN)

   The 5G Core Network (5GC) is made up of a set of NFs which fall into two main categories ({{figure-30}}):

   *  5GC User Plane:

      The User Plane Function (UPF) is the interconnect
      point between the mobile infrastructure and the Data Network (DN).
      It interfaces with the RAN via the N3 interface by encapsulating/
      decapsulating the User Plane Traffic in GTP Tunnels (aka GTP-U or
      Mobile User Plane).

   *  5GC Control Plane:

      The 5G Control Plane is made up of a
      comprehensive set of Network Functions.  An exhaustive list and
      description of these entities is out of the scope of this
      document.  The following NFs and interfaces are worth mentioning,
      since their connectivity may rely on the Transport Network:

      -  the AMF (Access and Mobility Function) connects with the RAN
         control plane over the N2 interface

      -  the SMF controls the 5GC UPF via the N4 interface

~~~ aasvg
  ┌ ─ ─ ─ ─ ┐    ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
      RAN               5G Core (5GC)
  │         │    │                         │
  │         │    │ [AUSF] [NRF] [UDM] etc. │
  │         │    │     (Service Based)     │
                       ( Architecture)
  │         │    │                         │
              N2     ┌─────┐ N11 ┌─────┐
  │    CP ───────────┤ AMF ├─────┤ SMF │   │
                     └─────┘     └──┬──┘
  │         │    │                  │      │
                                    │         Control Plane
═══════════════════════════════════════════════════════════
                                    │         User Plane
  │         │    │                  │ N4   │
              N3                 ┌──┴──┐     N6  .───────.
  │    UP ───────────────────────┤ UPF ├───────▶(   DN    )
                                 └─────┘         `───────'
  │         │    │                         │
   ─ ─ ─ ─ ─      ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
~~~
{: #figure-30 title="5G Core Network (CN)" artwork-align="center"}

##  Radio Access Network (RAN)

   The RAN connects cellular wireless devices to
   a mobile Core Network.  The RAN is made up of three components,
   which form the Radio Base Station:

   *  The Baseband Unit (BBU) provides the interface between the Core
      Network and the Radio Network.  It connects to the Radio Unit and
      is responsible for the baseband signal processing to packet.

   *  The Radio Unit (RU) is located close to the Antenna and controlled
      by the BBU.  It converts the Baseband signal received from the BBU
      to a Radio frequency signal.

   *  The Antenna converts the electric signal received from the RU to
      radio waves


   The 5G RAN Base Station is called a gNodeB (gNB).  It connects to the
   Core Network via the N3 (User Plane) and N2 (Control Plane)
   interfaces.

   The 5G RAN architecture supports RAN disaggregation in various ways.
   Notably, the BBU can be split into a DU (Distributed Unit) for
   digital signal processing and a CU (Centralized Unit) for RAN Layer 3
   processing.  Furthermore, the CU can be itself split into Control
   Plane (CU-CP) and User Plane (CU-UP).

   {{figure-31}} depicts a disaggregated RAN with NFs and interfaces.


~~~ aasvg
            ┌─────────────────────────────────┐    ┌ ─ ─ ─ ─ ─ ┐
            │                                 │ N3
┌────┐  NR  │                                 ├────┤  5G Core  │
│ UE ├──────┤             gNodeB              │
└────┘      │                                 ├────┤   (5GC)   │
            │                                 │ N2
            └─────────────────────────────────┘    └ ─ ─ ─ ─ ─ ┘
                            │ │
                            │ │
                            │ │
                           ─┘ └─
                           ╲   ╱
                            ╲ ╱
                             V
            ┌─────────────────────────────────┐    ┌ ─ ─ ─ ─ ─ ┐
            │           ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐ │
            │                                 │    │           │
┌────┐  NR  │ ┌────┐ F2 │┌────┐ F1-U ┌─────┐│ │ N3    ┌─────┐
│ UE ├────────┤ RU ├─────┤ DU ├──────┤CU-UP├──────────┤ UPF │  │
└────┘      │ └────┘    │└────┘      └──┬──┘│ │       └─────┘
            │                 ╲         │     │    │           │
            │           │      ╲        │   │ │
            │                   ╲       │     │    │           │
            │           │        ╲      │E1 │ │
            │                F1-C ╲     │     │    │           │
            │           │          ╲    │   │ │
            │                       ╲   │     │    │           │
            │           │            ╲  │   │ │
            │                        ┌──┴──┐  │ N2 │  ┌─────┐  │
            │           │            │CU-CP├──────────┤ AMF │
            │                        └─────┘  │    │  └─────┘  │
            │           │                   │ │
            │                 BBU split       │    │  5G Core  │
            │           └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘ │
            │                                 │    │   (5GC)   │
            │       disaggregated gNodeB      │
            └─────────────────────────────────┘    └ ─ ─ ─ ─ ─ ┘
~~~
{: #figure-31 title="RAN Disaggregation" artwork-align="center"}

##  Transport Network (TN)

   The 5G transport architecture defines three main segments for the
   Transport Network, which are commonly referred to as Fronthaul (FH),
   Midhaul (MH), and Backhaul (BH) {{TR-GSTR-TN5G}}:

   *  Fronthaul happens before the BBU processing.  In 5G, this
      interface is based on eCPRI (Enhanced CPRI) with native Ethernet
      or IP encapsulation.

   *  Midhaul is optional: this segment is introduced in the BBU split
      presented in Appendix B.3, where Midhaul network refers to the DU-
      CU interconnection (i.e., F1 interface).  At this level, all
      traffic is encapsulated in IP (signaling and user plane).

   *  Backhaul happens after BBU processing.  Therefore, it maps to the
      interconnection between the RAN and the Core Network.  All traffic
      is also encapsulated in IP.

   {{figure-32}} illustrates the different segments of the Transport Network
   with the relevant Network Functions.

~~~ aasvg
┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
│                         Transport Network               │
│                                                         │
    TN Segment 1  TN Segment 2  TN Segment 3
│    (Fronthaul)   (Midhaul)     (Backhaul)               │
   ┌───────────┐ ┌────────────┐ ┌───────────┐
│  │           │ │            │ │           │             │
 ─ ┼ ─ ─ ─ ─ ─ ┼ ┼ ─ ─ ─ ─ ─ ─│─│─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─
 ┌─┴──┐      ┌─┴─┴┐         ┌─┴─┴┐       ┌──┴──┐     .───.
 │ RU │      │ DU │         │ CU │       │ UPF ├────( DN  )
 └────┘      └────┘         └────┘       └─────┘     `───'
~~~
{: #figure-32 title="5G Transport Segments" artwork-align="center"}

   It is worth mentioning that a given part of the transport network can
   carry several 5G transport segments concurrently, as outlined in
   {{figure-33}}.  This is because different types of 5G network functions
   might be placed in the same location (e.g., the UPF from one slice
   might be placed in the same location as the CU-UP from another
   slice).

~~~ aasvg
┌ ─ ─ ─ ─ ┐
 ┌────┐     Colocated
││RU-1│   │ RU/DU
 └─┬──┘
│  │ FH-1 │
 ┌─┴──┐
││DU-1│   │  ┌────┐         ┌─────┐         .───.
 └─┬──┘      │CU-1│         │UPF-1├────────( DN  )
└ ─│─ ─ ─ ┘  └─┬─┬┘         └─┬───┘         `───'
┌ ─│─ ─ ─ ─ ─ ─│─│─ ─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │    MH-1   │ │    BH-1    │          Transport Network │
│  └───────────┘ └────────────┘
   ┌───────────┐ ┌────────────┐ ┌───────────┐              │
│  │    FH-2   │ │    MH-2    │ │    BH-2   │
 ─ ┼ ─ ─ ─ ─ ─ ┼ ┼ ─ ─ ─ ─ ─ ─│─│─ ─ ─ ─ ─ ─│─ ─ ─ ─ ─ ─ ─ ┘
 ┌─┴──┐      ┌─┴─┴┐         ┌─┴─┴┐        ┌─┴───┐     .───.
 │RU-2│      │DU-2│         │CU-2│        │UPF-2├────( DN  )
 └────┘      └────┘         └────┘        └─────┘     `───'
~~~
{: #figure-33 title="Concurrent 5G Transport Segments" artwork-align="center"}

# Acknowledgments
{:numbered="false"}

   The authors would like to thank Adrian Farrel, Joel Halpern and Tarek
   Saad for their reviews of this document and for providing valuable
   feedback on it.
