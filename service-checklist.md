# Internet Scale Services Checklist

A checklist for designing and developing internet scale services, inspired by James Hamilton's 2007 paper "On Desgining and Deploying Internet-Scale Services." 

* http://mvdirona.com/jrh/talksandpapers/jamesrh_lisa.pdf

## Basic tenets

- [ ] Does the design expect failures to happen regularly and handle them gracefully?
- [ ] Have we kept things as simple as possible?
- [ ] Have we automated everything?

## Overall Application Design & Development

- [ ] Can the service survive failure without human administrative interaction? 
- [ ] Are failure paths frequently tested?
- [ ] Have we documented all conceivable component failure modes and combinations thereof? 
- [ ] Does our design tolerate these failure modes? And if not, have we undertaken a risk assessment to determine the risk is acceptable?
- [ ] Are we targeting commodity hardware? (That is, our design does not require special h/w)
- [ ] Are we hosting all users on a single version of the software? 
- [ ] Can we support multi-tenancy without physical isolation?
- [ ] Have we implemented (and automated) a quick service health check? 
- [ ] Do our developers work in the full environment? (Requires single server deployment)
- [ ] Can we continue to operate in reduced capacity if services (components) you depend on fail?
- [ ] Does our design eliminate code redundancy across services/components?
- [ ] Can our pods/clusters of services continue to operate independently of each other?
- [ ] For rare emergency human intervention, have we worked with operations to come up with recovery plans,and documented, scripted, and tested them?
- [ ] Does each of our complexity adding optimizations (if any), give at least an order of magnitude improvement?
- [ ] Have we enforced admission control at all levels?
- [ ] Can we partition the service, and is that partitioning infinitely adjustable and fine-grained?
- [ ] Have we understood the network design and reviewed it with networking specialists?
- [ ] Have we analysed throughput and latency and determined the most important metric for capacity planning?
- [ ] Are all of our operations utilities following the same code review, source code control, testing etc. as the rest of the code base?
- [ ] Have we understood the load this service will put on any backend store / services? Have we measured and validated this load?
- [ ] Is everything versioned? The goal is to run single-version software, but multiple versions will always exist during rollout and testing etc. Versions n and n+1 of all components need to peacefully co-exist.
- [ ] Have we avoided single points of failure?

## Automatic Management and Provisioning

- [ ] Are all of our operations restartable?
- [ ] Is all persistent state stored redundantly?
- [ ] Can we support geo-distribution / multiple data center deployments
- [ ] Have we automated provisioning and installation?
- [ ] Are configuration and code delivered by development in a single unit?
- [ ] Is the unit created by development used all the way through the lifecycle (test and prod. deployment)?
- [ ] Is there an audit log mechanism to capture all changes made in production?
- [ ] Have we designed for roles rather than servers, with the ability to deploy each 'role' on as many or few servers as needed?
- [ ] Are we handling failures and correcting errors at the service level? 
- [ ] Have we eliminated any dependency on local storage for non-recoverable information?
- [ ] Is our deployment model as simple as it can possibly be? (Hard to beat file copy!)
- [ ] Are we using a chaos monkey in production?

## Dependency Management

(How to handle dependencies on other services / components).

- [ ] Can we tolerate highly variable latency in service calls? Do we have timeout mechanisms in place and can we retry interactions after a timeout (idempotency)?
- [ ] Are all retries reported, and have we bounded the number of retries?
- [ ] Do we have circuit breakers in place to prevent cascading failures? Do they 'fail fast'? 
- [ ] Are we depending upon shipping and proven components wherever possible?
- [ ] Have we implemented inter-service monitoring and alerting?
- [ ] Do the services we depend on have the same (or compatible) design points (e.g SLAs)?
- [ ] Can we continue operation (perhaps in a degraded mode) if a component or service we depend on fails?

## Release Cycle and Testing

- [ ] Are we shipping often enough?
- [ ] Have we defined specific criteria around the intended user experience? Are we continuously monitoring it?
- [ ] Are we collecting the actual numbers rather than just summary reports? Raw data will always be needed for diagnosis.
- [ ] Have we minimized false-positives in the alerting system?
- [ ] Are we analyzing trends on key metrics?
- [ ] Is the system health highly visible at all times?
- [ ] Is the system continuously monitored?
- [ ] Can we support version roll-back? Is this tested and proven?
- [ ] Do we support both forward and backward compatibility on every change?
- [ ] Can we deploy on a single server to support dev and test?
- [ ] Have we run stress tests?
- [ ] Do we have a process in place to catch performance and capacity degradations in new releases?
- [ ] Are we running tests using real data?
- [ ] Do we have (and run) system-level acceptance tests?
- [ ] Do we have an environment that lets us test at scale, with the same data collection and mining techniques using in production?

## Hardware Selection and Standardization 

(I deviate from the Hamilton paper here, on the assumption that you'll use at least an IaaS layer).

- [ ] Do we depend only on standard IaaS compute, storage, and network facilities?
- [ ] Have we avoided dependencies on specific hardware features?
- [ ] Have we abstracted the network and naming? (For service discovery)

## Operations and Capacity Planning

- [ ] Is there a devops team that takes shared responsibility for both developing and operating the service?
- [ ] Do we always do soft deletes so that we can recover accidentally deleted data?
- [ ] Are we tracking resource allocation for every service to understand the correlation between service metrics and underlying infrastructure requirements?
- [ ] Do we have a discipline of only making one change at a time?
- [ ] Is everything that might need to be configured or tuned in production able to be changed without a code change?

## Auditing, Monitoring, and Alerting

- [ ] Are we tracking the alerts:trouble-ticket ratio (goal is near 1:1)?
- [ ] Are we tracking the number of systems health issues that don't have corresponding alerts? (goal is near zero)
- [ ] Have we instrumented every customer interaction that flows through the system? Are we reporting anomalies?
- [ ] Do we have sufficient data to understand the normal operating behaviour?
- [ ] Do we have automated testing that takes a customer view of the service? 
- [ ] Do we have sufficient instrumentation to detect latency issues?
- [ ] Do we have performance counters for all operations? (at least latency and number ops/sec data)
- [ ] Is every operation audited?
- [ ] Do we have individual accounts for everyone who interacts with the system?
- [ ] Are we tracking all fault-tolerant mechanisms to expose failures they may be hiding?
- [ ] Do we have per-entity / entity-specific audit logs?
- [ ] Do we have sufficient assertions in the code base?
- [ ] Are we keeping historical performance and log data?
- [ ] Is logging configurable without needing to redeploy?
- [ ] Are we exposing suitable health information for monitoring?
- [ ] Is every error that we report actionable?
- [ ] Do our problem reports contain enough information to diagnose the problem?
- [ ] Can we snapshot system state for debugging outside of production?
- [ ] Are we recording all significant system actions? Both commands sent by users, and what the system internally does.

## Graceful Degradation and Admission Control

- [ ] Do we have a 'big red switch' mechanism to keep vital processing online while shedding or delaying non-critical workload?
- [ ] Have we implemented admission control?
- [ ] Can we meter admission to slowly bring a system back up after a failure?

## Customer and Press Communication Plan

- [ ] Do we have a communications plan in place for issues such as wide-scale system unavailability, data loss or corruption, security breaches, privacy violations etc..?

## Customer Self-Provisioning and Self-Help

- [ ] Can customers self-provision and self-help?
