# Evaluating the Design and Implementation of a Microservice

_[Fabrizio Montesi](http://www.fabriziomontesi.com/), University of Southern Denmark. If you cite content from this page, do not copy it but include a link to this page._

This is a small, and very preliminary, guide for evaluating the design of microservices in a Microservice Architecture (MSA). Some may call it a cheatsheet, or a checklist, for people who are already familiar with some of the concepts in here. If you have comments, please feel free to [contact the author](http://www.fabriziomontesi.com/). For example, I intentionally left out testing and verification, as these are more "high-level" aspects that can be built on top of the ones discussed below.

## Some key properties

We discuss a (non-exhaustive) list of some key properties that should be considered in the design of microservices. We will summarise these observations as a rubric in the next section.

### Composition

It should be possible to build new microservices by composing the operations of existing ones.
Each microservice should therefore provide well-defined interfaces for the operations it offers to the network. Preferably, compatibility of service interfaces should be machine checkable.
Another important aspect is whether service interfaces are [cohesive](http://en.wikipedia.org/wiki/Cohesion_(computer_science)), and whether it is easy to use them from different technologies (e.g., different programming languages and framework).

### Efficiency

A microservice should not waste resources, just as with any other software. Here we are particularly interested in the following three resources.
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

A microservice should be resilient to failures. Failures fall in different categories, for example:
* Service failures: other services in the system fail to provide some functionality our service depends on.
* Low-level failures: some endpoints become unreachable, application crash, sudden machine reboot, etc.

With respect to service failures, an important feature is their detection and correct propagation among the services in the MSA. These "high-level" errors are usually application level errors that should be dealt with in the application logic.

With respect to low-level failures, we are interested in seeing whether the service can go back to its normal execution once it becomes possible for it to operate again (e.g., reboot completes). An important decision is therefore about what data should persist throughout low-level failures, e.g., with the assistance of databases or filesystems.

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
	<td><ul><li>Handles service failures and does not lose state due to low-level failures.</li>
	</ul></td>
	<td><ul><li>Handles service failures but loses some state between low-level failures.</li>
	</ul></td>
	<td><ul><li>Handles only service failures.</li>
	</ul></td>
	<td><ul><li>Does not handle failures.</li>
	</ul></td>
</tr>
</table>


## Tensions / Synergies

Many aspects have tensions and synergies with other aspects, making the design of microservices nontrivial and exposing the need for customisation. Developers should keep them in mind when evaluating a design against requirements, and think about which compromises best fit the project at hand. We give a summary below (click on the star next to each item to get to the explanation).

| Property / Property | Composition | Efficiency | Elasticity | Monitoring | Resilience |
| --- | :---: | :---: | :---: | :---: | :---: |
| **Composition** | - | _Tension_ | _Synergy_ | _Synergy_ | - |
| **Efficiency** | _Tension_ | - | - | _Tension_ | _Tension_ |
| **Elasticity** | _Synergy_ | - | - | _Synergy_ | _Tension and Synergy_ |
| **Monitoring** | _Synergy_ | _Tension_ | _Synergy_ | - | _Synergy_ |
| **Resilience** | - | _Tension_ | _Synergy_ | _Synergy_ | - |

### Explanation

We give a brief explanation for each tension and synergy.

* Tension between **Composition** and **Efficiency**. The more granular an MSA is, the more messages may be required to perform tasks.
* Synergy between **Composition** and **Elasticity**. Replication and load balancing are easier to implement if the services are easily composable. Also, composition benefits from the mechanisms that usually come with elasticity, e.g., service registries.
* Synergy between **Composition** and **Monitoring**. Having a granular MSA helps monitoring, because the execution of tasks can be tracked more precisely as each task is implemented via observable message exchanges.
* Tension between **Efficiency** and **Monitoring**. Adding monitoring usually introduces a performance overhead, internally and/or in the network.
* Tension between **Efficiency** and **Resilience**. Making a service resilient usually entails using filesystems or databases instead of volatile memory, which is typically faster.
* Synergy between **Elasticity** and **Monitoring**. Elasticity and Monitoring usually go hand in hand. Monitoring of load and/or performance can help in determining when a service needs to be scaled up or down. Elasticity can also help in mitigating the overhead that monitoring introduces on efficiency, by distributing it over multiple nodes.
* Tension and Synergy between **Elasticity** and **Resilience**.
The tension comes from the persistent data that a resilient service usually has to store, which can make the service harder to replicate in an elastic environment (sometimes it is incorrect to replicate the data, too, which must be dealt with separately, increasing complexity). Observe that services with stateless interfaces often still have persistent data behind them.
The synergy arises from the fact that elasticity can help in making the service more resilient, since if one instance of the service goes down a client may be redirected to another one.
* Synergy between **Monitoring** and **Resilience**. Monitoring can be critical in reacting to some problems, such as excessive load, helping resilience. In some cases, this may justify the introduction of active monitoring, i.e., a monitoring infrastructure that can then apply changes to the behaviour of the MSA depending on the events it registers.


## Microservice aspects in [the Jolie programming language](http://www.jolie-lang.org/)

_It would be nice to have more information about other languages and frameworks here, so contributions are welcome!_

Composition can be easily achieved, since in Jolie all components are microservices and all collaborations among microservices are achieved through [communication ports](http://docs.jolie-lang.org/#!documentation/basics/communication_ports.html), which come with machine-checkable interfaces and integration facilities.

For efficiency, see the [FAQ](http://jolie-lang.org/faq.html). Jolie is optimised for the network but not for the efficiency of internal computation. Coordination is thus handled well using Jolie, and performance-critical components that require lower-level languages should be composed as sub-services (there is native support for Java and Javascript, and support for C and other languages through Java).

For elasticity, it helps that changing the deployment of Jolie services does not influence their behaviour definition (the "code" running inside of a service, or its implementation). Handling the elasticity of persistent data is still left as a manual task to the programmer (assuming the use of databases that can handle replication).

All Jolie services are optionally monitorable with a native and customisable infrastructure (monitors can be either internal or external services, see [here](http://docs.jolie-lang.org/#!documentation/jsl/Runtime.html), operation setMonitor).

For resilience, Jolie comes with [advanced fault handling](http://docs.jolie-lang.org/#!documentation/fault_handling/termination_and_compensation.html) features for handling service failures. Low-level failures are still left entirely to the programmer.
