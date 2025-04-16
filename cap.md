# CAP Theorem 


What is it: it's a theoretical model that describes behavior or distributed systems (such as databases) in case of network partitioning.

Terminology:

Distributed system: a collection of independent nodes connected by a communication network
Network partitioning: a subset of the nodes becomes unreachable from the other nodes

CAP Theorem operates with the following characteristics of a distributed system:

(C): Consistency: a distribute systems is consistent if any of the available nodes will respond with the most recent data commited to the system or an explicit error.

(A): Availability: any request received by an available node will receive a response

(P): Partition tolerance: a system remains operational despite an arbitrary number of messages between the nodes are wither dropped or delayed


CAP Theorem: formulation I

For any distributed system it is impossible to simultaneously guarantee all three properties

CAP Theorem: formulation II

In case of network partition, any distributes systems will have to choose at most two properties.

Interpretation: CAP Theorem forces us to make trade-offs. Namely, In case of network partitioning, a distributes system will fall on one of 3 states:

CA - consistent and available (this is a theoretical state and in practice it is impossible to implement)
CP - consistent and partition tolerant (but not available, response to an available node, may be delayed for a long time or dropped)
AP - available and partition tolerant (but not consistent). An operational node in an available system will respond with data but it may not be the most recent data committed to the system (Consistency is broken)






- CAP stands for Consistency, Availability, and Partitioning. CAP Theorem addresses trade-offs between these three properties in distributed systems (systems that are comprised of independent nodes that must agree on data or operations)

The CAP Principle states: any distributed system can have at most two out of 3 properties.

Strict definitions:

Consistency - every read must return the most recent write or an error
Availability - every request received by a non-failing node must return a response
Partition tolerance - the system continues to operate despite arbitrary number of messages being dropped or delayed by the network connecting notes

In the context of databases this theorems classifies distributed systems in 3 general classes

CA - consistent, available, but not partition tolerant (in case of network partitioning occurs, system as a whole may become unavailable)
CP - consistent, partition tolerant but not always available (in case of network failures, the system becomes unavailable - quorum among nodes must be established, since some nodes are not available, the system decided not to respond to data request)
AP - available and partition toleran systems, may return date that was not commited during most recent write 



RDBMS are typically built for consistency. This implies that they such systems can either be available to partition toleran
NoSQL systems are typically partition tolerant and available (PA) sacrificing consistency.

