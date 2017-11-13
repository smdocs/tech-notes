# Distributed Systems A few questions to consider

These are a set of common questions which arise when designing systems in a distributed environment. This is not a complete list by any 
means, but they may be a useful set of prompts for issues to consider.

### Fault tolerance
  - What happens when a dependency starts failing? What if it begins failing slowly?
  - How can the system degrade in a graceful manner?
  - How does the system react to overload?
  - Is it well conditioned?
  - What's the worst-case scenario for total failure?
  - How quickly can the system recover?
  - Is delayable work delayed?
  - Is the system as simple as possible?
  - How can the system shed load?
  - Which failures can be mitigated, and how?
  - Which operations may be retried? Are they?

### Scalability
  - How does the system grow? What is the chief metric with which the system scales?
  - How does the system scale to multiple datacenters?
  - How does demand vary? How do you ensure the system is always able to handle peak loads?
  - How much query processing is done? Is it possible to shape data into queries?
  - Is the system replicated?

### Operability
  - How can features be turned on or off?
  - How do you monitor the system? How do you detect anomalies?
  - Does the system have operational needs specific to the application?
  - How do you deploy the system? How do you deploy in an emergency?
  - What are the capacity needs? How does the system grow?
  - How do you configure the system? How do you configure the system quickly?
  - Does the system behave in a predictable manner? Where are there nonlinearities in load or failure responses?
