---
abbrev: virtual-orchestra-research-challenges
docname: draft-km-virt-orchestra-research-challenges-latest 
title: Virtual Orchestra Usecase and Research Challenges 
date:  false
category: info

ipr: trust200902
area: IRTF
workgroup: Independent Submission
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
-
  ins: K. Makhijani
  name: Kiran Makhijani
  org: Futurewei
  email: kiran.ietf@gmail.com
-
  ins: L. Dong
  name: Lijun Dong
  org: Futurewei
  street: Central Expy
  city: Santa Clara, CA 95050
  country: United States of America
  email: lijun.dong@futurewei.com

informative:

--- abstract

This document describes open research challenges for emerging media-oriented ensemble applications. One such driving scenario is the network delivery of virtual orchestra that imposes multi-disciplinary challenges. Specifically, of interest are the group communication patterns in the production, delivery and consumption as different dimensions relating to the communication networks.   
 This document brings forth current research and engineering challenges in immersive media ensembles. The network domain problems come down to the specification of coordination of   the received content with dependency constraints. The challenges depict both real and quasi- realtime behavior. A number of endpoint actors get involved in delivering the ensemble aspect, the research challenges also describe the expectations from the end points.

--- middle

# Introduction and Scope

   The multimedia segment has seen tremendous advancements in immersive multimedia technologies. One of the ongoing research question is how to deliver a complete immersion experience of the digital media. Such media is 
   produced from an ensemble of different actors or multiple sources that must coordinate as they perform 
   together in the real environment. It translates to generating a very high volume of generated data streams

   This memo presents research and engineering challenges in multi-user digital ensemble that need to be 
   addressed in order to achieve these
   goals, spanning from pure research and engineering/standards space.
   The objective of this memo is to document the technical challenges
   and corresponding current approaches and to expose requirements that
   should be addressed by future research and standards work.

# Terminology

* Co-flows: Co dependent flows
* Coordinated service: A network capability to support communications between co-dependent flows

# Scenario Description 

In an  orchestra ensemble the  multimedia streams of musicians, each in a different place in the world come together and perform live on the stage which may also be at a different location. 
 
 * A conductor directs the sound of the ensemble with his gestures. These gestures must be received at the same time by the remote musicians at different locations to play their instruments at a specific time with a specified tempo. 
 * Similarly, the music transmitted from those musicians' locations to the stage must be played together with the same beats and tempo. Any delay or early arrival of the sound from any one  instrument can cause the ensemble to go out of tune and destroy the entire performance.
 
 Performing an ensemble with multiple participants separated by large as well as varying distances (from less than a mile, to 1000 miles) is quite difficult for applications due to varying path and latency characteristics. 

 The network needs to support the coordination of directions from the conductor to all of the musicians and the audio/visuals from musicians to the stage. In particular, in a large-scale ensemble when many instruments are involved, in order to to preserve the integrity of performance, it may be necessary to allow for the dropping of sound and hologram streams of a musician that cannot arrive at the same time as the others and to provide mechanisms for subsequent fast synchronization. 

Virtual orchestra is a complex multi-disciplinary use case and requires in-depth knowledge in every field to recreate the real orchestral experience.

~~~~~
             +-------------+
             |  conductor  |
             +-------------+
                  | 
                  |         one to many  
   +--------------+--------------+
   |              |              |
   v              v              v
+-----+       +-------+      +-------+
| t0" |       |   t0  |      |  t0'  |
+-(A)-+       +--(B)--+      +--(C)--+
   |              |              |
   +-----------+  |  +-----------+
               |  |  |        many to one  
               v  v  v  
         +-----------------+ 
         | coordinator node|  
         +--------+--------+ 
                  |
                  v
              +--------+ 
              | Stage  | 
              +--------+
~~~~~
{:#virtorch title="Virtual Orchestra Delivery over Network"}

## Multiple Streams and actors
A virtual orchestra is a coordination of multiple flows as shown in {{virtorch}}. In the current network terminology this is equivalent to multicast group of a number of endpoints and requiring to meet cooperation between the endpoints on how to send and receive information. An application point of view sees this as a membership to publish/subscribe topic. 
In the above example, endpoint actors are the conductor, musicians and the stage. The characteristics of traffic are predictable and the following steps take place

### Conductor to Musicians {#one_ton}
* The conductor is initiator of the orchestral stream. Synchronized reception of the gestures of a conductor are critical to the performance.
* Musicians perform on cues or gestures received over the network. It is necessary that all the musicians receive those cues to start the performance.
* The performance follows the tempo and beats from the conductor, which must be delivered in a consistent (jitter free) manner (incurring no jitter).

### Musicians to Stage {#nto1}
Atleast one output stream per sources will be generated to create the ensemble performance, these sources may have variable latencies. They should be aggregated to be delivered to the stage as a unified stream.

The two scenarios are one to many {{one_ton}} and many to one{{nto1}} type of group communication. The coordination constraints involve several dependencies such as of synchronization at the start of play, maintenance of same tempo along the time scale throughout the streaming part, description of distance for spatial sound quality.

## Virtual Orchestra Scenario Challenges
In this section we draw forth scenarios with  difficulties in delivering virtual orchestra over the network.

Note that virtual orchestra application itself maybe delivered in different ways. Non-realtime scenarios are not relevant since, in that case it is a non-interactive content delivery, the content does  not require aggregation from multiple sources. An application and corresponding network can use buffering, low latency techniques and existing transport protocols to meet the expectations of an end-user.

Specific to real-time streaming of virtual orchestra, the performance is pseudo-real-time. It means that the synchronization of content originating from different sources  is only as fast as its slowest path. In other words, one source-destination path of the co-flow  will cause the pace of the group stream to slow-down, even though the other, shorter latency paths may deliver content sooner. This in a major co-dependency challenge, since the slowest path should not have any impact on the tempo and the beats. Thus 3 dependency considerations for the network are:
- Feasibility Dependency: Assess and determine that with the slowest flow-member of the group if such a flow is even feasible.
- Membership Dependency (spatial): The mechanisms to establish and determine membership and establish relationship is needed. Corelating to publisher (conductor and stage) and subscriber (performers) group communication model, not all subscribers need to know about each other.
- Start Time Dependency (temporal): Each performer depends on the trigger to start from there on time-scale, tempo and beats of the performance must be preserved.

From a logical architectural point of view, coordination node is a function that synchronizes all the incoming streams, it  may then either deliver all the streams or as a single stream.

# Generlized Coordinated Service Concept

There are several examples of multi-party immersive applications (TBD - add section) in which remote entities will be required to recreate the behavior of being present in the same scene or environment. Therefore, they are co-dependent on each other's spatio-temporal behavior changes. For example, in an orchestra tempo or beats and gestures must remain the same for all performers and position of a musician is computed to create spatial audio. 

A generalized in-network capability is introduced that consumes group communication membership and constraints and delivers service with in the specified constriaints. 

Keeping in the network context, important terms and components of coordinated service are introduced as below:

~~~~~
                                     .-.                       
                                    (---)--->                  
                                     `-'---->                  
                              .---.           +-----+          
                             (     )----------|Co-EP|          
                member        `---'           +-----+          
 +-----+      |  flow         Co-SN                            
 |Co-EP|----+ |                 ^^                             
 +-----+    | |                .||.                             
            | |               ( ||)        .-.                  
            | v                `||'   ----(--)---->            
          .---.  -------->    .---.  -----`-'----->    +-----+ 
         (     )-------------(     )-------------------|Co-EP| 
          `---'   ------->    `---'                    +-----+ 
+-----+     |                 Co-SN                            
|Co-EP|-----+                                                  
+-----+ ------>   Co-EP: coordinated service end point         
                  Co-SN: coordinated service node              
                  Co-Flow: coordinated flow            
                  Member flow: member of a co-flow             
~~~~~

{:#coordnetwork title=Elements in Coordinated Service Network}

- Coordinated Service: A coordinated service provides guarantee of delivery of multiple flows in a dependent manner. It is a type of group communication service supported by the network in which each member of the group has a dependency on other group-members. A coordinated service should be able to coordinate delivery of co-flows over different categories of group communications.
- Coordinated Service end point (Co-EP): An endpoint in a group communication where in coordination of resources or constraints is required.
- Coordinated service  Points (Co-SN):  The mechanisms to support coordinated services in network requires new capabilities referred to as network coordination functions. A service node is a part of the network that understands and processes network specific functions of coordination.
- Co-dependent Flow(Co-Flow): A set of flows that have dependencies in relation to eachother.  Each flow in the co-dependent flow set is referred to as a member-flow. The co-flows may express different kinds of dependencies or relationships. It may be point to mltipoint, multipoint to point or multipoint to multipoint.         
- Member flow: A single point to point member flow of a co-flow.

Coordinated services are a form of group comminication with a clearly expressed dependencies. Possible approaches will figure out mechanisms to manage those dependencies. 

#  Virtual Orchestra Challenges

The internet is a spatial-temporal heterogeneous environment, yielding different content delivery behaviours in time and space. No two paths (or even different flows on the same path) can be assumed to have identical properties in terms of latency, jitter, and bandwidth. 

Currently, any effort to support virtual orchestra in the networks is not feasible.  When left up to applications on endpoints to manage flow dependencies, they cannot always guarantee absolute time constraints due to unpredictable changes in network conditions. Coordinated services can be used to minimize the complexity at the endpoints and to facilitate dependent delivery from the network.

## Out-of-band Coordination
  The role of out-of-band function is to achieve distribution of coflows in the network. The membership of co-dependent flows is conveyed from the end-points potentially when the flows are set up, so that the coarse-grained service (and service level objectives) can be enabled in the network. A distribution graph of coflows and associated dependency constraints may be constructed, and those nodes enhance their scheduling and forwarding by factoring in the timing information in the meta-data of packets.

## In-band Coordination 
Additionally, timing information (timestamp of transmission from sender and time window for delivery to receiver) may be conveyed as meta-data in packets transmitted from the senders. In-band signaling conveys intermediate coordination points about the dependencies and interrelationship. To formalize these mechanisms to carry them in data path.

This instruction is per packet to indicate a member of coflow and dependency in terms of parameters: marker, Ts, and δt.

## In-node Coordinated-forwarding
Actual coordination effort is done on the coordination points. The scheduling and forwarding engine should allow packets within sync markers to be sent as per remaining δt. It needs to compare the remaining coordination time and accordingly schedule or pace the packet forwarding.

# IANA Considerations

This document requires no actions from IANA.

# Security Considerations

This document introduces no new security issues.

--- back
