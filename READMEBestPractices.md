## Best Practices

### Build small containers
* Using the default base images can lead to large images with full of security vulnerabilities
* Most docker images use Debian or Ubuntu as the base image, these base images can add hundreds of megabytes of additional
overhead to the container
* Methods to reduce image size
    * Small Base Images
        * Languages / stack that using provides an official image that's much smaller than the default image.
        * If language / stack does not have for a small image , build the container using raw Alpine Linux as a starting point
    * Builder Pattern
        * This pattern is useful static language that compile like Go and C++ or Typescript for Node.js
* Two areas where small container shine
    * Performance
        * The duration it takes to build a container, push it to a registry and then pull it down from the registry is very less when compared to larger images
    * Security
        * Smaller images are more secure because they have less surface area for attacks

### Internals of Container
* Use a non-root user inside the container
* Make the file system read-only
* One process per container
* Log everything to stdout and stderr

### Organising Kubernetes with Namespaces
* Namespaces help teams with organisation, security and performance
* In most kubernetes distributions, the cluster comes three namespaces, default, kube system, and kube public.
* Kube public really isn't used for much right now
* It's usually a good idea to leave kube system alone in any managed system
* This leaves default namespace as the place where your services and apps are created. It's great for getting started in smaller production systems, it's not recommended to use against larger production systems
* If multiple teams using the cluster(with default namespace), it's very easy for a team to accidentally override, or disrupt another team without even realising it. Instead, we should create multiple namespaces and use them to segment services into manageable chunks
* To manage namespaces, we can use kubens(https://kubectx.dev) cli tool , this will help to overcome of adding --namespace flag for every kubectl command
* __Cross Namespace Communication__
    * When one app wants to access a kubernetes service, we can use the built-in DNS service discovery (or)
    * If service with same name exists in multiple namespaces , it's easy to get around this by using the expanded form of the DNS address.
        * Services in kubernetes expose their endpoint using a common DNS pattern __Service Name.Namespace Name.svc.cluster.local__
        * Normally, with just service name, DNS will automatically resolve to the full address. However, if need to access a service in another namespace , just use the service name plus namespace name
    * If want to isolate and limit access to a namespace , Kubernetes lets us do this with network policies
* __Namespace Granularity__
    * The Small Team (5-10 micro-services) - it makes sense to launch all services into the default namespace
    * Rapidly growing team(s) (10 or more micro-services) - its good to have multiple namespaces for easier manageability
    * The Large Companies (everyone doesn't know everyone else)- Using a kubernetes aware CD system like Spinnaker is highly recommended. Here, each team must definitely needs their own namespace
    * The Large Enterprise Companies
        * There are groups that don't even know about the existence of other groups
        * Groups might as well be external companies and services are consumed through well-documented APIs
        * It probably makes sense to have multiple clusters to reduce the blast radius of poorly configured applications and to make billing and resource management easier
        * As the number of micro-services and teams increases, namespaces can really make Kubernetes a lot more manageable and give you increased control, security and flexibility
        
### Health Checks with Readiness and Liveness probes
* Health checks are a simple way to let the system know if an instance of app is working or not.
    * If instances of app is not working, then other services should not access it or send requests to it
    * Instead request to be sent to another instance of app that is ready or retired at a later time
    * System also bring the app back to a healthy state
* We can make our deployments more robust by creating custom health checks
* Types of Health Checks
    * Readiness probes
        * Designed to let Kubernetes know when the app is ready to serve traffic
        * Kubernetes will make sure the readiness probe passes before allowing the service to send traffic to the pod
        * If a readiness probe starts to fail, Kubernetes will stop sending traffic to the pod until it passes again
    * Liveness probes
        * Lets Kubernetes know if the app is alive or dead
        * If app is alive, Kubernetes will leave it alone
        * If app is dead then Kubernetes will remove the pod and start a new one to replace it
* Types of probes
    * HTTP
    * Command
    * TCP
* Configuring probes
    * initialDelaySeconds - use P99 startup time
    * periodSeconds
    * timeoutSeconds
    * successThreshold
    * failureThreshold
    
### Deployments
* Use the "record" option for easier rollbacks
* Use plenty of descriptive labels
* Use sidecars for proxies, watchers etc
* Don't use sidecars for bootstrapping!
* Don't use latest or no tag

### Setting Resource Request and Limits
* Requests and Limits are the mechanisms Kubernetes uses to control resources such as CPU and memory.
* Requests are what the container is guaranteed to get.
* Limits, on the other hand, make sure a container never goes above a value.
* CPU resources are defined in millicores
    * If container needs two full cores to run, put the value 2000m
    * If container only needs 1/4 of a core, put a value of 250m
    * Unless the app is specifically designed to take advantage of multiple cores like Scientific Computing, databases.
        * It's usually a best practice to keep the CPU request at one or below and then run more replicas to scale it out.
        * This gives the system more flexibility and more reliability
* CPU Limits
    * CPU is consider as a compressible resource
    * If app starts hitting CPU limits, Kubernetes will start to throttle your container.
        * This means CPU will be artificially restricted, giving the app potentially worse performance.
        * However, it won't be terminated or evicted
* Memory resources are defined in bytes, can give it anything from bytes to petabytes
* Unlike CPU resources, memory is not compressible because there's no way to throttle memory usage.
    * If a container goes pass its memory limit,it will be terminated
* It's important to remember that you cannot set requests that are larger than the resources provided by nodes.
* In an ideal world, the container settings would be good enough to take care of everything.
    * People can easily forget to set the resources, or a rogue team can set their requests and limits very high and take up more than their fair share of the cluster
    * To prevent these scenarios, we can setup at namespace level
        * ResourceQuota
        * LimitRange
    * Adding requests and limits to pods and namespaces only takes a little extra effort, and it can save you from running into many headaches down the line
    
### Terminating with Grace
* Kubernetes uses an invent loop(Observe - Difference - Act) to make sure that resources are healthy from containers to the nodes themselves.
* Kubernetes terminates pods for many reasons. For instance, if a resource has failed a health check, Kubernetes will just automatically spin up a replacement
* So, it's important that the application can handle termination gracefully so that there is a minimal impact on the end user and there's a minimal impact on the time to recovery
    * This means it should save all the data that needs to be saved, close down any network connections, finish any work that's left or any other similar tasks
    * In practice, the application needs to handle the sigterm message
* Once kubernetes decided to terminate pod, a series of events take place
    * Pod will stop getting the new traffic
    * Prestop hook is a special command or HTTP request that is sent to the containers in the pod
        * its a great way to trigger a graceful shutdown, without modifying the application
    * Send SIGTERM signal sent to the containers in the pod
        * code should listen for this event and then start shutting down cleanly at this point
            * This may include stopping any long-lived connections like a database connection or a WebSocket stream, saving the current state and doing the things like that
        * Even if use Prestop hook, it's important to test what happens to the application when it receives the sigterm signal
        * At this time, Kubernetes will wait for a specified time called "terminationGracePeriod"(defaults to 30 seconds)
        * Kubernetes will not wait for the prestop hook or the sigterm signal to finish
        * If app finishes shutting down before the termination grace period is done, Kubernetes will just move to the next step immediately
    * If containers are still running after the grace period, they are sent the SIGKILL signal and then forcibly removed
        * At this point, Kubernetes cleans up all the objects as well
        
### Mapping External Services
* Use DNS-based service discovery to map external services just like for native kubernetes services
* If external services are created in the same network, or VPC, as the Kubernetes cluster, it can be accessed using the high-performance internal IP address
    * Create Kubernetes service(of type - ClusterIP) and there will be no pod selectors for this service
    * Now create endpoints object that will receive traffic from the service (use the same name as the service)
* If external services are from a third party i.e with Domain Names
    * Create Kubernetes service(of type - ExternalName), this service will do a simple CNAME redirect at the kernel level
