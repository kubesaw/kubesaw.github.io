# Welcome to KubeSaw
KubeSaw is a powerful solution designed to manage multi-tenant Kubernetes clusters, helping organizations minimize operational costs while maintaining high security standards. KubeSaw slices Kubernetes clusters into smaller, manageable units called Spaces, each composed of one or more Kubernetes Namespaces.
While KubeSaw can be described as a Namespace-as-a-Service (NaaS) solution, it provides much more. KubeSaw also supports multi-cluster environments, allowing organizations to manage namespaces and users within their cluster fleet. KubeSaw empowers teams to manage one or more Kubernetes clusters at scale, ensuring security, flexibility, and cost efficiency.

NOTE: KubeSaw supports only OpenShift clusters for now.

## NaaS via Spaces
An essential part of each multi-tenant solution running in a Kubernetes cluster is namespace-as-a-service (NaaS) functionality. KubeSaw orchestrates namespaces through small, manageable units called Spaces. Each Space consists of one or more namespaces (and optionally cluster-scoped resources) that are provisioned and managed through a set of customizable templates. All namespaces provisioned as part of one Space share the same lifecycle and behave as one entity.

## Users
KubeSaw has an abstraction for a user entity. These entities store user information taken from the company’s SSO and can be associated with Spaces. When there is a binding between a Space and user, then KubeSaw grants corresponding permissions to the user in the underlying namespace(s).
KubeSaw also provides and manages the full lifecycle of users and Spaces. Users can be in various states such as pending, approved, deactivated, or banned, with additional intermediate states.

NOTE: KubeSaw supports only OpenID Connect compatible SSOs (eg. Keycloak) for now.

## Tiers
A Tier is a set of templates and additional configuration which define how Spaces and the underlying namespaces are provisioned in the cluster.
The Tier templates can contain both namespace-scoped and cluster-scoped resources to be provisioned for each Space. By defining NetworkPolicies, limit ranges, quotas, and permissions, administrators can ensure that:
Each Space - and its underlying namespaces - are provisioned securely and in isolation.
Workloads are prevented from exceeding quotas, keeping infrastructure costs minimal.

## Multi-cluster
To ensure sufficient scalability options, KubeSaw was designed to support multi-cluster environments. When KubeSaw is installed in a multi-cluster environment, then there is one cluster which works as the main control plane (called “host”), and one or more clusters (called “members”) where the actual Spaces - and the underlying namespaces - are provisioned. The host cluster can also function as a member cluster.
It's common for teams to start with a single cluster. As its capacity is exceeded, additional member clusters can be added. This is true for KubeSaw environments, where the initial cluster can serve both as host and member, with more clusters added over time as needed.
Multi-cluster architecture also helps with managing different types of clusters with different API versions, infrastructure or installed tools or operators.

## Proxy
In multi-cluster environments, it can be challenging for users to remember which clusters their Spaces are provisioned in. Users need to keep track of URLs and manage kubeconfigs for every cluster they have access to. To simplify this, KubeSaw offers a multi-cluster proxy. Users interact with a single URL, and only the context of the Space changes. The proxy forwards requests to the correct clusters based on the provided context. Users also use the company’s SSO when authenticating against the proxy, without any need of getting the cluster-specific tokens.

## Ksctl
For easy management of Spaces and users in the KubeSaw instance, there is a ksctl command-line tool that simplifies operations to one single command execution. By running ksctl commands, the administrators can approve, deactivate, or ban users, or for example promote a Space from one tier to another or re-provision the Space to a different member cluster.
