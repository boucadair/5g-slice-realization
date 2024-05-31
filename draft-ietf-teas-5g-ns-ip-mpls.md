---
title: "A Realization of Network Slices for 5G Networks Using Current IP/MPLS Technologies"
abbrev: "Implementing 5G Transport Slices"
category: info

pi:
  toc: yes
  tocdepth: 2
  sortrefs: yes
  symrefs: yes
  compact: yes
  comments: yes

docname: draft-ietf-teas-5g-ns-ip-mpls-latest
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
   city: Sunnyvale
   country: United States of America
   email: je_drake@yahoo.com

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
   email: amitd@arrcus.com

 -
   fullname: Mojdeh Amani
   organization: British Telecom
   city: London
   country: United Kingdom
   email: mojdeh.amani@bt.com

normative:

informative:
   5G-Book:
              title: "5G Mobile Networks: A Systems Approach"
              author:
                -
                  fullname: Larry Peterson
                -
                  fullname: Oguz Sunay
                -
                  fullname: Bruce Davie
              date: 2022
              target: https://5g.systemsapproach.org/

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
              target: https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId-3144

   TS-28.530:
              title: "TS 23.530: Management and orchestration; Concepts, use cases and requirements)"
              author:
               org: 3GPP
              date: 2023
              target: https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId-3273

   O-RAN.WG9.XPSAAS:
              title: "O-RAN.WG9.XPSAAS: O-RAN WG9 Xhaul Packet Switched Architectures and Solutions Version 04.00"
              author:
               org: O-RAN Alliance
              date: March 2023
              target: https://www.o-ran.org/specifications

   NG.113:
          title: "NG.113: 5GS Roaming Guidelines Version 4.0"
          author:
           org: GSMA
          date: 28 May 2021
          target: https://www.gsma.com/newsroom/wp-content/uploads//NG.113-v4.0.pdf

   IEEE802.1AE:
          title: "802.1AE: MAC Security (MACsec)"
          author:
           org: IEEE
          target: https://1.ieee802.org/security/802-1ae/

   ECPRI:
              title: "Common Public Radio Interface: eCPRI Interface Specification"
              author:
                org: Common Public Radio Interface
              target: http://www.cpri.info/downloads/eCPRI_v_2.0_2019_05_10c.pdf

--- abstract

Slicing is a feature that was introduced by the 3rd Generation Partnership Project (3GPP) in mobile networks. Realization of 5G slicing implies requirements for all mobile domains, including the Radio Access Network (RAN), Core Network (CN), and Transport Network (TN).

This document describes a Network Slice realization model for IP/MPLS networks with a focus on the Transport Network fulfilling 5G slicing connectivity service objectives. The realization model reuses many building blocks currently commonly used in service provider networks.


--- middle


#  Introduction

This document focuses on network slicing for 5G networks, covering the connectivity between Network Functions (NFs) across multiple domains such as edge clouds, data centers, and the Wide Area Network (WAN). The document describes a Network Slice realization approach that fulfills 5G slicing requirements by using existing IP/MPLS technologies to optimally control connectivity Service Level Agreements (SLAs) offered for 5G slices. To that aim, this document describes the scope of the Transport Network in 5G architectures ({{sec-scope}}), disambiguates 5G Network Slicing versus Transport Network Slicing ({{sec-5gtn}}), draws the perimeter of the various orchestration domains to realize slices ({{sec-orch}}), and identifies the required coordination between these orchestration domains for adequate setup of Attachment Circuits (ACs) ({{sec-tn-nsi}}).

This work is compatible with the framework defined in {{!RFC9543}} which describes network slicing in the context of networks built from IETF technologies. Specifically, this document explains how RFC 9543 Network Slices are realized within provider networks and how such slices are stitched to Transport Network resources in a customer site in the context of Transport Network Slices ({{fig-end-to-end}}).
Concretely, the realization of an RFC 9543 Network Slice (i.e., connectivity with performance commitments) involves the provider network and partially the AC (the PE-side of the AC). This document assumes that the customer site infrastructure is over-provisioned and involves short distances (low latency) where basic QoS/scheduling logic is sufficient to comply with the Service Level Objectives (SLOs).

~~~~
{::include ./drawings/tn-sections.txt}
~~~~
{: #fig-end-to-end title="Transport Network Slice &  RFC 9543 Network Slice Scopes" artwork-align-"center"}

The realization approach described in this document is typically triggered by Network Slice Service requests. How a Network Slice Service request is placed for realization, including how it is derived from a 5G Slice Service request, is out of scope. Mapping considerations between 3GPP and IETF Network Slice Service (e.g., mapping of service parameters) are discussed, e.g., in {{?I-D.ietf-teas-5g-network-slice-application}}.

The 5G control plane uses the Single Network Slice Selection Assistance Information (S-NSSAI) for slice
identification {{TS-23.501}}. Because S-NSSAIs are not visible to the transport domain, 5G domains can expose the 5G slices to the transport
domain by mapping to explicit data plane identifiers (e.g., Layer 2, Layer 3, or Layer 4). The realization of the mapping between customer sites and provider networks is refered to as the "hand-off". {{sec-handoff-domains}} lists a set of such hand-off methods.

The realization model described in this document uses a set of building blocks commonly used in service provider networks. Concretely, the model uses (1) Layer 2 Virtual Private Network (L2VPN) {{?RFC4664}} and/or Layer 3 Virtual Private Network (L3VPN) {{?RFC4364}} service instances for logical separation, (2) fine-grained resource control at the Provider Edges (PEs), (3) coarse-grained resource control at within the provider network, and (4) capacity management. More details are provided in Sections {{<sec-over-rea-model}}, {{<sec-qos-map}}, {{<transport-plane-mapping-models}}, and {{<sec-capacity-planning}}.

This realization model uses a single Network Resource Partition (NRP) ({{Section 7.1 of !RFC9543}}). The applicability to multiple NRPs is out of scope.

Although this document focuses on 5G, the realizations are not fundamentally constrained by the 5G use case. The document is not intended to be a BCP and does not claim to specify mandatory mechanisms to realize network slices. Rather, a key goal of the document is to provide pragmatic implementation approaches by leveraging existing readily-available, widely-deployed techniques. The document is also intended to align the mobile and the IETF perspectives of slicing from a realization perspective.

A brief 5G overview is provided in {{sec-5g-overview}} for the reader's convenience. For a definitive description of 3GPP network architectures, the reader should refer to {{TS-23.501}}. More  details can be found in {{5G-Book}}.

# Definitions

The document uses the terms defined in {{!RFC9543}}. See {{sec-ref-design}} for the contextualization of some of these terms.

An extended list of abbreviations used in this document is provided in {{ext-abbr}}.

"5G Network Slicing" (or "5G Network Slice") refers to "Network Slicing" (or "Network Slice") as defined in the 3GPP {{TS-28.530}}.

This document makes use of the following terms:

Customer:
: An entity that is responsible for managing and orchestrating the end-to-end 5G Mobile Network, notably the Radio Access Network (RAN) and Core Network (CN).
: This entity is distinct from the customer of a 5G Network Slice Service.

Customer site:
: A customer manages and deploys 5G NFs (e.g., gNodeB (gNB) and 5G Core (5GC)) in customer sites. A customer site can be either a physical or a virtual location.
: Examples of customer sites are a customer private locations (Point of Presence (PoP), Data Center (DC)), a Virtual Private Cloud (VPC), or servers hosted within the provider network or colocation service.

Provider:
: An entity responsible for interconnecting customer sites.
: A provider orchestrates and manages a provider network.

#  5G Network Slicing Integration in Transport Networks {#sec-5g}

## Scope of the Transport Network {#sec-scope}

{{sec-5g-overview}} provides an overview of 5G network building blocks: the Radio Access Network (RAN), Core Network (CN), and Transport Network (TN). The Transport Network is defined by the 3GPP as:

{:quote}
> "part supporting connectivity within and between CN and RAN parts" (Section 1 of {{TS-28.530}}).

As discussed in Section 4.4.1 of {{TS-28.530}}, the 3GPP management system does not directly control the Transport Network: it is considered as a non-3GPP managed system.

{:quote}
> "The non-3GPP part includes TN parts. The 3GPP management system provides the network slice requirements to the corresponding management systems of those non-3GPP parts, e.g. the TN part supports connectivity within and between CN and AN parts." (Section 4.4.1 of {{TS-28.530}})

In practice, the TN may not map to a monolithic architecture and management domain. It is frequently segmented, non-uniform, and managed by different entities. For example, {{fig-1}} depicts an NF instance that is deployed in an edge data center (DC) connected to an NF located in a Public Cloud via a WAN (e.g., MPLS-VPN service). In this example, the TN can be seen as an abstraction representing an end-to-end connectivity based upon three distinct domains: DC, WAN, and Public Cloud. A model for the Transport Network based on orchestration domains is introduced in {{sec-orch}}.

~~~~
{::include ./drawings/TN-abstraction.txt}
~~~~
{: #fig-1 title="An Example of Transport Network Decomposition" artwork-align-"center"}


##  5G Network Slicing versus Transport Network Slicing {#sec-5gtn}

Network slicing has a different meaning in the 3GPP mobile world and transport
world. This difference can be seen from the descriptions below that set out
the objectives of 5G Network Slicing ({{sec-5g-slicing}}) and Transport Network
Slicing ({{sec-tn-slicing}}). These descriptions are not intended to be exhaustive.

### 5G Network Slicing  {#sec-5g-slicing}

5G Network Slicing is defined by the 3GPP  {{TS-28.530}} as an approach:

{:quote}
> "where logical networks/partitions are created, with appropriate isolation, resources and optimized topology to serve a purpose or service category (e.g. use case/traffic category, or for MNO internal reasons) or customers (logical system created "on demand")."

These resources are from the TN, RAN, CN domains, and the underlying infrastructure.

Section 3.1 of {{TS-28.530}} defines 5G Network Slice as:

{:quote}
> "a logical network that provides specific network capabilities and network characteristics, supporting various service properties for network slice customers."

### Transport Network Slicing {#sec-tn-slicing}

The term "TN slice" refers to a slice in the Transport Network domain of the 5G architecture.

The objective of Transport Network Slicing is to isolate,
guarantee, or prioritize Transport Network resources for Slice Services. Examples of such resources are:
buffers, link capacity, or even Routing Information Base (RIB) and Forwarding Information Base (FIB).

Transport Network Slicing provides various degrees of sharing of resources between slices. For example, the network capacity can be shared by all slices, usually with a guaranteed minimum per slice, or each individual slice can be allocated dedicated network capacity. Parts of a given network may use the former, while others use the latter. For example, in order to satisfy local engineering guidelines and specific service requirements, shared TN resources could be provided in the backhaul (or midhaul), and dedicated TN resources could be provided in the midhaul (or backhaul). The capacity partitioning strategy is deployment specific.

There are different components to implement TN slices based upon
mechanisms such as Virtual Routing and Forwarding instances (VRFs)
for logical separation, Quality of Service (QoS), and Traffic
Engineering (TE). Whether all or a subset of these components are enabled is a deployment choice.

## Transport Network Reference Design {#sec-ref-design}

{{fig-tn-arch}} depicts the reference design used in this document for modelling the Transport Network based on management perimeters (Customer vs. Provider).

~~~~
{::include ./drawings/pe-ce-ac.txt}
~~~~
{: #fig-tn-arch title="Reference Design with Customer Site and Provider Network" artwork-align-"center"}

The description of the main components shown in {{fig-tn-arch}} is provided in the following subsections.

### Customer Site {#sec-cs}

On top of 5G NFs, a customer may manage additional TN elements (e.g., servers, routers, and switches) within a customer site.

NFs may be hosted on a CE, directly connected to a CE, or be located multiple IP hops from a CE.

The orchestration of the TN within a customer site involves a set of controllers for automation purposes (e.g., Network Functions Virtualization Infrastructure (NFVI), Container Network Interface (CNI), Fabric Managers, or Public Cloud APIs). It is out of scope to document how these controllers are implemented.

### Customer Edge (CE) {#sec-ce}

A CE is a function that provides logical connectivity of a customer site ({{sec-cs}}) to the provider network ({{sec-pn}}). The logical connectivity is enforced at Layer 2 and/or Layer 3 and is denominated an Attachment Circuit (AC) ({{sec-ac}}). Examples of CEs include TN components (e.g., router, switch, and firewalls) and also 5G NFs (i.e., an element of the 5G domain such as Centralized Unit (CU), Distributed Unit (DU), or User Plane Function (UPF)).

A CE is typically managed by the customer, but it can also be co-managed with the provider. A co-managed CE is orchestrated by both the customer and the provider. In this case, the customer and provider usually have control on distinct device configuration perimeters. A co-managed CE has both PE and CE functions and there is no strict AC connection, although one may consider that the AC stitching logic happens internally within the CE itself. The provider manages the AC between the CE and the PE.

This document generalizes the definition of a CE with the introduction of "Distributed CE"; that is, the logical connectivity is realized by configuring multiple devices in the customer domain. The CE function is distributed. An example of distributed CE is the realization of an interconnection using a L3VPN service based on a distributed CE composed of a switch (Layer 2) and a router (Layer 3) ({{fig-distribute-ce}}). Another example of distributed CE is shown in {{fig-50}}.

~~~~
+--------------+                    +--------------+
|   Customer   |                    |   Provider   |
|     Site     |                    |    Network   |
|.................                  |              |
| +-----+ +----+|               +----+            |
| |     | |    ------------------    |            |
| |     +------------AC---------+ PE |            |
| | RTR | | SW ------------------    |            |
| +-----+ +----+|               +----+            |
|'..Distributed..'                  |              |
|       CE     |                    |              |
+--------------+                    +--------------+
~~~~
{: #fig-distribute-ce title="Example of Distributed CE" artwork-align-"center"}

While in most cases CEs connect to PEs using IP (e.g., VLANs subinterface on a Layer 3 interface), a CE may also connect to the provider network using other technologies such as MPLS -potentially over IP tunnels- or Segment Routing over IPv6 (SRv6) {{?RFC8986}}. The CE has thus awareness of provider services configuration (e.g., control plane identifiers such as Route Targets (RTs) and Route Distinguishers (RDs)). However, the CE is still managed by the customer and the AC is based on MPLS or SRv6 data plane technologies. The complete termination of the AC within the provider network may happen on distinct routers: this is another example of distributed PE. Service-aware CEs are used, for example, in the deployments discussed in Sections {{<sec-10b}} and {{<sec-10c}}.

### Provider Network {#sec-pn}

A provider uses a provider network to interconnect customer sites. This document assumes that the provider network is based on IP, MPLS, or both.

### Provider Edge (PE) {#sec-pe}

PE is a device managed by a provider that is connected to a CE. The connectivity between a CE and a PE is achieved using one or multiple ACs ({{sec-ac}}).

This document generalizes the PE definition with the introduction of "Distributed PE"; that is, the logical connectivity is realized by configuring multiple devices in the provider network (i.e., provider orchestration domain). The PE function is distributed.

An example of a distributed PE is the "Managed CE service". For example, a provider delivers VPN services using CEs and PEs which are both managed by the provider (case (i) in {{fig-50}}). The managed CE can also be a Data Center Gateway as depicted in the example (ii) of {{fig-50}}. A provider-managed CE may attach to CEs of multiple customers. However, this device is part of the provider network.

~~~~
+--------------+                    +--------------+
|   Customer   |                    |   Provider   |
|     Site     |                    |    Network   |
|              |                .................  |
|          +----+               |+----+   +----+|  |
|          |    ------------------Mngd|   |    |  |
|          | CE +--------AC------+ CE +---+ PE |  |
|          |    ------------------    |   |    |  |
|          +----+               |+----+   +----+|  |
|              |                '..Distributed..'  |
|              |                    |  PE          |
+--------------+                    +--------------+
                  (i) Distributed PE

+--------------+                    +--------------+
|   Customer   |                    |   Provider   |
|     Site     |                    |    Network   |
|  ..................           .................. |
|  |    IP Fabric   |           |+----+   +----+ | |
|  |.-----. .-----. -------------- DC |   |    | | |
|  |'-----' '-----' +-----AC-----+ GW +---+ PE | | |
|  |.-. .-. .-. .-. --------------    |   |    | | |
|  |'-' '-' '-' '-' |           |+----+   +----+ | |
|  '...Distributed..'           '...Distributed..' |
|          CE  |                    |  PE          |
|              |                    |              |
+--Data Center-+                    +--------------+
              (ii) Distributed PE and CE
~~~~
{: #fig-50 title="Examples of Distributed PE" artwork-align-"center"}

In subsequent sections of this document, the terms CE and PE are used for both single and distributed devices.

### Attachment Circuit (AC) {#sec-ac}

The AC is the logical connection that attaches a CE ({{sec-ce}}) to a PE ({{sec-pe}}). A CE is connected to a PE via one or multiple ACs. An AC is technology-specific.

This document uses the concept of distributed CEs and PEs (e.g., {{Section 3.4.3 of ?RFC4664}}) to consolidate a CE/AC/PE definition that is consistent with the orchestration perimeters ({{sec-orch}}). The CEs and PEs delimit respectively the customer and provider orchestration domains, while an AC interconnects these domains.

For consistency with the AC data models terminology (e.g., {{?I-D.ietf-opsawg-teas-attachment-circuit}} and {{?I-D.ietf-opsawg-ntw-attachment-circuit}}), this document assumes that an AC is configured on a "bearer", which represents the underlying connectivity. For example, the bearer is illustrated with "---" in Figures {{<fig-distribute-ce}} and {{<fig-50}}.

Examples of ACs are Virtual Local Area Networks (VLANs) (AC) configured on a physical interface (bearer) or an Overlay VXLAN EVI (AC) configured on an IP underlay (bearer).

Deployment cases where the AC is also managed by the provider are not discussed in the document because the setup of such an AC does not require any coordination between the customer and provider orchestration domains.

{:aside}
> In order to keep the figures simple, only one AC and single-homed CEs are represented.
> However, this document does not exclude the instantiation of multiple ACs between a CE and a PE nor the presence of CEs that are attached to more than one PE.

##  Orchestration Overview {#sec-orch}

###  5G End-to-End Slice Orchestration Architecture {#sec-5g-sli-arch}

This section introduces a global framework for the orchestration of a 5G end-to-end slice (a.k.a. 5G Network Slice) with a zoom on TN parts. This framework helps to delimit the realization scope of RFC 9543 Network Slices and identify interactions that are required for the realization of such slices.

This framework is consistent with the management coordination example shown in Figure 4.7.1 of {{TS-28.530}}.

In reference to {{figure-orch}}, a 5G End-to-End Network Slice Orchestrator (5G NSO) is responsible for orchestrating 5G Network Slices end-to-end. The details of the 5G NSO are out of the scope of this document. The realization of the 5G Network Slices spans RAN, CN, and TN. As mentioned in {{sec-scope}}, the RAN and CN are under the responsibility of the 3GPP Management System, while the TN is not. The orchestration of the TN is split into two sub-domains in conformance with the reference design in {{sec-ref-design}}:

Provider Network Orchestration domain:
: As defined in {{!RFC9543}}, the provider relies on a Network Slice Controller (NSC) to manage and orchestrate RFC 9543 Network Slices in the provider network. This framework permits to manage connectivity together with SLOs.

Customer Site Orchestration domain:
: The Orchestration of TN elements of the customer sites relies upon a variety of  controllers (e.g., Fabric Manager, Element Management System, or Virtualized Infrastructure Manager (VIM)).

A TN slice relies upon resources that can involve both the provider and customer TN domains. More details are provided in {{sec-tn-nsi}}.

A TN slice might be considered as a variant of horizontal composition of Network Slices mentioned in Appendix A.6 of {{!RFC9543}}.

~~~~
{::include ./drawings/tn-orchestration.txt}
~~~~
{: #figure-orch title="5G End-to-End Slice Orchestration with TN" artwork-align-"center"}

The various orchestration depicted in {{figure-orch}} encompass the 3GPP's Network Slice Subnet Management Function (NSSMF) mentioned, e.g., in Figure 5 of {{?I-D.ietf-teas-5g-network-slice-application}}.


### Transport Network Segments and Network Slice Instantiation {#sec-tn-nsi}

This document focuses on deployments where the Service Demarcation Points (SDPs) are located per Types 3 and 4 of Figure 1 of {{!RFC9543}}. The concept of distributed PE ({{sec-pe}}) assimilates CE-based SDPs defined in {{Section 5.2 of !RFC9543}} (i.e., Types 1 and 2) as SDP Type 3 or 4 in this document.

In reference to the architecture depicted in {{sec-5g-sli-arch}}, the connectivity between NFs can be decomposed into three main segment types that are as follows:

Customer Site:
: Either connects NFs located in the same customer site or connects an NF to a CE.
: This segment may not be present if the NF is the CE. In this case the AC connects the NF to a PE.
: The realization of this segment is driven by the 5G Network Orchestration (e.g., NFs instantiation) and the Customer Site Orchestration for the TN part.

Provider Network:
: Represents the connectivity between two PEs. The realization of this segment is controlled by an NSC ({{Section 6.3 of !RFC9543}}).

Attachment Circuit:
: The orchestration of this segment relies partially upon an NSC for the configuration of the AC on the PE customer-facing interfaces and the Customer Site Orchestration for the configuration of the AC on the CE.
: PEs and CEs that are connected via an AC need to be
provisioned with consistent data plane and control plane information (VLAN-
IDs, IP addresses/subnets, BGP  Autonomous System (AS) Number, etc.). Hence, the realization of this
interconnection is technology-specific and requires coordination between the Customer Site Orchestration and an NSC. Automating the provisioning and management of the AC is thus key to automate the overall service provisioning. Aligned with {{?RFC8969}}, this document assumes that this coordination is based upon standard YANG data models and APIs.
: The provisioning of a Network Slice may rely on new or existing ACs.
: {{figure-4}} is a basic example of a Layer 3 CE-PE link realization
with shared network resources (such as VLAN-IDs and IP prefixes) which
are passed between Orchestrators via a dedicated interface, e.g., the Network Slice Service Model (NSSM) {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} or the Attachment Circuit-as-a-Service (ACaaS) {{?I-D.ietf-opsawg-teas-attachment-circuit}}.

~~~~
{::include ./drawings/ac-api-synch.txt}
~~~~
{: #figure-4 title="Coordination of Transport Network Resources for the AC Provisioning" artwork-align-"center"}

## Mapping 5G Network Slices to Transport Network Slices {#sec-mapping}

   There are multiple options for mapping 5G Network Slices to TN slices:

   * 1 to N:
A single 5G Network Slice can be mapped to multiple TN slices (1 to N). For instance, consider the scenario depicted in {{figure-5}}, illustrating the separation of the 5G control plane and user plane in TN slices for a single 5G Enhanced Mobile Broadband (eMBB) network slice. It is important to note that this mapping can serve as an interim step to M to N mapping. Further details about this scheme are described in {{sec-firstslice}}.

   * M to 1:
      Multiple 5G Network Slices may rely upon the same TN slice.  In such a case, the Service Level Agreement (SLA) differentiation of slices
      would be entirely controlled at the 5G control plane, for example, with
      appropriate placement strategies: this use case is represented in
      {{figure-6}}, where a User Plane Function (UPF) for the Ultra Reliable Low Latency Communication (URLLC) slice is
      instantiated at the edge cloud close to the gNB Centralized Unit User Plane (CU-UP) for
      better latency/jitter control, while the 5G control plane and the UPF
      for eMBB slice are instantiated in the regional cloud.

   * M to N:
      The 5G to TN slice mapping combines both
      approaches with a mix of shared and dedicated associations.

      In this scenario, a subset of the TN slices can be intended for sharing by multiple 5G Network Slices (e.g., the control plane TN slice is shared by multiple 5G network Slices).

      In practice, for operational and scaling reasons, typically M to N would be used, with M >> N.

~~~
{::include ./drawings/1-to-n-mapping.txt}
~~~
{: #figure-5 title="1 (5G Slice) to N (RFC 9543 Network Slice) Mapping" artwork-align-"center"}

~~~
{::include ./drawings/n-to-1-mapping.txt}
~~~
{: #figure-6 title="N (5G Slice) to 1 (RFC 9543 Network Slice) Mapping" artwork-align-"center"}

   Note that the actual realization of the mapping depends on several
   factors, such as the actual business cases, the NF vendor
   capabilities, the NF vendor reference designs, as well as service
   provider or even legal requirements.

Mapping approaches that preserve the 5G slice identification in the TN (e.g., {{sec-ip-hof}}) may simplify required operations to map back TN slices to 5G slices. However, such considerations are not detailed in this document because these are under the responsibility of the 3GPP orchestration domain.

##  First 5G Slice versus Subsequent Slices {#sec-firstslice}

An operational 5G Network Slice incorporates both 5G control plane and user plane capabilities.
For instance, consider a slice based on split-CU in the RAN, both CU-UP and Centralized Unit Control Plane (CU-CP) need to be deployed along with the associated interfaces E1, F1-c, F1-u, N2, and N3 which are conveyed in the TN. In this regard, the creation of the "first slice" can be subject to a specific logic that does not apply to subsequent slices. Let us consider the example depicted in {{figure-7}} to illustrate this deploloyment. In this example, the first 5G slice relies on the deployment of NF-CP and NF-UP functions together with two TN slices for control and user planes (INS-CP and INS-UP1). Next, the deployment of a second slice relies solely on the instantiation of a UPF (NF-UP2) together with a dedicated user plane TN slice (INS-UP2). In this example, the control plane of the first 5G slice is also updated to integrate the second slice: the TN slice (INS-CP) and Network Functions (NF-CP) are shared.

   At the time of writing (2024), Section 6.1.2 of {{NG.113}} specifies that the
   eMBB slice (SST-1 and no Slice Differentiator (SD)) should be supported globally.  This 5G
   slice would be the first slice in any 5G deployment.

~~~ aasvg
{::include ./drawings/1st-slice.txt}
~~~
{: #figure-7 title="First and Subsequent Slice Deployment" artwork-align-"center"}

Overall, policies might be provided by an operator (e.g., to Network Slice Controllers) to indicate whether the same or dedicated CP NFs are allowed when processing a new slice creation request. Providing such a policy is meant to better automate the realization of 5G slices and minimize the realization delay that might be induced by extra cycles to seek for operator validation.

##  Overview of the Transport Network Realization Model {#sec-over-rea-model}

   The realization model described in this document is depicted in
   {{figure-high-level-qos}}. The following building blocks are used:

   *  L2VPN {{?RFC4664}} and/or L3VPN {{?RFC4364}} service instances for logical separation:

      This realization model of transport for 5G slices assumes Layer 3
      delivery for midhaul and backhaul transport connections, and a
      Layer 2 or Layer 3 delivery for
      fronthaul connections. Enhanced Common Public Radio Interface (eCPRI) {{ECPRI}} supports both delivery models. L2VPN/L3VPN service instances might be
      used as a basic form of logical slice separation.  Furthermore, using
      service instances results in an additional outer header (as packets
      are encapsulated/decapsulated at the nodes hosting service instances) providing clean discrimination between 5G QoS and TN
      QoS, as explained in {{sec-qos-map}}.

      The use of VPNs for realizing Network Slices is briefly described in Appendix A.4 of {{!RFC9543}}.

   *  Fine-grained resource control at the PE:

      This is sometimes called 'admission control' or 'traffic
      conditioning'.  The main purpose is the enforcement of the
      bandwidth contract for the slice right at the edge of the
      provider network where the traffic is handed-off between the
      customer site and the provider network.

      The method used here is granular ingress policing (rate limiting)
      to enforce contracted bandwidths per slice and, potentially, per
      traffic class within the slice.  Traffic above the enforced rate might be
      immediately dropped, or marked as high drop-probability traffic,
      which is more likely to be dropped somewhere inside the provider network if
      congestion occurs.  In the egress direction at the PE node,
      hierarchical schedulers/shapers can be deployed,
      providing guaranteed rates per slice, as well as guarantees per
      traffic class within each slice.

      For managed CEs, edge admission control can be distributed between CEs
      and PEs, where a part of the admission control is implemented on the CE
      and other part of the admission control is implemented on the PE.

   *  Coarse-grained resource control at the transit (non-attachment
      circuits) links in the provider network, using a single NRP (called "base NRP" in {{figure-high-level-qos}}), spanning the entire provider network.
      Transit nodes in the provider network do not maintain any state of individual slices.
      Instead, only a flat (non-hierarchical) QoS model is used on
      transit links in the provider network, with up to 8 traffic classes.  At the PE,
      traffic-flows from multiple slice services are mapped
      to the limited number of traffic classes used on provider network transit links.

   *  Capacity planning/management for efficient usage of provider network resources:

      The role of capacity management is to ensure the provider network
      capacity can be utilized without causing any bottlenecks.  The
      methods used here can range from careful network planning, to
      ensure a more or less equal traffic distribution (i.e., equal cost load
      balancing), to advanced TE techniques, with or
      without bandwidth reservations, to force more consistent load
      distribution even in non-ECMP friendly network topologies. See also {{Section 8 of ?RFC9522}}).

~~~~
{::include ./drawings/high-level-qos.txt}
~~~~
{: #figure-high-level-qos title="Resource Allocation Slicing Model with a Single NRP" artwork-align-"center"}

This document does not describe in detail how to manage an L2VPN or L3VPN, as this is already well-documented. For example, the reader may refer to {{?RFC4176}} and {{?RFC6136}} for such details.

#  Hand-off Between Domains {#sec-handoff-domains}

   The 5G control plane relies upon 32-bit S-NSSAIs for slice
   identification. The S-NSSAI is not visible to the transport domain.
   So instead, 5G network functions can expose the 5G slices to the transport
   domain by mapping to explicit Layer 2 or Layer 3 identifiers, such as VLAN-IDs, IP
   addresses, or Differentiated Services Code Point (DSCP) values. These section lists few hand-off methods for slice mapping
   between customer sites and provider networks.

   More details about the mapping between 3GPP and RFC 9543 Network Slices is provided in {{?I-D.ietf-teas-5g-network-slice-application}}.

   <!---
   That document includes additional methods for mapping 5G slices to TN slices (e.g., source UDP port number), but these
   methods are not discussed here because of the shortcomings of these methods (e.g., load balancing, NAT).
   -->

##  VLAN Hand-off {#sec-vlan-handoff}

   In this option, the RFC 9543 Network Slice, fulfilling connectivity
   requirements between NFs that belong to a 5G slice, is represented at an SDP
   by a VLAN ID (or double VLAN IDs, commonly known as QinQ), as depicted in {{figure-vlan-hand-off}}.

~~~~
{::include ./drawings/vlan-hand-off.txt}
~~~~
{: #figure-vlan-hand-off title="Example of 5G Slice with VLAN Hand-off Providing End-to-End Connectivity" artwork-align-"center"}

   Each VLAN
   represents a distinct logical interface on the ACs;
   hence it provides the possibility to place these logical interfaces
   in distinct Layer 2 or Layer 3 service instances and implement separation
   between slices via service instances. Since the 5G interfaces are IP-based
   interfaces (with an exception of the F2 fronthaul-interface, where eCPRI with Ethernet encapsulation is used), this
   VLAN is typically not transported across the provider network.  Typically,
   it has only local significance at a particular SDP.  For
   simplification, a deployment may rely on the same VLAN identifier
   for all ACs. However, that may not be always possible. As such, SDPs for a same slice at
   different locations may use different VLAN values.  Therefore, a
   VLAN to RFC 9543 Network Slice mapping table is maintained for each
   AC, and the VLAN allocation is coordinated between customer orchestration and
   provider orchestration.

   While VLAN hand-off is simple for NFs, it adds complexity at the provider network because of the requirement of maintaining
   mapping tables for each SDP and performing a configuration task for new VLANs and
   IP subnet for every slice on every AC.

##  IP Hand-off {#sec-ip-hof}

   In this option, an explicit mapping between source/destination IP addresses and
   slice's specific S-NSSAI is used. The mapping can have either local (e.g.,
   pertaining to single NF attachment) or global TN significance. The mapping can
   be realized in multiple ways, including (but not limited to):

   * S-NSSAI to a dedicated IP address for each NF
   * S-NSSAI to a pool of IP addresses for global TN deployment
   * S-NSSAI to a subset of bits of an IP address
   * S-NSSAI to a DSCP value
   * Use a deterministic algorithm to map S-NSAAI to an IP subnet, prefix, or pools. For example, adaptations to the algorithm defined in {{?RFC7422}} may be considered.

   Mapping S-NSSAIs to IP addresses makes IP addresses an identifier for slice-related
   policy enfocement in the Transport Network (e.g., Differentiated Services,
   traffic steering, bandwidth allocation, security policies, or monitoring).

   One example of the IP hand-off realization is the arrangement, where the slices in the TN
   domain are instantiated using IP tunnels (e.g., IPsec or GTP-U tunnels)
   established between NFs, as depicted in {{figure-ip-hand-off}}. The transport for
   a single 5G slice might be constructed with multiple such tunnels, since a
   typical 5G slice contains many NFs - especially DUs and CUs. If a shared NF (i.e.,
   an NF that serves multiple slices, for example, a shared DU) is deployed, multiple
   tunnels from shared NF are established, each tunnel representing a single slice.

~~~~
{::include ./drawings/ip-hand-off.txt}
~~~~
{: #figure-ip-hand-off title="Example of 5G Slice with IP Hand-off Providing End-to-End Connectivity" artwork-align-"center"}

   As opposed to the VLAN hand-off case ({{sec-vlan-handoff}}), there is no logical interface representing
   a slice on the PE, hence all slices are handled within a single service instance.
   The IP and VLAN hand-offs are not mutually exclusive, but instead could be used
   concurrently. Since the TN doesn't recognize S-NSSAIs, a mapping table similar to
   the VLAN Hand-off solution is needed ({{sec-vlan-handoff}}).

   The mapping table can be simplified if, for example, IPv6 addressing is used to
   address NFs. An IPv6 address is a 128-bit long field, while the S-NSSAI is a
   32-bit field: Slice/Service Type (SST): 8 bits, Slice Differentiator (SD): 24
   bits. 32 bits, out of 128 bits of the IPv6 address, may be used to encode the
   S-NSSAI, which makes an IP to Slice mapping table unnecessary.

   The S-NSSAI/IPv6 mapping is a local IPv6 address allocation method to NFs not disclosed to on-path nodes. IP forwarding is not altered by this method and is
   still achieved following BCP 198 {{!RFC7608}}. Concretely, intermediary TN nodes are not required to associate any additional semantic with IPv6 address.

   However, operators using such mapping methods should be aware of the implications
   of any change of S-NSSAI on the IPv6 addressing plans. For example, modifications of the S-NSSAIs in-use will require
   updating the IP addresses used by NFs involved in the associated slices.

### An Example of Local IPv6 Addressing Plan for Network Functions

   Different IPv6 address allocation
   schemes following the above approach may be used, with one example allocation shown
   in {{figure-11}}.

~~~
             NF-specific          Reserved
        (not slice specific)     for S-NSSAI
   <----------------------------><--------->
   +----+----+----+----+----+----+----+----+
   |xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:ttdd:dddd|
   +----+----+----+----+----+----+----+----+
   <------------------128 bits------------->

    tt     - SST (8 bits)
    dddddd - SD (24 bits)
~~~
{: #figure-11 title="An Example of S-NSSAI Embedded into an IPv6 Address" artwork-align-"center"}

   In reference to {{figure-11}}, the most significant 96 bits of the IPv6 address
   are unique to the NF, but do not carry any slice-specific information. The S-NSSAI information is embedded in the least
   significant 32 bits. The 96-bit part of the address may be structured by the provider, for example, on the
   geographical location or the DC identification. Refer to {{Section 2.1. of ?RFC9099}} for a discussion on the benefits of structuring an address plan around both services and geographic locations for more structured security policies in a network.

   {{figure-s-nssai-deployment}} uses the example from {{figure-11}} to demonstrate a
   slicing deployment, where the entire S-NSSAI is embedded into IPv6 addresses used by
   NFs. Let us consider that "NF-A" has a set of tunnel termination points with unique per-slice IP addresses
   allocated from 2001:db8:a:0::/96, while "NF-B" uses a set of tunnel termination
   points with per-slice IP addresses allocated from 2001:db8:b:0::/96. This example shows
   two slices: "customer A eMBB" (SST-01, SD-00001) and "customer B Massive Internet of Things (MIoT)" (SST-03, SD-00003).
   For "customer A eMBB" slice, the tunnel IP addresses are auto-derived as the IP addresses {2001:db8:a::100:1, 2001:db8:b::100:1},
   where {:0100:0001} is used as the last two octets. "customer B MIoT" slice (SST-3,
   SD-00003) tunnel uses the IP addresses {2001:db8:a::300:3, 2001:db8:b::300:3} and simply
   adds {:0300:0003} as the last two octets. Leading zeros are not represented in the resulting IPv6 addresses as per {{?RFC5952}}.

~~~~
{::include ./drawings/S-NSSAI-deployment.txt}
~~~~
{: #figure-s-nssai-deployment title="Deployment Example with S-NSSAI Embedded into IPv6 Addresses" artwork-align-"center"}


##  MPLS Label Hand-off {#sec-mpls-ho}

   In this option, the service instances representing different slices
   are created directly on the NF, or within the customer site
   hosting the NF, and attached to the provider network.  Therefore, the packet
   is encapsulated outside the provider network with MPLS
   encapsulation or MPLS-in-UDP encapsulation {{?RFC7510}}, depending on the capability
   of the customer site, with the service label depicting
   the slice.

   There are three major methods (based upon {{Section 10 of !RFC4364}}) for interconnecting MPLS services over multiple service domains:

   Option A ({{sec-10a}}):
   : VRF-to-VRF connections.

   Option B ({{sec-10b}}):
   : redistribution of labeled VPN routes with next-hop
     change at domain boundaries.

   Option C ({{sec-10c}}):
   :  redistribution of labeled VPN routes without next-hop
    change and redistribution of labeled transport routes with next-hop
    change at domain boundaries.

{{figure-51}} illustrates the use of service-aware CE ({{sec-ce}}) for the deployment discussed in Sections {{<sec-10b}} and {{<sec-10c}}.

~~~~
{::include ./drawings/mpls-ac.txt}
~~~~
{: #figure-51 title="Example of MPLS-based Attachment Circuit" artwork-align-"center"}

###  Option A {#sec-10a}

   This option is not based on MPLS label hand-off, but VLAN hand-off, described in {{sec-vlan-handoff}}.

###  Option B {#sec-10b}

   In this option, L3VPN service instances are instantiated outside the
   provider network.  These L3VPN service instances
   are instantiated in the customer site which could be, for example, either on the compute that hosts mobile NFs ({{figure-mpls-10b-hand-off}}, left hand side) or within the DC/cloud
   infrastructure itself (e.g., on the top of the rack or leaf switch
   within cloud IP fabric ({{figure-mpls-10b-hand-off}}, right hand side)). On the
   AC connected to a PE, packets are already MPLS
   encapsulated (or MPLS-in-UDP/MPLS-in-IP encapsulated, if cloud or compute
   infrastructure don't support MPLS encapsulation). Therefore,
   the PE uses neither a VLAN nor an IP address for slice
   identification at the SDP, but instead uses the MPLS label.

~~~~
{::include ./drawings/mpls-10b-hand-off.txt}
~~~~
{: #figure-mpls-10b-hand-off title="Example of MPLS Hand-off with Option B" artwork-align-"center"}


   MPLS labels are allocated dynamically in Option B
   deployments, where at the domain boundaries service prefixes are
   reflected with next-hop self, and a new label is dynamically allocated,
   as visible in {{figure-mpls-10b-hand-off}} (e.g., labels A, A', and A" for the first depicted slice).  Therefore, for any slice-specific per-hop
   behavior at the provider network edge, the PE needs to determine
   which label represents which slice.  In the BGP control plane, when
   exchanging service prefixes over an AC, each slice might be represented by a unique BGP community, so
   tracking label assignment to the slice might be possible.  For example, in
   {{figure-mpls-10b-hand-off}}, for the slice identified with COM-1, the PE advertises a
   dynamically allocated label A". Since, based on the community, the
   label to slice association is known, the PE can use this dynamically
   allocated label A" to identify incoming packets as belonging to "slice 1"
   and execute appropriate edge per-hop behavior.

   It is worth noting that slice identification in the BGP control plane
   might be with per-prefix granularity.  In the extreme case, each prefix can have
   different community representing a different slice.  Depending on the
   business requirements, each slice could be represented by a different
   service instance as outlined in {{figure-mpls-10b-hand-off}}.  In that case, the route
   target extended community ({{Section 4 of ?RFC4360}}) might be used as slice differentiator.  In
   other deployments, all prefixes (representing different slices)
   might be handled by a single 'mobile' service instance, and some other
   BGP attribute (e.g., a standard community {{?RFC1997}}) might be used for slice
   differentiation.  There could be also a deployment option that groups multiple
   slices together into a single service instance, resulting in a
   handful of service instances.  In any case, fine-grained per-hop
   behavior at the edge of provider network is possible.

###  Option C {#sec-10c}

Option B relies upon exchanging service prefixes between customer sites
and the provider network. This may lead to scaling challenges in large
scale 5G deployments as the PE node needs to carry all service prefixes.
To alleviate this scaling challenge, in Option C, service prefixes are
exchanged between customer sites only. In doing so, the provider network is offloaded from
carrying, propagating, and programing appropriate forwarding entries
for service prefixes.

Option C relies upon exchanging service prefixes via multi-hop BGP sessions
between customer sites, without changing the NEXT_HOP BGP attribute.
Additionally, IPv4/IPv6 labeled unicast (SAFI-4) host routes, used as NEXT_HOP
for service prefixes, are exchanged via direct single-hop BGP sessions between
adjacent nodes in a customer site and a provider network, as depicted in {{figure-mpls-10c-hand-off}}.
As a result, a node in a customer site performs hierarchical next-hop resolution.

~~~~
     <-------------------------------------------
             BGP VPN
               COM-1, L-A, NEXT_HOP-CS2
               COM-2, L-B, NEXT_HOP-CS2
               COM-3, L-C, NEXT_HOP-CS2
     <------------------------------------------>

      <------        <------        <------
      BGP LU         BGP LU         BGP LU
        CS2, L-X"      CS2, L-X'      CS2, L-X
     <-------------><------------><------------->
                nhs  nhs      nhs  nhs
                                                         VLANs
  service instances                service instances  representing
 representing slices              representing slices    slices
+ - + |           + - - - - - - -+            +-|-----------|--------+
|   | |           |     Provider |            | |           |        |
|+--+-v-+       +-+---+       +--+--+       +-+-v----+    v  +------+|
|    # |       |*    |       |    *|       | #<><><>x.......x      |
| NF # +-------+* PE |       | PE *+-------+ #<><><>x.......x   NF |
|    # |       |*    |       |    *|       | #<><><>x.......x      |
|+---+--+       +-+---+       +---+-+       +-+------+       +------+|
| CS1|            |      Network  |           |          CS2         |
+----+            +---------------+           +----------------------+

   x Logical interface represented by a VLAN on s physical interface
   # Service instances (with unique MPLS label)
   * SDP
~~~~
{: #figure-mpls-10c-hand-off title="MPLS Hand-off with Option C" artwork-align-"center"}

This architecture requires an end-to-end Label Switched Path (LSP) leading from a packet's
ingress node inside one customer site to its egress inside another customer
site, through a provider network. Hence, at the domain (customer site, provider network)
boundaries NEXT_HOP attribute for IPv4/IPv6 labeled unicast needs to be modified to "next-hop self" (nhs),
which results in new IPv4/IPv6 labeled unicast label allocation. Appropriate label swap
forwarding entries for IPv4/IPv6 labeled unicast labels are programmed in the data plane.
On the AC there is no additional 'labeled transport' protocol (i.e., no LDP, RSVP, SR, ...).

Packets are transmitted over the AC with the IPv4/IPv6 labeled
unicast as the top label, with service label deeper in the label stack. In Option C,
the service label is not used for forwarding lookup on the PE. This significantly
lowers the scaling pressure on PEs, as PEs need to program forwarding entries only for
IPv4/IPv6 labeled unicast host routes, used as NEXT_HOP for service prefixes. Also,
since one IPv4/IPv6 labeled unicast host route represent one customer site, regardless
of the number of slices in the customer site, the number of forwarding entries
on a PE is considerably reduced.

For any slice-specific per-hop behavior at the provider network edge, as described
in details in {{sec-over-rea-model}}, the PE need to determine which label in the packet
represents which slice. This can be achieved, for example, by allocating non-overlapping service label
ranges for each slice, and use these ranges for slice identification purposes on PE.

#  QoS Mapping Realization Models {#sec-qos-map}

## QoS Layers {#sec-qos-layers}

   The resources are managed via various QoS policies deployed in the
   network.  QoS mapping models to support 5G slicing connectivity
   implemented over packet switched provider network uses two layers of QoS that are discussed in {{sec-qos-layers}}.

### 5G QoS Layer

   QoS treatment is indicated in the 5G QoS layer by the 5G QoS
   Indicator (5QI), as defined in {{TS-23.501}}. A 5QI is an identifier that is
   used as a reference to 5G QoS characteristics (e.g., scheduling
   weights, admission thresholds, queue management thresholds, and link
   layer protocol configuration) in the RAN domain.  Given that
   5QI applies to the RAN domain, it is not visible to the
   provider network.  Therefore, if 5QI-aware treatment is desired in the provider
   network as well, 5G network functions might set DSCP with a value
   representing 5QI so that differentiated treatment can implemented in the provider network
   as well.  Based on these DSCP values, at SDP of each provider network segment
   used to construct transport for given 5G slice, very granular QoS
   enforcement might be implemented.

   The exact mapping between 5QI and
   DSCP is out of scope for this document.  Mapping recommendations
   are documented, e.g., in {{?I-D.cbs-teas-5qi-to-dscp-mapping}}.

   Each slice service might have flows with multiple 5QIs. 5QIs (or, more precisely,
   corresponding DSCP values) are visible to the provider network at SDPs
   (i.e., at the edge of the provider network).

   In this document, this layer of QoS is referred to as '5G QoS
   Class' ('5G QoS' in short) or '5G DSCP'.

### TN QoS Layer

   Control of the TN resources on provider network transit links, as well as traffic
   scheduling/prioritization on provider network transit links, is based on a flat
   (non-hierarchical) QoS model in this Network Slice
   realization.  That is, RFC 9543 Network Slices are assigned dedicated
   resources (e.g., QoS queues) at the edge of the provider network (at
   SDPs), while all RFC 9543 Network Slices are sharing resources (sharing
   QoS queues) on the transit links of the provider network.  Typical router
   hardware can support up to 8 traffic queues per port, therefore
   the document assumes 8 traffic queues per port support in
   general.

   At this layer, QoS treatment is indicated by a QoS indicator
   specific to the encapsulation used in the provider network. Such an indicator may
   be DSCP or MPLS Traffic Class (TC). This layer of QoS is referred to as 'TN QoS
   Class', or 'TN QoS' for short, in this document.

## QoS Realization Models

   While 5QI might be exposed to the provider network via the DSCP value
   (corresponding to specific 5QI value) set in the IP packet generated
   by NFs, some 5G deployments might use 5QI in the RAN domain only,
   without requesting per-5QI differentiated treatment from the provider network.
   This might be due to an NF limitation (e.g., no capability to set
   DSCP), or it might simply depend on the overall slicing deployment
   model.  The O-RAN Alliance, for example, defines a phased approach to
   the slicing, with initial phases utilizing only per-slice, but not
   per-5QI, differentiated treatment in the TN domain
   (Annex F of {{O-RAN.WG9.XPSAAS}}).

   Therefore, from a QoS perspective, the 5G slicing connectivity
   realization defines two high-level realization models
   for slicing in the TN domain: a 5QI-unaware model and a 5QI-
   aware model.  Both slicing models in the TN domain could be
   used concurrently within the same 5G slice.  For example, the TN
   segment for 5G midhaul (F1-U interface) might be 5QI-aware, while
   at the same time the TN segment for 5G backhaul (N3 interface) might
   follow the 5QI-unaware model.

   These models are further elaborated in the following two subsections.

###  5QI-unaware Model {#sec-5QI-unaware}

   In 5QI-unaware mode, the DSCP values in the packets received from NF
   at SDP are ignored.  In the provider network, there is no QoS
   differentiation at the 5G QoS Class level.  The entire RFC 9543 Network
   Slice is mapped to a single TN QoS Class, and, therefore, to a single
   QoS queue on the routers in the provider network.  With a small number of
   deployed 5G slices (for example, only two 5G slices: eMBB and MIoT),
   it is possible to dedicate a separate QoS queue for each slice on
   transit routers in the provider network.  However, with the introduction of private/enterprises
   slices, as the number of 5G slices (and thus corresponding RFC 9543
   Network Slices) increases, a single QoS queue on transit links in the provider network serves
   multiple slices with similar characteristics.  QoS enforcement on
   transit links is fully coarse-grained (single NRP, sharing resources among
   all RFC 9543 Network Slices), as displayed in {{figure-QoS-5QI-unaware}}.

~~~~
{::include ./drawings/QoS-5QI-unaware.txt}
~~~~
{: #figure-QoS-5QI-unaware title="Slice to TN QoS Mapping (5QI-unaware Model)" artwork-align-"center"}

   When the IP traffic is handed over at the SDP from the AC to the provider network, the PE encapsulates the
   traffic into MPLS (if MPLS transport is used in the provider network), or
   IPv6 - optionally with some additional headers (if SRv6 transport is
   used in the provider network), and sends out the packets on the provider network transit
   link.

   The original IP header retains the DCSP marking (which is ignored in
   5QI-unaware model), while the new header (MPLS or IPv6) carries QoS
   marking (MPLS Traffic Class bits for MPLS encapsulation, or DSCP for
   SRv6/IPv6 encapsulation) related to TN Class of Service (CoS).  Based on TN CoS
   marking, per-hop behavior for all RFC 9543 Network Slices is executed on
   provider network transit links.  Provider network transit routers do not evaluate the original IP
   header for QoS-related decisions.  This model is outlined in
   {{figure-15}} for MPLS encapsulation, and in {{figure-16}} for SRv6
   encapsulation.

~~~
                                 +--------------+
                                 | MPLS Header  |
                                 +-----+-----+  |
                                 |Label|TN TC|  |
+--------------+ - - - - - - - - +-----+-----+--+
|  IP Header   |         |\      |  IP Header   |
|      +-------+         | \     |      +-------+
|      |5G DSCP|---------+  \    |      |5G DSCP|
+------+-------+             \   +------+-------+
|              |              \  |              |
|              |               \ |              |
|              |                 |              |
|   Payload    |               / |   Payload    |
|(GTP-U/IPsec) |              /  |(GTP-U/IPsec) |
|              |             /   |              |
|              |---------+  /    |              |
|              |         | /     |              |
|              |         |/      |              |
+--------------+ - - - - - - - - +--------------+
~~~
{: #figure-15 title="QoS with MPLS Encapsulation" artwork-align-"center"}

~~~
                                 +--------------+
                                 | IPv6 Header  |
                                 |      +-------+
                                 |      |TN DSCP|
                                 +------+-------+
                                 :   Optional   :
                                 :     IPv6     :
                                 :    Headers   :
+--------------+ - - - - - - - - +-----+-----+--+
|  IP Header   |         |\      |  IP Header   |
|      +-------+         | \     |      +-------+
|      |5G DSCP|---------+  \    |      |5G DSCP|
+------+-------+             \   +------+-------+
|              |              \  |              |
|              |               \ |              |
|              |                 |              |
|   Payload    |               / |   Payload    |
|(GTP-U/IPsec) |              /  |(GTP-U/IPsec) |
|              |             /   |              |
|              |---------+  /    |              |
|              |         | /     |              |
|              |         |/      |              |
+--------------+ - - - - - - - - +--------------+
~~~
{: #figure-16 title="QoS with IPv6 Encapsulation" artwork-align-"center"}

   From a QoS perspective, both options are similar.  However, there
   is one difference between the two options.  The MPLS TC is only 3
   bits (8 possible combinations), while DSCP is 6 bits (64 possible
   combinations).  Hence, SRv6 provides more flexibility for TN CoS
   design, especially in combination with soft policing with in-profile/
   out-profile traffic, as discussed in {{sec-inbound-edge-resource-control}}.

   Provider network edge resources are controlled in a granular, fine-grained
   manner, with dedicated resource allocation for each RFC 9543 Network
   Slice.  The resource control/enforcement happens at each SDP in two
   directions: inbound and outbound.


####  Inbound Edge Resource Control {#sec-inbound-edge-resource-control}

   The main aspect of inbound provider network edge resource control is per-slice traffic
   volume enforcement.  This kind of enforcement is often called
   'admission control' or 'traffic conditioning'.  The goal of this
   inbound enforcement is to ensure that the traffic above the
   contracted rate is dropped or deprioritized, depending on the
   business rules, right at the edge of provider network.  This, combined with
   appropriate network capacity planning/management ({{sec-capacity-planning}}) is required to ensure proper isolation between slices in
   a scalable manner.  As a result, traffic of one slice has no influence
   on the traffic of other slices, even if the slice is misbehaving
   (e.g., Distributed Denial-of-Service (DDoS) attacks or node/link failures) and generates traffic
   volumes above the contracted rates.

   The slice rates can be characterized with following parameters
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}:

   *  CIR: Committed Information Rate (i.e., guaranteed bandwidth)

   *  PIR: Peak Information Rate (i.e., maximum bandwidth)

   These parameters define the traffic characteristics of the slice and
   are part of SLO parameter set provided by the 5G NSO to an NSC.  Based
   on these parameters, the provider network's inbound policy can be implemented using one
   of following options:

   *  1r2c (single-rate two-color) rate limiter

      This is the most basic rate limiter, described in {{Section 2.3 of ?RFC2475}}.
      It meters at the SDP a
      traffic stream of given slice and marks its packets as in-profile
      (below CIR being enforced) or out-of-profile (above CIR being enforced).
      In-profile packets are accepted and forwarded.  Out-of profile
      packets are either dropped right at the SDP (hard rate limiting),
      or remarked (with different MPLS TC or DSCP TN markings) to
      signify 'this packet should be dropped in the first place, if
      there is a congestion' (soft rate limiting), depending on the
      business policy of the provider network.  In the second case, while
      packets above CIR are forwarded at the SDP, they are subject to being
      dropped during any congestion event at any place in the provider network.

   *  2r3c (two-rate three-color) rate limiter

      This was initially defined in {{?RFC2698}}, and its improved version
      in {{?RFC4115}}.  In essence, the traffic is assigned to one of the these three
      categories:

        -  Green, for traffic under CIR

        -  Yellow, for traffic between CIR and PIR

        -  Red, for traffic above PIR

      An inbound 2r3c meter implemented with {{?RFC4115}}, compared to
      {{?RFC2698}}, is more 'customer friendly' as it doesn't impose
      outbound peak-rate shaping requirements on customer edge (CE)
      devices. 2r3c meters in general give greater flexibility for provider network edge
      enforcement regarding accepting the traffic (green), de-
      prioritizing and potentially dropping the traffic on transit during
      congestion (yellow), or hard dropping the traffic (red).

   Inbound provider network edge enforcement model for 5QI-unaware model, where all packets
   belonging to the slice are treated the same way in the provider network (no
   5Q QoS Class differentiation in the provider) is outlined in
   {{figure-17}}.

~~~
            Slice
           policer     +---------+
              |    +---|--+      |
              |    |      |      |
              |    |    S |      |
              |    |    l |      |
              v    |    i |      |
--------------p----|--> c |      |
                   |    e |  A   |
                   |      |  t   |
                   |    1 |  t   |
                   |      |  a   |
                    ------   c   |
                   |      |  h   |
                   |    S |  m   |
                   |    l |  e   |
                   |    i |  n   |
--------------p----|--> c |  t   |
                   |    e |      |
                   |      |  C   |
                   |    2 |  i   |
                   |      |  r   |
                    ------   c   |
                   |      |  u   |
                   |    S |  i   |
                   |    l |  t   |
                   |    i |      |
--------------p----|--> c |      |
                   |    e |      |
                   |      |      |
                   |    3 |      |
                   |      |      |
                   +---|--+      |
                       +---------+
~~~
{: #figure-17 title="Ingress Slice Admission Control (5QI-unware Model)" artwork-align-"center"}

####  Outbound Edge Resource Control

   While inbound slice admission control at the provider network edge is
   mandatory in the architecture described in this document, outbound provider network edge resource control might not be
   required in all use cases.  Use cases that specifically call for
   outbound provider network edge resource control are:

   *  Slices use both CIR and PIR parameters, and provider network edge links
      (ACs) are dimensioned to fulfil the aggregate of
      slice CIRs.  If at any given time, some slices send the traffic
      above CIR, congestion in outbound direction on the provider network edge
      link (AC) might happen.  Therefore, fine-grained resource control to
      guarantee at least CIR for each slice is required.

   *  Any-to-Any (A2A) connectivity constructs are deployed, again
      resulting in potential congestion in outbound direction on the
      provider network edge links, even if only slice CIR parameters are used.
      This again requires fine-grained resource control per slice in
      outbound direction at the provider network edge links.

   As opposed to inbound provider network edge resource control, typically implemented
   with rate-limiters/policers, outbound resource control is typically
   implemented with a weighted/priority queuing, potentially combined
   with optional shapers (per slice).  A detailed analysis of different
   queuing mechanisms is out of scope for this document, but is provided
   in {{?RFC7806}}.

   {{figure-18}} outlines the outbound provider network edge resource control model
   for 5QI-unaware slices.  Each slice is
   assigned a single egress queue.  The sum of slice CIRs, used as the
   weight in weighted queueing model, should not exceed the physical
   capacity of the AC.  Slice requests above this limit
   should be rejected by the NSC, unless an already established slice with
   lower priority, if such exists, is preempted.

~~~
      +---------+        QoS output queues
      |     +---|--+- - - - - - - - - - - - - - - - - - - - - - - - - -
      |     | S    |                            \|/
      |     | l    |                             |
      |     | i    |                             |
      |  A  | c    |                             |  weight-Slice-1-CIR
      |  t  | e  +-|--------------------------+  | shaping-Slice-1-PIR
   ---|--t--|---->                            |  |
      |  a  | 1  +-|--------------------------+ /|\
      |  c   ------ - - - - - - - - - - - - - - - - - - - - - - - - - -
      |  h  | S    |                            \|/
      |  m  | l    |                             |
      |  e  | i    |                             |
      |  n  | c    |                             |  weight-Slice-2-CIR
      |  t  | e  +-|--------------------------+  | shaping-Slice-2-PIR
   ---|-----|---->                            |  |
      |  C  | 2  +-|--------------------------+ /|\
      |  i   ------ - - - - - - - - - - - - - - - - - - - - - - - - - -
      |  r  | S    |                            \|/
      |  c  | l    |                             |
      |  u  | i    |                             |
      |  i  | c    |                             |  weight-Slice-3-CIR
      |  t  | e  +-|--------------------------+  | shaping-Slice-3-PIR
   ---|-----|---->                            |  |
      |     | 3  +-|--------------------------+ /|\
      |     +---|--+- - - - - - - - - - - - - - - - - - - - - - - - - -
      +---------+
~~~
{: #figure-18 title="Ingress Slice Admission control (5QI-unaware Model)" artwork-align-"center"}

###  5QI-aware Model

   In the 5QI-aware model, potentially a large number of 5G QoS Classes, represented via the DSCP set by NFs
   (the architecture scales to thousands of 5G slices) is mapped
   (multiplexed) to up to 8 TN QoS Classes used in a provider network transit
   equipment, as outlined in {{figure-QoS-5QI-aware}}.


~~~~
{::include ./drawings/QoS-5QI-aware.txt}
~~~~
{: #figure-QoS-5QI-aware title="Slice 5Q QoS to TN QoS Mapping (5QI-aware Model)" artwork-align-"center"}


   Given that in deployments with a large number of 5G
   slices, the number of potential 5G QoS Classes is much higher than
   the number of TN QoS Classes, multiple 5G QoS Classes with similar
   characteristics - potentially from different slices -
   would be grouped with common operator-defined TN logic and mapped to a same TN QoS Class when transported in the
   provider network.  That is, common Per-hop Behavior (PHB) {{?RFC2474}} is executed on
   transit provider network routers for all packets grouped together. An example of this
   approach is outlined in {{figure-QoS-5QI-mapping-example}}. A provider may decide
   to implement Diffserv-Intercon PHBs at the boundaries of its network domain {{?RFC8100}}.

Note:
: The numbers indicated in {{figure-QoS-5QI-mapping-example}} (S-NSSAI, 5QI, DSCP, queue, etc.) are provided for illustration purposes only and should not be considered as deployment guidance.


~~~~
{::include ./drawings/QoS-5QI-mapping-example.txt}
~~~~
{: #figure-QoS-5QI-mapping-example title="Example of 3GPP QoS Mapped to TN QoS" artwork-align-"center"}

In current SDO progress of 3GPP (Release 17) and O-RAN, the mapping of 5QI to
DSCP is not expected to be in a per-slice fashion, where 5QI to DSCP mapping may
vary from 3GPP slice to 3GPP slice, hence the mapping of 5G QoS DSCP values
to TN QoS Classes may be rather common.

   Like in the 5QI-unaware model, the original IP header retains the DCSP
   marking corresponding to 5QI (5G QoS Class), while the new header
   (MPLS or IPv6) carries QoS marking related to TN QoS Class.  Based on
   TN QoS Class marking, per-hop behavior for all aggregated 5G QoS
   Classes from all RFC 9543 Network Slices is executed on the provider network transit links.  Provider network
   transit routers do not evaluate the original IP header for QoS
   related decisions.  The original DSCP marking retained in the
   original IP header is used at the PE for fine-grained per slice and
   per 5G QoS Class inbound/outbound enforcement on the AC.

   In the 5QI-aware model, compared to the 5QI-unware model, provider network edge resources are controlled in an even more
   granular, fine-grained manner, with dedicated resource allocation for
   each RFC 9543 Network Slice and dedicated resource allocation for number
   of traffic classes (most commonly up 4 or 8 traffic classes,
   depending on the Hardware capability of the equipment) within each RFC 9543
   Network Slice.

####  Inbound Edge Resource Control

   Compared to the 5QI-unware model, admission control (traffic
   conditioning) in the 5QI-aware model is more granular, as it enforces
   not only per slice capacity constraints, but may as well enforce the
   constraints per 5G QoS Class within each slice.

   A 5G slice using multiple 5QIs can potentially specify rates in one of
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

~~~
                     Class             +---------+
                    policer         +--|---+     |
                                    |      |     |
5Q-QoS-A: CIR-1A ------p------------|--> S |     |
5Q-QoS-B: CIR-1B ------p------------|--> l |     |
5Q-QoS-C: CIR-1C ------p------------|--> i |     |
                                    |    c |     |
                                    |    e |     |
   BE CIR/PIR-1D ------p------------|-->   |  A  |
                                    |    1 |  t  |
                                    |      |  t  |
                                     ------   a  |
                                    |      |  c  |
5Q-QoS-A: CIR-2A ------p------------|->  S |  h  |
5Q-QoS-B: CIR-2B ------p------------|->  l |  m  |
5Q-QoS-C: CIR-2C ------p------------|->  i |  e  |
                                    |    c |  n  |
                                    |    e |  t  |
   BE CIR/PIR-2D ------p------------|->    |     |
                                    |    2 |  C  |
                                    |      |  i  |
                                     ------   r  |
                                    |      |  c  |
5Q-QoS-A: CIR-3A ------p------------|->  S |  u  |
5Q-QoS-B: CIR-3B ------p------------|->  l |  i  |
5Q-QoS-C: CIR-3C ------p------------|->  i |  t  |
                                    |    c |     |
                                    |    e |     |
   BE CIR/PIR-3D-------p------------|->    |     |
                                    |    3 |     |
                                    |      |     |
                                    +--|---+     |
                                       +---------+
~~~
{: #figure-20 title="Ingress Slice Admission Control (5QI-aware Model)" artwork-align-"center"}

   The second model combines the advantages of 5QI-unaware model (per
   slice admission control) with the per traffic class admission
   control, as outlined in {{figure-20}}.  Ingress admission control is at
   class granularity for premium classes (CIR only).  Non-premium class
   (i.e.,  Best Effort) has no separate class admission control policy,
   but it is allowed to use the entire slice capacity, which is available at
   any given moment.  I.e., slice capacity, which is not consumed by
   premium classes.  It is a hierarchical model, as depicted in
   {{figure-21}}.

~~~
                              Slice
                             policer   +---------+
                   Class        .   +--|---+     |
                  policer      ; :  |      |     |
5Q-QoS-A: CIR-1A ----p---------|-|--|--> S |     |
5Q-QoS-B: CIR-1B ----p---------|-|--|--> l |     |
5Q-QoS-C: CIR-1C ----p---------|-|--|--> i |     |
                               | |  |    c |     |
                               | |  |    e |     |
   BE CIR/PIR-1D --------------|-|--|-->   |  A  |
                               | |  |    1 |  t  |
                               : ;  |      |  t  |
                                .    ------   a  |
                               ; :  |      |  c  |
5Q-QoS-A: CIR-2A ----p---------|-|--|--> S |  h  |
5Q-QoS-B: CIR-2B ----p---------|-|--|--> l |  m  |
5Q-QoS-C: CIR-2C ----p---------|-|--|--> i |  e  |
                               | |  |    c |  n  |
                               | |  |    e |  t  |
   BE CIR/PIR-2D --------------|-|--|-->   |     |
                               | |  |    2 |  C  |
                               : ;  |      |  i  |
                                .    ------   r  |
                               ; :  |      |  c  |
5Q-QoS-A: CIR-3A ----p---------|-|--|--> S |  u  |
5Q-QoS-B: CIR-3B ----p---------|-|--|--> l |  i  |
5Q-QoS-C: CIR-3C ----p----- ---|-|--|--> i |  t  |
                               | |  |    c |     |
                               | |  |    e |     |
   BE CIR/PIR-3D --------------|-|--|-->   |     |
                               | |  |    3 |     |
                               : ;  |      |     |
                                '   +--|---+     |
                                       +---------+
~~~
{: #figure-21 title="Ingress Slice Admission Control (5QI-aware) - Hierarchical" artwork-align-"center"}

####  Outbound Edge Resource Control

   {{figure-22}} outlines the outbound edge resource control model at the
   transport network layer for 5QI-aware slices.  Each slice is assigned
   multiple egress queues.  The sum of queue weights, which are 5Q QoS
   queue CIRs within the slice, should not exceed the CIR of the slice
   itself.  And, similarly to the 5QI-aware model, the sum of slice CIRs
   should not exceed the physical capacity of the AC.

~~~
   +---------+        QoS output queues
   |      ---|-- - - - - - - - - - - - - - - - - - - - - - - - - - -
   |     |   |.-|--------------------------. \|/
---|-----|----> 5Q-QoS-A: w-5Q-QoS-A-CIR   |  |
   |     | S |'-|--------------------------'  |
   |     | l |.-|--------------------------.  |
---|-----|-i--> 5Q-QoS-B: w-5Q-QoS-B-CIR   |  |
   |     | c |'-|--------------------------'  |  weight-Slice-1-CIR
   |     | e |.-|--------------------------.  | shaping-Slice-1-PIR
---|-----|----> 5Q-QoS-C: w-5Q-QoS-C-CIR   |  |
   |     | 1 |'-|--------------------------'  |
   |     |   |.-|--------------------------.  |
---|-----|----> Best Effort (remainder)    |  |
   |     |   |'-|--------------------------' /|\
   |  A   ---|-- - - - - - - - - - - - - - - - - - - - - - - - - - -
   |  t  |   |.-|--------------------------. \|/
   |  t  |   | |                          |  |
   |  a  |   |'-|--------------------------'  |
   |  c  | S |.-|--------------------------.  |
   |  h  | l | |                          |  |
   |  m  | i |'-|--------------------------'  |  weight-Slice-2-CIR
   |  e  | c |.-|--------------------------.  | shaping-Slice-2-PIR
   |  n  | e | |                          |  |
   |  t  |   |'-|--------------------------'  |
   |     | 2 |.-|--------------------------.  |
   |  C  |   | |                          |  |
   |  i  |   |'-|--------------------------' /|\
   |  r   ---|-- - - - - - - - - - - - - - - - - - - - - - - - - - -
   |  c  |   |.-|--------------------------. \|/
   |  u  |   | |                          |  |
   |  i  | S |'-|--------------------------'  |
   |  t  | l |.-|--------------------------.  |
   |     | i | |                          |  |
   |     | c |'-|--------------------------'  |  weight-Slice-3-CIR
   |     | e |.-|--------------------------+  | shaping-Slice-3-PIR
   |     |   | |                          |  |
   |     | 3 |'-|--------------------------'  |
   |     |   |.-|--------------------------.  |
   |     |   | |                          |  |
   |     |   |'-|--------------------------' /|\
   |      ---|-- - - - - - - - - - - - - - - - - - - - - - - - - - -
   +---------+
~~~
{: #figure-22 title="Egress Slice Admission Control (5QI-aware)" artwork-align-"center"}

##  Transit Resource Control

   Transit resource control is much simpler than Edge resource control in the provider network.
   As outlined in {{figure-QoS-5QI-aware}}, at the provider network edge, 5Q QoS Class marking
   (represented by DSCP related to 5QI set by mobile network functions
   in the packets handed off to the TN) is mapped to the TN QoS Class.
   Based on TN QoS Class, when the packet is encapsulated with outer
   header (MPLS or IPv6), TN QoS Class marking (MPLS TC or IPv6 DSCP in
   outer header, as depicted in {{figure-15}} and {{figure-16}}) is set in the
   outer header.  PHB in provider network transit routers is based exclusively on that TN QoS
   Class marking, i.e., original 5G QoS Class DSCP is not taken into
   consideration on transit.

   Provider network transit resource control does not use any inbound interface policy,
   but only outbound interface policy, which is based on priority queue
   combined with weighted or deficit queuing model, without any shaper.
   The main purpose of transit resource control is to ensure that during
   network congestion events, for example caused by network failures and
   temporary rerouting, premium classes are prioritized, and any drops
   only occur in traffic that was de-prioritized by ingress admission control {{sec-inbound-edge-resource-control}} or in non-premium (best-effort) classes.  Capacity planning and management, as described in {{sec-capacity-planning}}, ensures that enough
   capacity is available to fulfill all approved slice requests.

#  Inter-PE Transfer Plane Mapping Models {#transport-plane-mapping-models}

   An inter-PE transfer plane (or "transfer plane") refers to a specific forwarding behavior between PEs in order to provide packet delivery that is consistent with the corresponding SLOs.
   A network operator can define multiple inter-PE transfer planes within a single NRP. A transfer plane may be realized in multiple ways such as (but not limited to):

   * A mesh of RSVP-TE {{?RFC3209}} or SR-TE {{?RFC9256}} tunnels created with specific optimization criteria and
   constraints. For example, mesh "A" might represent tunnels optimized for latency, and mesh "B" might represent tunnels optimized for high capacity.
   * A Flex-Algorithm {{?RFC9350}} with a particular metric-type (e.g., latency), or one that only uses links with particular properties (e.g., MACsec link {{IEEE802.1AE}}), or excludes links that are within a particular geography.

These protocols can be controlled, e.g., by tuning the protocol list under the "underlay-transport" data node defined in the L3VPN Network Model (L3NM) {{?RFC9182}} and the L2VPN Network Model (L2NM) {{?RFC9291}}.

Also, inter-PE transfer planes may be realized using separate NRPs. However, such an approach is left out of the scope given the current state of the technology (2024).

   {{figure-23}} depicts an example of a simple network with two transfer
   planes, each using a mesh of TE tunnels with or without Path Computation Element (PCE) {{?RFC5440}}, and with or without bandwidth
   reservations.
   {{sec-capacity-planning}} discusses in detail different bandwidth
   models that can be deployed in the provider network.  However,
   discussion about how to realize or orchestrate inter-PE transfer planes is
   out of scope for this document.

~~~
+---------------+                                    +------+
|  Ingress PE   |   .------------------------------->| PE-A |
|               |   |   .-------------------------->>|      |
|  +---------+  |   |   '---------------------.      +------+
|  |         x------'   .---------------------'
|  |Transfer x--------------------------------.      +------+
|  | Plane A x-------------.                  '----->| PE-B |
|  |         x-------.  |  |  .---.   .---.   .---->>|      |
|  +---------+  |    |  |  |  |   |   |   |   |      +------+
|               |    |  |  |  |   '---'   '---'
|  +---------+  |    |  |  |  |                      +------+
|  |         o-------|--'  '------------------------>| PE-C |
|  |Transfer o-------|--------'               .---->>|      |
|  | Plane B o-------|-----------------.      |      +------+
|  |         o-----. '---------------. |      |
|  +---------+  |  | .-. .-. .-. .-. | '------'      +------+
|               |  | | | | | | | | | '-------------->| PE-D |
+---------------+  '-' '-' '-' '-' '--------------->>|      |
                                                     +------+
 x----->   Tunnels of Inter-PE Transfer Plane A
 o---->>   Tunnels of Inter-PE Transfer Plane B
~~~
{: #figure-23 title="Example of Inter-PE Transfer Planes Relying on TE Tunnels" artwork-align-"center"}

   For illustration purposes, {{figure-23}} shows only single
   tunnels per transfer plane for (ingress PE, egress PE) pair. However, there might be multiple tunnels within a single transfer plane
   between any pair of PEs.

   It is worth noting that TN QoS Classes and inter-PE transfer planes are
   orthogonal.  The TN domain can be operated with, e.g., 8 TN QoS Classes (representing 8 hardware queues in the
   routers), and 2 transfer planes (e.g., latency optimized transfer
   plane using link latency metrics for path calculation, and transfer
   plane following Interior Gateway Protocol (IGP) metrics).  TN QoS Class determines the per-hop
   behavior when the packets are transiting through the provider network,
   while transfer plane determines the paths for packets through provider
   network based on operator's business model (operator's requirements).
   This path can be optimized or constrained.

   Similar to the QoS mapping models discussed in {{sec-qos-map}}, for mapping
   to transfer planes at the ingress PE, both 5QI-unaware and 5QI-aware
   models are defined.  Essentially, entire slices can be mapped to
   transfer planes without 5G QoS consideration (5QI-unaware model). For example,
   flows with different 5G QoS Classes, even from same
   slice, can be mapped to different transfer planes (5QI-aware
   model).

##  5QI-unaware Model

   As discussed in {{sec-5QI-unaware}}, in the 5QI-unware model, the provider network
   doesn't take into account 5G QoS during execution of per-hop
   behavior.  The entire slice is mapped to single TN QoS Class,
   therefore the entire slice is subject to the same per-hop behavior.
   Similarly, in 5QI-unaware inter-PE transfer plane mapping model, the entire
   slice is mapped to a single transfer plane, as depicted in
   {{figure-24}}.

~~~
   +-----------------------------------------+
   |.. .. .. .. .. ..                        |
   :        AC       :      PE               |
   :+---------------+:                       |
   :|  SDP          |:                       |
   :|  +----------+ |:                       |
   :|  |     NS 1 +----------+               |
   :|  +----------+ |:       |               |
   :+---------------+:       |               |
   :+---------------+:       |   +---------+ |
   :|  SDP          |:       |   |         | |
   :|  +----------+ |:       |   |Transfer | |
   :|  |     NS 2 +------+   +--->  Plane  | |
   :|  +----------+ |:   |   |   |    A    | |
   :+---------------+:   |   |   |         | |
   :+---------------+:   |   |   +---------+ |
   :|  SDP          |:   |   |               |
   :|  +----------+ |:   |   |               |
   :|   |     NS 3 +-----+   |               |
   :|  +----------+ |:   |   |   +---------+ |
   :+---------------+:   |   |   |         | |
   :+---------------+:   |   |   |Transfer | |
   :|  SDP          |:   +------->  Plane  | |
   :|  +----------+ |:   |   |   |    B    | |
   :|  |     NS 4 +------+   |   |         | |
   :|  +----------+ |:       |   +---------+ |
   :+---------------+:       |               |
   :+---------------+:       |               |
   :|  SDP          |:       |               |
   :|  +----------+ |:       |               |
   :|  |     NS 5 +----------+               |
   :|  +----------+ |:                       |
   :+---------------+:                       |
   '.. .. .. .. .. ..                        |
   +-----------------------------------------+
~~~
{: #figure-24 title="Network Slice to Inter-PE Transfer Plane Mapping (5QI-unaware Model)" artwork-align-"center"}

##  5QI-aware Model

   In 5QI-aware model, the traffic can be mapped to transfer planes at
   the granularity of 5G QoS Class.  Given that the potential number of
   inter-PE transfer planes is limited, packets from multiple 5G QoS Classes
   with similar characteristics are mapped to a common transfer plane,
   as depicted in {{figure-25}}.

~~~
     +-------------------------------------------+
     |.. .. .. .. .. ..                          |
     :        AC       :      PE                 |
     :+---------------+:                         |
   R :|  SDP          |:                         |
   F :|  +----------+ |:                         |
   C :|  | 5G QoS A +------+                     |
   9 :|  +----------+ |:   |                     |
   5 :|  +----------+ |:   |                     |
   4 :|  | 5G QoS B +------+                     |
   3 :|  +----------+ |:   |         +---------+ |
     :|  +----------+ |:   |         |         | |
   N :|  | 5G QoS C +-----------+    |Transfer | |
   S :|  +----------+ |:   +--------->  Plane  | |
     :|  +----------+ |:   |    |    |    A    | |
   1 :|  | 5G QoS D +-----------+    |         | |
     :|  +----------+ |:   |    |    +---------+ |
     :+---------------+:   |    |                |
   R :+---------------+:   |    |                |
   F :|  +----------+ |:   |    |                |
   C :|  | 5G QoS A +------+    |    +---------+ |
   9 :|  +----------+ |:   |    |    |         | |
   5 :|  +----------+ |:   |    |    |Transfer | |
   4 :|  | 5G QoS E +------+    +---->  Plane  | |
   3 :|  +----------+ |:        |    |    B    | |
     :|  +----------+ |:        |    |         | |
   N :|  | 5G QoS F +-----------+    +---------+ |
   S :|  +----------+ |:        |                |
     :|  +----------+ |:        |                |
   2 :|  | 5G QoS G +-----------+                |
     :|  +----------+ |:                         |
     :|  SDP          |:                         |
     :+---------------+:                         |
     '.. .. .. .. .. ..                          |
     +-------------------------------------------+
~~~
{: #figure-25 title="Network Slice to Inter-PE Transfer Plane mapping (5QI-aware Model)" artwork-align-"center"}

#  Capacity Planning/Management {#sec-capacity-planning}

## Bandwidth Requirements

   This section describes the information conveyed by the 5G NSO to the
   NSC with respect to slice bandwidth requirements.

   {{figure-multi-DC}} shows three DCs that contain instances of network
   functions.  Also shown are PEs that have links to the DCs.  The PEs
   belong to the provider network.  Other details of the provider
   network, such as P-routers and transit links are not shown.  Also
   details of the DC infrastructure in customer sites, such as switches and routers, are not
   shown.

   The 5G NSO is aware of the existence of the network functions and their
   locations.  However, it is not aware of the details of the provider
   network.  The NSC has the opposite view - it is
   aware of the provider network infrastructure and the links between the PEs
   and the DCs, but is not aware of the individual network functions at customer sites.

~~~~
{::include ./drawings/multi-DC.txt}
~~~~
{: #figure-multi-DC title="An Example of Multi-DC Architecture" artwork-align-"center"}


   Let us consider 5G slice "X" that uses some of the network functions in
   the three DCs.  If this slice has latency requirements, the 5G NSO will
   have taken those into account when deciding which NF instances
   in which DC are to be invoked for this slice.  As a result of such a
   placement decision, the three DCs shown are involved in 5G slice "X",
   rather than other DCs.  For its decision-making, the 5G NSO
   needs information from the NSC about the observed latency between DCs.
   Preferably, the NSC would present the topology in an abstracted form,
   consisting of point-to-point abstracted links between pairs of DCs
   and associated latency and, optionally, delay variation and link loss
   values.  It would be valuable to have a mechanism for the 5G NSO to
   inform the NSC which DC-pairs are of interest for these metrics -
   there may be of order thousands of DCs, but the 5G NSO will only be
   interested in these metrics for a small fraction of all the possible
   DC-pairs, i.e. those in the same region of the provider network.  The
   mechanism for conveying the information is out of scope for this document.

   {{table-x}} shows the matrix of bandwidth demands for 5G slice "X".
   Within the slice, multiple NF instances might be
   sending traffic from DCi to DCj.  However, the 5G NSO sums the
   associated demands into one value.  For example, "NF1A" and "NF1B" in "DC1"
   might be sending traffic to multiple NFs in "DC2", but this is
   expressed as one value in the traffic matrix: the total bandwidth
   required for 5G slice "X" from "DC1" to "DC2" (8 units).  Each row in the
   right-most column in the traffic matrix shows the total amount of
   traffic going from a given DC into the transport network, regardless
   of the destination DC.  Note that this number can be less than the
   sum of DC-to-DC demands in the same row, on the basis that not all
   the NFs are likely to be sending at their maximum rate
   simultaneously.  For example, the total traffic from "DC1" for slice "X"
   is 11 units, which is less than the sum of the DC-to-DC demands in
   the same row (13 units).  Note, as described in {{sec-qos-map}}, a slice
   may have per-QoS class bandwidth requirements, and may have CIR and
   PIR limits.  This is not included in the example, but the same
   principles apply in such cases.

|From/To|	DC 1|	DC 2|	DC 3|	Total from DC|
|---|---|---|---|:-------------:|
| DC 1 | n/a  |  8   |  5   |     11.0     |
| DC 2 |  1   | n/a  |  2   |      2.5     |
| DC 3 |  4   |  7   | n/a  |     10.0     |
{: #table-x title="Inter-DC Traffic Demand Matrix (Slice X)"}

   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} can be used to convey all
   of the information in the traffic matrix to an NSC.  The
   NSC applies policers corresponding to the last column in the traffic
   matrix to the appropriate PE routers, in order to enforce the
   bandwidth contract.  For example, it applies a policer of 11 units to
   PE1A and PE1B that face DC1, as this is the total bandwidth that DC1
   sends into the provider network corresponding to Slice X.  Also, the
   controller may apply shapers in the direction from the TN to the DC,
   if otherwise there is the possibility of a link in the DC being
   oversubscribed.  Note that a peer NF endpoint of an AC can be
   identified using 'peer-sap-id' as defined in {{?RFC9408}}.

   Depending on the bandwidth model used in the provider network ({{sec-bw}}),
   the other values in the matrix, i.e., the DC-to-DC demands, may not
   be directly applied to the provider network.  Even so, the
   information may be useful to the NSC for capacity planning and
   failure simulation purposes.  If, on the other hand, the DC-to-DC
   demand information is not used by the NSC, the IETF YANG Data
   Model for L3VPN Service Delivery {{?RFC8299}} or the IETF YANG Data
   Model for L2VPN Service Delivery {{?RFC8466}} could be used instead of
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}, as they support
   conveying the bandwidth information in the right-most column of the
   traffic matrix.

   The provider network may be implemented in such a way that it has
   various types of paths, for example low-latency traffic might be
   mapped onto a different transport path to other traffic (for example
   a particular Flex-Algorithm, a particular set of TE paths, or a specific queue {{?RFC9330}}), as discussed
   in {{sec-qos-map}}.  The 5G NSO can use
   {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} to request low-latency
   transport for a given slice if required.  However, {{?RFC8299}} or
   {{?RFC8466}} do not support requesting a particular transport-type,
   e.g., low-latency.  One option is to augment these models to convey
   this information.  This can be achieved by reusing the 'underlay-
   transport' construct defined in {{?RFC9182}} and {{?RFC9291}}.

##  Bandwidth Models {#sec-bw}

   This section describes three bandwidth management schemes that could
   be employed in the provider network.  Many variations are possible,
   but each example describes the salient points of the corresponding
   scheme.  Schemes 2 and 3 use TE; other variations on TE are possible
   as described in {{?RFC9522}}.

### Scheme 1: Shortest Path Forwarding (SPF)

   Shortest path forwarding is used according to the IGP metric.  Given
   that some slices are likely to have latency SLOs, the IGP metric on
   each link can be set to be in proportion to the latency of the link.
   In this way, all traffic follows the minimum latency path between
   endpoints.

   In Scheme 1, although the operator provides bandwidth guarantees to
   the slice customers, there is no explicit end-to-end underpinning of
   the bandwidth SLO, in the form of bandwidth reservations across the
   provider network.  Rather, the expected performance is achieved via
   capacity planning, based on traffic growth trends and anticipated
   future demands, in order to ensure that network links are not over-
   subscribed.  This scheme is analogous to that used in many existing
   business VPN deployments, in that bandwidth guarantees are provided
   to the customers but are not explicitly underpinned end to end across
   the provider network.

   A variation on the scheme is that Flex-Algorithm {{?RFC9350}} is used. For example, one Flex-Algorithm could
   use latency-based metrics and another Flex-Algorithm could use the IGP
   metric. There would be a many-to-one mapping of Network Slices to Flex-Algorithms.

   While Scheme 1 is technically feasible, it is vulnerable to
   unexpected changes in traffic patterns and/or network element
   failures resulting in congestion.  This is because, unlike Schemes 2
   and 3 which employ TE, traffic cannot be diverted from the shortest
   path.

###  Scheme 2: TE Paths with Fixed Bandwidth Reservations

   Scheme 2 uses RSVP-TE {{?RFC3209}} or SR-TE paths {{?RFC9256}} with fixed bandwidth
   reservations.  By "fixed", we mean a value that stays constant over
   time, unless the 5G NSO communicates a change in slice bandwidth
   requirements, due to the creation or modification of a slice.  Note
   that the "reservations" would be in the mind of the transport
   controller - it is not necessary (or indeed possible for SR-TE) to
   reserve bandwidth at the network layer.  The bandwidth requirement
   acts as a constraint whenever the controller (re)computes a path.  There could be a single mesh of paths between endpoints that
   carry all of the traffic types, or there could be a small handful of
   meshes, for example one mesh for low-latency traffic that follows the
   minimum latency path and another mesh for the other traffic that
   follows the minimum IGP metric path, as described in {{sec-qos-map}}.
   There would be a many-to-one mapping of slices to paths.

   The bandwidth requirement from DCi to DCj is the sum of the DCi-DCj
   demands of the individual slices.  For example, if only slices "X" and
   "Y" are present, then the bandwidth requirement from "DC1" to "DC2"
   is 12 units (8 units for slice "X" ({{table-x}}) and 4 units for slice "Y" ({{table-y}})).  When the
   5G NSO requests a new slice, the NSC, in its mind,
   increments the bandwidth requirement according to the requirements of
   the new slice.  For example, in {{figure-multi-DC}}, suppose a new slice is
   instantiated that needs 0.8 Gbps from "DC1" to "DC2".  The transport
   controller would increase its notion of the bandwidth requirement
   from "DC1" to "DC2" from 12 Gbps to 12.8 Gbps to accommodate the
   additional expected traffic.

|From/To|	DC 1|	DC 2|	DC 3|	Total from DC|
|---|---|---|---|:-------------:|
| DC 1 | n/a  |  4   | 2.5  |     6.0      |
| DC 2 | 0.5  | n/a  | 0.8  |     1.0      |
| DC 3 | 2.6  |  3   | n/a  |     5.1      |
{: #table-y title="Inter-DC Traffic Demand Matrix (Slice Y)"}

   In the example, each DC has two PEs facing it for reasons of
   resilience.  The NSC needs to determine how to map
   the "DC1" to "DC2" bandwidth requirement to bandwidth reservations of TE
   LSPs from "DC1" to "DC2".  For example, if the routing configuration is
   arranged such that in the absence of any network failure, traffic
   from "DC1" to "DC2" always enters "PE1A" and goes to "PE2A", the controller
   reserves 12.8 Gbps of bandwidth on the path from "PE1A" to "PE2A".  If, on
   the other hand, the routing configuration is arranged such that in
   the absence of any network failure, traffic from "DC1" to "DC2" always
   enters "PE1A" and is load-balanced across "PE2A" and "PE2B", the controller
   reserves 6.4 Gbps of bandwidth on the path from "PE1A" to "PE2A" and
   6.4 Gbps of bandwidth on the path from "PE1A" to "PE2B".  It might be tricky
   for the NSC to be aware of all conditions that
   change the way traffic lands on the various PEs, and therefore know
   that it needs to change bandwidth reservations of paths accordingly.
   For example, there might be an internal failure within "DC1" that
   causes traffic from "DC1" to land on "PE1B", rather than "PE1A".  The
   NSC may not be aware of the failure and therefore
   may not know that it now needs to apply bandwidth reservations to
   paths from "PE1B" to "PE2A" / "PE2B".

### Scheme 3: TE Paths without Bandwidth Reservation

   Like Scheme 2, Scheme 3 uses RSVP-TE or SR-TE paths.  There could be a
   single mesh of paths between endpoints that carry all of the traffic
   types, or there could be a small handful of meshes, for example one
   mesh for low-latency traffic that follows the minimum latency path
   and another mesh for the other traffic that follows the minimum IGP
   metric path, as described in {{sec-qos-map}}.  There would be a many-to-one
   mapping of slices to paths.

   The difference between Scheme 2 and Scheme 3 is that Scheme 3 does
   not have fixed bandwidth reservations for the paths.  Instead, actual
   measured data-plane traffic volumes are used to influence the
   placement of TE paths.  One way of achieving this is to use
   distributed RSVP-TE with auto-bandwidth.  Alternatively, the
   NSC can use telemetry-driven automatic congestion
   avoidance.  In this approach, when the actual traffic volume in the
   data plane on given link exceeds a threshold, the controller, knowing
   how much actual data plane traffic is currently travelling along each
   RSVP or SR-TE path, can tune the paths of one or more paths using the
   link such that they avoid that link. This approach is similar to that described in {{Section 4.3.1 of ?RFC9522}}.

   It would be undesirable to move a path that has latency as its cost function, rather than
   another type of path, in order to ease the congestion, as the altered path
   will typically have a higher latency.  This can be avoided by
   designing the algorithms described in the previous paragraph such
   that they avoid moving minimum-latency paths unless there is no
   alternative.

# Network Slicing OAM

   The deployment and maintenance of slices within a network imply
   that a set of OAM functions ({{?RFC6291}}) need to be deployed by the providers, e.g.:

   *  Providers should be able to execute OAM tasks on a per Network Slice
      basis. These tasks can cover the "full" slice within a domain or a
      portion of that slice (for troubleshooting purposes, for example).

      For example, per-slice OAM tasks can consist of (but not limited to):

        - tracing resources that are bound to a given Network Slice,
        - tracing resources that are invoked when forwarding a given flow bound to a given Network Slice,
        - assessing whether flow isolation characteristics are in
      conformance with the Network Slice Service requirements, or
        - assessing the compliance of the allocated Network Slice resources against flow/
      customer service requirements.

      {{?RFC7276}} provides an overview of available OAM
      tools. These technology-specific tools can be reused in the context
      of network slicing. Providers that deploy network slicing
      capabilities should be able to select whatever OAM technology or specific feature that would address their needs.

   *  Providers may want to enable differentiated failure
      detect and repair features for a subset of network
      slices. For example, a given Network Slice may require fast detect and
      repair mechanisms, while others may
      not be engineered with such means. The provider can use
      techniques such as {{?RFC5286}}, {{?RFC5714}}, or {{?RFC8355}}.

   *  Providers may deploy means to dynamically discover the set of Network Slices that
      are enabled within its network. Such dynamic discovery capability
      facilitates the detection of any mismatch between the view
      maintained by the control/management plane and the actual network
      configuration.  When mismatches are detected, corrective actions
      should be undertaken accordingly. For example, a provider may rely
      upon the L3NM {{?RFC9182}} or the L2NM {{?RFC9291}} to maintain the full
      set of L3VPN/L2VPNs that are used to deliver Network Slice Services.
      The correlation between an LxVPN instance and a Network Slice Service
      is maintained using "parent-service-id" attribute ({{Section 7.3 of ?RFC9182}}).

   *  Means to report a set of network performance metrics to assess
      whether the agreed slice service objectives are honored. These means are used for SLO monitoring and violation detect purposes. For example,
      {{?RFC9375}} can be used to report links' one-way delay,
      one-way delay variation, etc. Both conventional active/passive
      measurement methods {{?RFC7799}} and more recent telemetry methods
      (e.g., YANG Push {{?RFC8641}}) can be used.

   * Means to report and expose observed performance metrics and other OAM state to customer.
     For example, {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} exposes a set of statistics per SDP, connectivity construct, and connection group.

# Scalability Implications {#sec-sca-impli}

The mapping between 5G slice to TN slices (see {{sec-mapping}}) is a design choice of service operators that may be a function of, e.g., the number of instantiated slices, requested services, or local engineering capabilities and guidelines. However, operators should carefully consider means to ease slice migration strategies. For example, a provider may initially adopt a 1-to-1 mapping if it has to instantiate just a few Network Slices and accommodate the need of only a few customers. That provider may decide to move to a N-to-1 mapping for aggregation/scalability purposes if sustained increased slice demand is observed.

Putting in place adequate automation means to realize Network Slices (including the adjustment of Slice Services to Network Slices mapping) would ease slice migration operations.

The realization model described in the document inherits the scalability properties of the underlying L2VPN and L3VPN technologies ({{sec-over-rea-model}}). Readers may refer, for example, to {{Section 13 of ?RFC4365}} or {{Section 1.2.5 of ?RFC6624}} for a scalability assessment of some of these technologies. Providers may adjust the mapping model to better handle local scalability constraints.

# IANA Considerations

   This document does not make any IANA request.

#  Security Considerations

{{Section 10 of !RFC9543}} discusses generic security considerations that are applicable to network slicing, with a focus on the following considerations:


 * Conformance to security constraints:

      Specific security requests, such as not routing traffic through a particular geographical region can be met by mapping the traffic to an inter-PE transfer plane that avoids that region.

 * IETF NSC authentication:

      This is out of the scope for this document. It should be addressed in documents that describe IETF NSC realization (e.g., {{?I-D.ietf-teas-ns-controller-models}}).

 * Specific isolation criteria:

      Adequate admission control policies, for example policers as described in {{sec-inbound-edge-resource-control}}, should be configured in the edge of the provider network to control access to specific slice resources. This prevents the possibility of one slice consuming resources at the expense of other slices. Likewise, access to classification and mapping tables have to be controlled to prevent misbehaviors (an unauthorized entity may modify the table to bind traffic to a random slice, redirect the traffic, etc.). Network devices have to check that a required access privilege is provided before granting access to specific data or performing specific actions.

 * Data Confidentiality and Integrity of an IETF Network Slice:

     As described in {{Section 5.1.2.1 of !RFC9543}}, the customer might request an SLE that mandates encryption. As described in {{transport-plane-mapping-models}}, this can be achieved, e.g., by mapping the traffic to an inter-PE transfer plane that uses only MACsec-encrypted links.

Many of the YANG modules cited in this document define schema for data that is designed to be accessed via network management protocols such as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}. The lowest NETCONF layer is the secure transport layer, and the mandatory-to-implement secure transport is Secure Shell (SSH) {{!RFC6242}}. The lowest RESTCONF layer is HTTPS, and the mandatory-to-implement secure transport is TLS {{!RFC8446}}.

The NETCONF access control model {{!RFC8341}} provides the means to restrict access for particular NETCONF or RESTCONF users to a preconfigured subset of all available NETCONF or RESTCONF protocol operations and content.

In order to avoid the need for a mapping table to associate source/destination IP
addresses and slices' specific S-NSSAIs, {{sec-ip-hof}} describes an approach where some or all S-NSSAI bits
are embedded in an IPv6 address using an algorithm approach. An attacker from within the transport network
who has access to the mapping configuration may infer the slices to which belong a packet. It may also
alter these bits which may lead to steering the packet via a distinct network slice, and thus lead to
service disruption. Note that such an on-path attacker may make more damage (e.g., randomly drop packets).

Security considerations specific to each of the technologies and protocols listed in the document are discussed in the specification documents of each of these protocols.




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

   IGP: Interior Gateway Protocol

   L2VPN: Layer 2 Virtual Private Network

   L3VPN: Layer 3 Virtual Private Network

   LSP: Label Switched Path

   MH: Midhaul

   MIoT: Massive Internet of Things

   MPLS: Multiprotocol Label Switching

   NF: Network Function

   NRF: Network Function Repository

   NRP: Network Resource Partition

   NSC: Network Slice Controller

   PE: Provider Edge

   PIR: Peak Information Rate

   QoS: Quality of Service

   RAN: Radio Access Network

   RIB: Routing Information Base

   RSVP: Resource Reservation Protocol

   RU: Radio Unit

   SD: Slice Differentiator

   SDP: Service Demarcation Point

   SLA: Service Level Agreement

   SLO: Service Level Objective

   SMF: Session Management Function

   S-NSSAI: Single Network Slice Selection Assistance Information

   SST: Slice/Service Type

   SR: Segment Routing

   SRv6: Segment Routing version 6

   TC: Traffic Class

   TE: Traffic Engineering

   TN: Transport Network

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

#  An Overview of 5G Networking {#sec-5g-overview}

   This section provides a brief introduction to 5G mobile networking
   with a perspective on the Transport Network.  This section does not
   intend to replace or define 3GPP architecture, instead its objective is to provide an
   overview for readers that do not have a mobile background.  For
   more comprehensive information, refer to {{TS-23.501}}.

##  Key Building Blocks

   {{TS-23.501}} defines the Network Functions (UPF, Access and Mobility Function (AMF), etc.) that
   compose the 5G System (5GS) Architecture together with related
   interfaces (e.g., N1 and N2).  This architecture has built-in control
   and user plane separation, and the control plane leverages a Service-
   Based Architecture (SBA).  {{figure-28}} outlines an example 5GS architecture
   with a subset of possible NFs and network interfaces.

~~~

  +-----+  +-----+  +-----+    +-----+  +-----+  +-----+
  |NSSF |  | NEF |  | NRF |    | PCF |  | UDM |  | AF  |
  +--+--+  +--+--+  +--+--+    +--+--+  +--+--+  +--+--+
Nnssf|    Nnef|    Nnrf|      Npcf|    Nudm|        |Naf
  ---+--------+--+-----+----------+---+----+--------+----
            Nausf|    Namf|       Nsmf|
              +--+--+  +--+--+     +--+------+
              |AUSR |  | AMF |     |   SMF   |
              +-----+  +--+--+     +--+------+
                       /  |           |      \
Control Plane      N1 /   |N2         |N4     \N4
------------------------------------------------------------
User Plane          /     |           |         \
                +---+  +--+--+  N3 +--+--+ N9 +-----+ N6  .---.
                |UE +--+(R)AN+-----+ UPF +----+ UPF +----( DN  )
                +---+  +-----+     +-----+    +-----+     '---'
~~~
{: #figure-28 title="5GS Architecture and Service-based Interfaces" artwork-align-"center"}

   Similar to previous versions of 3GPP mobile networks {{?RFC6459}}, a 5G mobile network is split
   into the following four major domains ({{figure-29}}):

   *  UE, MS, MN, and Mobile:

      The terms User Equipment (UE), Mobile Station (MS), Mobile
      Node (MN), and mobile refer to the devices that are hosts with the
      ability to obtain Internet connectivity via a 3GPP network.  An MS
      is comprised of a Terminal Equipment (TE) and a Mobile Terminal
      (MT).

   *  Radio Access Network (RAN):

      Provides wireless connectivity to UEs. A RAN is
      made up of the Antenna that transmits and receives signals to
      UEs and the Base Station that digitizes the signal and converts the
      Radio Frequency (RF) data stream to IP packets.

   *  Core Network (CN):

      Controls the CP of the RAN and provides connectivity to the Data
      Network (e.g., the Internet or a private VPN).  The Core Network
      hosts dozens of services such as authentication, phone registry,
      charging, access to Public Switched Telephony Network (PSTN) and handover.

   *  Transport Network (TN):

      Provides connectivity between 5G NFs.  The TN may provide connectivity from the RAN to the CN as well as  within the RAN or within the CN.  The
      traffic generated by NFs is - mostly - based on IP or Ethernet.

~~~
+----------------------------------------------+
|             +------------+    +------------+ |
| +----+      |            |    |            | |   .-------.
| | UE +------+    RAN     |    |     CN     +----(    DN   )
| +----+      |            |    |            | |   '-------'
|             +------+-----+    +------+-----+ |
|                    |                 |       |
|              +-----+-----------------+----+  |
|              |     Transport Network      |  |
|              +----------------------------+  |
|                                              |
|                    5G System                 |
+----------------------------------------------+
~~~
{: #figure-29 title="Building Blocks of 5G Architecture (A High-Level Representation)" artwork-align-"center"}

##  Core Network (CN)

   The 5G Core Network (5GC) is made up of a set of NFs which fall into two main categories ({{figure-30}}):

   *  5GC User Plane:

      The UPF is the interconnect
      point between the mobile infrastructure and the Data Network (DN).
      It interfaces with the RAN via the N3 interface by encapsulating/
      decapsulating the user plane traffic in GTP tunnels (aka GTP-U or
      Mobile user plane).

   *  5GC Control Plane:

      The 5G control plane is made up of a
      comprehensive set of NFs.  The description of these entities is out of the scope of this
      document. The following NFs and interfaces are worth mentioning,
      since their connectivity may rely on the Transport Network:

      -  the AMF connects with the RAN control plane over the N2 interface

      -  the SMF controls the 5GC UPF via the N4 interface

~~~
  +---------+    +-------------------------+
  |   RAN   |    |      5G Core (5GC)      |
  |         |    |                         |
  |         |    |   [AUSF  NRF  UDM ...]  |
  |         |    |         (SBA)           |
  |         |    |                         |
  |         | N2 |   +-----+ N11 +-----+   |
  |    CP -----------+ AMF +-----+ SMF |   |
  |         |    |   +-----+     +--+--+   |
  |         |    |                  |      |  Control Plane
-----------------------------------------------------------
  |         |    |                  | N4   |  User Plane
  |         | N3 |               +--+--+   | N6  .-------.
  |    UP -----------------------+ UPF +------->(   DN    )
  |         |    |               +-----+   |     `-------'
  +---------+    +-------------------------+
~~~
{: #figure-30 title="5G Core Network (CN)" artwork-align-"center"}

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


~~~
            +---------------------------------+    +-----------+
            |                                 | N3 |           |
+----+  NR  |                                 +----+  5G Core  |
| UE +------+             gNodeB              |    |           |
+----+      |                                 +----+   (5GC)   |
            |                                 | N2 |           |
            +---------------------------------+    +-----------+
                            | |
                           .+ +.
                           \   /
                            \ /
            +---------------------------------+    +-----------+
            |           +-------------------+ |    |           |
            |           |                   | |    |           |
+----+  NR  | +----+ F2 |+----+ F1-U +-----+| | N3 |  +-----+  |
| UE +--------+ RU +-----+ DU +------+CU-UP+----------+ UPF |  |
+----+      | +----+    |+-+--+      +--+--+| |    |  +-----+  |
            |           |  |            |E1 | |    |           |
            |           |  | F1-C       |   | |    |           |
            |           |  |         +--+--+| | N2 |  +-----+  |
            |           |  +---------+CU-CP+----------+ AMF |  |
            |           |            +-----+| |    |  +-----+  |
            |           |     BBU split     | |    |  5G Core  |
            |           +-------------------+ |    |           |
            |       Disaggregated gNodeB      |    |           |
            +---------------------------------+    +-----------+
~~~
{: #figure-31 title="RAN Disaggregation" artwork-align-"center"}

##  Transport Network (TN)

   The 5G transport architecture defines three main segments for the
   Transport Network, which are commonly referred to as Fronthaul (FH),
   Midhaul (MH), and Backhaul (BH) {{TR-GSTR-TN5G}}:

   *  Fronthaul happens before the BBU processing.  In 5G, this
      interface is based on eCPRI with Ethernet
      or IP encapsulation.

   *  Midhaul is optional: this segment is introduced in the BBU split
      presented in Appendix B.3, where Midhaul network refers to the DU-
      CU interconnection (i.e., F1 interface).  At this level, all
      traffic is encapsulated in IP (signaling and user plane).

   *  Backhaul happens after BBU processing.  Therefore, it maps to the
      interconnection between the RAN and the CN.  All traffic
      is encapsulated in IP.

   {{figure-32}} illustrates the different segments of the Transport Network
   with the relevant NFs.

~~~
+---------------------------------------------------------+
|                    Transport Network                    |
|                                                         |
|    Fronthaul       Midhaul       Backhaul               |
|  +-----------+ +------------+ +-----------+             |
|  |           | |            | |           |             |
+--|-----------|-|------------|-|-----------|-------------+
 +-+--+      +-+-++         +-+-++        +-+---+     .---.
 | RU |      | DU |         | CU |        | UPF :----( DN  )
 +----+      +----+         +----+        +-----+     `---'
~~~
{: #figure-32 title="5G Transport Segments" artwork-align-"center"}

   A given part of the transport network can
   carry several 5G transport segments concurrently, as outlined in
   {{figure-33}}.  This is because different types of 5G NFs
   might be placed in the same location (e.g., the UPF from one slice
   might be placed in the same location as the CU-UP from another
   slice).

~~~
+---------+
|+----+   | Colocated
|RU-1|   | RU/DU
|+-+--+   |
|  | FH-1 |
|+-+--+   |
|DU-1|   |  +----+         +-----+         .---.
|+-+--+   |  |CU-1|         |UPF-1+--------( DN  )
+--|------+  +-+-++         +-+---+         `---'
+--|-----------|-|------------|----------------------------+
|  |    MH-1   | |    BH-1    |          Transport Network |
|  +-----------+ +------------+                            |
|  +-----------+ +------------+ +-----------+              |
|  |    FH-2   | |    MH-2    | |    BH-2   |              |
+--|-----------|-|------------|-|-----------|--------------+
 +-+--+      +-+-++         +-+-++        +-+---+     .---.
 |RU-2|      |DU-2|         |CU-2|        |UPF-2+----( DN  )
 +----+      +----+         +----+        +-----+     `---'
~~~
{: #figure-33 title="Concurrent 5G Transport Segments" artwork-align-"center"}

# Acknowledgments
{:numbered-"false"}

   The authors would like to thank Adrian Farrel, Joel Halpern, Tarek
   Saad, Greg Mirsky, Rüdiger Geib, Nicklous D. Morris, 	Daniele Ceccarelli, Bo Wu, Xuesong Geng, and Deborah Brungard for
   their review of this document and for providing valuable comments.

   Special thanks to Jie Dong and Adrian Farrel for the detailed and careful reviews.

   Thanks to Alvaro Retana for the rtg-dir review, Yoshifumi Nishida for
   the tsv-art review, and Timothy Winters for the int-dir review.
