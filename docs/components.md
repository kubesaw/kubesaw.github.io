# Components
This page provides a brief description of each KubeSaw component.

## Host Operator
The host-operator is _KubeSaw's control-plane_, which is responsible for managing the users, spaces, notifications and monitoring the KubeSaw instance.
One of the resources managed by host operator is the UserSignup resource, which is the source of truth for all user accounts. All the other user-related resources are created from the UserSignup.

The source code is available here [host-operator](https://github.com/codeready-toolchain/host-operator)

## Member Operator
The member operator is _KubeSaw's data-plane_, which is responsible for provisioning and managing the user namespaces and all the configurations inside those namespaces.

There can be multiple member operators deployed as part of the same KubeSaw instance. Member operators are usually deployed per-cluster (member cluster) but can even co-exist in the same cluster.

The source code is available here [member-operator](https://github.com/codeready-toolchain/member-operator)

## Registration Service
The Registration Service is a standalone web application that runs on the same cluster as the host operator
(host cluster).
It provides a number of REST endpoints that the signup page can use to allow a user to sign up for a KubeSaw account. 
The Registration Service application makes use of third party libraries such as Gin (an HTTP web framework)
and Kubernetes client libraries (for interacting with the Kubernetes API) among others.

### Proxy
The Proxy is part of the registration service application deployment.
It can be used in a multi-cluster environment to forward requests without the need to specify the cluster context,
the proxy infers the target cluster from the user information and the target workspace/namespace of the request.

Both Proxy and Registration-service are optional components and not required for having a fully functional KubeSaw instance.
The registration service can be useful if your KubeSaw service requires a signup mechanism/frontend.
The proxy can help, in the case of a multi-cluster solution, with removing some of the _cluster awareness_ and friction for the end users or clients interacting with the member clusters. Another key feature of the proxy is that it accepts SSO tokens, not kube tokens. This keeps authorization and authentication centralized, which can be managed from the identity provider (atm only Keycloak is supported).

The source code for both registration-service and proxy is available here [registration-service](https://github.com/codeready-toolchain/registration-service)

## KSCTL
ksctl is a command-line tool that helps you manage your KubeSaw instance. It provides commands for managing the clusters, spaces and users in KubeSaw.

The source code is available here [ksctl](https://github.com/kubesaw/ksctl)

See also the [ksctl cheat sheet](ksctl-cheat-sheet.md)
