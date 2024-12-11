# How to configure

## Create toolchainconfiguration

Api definition: [toolchainconfig api](https://github.com/codeready-toolchain/api/blob/master/api/v1alpha1/docs/apiref.adoc#toolchainconfig)

In the host cluster create the `toolchainconfiguration` in `toolchain-host-operator` namespace:


```yaml
apiVersion: toolchain.dev.openshift.com/v1alpha1
kind: ToolchainConfig
metadata:
  name: config
  namespace: toolchain-host-operator
spec:
  host:
    tiers:
      defaultSpaceTier: 'base'
    automaticApproval:
      enabled: true
    notifications:
      adminEmail: <admin-email-list>@domain.com
      secret:
        mailgunAPIKey: mailgun.api.key
        mailgunDomain: mailgun.domain
        mailgunReplyToEmail: mailgun.replyto.email
        mailgunSenderEmail: mailgun.sender.email
        ref: host-operator-secret
      templateSetName: 'templateSet name'
    registrationService:
      auth:
        authClientConfigRaw: '{
                  "realm": "realm-name",
                  "auth-server-url": "https://sso.domain.com/auth",
                  "ssl-required": "ALL",
                  "resource": "sso-resource-name",
                  "clientId": "sso-client-id",
                  "public-client": true
                }'
        authClientLibraryURL: https://sso.domain.com/auth/js/keycloak.js
        authClientPublicKeysURL: https://sso.domain.com/auth/realms/realm-name/protocol/openid-connect/certs
        ssoBaseURL: https://sso.domain.com
        ssoRealm: realm-name
      environment: prod
      replicas: 5
      registrationServiceURL: https://<my-domain>
  members:
    default:
      auth:
        idp: IdpName
      autoscaler:
        bufferMemory: 3Gi
        bufferReplicas: 10
        deploy: true
```

## Members Configuration
Api definition: [member operator config](https://github.com/codeready-toolchain/api/blob/master/api/v1alpha1/docs/apiref.adoc#memberoperatorconfig)


The `ToolchainConfig.Spec.Members` section is automatically propagated by the host operator to all member clusters. The host-operator will create a `MemberOperatorConfig` CR with the configuration specified in that section.

You can also provide specific configuration per member cluster by configuring the `specificPerMemberCluster` field :
```yaml
apiVersion: toolchain.dev.openshift.com/v1alpha1
kind: ToolchainConfig
metadata:
  name: config
  namespace: toolchain-host-operator
spec:
   ….
   ….
  members:
    default:
      auth:
        idp: DevSandbox
      autoscaler: 
        bufferMemory: 3Gi
        bufferReplicas: 10
        deploy: true
    specificPerMemberCluster:
     <memberClusterName>:
       webhook:
         deploy: false
```

In the previous example, we are disabling the deployment of the webhook for a specific member cluster. The `specificPerMemberCluster` configuration will override the default configuration section for that member cluster.

## Create the SpaceProvisionerConfig
Api definition: [space provisioner config](https://github.com/codeready-toolchain/api/blob/master/api/v1alpha1/docs/apiref.adoc#spaceprovisionerconfig)

Create the `SpaceProvisionerConfig` in the `toolchain-host-operator` namespace which opens a member cluster for user/workload onboarding, there should be one `SpaceProvisionerConfig` for each registered member cluster:

```yaml
apiVersion: toolchain.dev.openshift.com/v1alpha1
kind: SpaceProvisionerConfig
metadata:
  name: <name>
  namespace: toolchain-host-operator
spec:
  toolchainCluster: <name of the toolchaincluster CR in toolchain-host-operator namespace>
  enabled: true
  capacityThresholds:
    maxNumberOfSpaces: 1000
    maxMemoryUtilizationPercent: 90
  placementRoles:
  - cluster-role.toolchain.dev.openshift.com/tenant
```

## Create secrets referenced in toolchainconfig
Create the `host-operator-secret` in the `toolchain-host-operator` namespace:

```yaml
kind: Secret
apiVersion: v1
metadata:
  name: host-operator-secret
  namespace: toolchain-host-operator
data:
  mailgun.api.key: <base64 api key>
  mailgun.domain: <base64 mailgun domain>
  mailgun.replyto.email: <base64 reply to email>
  mailgun.sender.email: <base64 sender email>
type: Opaque
```

Create the `member-operator-secret` in `toolchain-member-operator` namespace:
```yaml
kind: Secret
apiVersion: v1
metadata:
  name: member-operator-secret
  namespace: toolchain-member-operator
data:
  github.access.token: <base64 token>  
type: Opaque
```

### Secret loading

As you can notice, the name of the secret is referenced in the `toolchainconfig` CR ( see `ToolchainConfig.Spec.Host.Notifications.Secret.Ref` field ) and every field in the data section of the secret was referenced also in the `ToolchainConfig.Spec.Host.Notifications.Secret`, in this way the host-operator would know which secret and which fields to load the configuration from. Same thing for secrets referenced in the members section, which will be read by the member operator.

## Provisioning KubeSaw admins
You can set up admin access to the KubeSaw instances by leveraging the ksctl command line, please refer to the [Admin Usage section](ksctl-cheat-sheet.md#admin-usage). 
