# Service Mesh

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
was to implement it as a set of proxies. The idea here is that a service won’t connect directly to its downstream dependencies,
but instead all of the traffic will go through a small piece of software that transparently adds the desired features.

![](https://philcalcado.com/img/service-mesh/6-a.png)
