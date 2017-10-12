
### What/When/Why
Just like classes, components should have low coupling between them but be high cohesive within them. When components need to collaborate, 
let’s say a component “A” needs to trigger some logic in component “B”, the natural way to do it is simply to have component A call a 
method in an object of component B. However, if A knows about the existence of B, they are coupled, A depends on B, making the system more 
difficult to change and maintain. 

Events can be used to prevent coupling. Furthermore, as a side effect of using events and having decoupled components, if we have a team
working only on component B, it can change how component B reacts to logic in component A without even talking to the team responsible 
for component A. 

Components can evolve independently: our application becomes more organic.

Even within the same component, sometimes we have code that needs to be executed as a result of an action, but it doesn’t need to be 
executed immediately, in the same request/response. The most flagrant example is sending out an email. In this case, we can immediately 
return a response to the user and send out the email at a later time, in an async fashion and avoid having the user waiting for an email 
to be sent.

Nevertheless, there are dangers to it. If we use it indiscriminately, we run the risk of ending up with logic flows that are conceptually
highly cohesive but wired up together by events which are a decoupling mechanism. In other words, code that should be together will be 
separated and will be difficult to track its flow (kind of like the goto statement), to understand it and reason about it: It will be 
spaghetti code!

To prevent turning our codebase into a big pile of spaghetti code, we should keep the usage of events limited to clearly identified 
situations. In my experience, there are three cases in which to use events:

- To decouple components
- To perform async tasks
- To keep track of state changes (audit log)

### 1. To decouple components

When component A performs the logic that needs to trigger the component B logic, instead of calling it directly, we can trigger an event
sending into an event dispatcher. Component B will be listening to that specific event in the dispatcher and will act whenever the event
occurs.

This means that both A and B will be depending on the dispatcher and the event, but they will have no knowledge of each other: they will
be decoupled.

Ideally, both the dispatcher and the event should live in neither component:

The dispatcher should be a library completely independent of our application and therefore installed in a generic location using a dependency
management system. In PHP world, this is something installed in the vendor folder, using Composer.
The event, nevertheless, is part of our application but should live outside both components as to keep them unaware of each other. The 
event is shared between the components and it is part of the core of the application. Events are part of what DDD calls the Shared 
Kernel. This way, both components will depend on the Shared Kernel but will remain unaware of each other.

Nevertheless, in a Monolithic application, for convenience, it is acceptable to place it in the component that triggers the event.
Shared Kernel

>> Designate with an explicit boundary some subset of the domain model that the teams agree to share. Keep this kernel small. This 
explicitly shared stuff has special status, and shouldn’t be changed without consultation with the other team.
Eric Evans 2014, Domain-Driven Design Reference

[](https://herbertograca.com/2017/10/05/event-driven-architecture/#more-10802)
