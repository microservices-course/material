# Evaluating the Design and Implementation of a Microservice Architecture (MSA)

_[Fabrizio Montesi](http://www.fabriziomontesi.com/), University of Southern Denmark_

## Some key properties

### Composition

It should be possible to build new microservices by composing the operations of existing ones.
Each microservice should therefore provide well-defined interfaces for the operations it offers to the network. Preferably, compatibility of service interfaces should be machine checkable.
Another important aspect is whether service interfaces are [cohesive](http://en.wikipedia.org/wiki/Cohesion_(computer_science)), and whether it is easy to use them from different technologies (e.g., different programming languages and framework).

### Efficiency

A microservice should not waste resources. Here we are particularly interested in the following three resources.
* Network: how many messages are used to implement a functionality, and how big is their respective payload.
* Memory: how much memory does the service use for its internal computation.
* Time: how much time does it take to complete internal computations.

Efficiency can be hard to evaluate and requires careful consideration about the concrete scenario we are dealing with.
Resources which are given up in order to gain productivity or compatibility (e.g., by using HTTP in a firewall-controlled network) are not considered wasted, if they do not go against any of the project requirements.

### Elasticity

Given a microservice, it should be possible to run multiple instances of it according to its load, independently of the other services in the architecture. The idea is then to balance the load coming from the network among the different instances.
A system administrator should be able to adjust the resources given to a microservice, preferably without any down time.

### Monitoring

The communications happening inside of an MSA should be observable. This can be used for several purposes, for example:
* debugging the communication flows among the services in the MSA;
* providing data for statistics on the usage of the MSA;
* recording a history of the transactions made in the system.

### Resilience

A microservice should be resilient to failures. Failures fall in different categories, for examples the ones in the following.
* Service failures: other services in the system fail to provide some functionality our service depends on.
* Network failures: some endpoints become unreachable, sudden disconnection.
* Low-level failures: unexpected machine reboots, crash of an underlying middleware, etc.

## Rubric for the Evaluation of Microservice Design

<table>
<tr>
<th>Property / Score</th><th>Exemplary</th><th>Good</th><th>Poor</th><th>Fail</th>
</tr>
<tr>
	<td><strong>Composition</strong></td>
	<td><ul><li>Formally-defined interfaces.</li>
	<li>Interfaces are cohesive and can be integrated with different technologies.</li>
	</ul></td>
	<td><ul><li>Formally-defined interfaces.</li>
	<li>Interfaces are not cohesive, or support only one client technology.</li>
	</ul></td>
	<td><ul><li>Interfaces are only informally documented.</li>
	<li>Interfaces are not cohesive nor support different client technologies.</li>
	</ul></td>
	<td><ul><li>Interfaces are not specified.</li>
	</ul></td>
</tr>
<tr>
	<td><strong>Efficiency</strong></td>
	<td><ul><li>Wastes little to no resources: time, memory, network.</li>
	</ul></td>
	<td><ul><li>Wastes some time and/or memory, but no network.</li>
	</ul></td>
	<td><ul><li>Barely respects the requirements; wastes time, memory, and network.</li>
	</ul></td>
	<td><ul><li>Does not meet the non-functional requirements.</li>
	</ul></td>
</tr>
<tr>
	<td><strong>Elasticity</strong></td>
	<td><ul><li>Can be replicated without any manual intervention.</li>
	</ul></td>
	<td><ul><li>Can be replicated but requires some re-configuration of its parameters.</li>
	</ul></td>
	<td><ul><li>Can be replicated but requires intervention on its implementation.</li>
	</ul></td>
	<td><ul><li>Cannot be replicated.</li>
	</ul></td>
</tr>
<tr>
	<td><strong>Monitoring</strong></td>
	<td><ul><li>All messages and their entire payloads can be monitored.</li>
	</ul></td>
	<td><ul><li>Messages can be monitored, but without their payloads.</li>
	</ul></td>
	<td><ul><li>Only some messages can be monitored.</li>
	</ul></td>
	<td><ul><li>Messages cannot be monitored.</li>
	</ul></td>
</tr>
<tr>
	<td><strong>Resilience</strong></td>
	<td><ul><li>Can detect and react to all failures.</li>
	</ul></td>
	<td><ul><li>Can react to service and network failures, but not low-level failures.</li>
	</ul></td>
	<td><ul><li>Can only react to service or network failures.</li>
	</ul></td>
	<td><ul><li>Cannot react to any failure.</li>
	</ul></td>
</tr>
</table>


## Tensions / Synergies

| Property / Property | Composition | Efficiency | Elasticity | Monitoring | Resilience |
| --- | :---: | :---: | :---: | :---: | :---: |
| **Composition** | - | _Tension_ | _Synergy_ | - | - |
| **Efficiency** | _Tension_ | - | - | _Tension_ | _Tension_ |
| **Elasticity** | _Synergy_ | - | - | _Synergy_ | _Synergy_ |
| **Monitoring** | _Synergy_ | _Tension_ | _Synergy_ | - | _Synergy_ |
| **Resilience** | - | _Tension_ | _Synergy_ | _Synergy_ | - |

<!--
### Explanation

**Composition**
 
* _Tension_ with **Efficiency**. The more granular an MSA is, the more messages are required to perform tasks.
* _Synergy_ with **Elasticity**. Replication and load balancing are easier to implement if the services are easily composable. Also, composition benefits from the mechanisms that usually come with elasticity, e.g., service registries. 

**Efficiency**
  -->
