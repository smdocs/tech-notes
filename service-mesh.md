# Service Mesh

> A service mesh is a dedicated infrastructure layer for handling service-to-service communication. It’s responsible for the reliable delivery of requests through the complex topology of services that comprise a modern, cloud native application. In practice, the service mesh is typically implemented as an array of lightweight network proxies that are deployed alongside application code, without the application needing to be aware.

### Traditional Service to Service Communications

![](https://philcalcado.com/img/service-mesh/4.png)

### General Requirements for Service based architecture
- Rapid provisioning of compute resources
- Basic monitoring
- Rapid deployment
- Easy to provision storage
- Easy access to the edge
- Authentication/Authorisation
- Standardised RPC
- Service discovery
- Circuit breakers

![](https://philcalcado.com/img/service-mesh/5.png)

The next logical step would be highly desirable to extract the features required by massively distributed services 
into an underlying platform. Built a platform that implements the common patterns such as service discovery, circuit breakers
required by massively distributed services.


Unfortunately, changing the networking stack to add this layer isn’t a feasible task. The solution found by many practitioners
was to implement it as a set of proxies. The idea here is that a service won’t connect directly to its downstream dependencies,but instead all of the traffic will go through a small piece of software that transparently adds the desired features.

E.g. Netflix OSS, Prana, Synapse and Nerve


We  have recently seen a new wave of proxies that are flexible enough to adapt to different infrastructure components and preferences. The first widely known system on this space was Linkerd, created by Buoyant based on their engineers’ prior work on Twitter’s microservices platform. Soon enough, the engineering team at Lyft announced Envoy which follows a similar principle.

![](https://philcalcado.com/img/service-mesh/6-a.png)

Probably the most powerful aspect of his definition is that it moves away from thinking of proxies as isolated components and acknowledges the network they form as something valuable in itself.

Finally, a service mesh, works exactlt as proxies that manage 

![](https://philcalcado.com/img/service-mesh/6-b.png)

A bird's eye view of ServiceMesh

![](https://philcalcado.com/img/service-mesh/mesh3.png)

