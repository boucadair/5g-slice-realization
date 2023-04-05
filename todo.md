HERE NOTES AND WRAP OF THINGS:

# NOTES

- Shared section versus Dedicated



Additionally, sites can be of
different types (such as Edge, Data Center, or Public Cloud), each with
specific network design, hardware dependencies, management interface,
and diverse networking technologies (e.g., MPLS, SRv6, VXLAN, or L2VPN vs.
L3VPN).  The objective of this section is to clarify the scope
of the Transport Network rather than to cover random technology or
design combination.


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


===================================

   Note that the allocation of these resources (e.g.,  VLAN-IDs or IP prefixes)
   can be either managed by the SMO or the Transport Network.  In other
   words, the initial SMO request for the creation of a new IETF Network
   Slice on a given 5G site may or may not include all network
   resources.  In the latter case, this information is exchanged in a
   second step.

   ==========================

   
# CARRY-ALL






