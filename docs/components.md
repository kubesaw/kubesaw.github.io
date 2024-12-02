# Components
This page provides a brief description of each KubeSaw component.

## Host Operator
The host-operator is _KubeSaw's control-plane_, is responsible for managing the users, spaces, notification and monitoring the kubesaw instance.
One of the resources managed by host operator is the UserSignup resource, which is the source of truth for all user accounts. All other user-related resources are created from the UserSignup.

At the moment, there can be only one host operator deployed per KubeSaw instance.

The source code is available here [host-operator](https://github.com/codeready-toolchain/host-operator)

## Member Operator
The member operator is _KubeSaw's data-plane_, is responsible for provisioning and managing the user namespaces and all the configuration inside those namespaces.

There can be multiple member-operator deployed per KubeSaw instance, ideally there would be a member operator per each member cluster.

The source code is available here [member-operator](https://github.com/codeready-toolchain/member-operator)

## Registration Service
The Registration Service is a standalone web application that runs on the host cluster.  It provides a number of REST endpoints that the signup page can use to allow a user to sign up for a KubeSaw account. 
The Registration Service application makes use of third party libraries such as Gin (an HTTP web framework) and Kubernetes client libraries (for interacting with the Kubernetes API) among others.

### Proxy
The Proxy is part of the registration-service application deployment, basically it's another set of REST endpoints implemented using the echo framework. It can be used in a multi-cluster environment to forward requests without the need to specify the cluster name, the proxy infers the target cluster from the user information and the target workspace/namespace of the request.

Both Proxy and Registration-service are optional components and not required for having a fully functional KubeSaw instance.
The registration service can be useful if your KubeSaw instance needs a signup mechanism/frontend.
The proxy can help in the case of a multi-cluster solution with removing some of the _cluster awareness_ and friction for the end users or clients interacting with the instance.

The source code for both registration-service and proxy is available here [registration-service](https://github.com/codeready-toolchain/registration-service)

## KSCTL
ksctl is a command-line tool that helps you with managing your instance of KubeSaw.

The source code is available here [ksctl](https://github.com/kubesaw/ksctl)