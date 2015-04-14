# libnetwork - networking for containers

[![Circle CI](https://circleci.com/gh/docker/libnetwork/tree/master.svg?style=svg)](https://circleci.com/gh/docker/libnetwork/tree/master) [![Coverage Status](https://coveralls.io/repos/docker/libnetwork/badge.svg)](https://coveralls.io/r/docker/libnetwork)

Libnetwork provides a native Go implementation for connecting containers

The goal of libnetwork is to deliver a robust Container Network Model that provides a consistent programming interface and the required network abstractions for applications.

**NOTE**: libnetwork project is under heavy development and is not ready for general use.

#### Current Status
Please watch this space for updates on the progress.

Currently libnetwork is nothing more than an attempt to modularize the Docker platform's networking subsystem by moving it into libnetwork as a library.
  
Please refer to the [roadmap](ROADMAP.md) for more information.

#### Using libnetwork

There are many networking solutions available to suit a broad range of use-cases. libnetwork uses a driver / plugin model to support all of these solutions while abstracting the complexity of the driver implementations by exposing a simple and consistent Network Model to users.

```go
 // Create a new controller instance
 controller := libnetwork.New()
 
 options := options.Generic{}
 // Create a network for containers to join.
 network, err := controller.NewNetwork("simplebridge", "network1", options)
 if err != nil {
    return
 }
 
 // For a new container: create a sandbox instance (providing a unique key).
 // For linux it is a filesystem path
 networkPath := "/var/lib/docker/.../4d23e"
 networkNamespace, err := sandbox.NewSandbox(networkPath)
 if err != nil {
    return
 }
 
 // For each new container: allocate IP and interfaces. The returned network
 // settings will be used for container infos (inspect and such), as well as
 // iptables rules for port publishing.
 _, sinfo, err := network.CreateEndpoint("Endpoint1", networkNamespace.Key(), "")
 if err != nil {
    return
 }
 
 // Add interfaces to the namespace.
 for _, iface := range sinfo.Interfaces {
     if err := networkNamespace.AddInterface(iface); err != nil {
     	    return
     }
 }
 
 // Set the gateway IP
 if err := networkNamespace.SetGateway(sinfo.Gateway); err != nil {
    return
 }
```

## Future
See the [roadmap](ROADMAP.md).

## Contributing

Want to hack on libnetwork? [Docker's contributions guidelines](https://github.com/docker/docker/blob/master/CONTRIBUTING.md) apply.

## Copyright and license
Code and documentation copyright 2015 Docker, inc. Code released under the Apache 2.0 license. Docs released under Creative commons.

