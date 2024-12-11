# Cheat sheet for commands

This section covers the most useful commands. However, the `ksctl` binary provides other commands than those that are listed here. To see all of them, run:
```shell
ksctl --help
```

The `ksctl --version` command shows the version of the binary (based on the commit hash)
```shell
ksctl --version
```

**NOTE**: Prerequisite: The `.ksctl.yaml` config file is needed to run user-management related `ksctl` commands. The default location is your home directory: `~/.ksctl.yaml`, but you can use the `--config` flag to specify a different path. It contains the configuration settings for the host and member clusters together with the granted token.

## Finding UserSignup name

When users sign up, a `UserSignup` resource is created on their behalf on the Host cluster. For most of the user-management operations, the name of the `UserSignup` resource is needed.
To see all `UserSignup` resource names run:

```shell
$ ksctl get usersignup -t host

NAME                 USERNAME     COMPLETE   REASON            COMPLIANTUSERNAME   EMAIL
...
2237e8be-f678d76ff   dummy-name   False      PendingApproval                       dummy@email.com
...
```

The first column is the name of the `UserSignup` resource.

To look up a UserSignup resource from the user's email address, run:
in Linux:
```shell
ksctl get -t host usersignups -l toolchain.dev.openshift.com/email-hash=`echo -n <email_address> | md5sum | cut -d ' ' -f 1`
```
in macOS:
```shell
ksctl get -t host usersignups -l toolchain.dev.openshift.com/email-hash=`echo -n <email_address> | md5`
```


## Approving a user

To approve a user, either use the user's email:
```shell
ksctl approve --email <user_email>
```

or [get the UserSignup name](#finding-usersignup-name), and then run:
```shell
ksctl approve --name <usersignup_name>
```

**WARNING**: By default, the `approve` command checks if the user has already initiated the phone verification process. To skip this check for the users or environments where the phone verification is not required, use the `--skip-phone-check` flag.

The command will print out additional information about the `UserSignup` resource to be approved, and it will also ask for a confirmation.


## Deactivating a user

To deprovision a user from the platform and keep the `UserSignup` resource there, use `deactivate` command. First [get the UserSignup name](#finding-usersignup-name), then run:

```shell
ksctl deactivate <usersignup_name>
```

The command will print out additional information about the `UserSignup` resource to be deactivated and it will also ask for a confirmation.


## Deleting a user

To completely remove a user from the platform including the `UserSignup` resource (for example, as part of a GDPR request), use the `gdpr-delete` command. First [get the UserSignup name](#finding-usersignup-name), then run:

```shell
ksctl gdpr-delete <usersignup_name>
```

The command will print out additional information about the `UserSignup` resource to be deleted and it will also ask for a confirmation.


## Banning a user

To ban a user which in turn de-provisions the account and doesn't allow the user to sign up again, use the `ban` command. First [get the UserSignup name](#finding-usersignup-name), then run:

```shell
ksctl ban <usersignup_name> <ban_reason>
```

The command will print out additional information about the `UserSignup` resource to be banned, and it will also ask for a confirmation.

## Creating an Event

Social Events are a feature allowing users to sign up without having to go through the phone verification process. This is useful when running labs or workshops, as it lets attendees get up and run it quickly without having to fulfill all the requirements of the standard sign up process.

Social Events are temporary in nature; creating an event will produce a unique activation code that may be used for a predefined period of time, after which the code will no longer work.

Use the `create-event` command to create a new event, specifying a `description`, the `start-date` and `end-date` range and `max-attendees`.  The date range should encompass the dates of the event (it is recommended that the range actually be expanded to include the day before and after the event just to be safe), and the maximum attendees should also be slightly higher than the expected attendees in the rare case of technical difficulties or additional attendees.

Here's an actual example:

```shell
ksctl create-event --description="Summit Connect Dallas / SF" --start-date=2022-09-27 --end-date=2022-09-30 --max-attendees=70
```

The output from this command should look something like this:

```
Social Event successfully created. Activation code is 'bduut'
```

The activation code should be kept secret, and only provided to the event organizer.

## Admin usage

There is a provisioning flow for KubeSaw administrators separate from what the standard KubeSaw users use when they are signing up through the registration service.
There are two ways of granting permissions to the KubeSaw administrators, either via a ServiceAccount or via an OpenShift user.

### Admin manifests

The admin manifests are generated via `ksctl generate admin-manifests` command. The command generates manifests in a Kustomize folders, so it can be easily synced by another tool (eg. ArgoCD) to the cluster.
The content of the admin manifests is defined in `kubesaw-admins.yaml` file, which is used also as the source for `ksctl generate admin-manifests` command.
You can see an example of such a file in [kubesaw-admins.yaml](https://github.com/kubesaw/ksctl/blob/master/test-resources/dummy.openshiftapps.com/kubesaw-admins.yaml).

### Clusters

The required sections of the `kubesaw-admins.yaml` file is a `clusters` section defining location and names of the clusters used in the KubeSaw instance. This is necessary for running `ksctl generate cli-configs` command which adds the information to all generated `ksctl.yaml` files.

```yaml
clusters:
  host:
    api: https://api.dummy-host.openshiftapps.com:6443
  members:
  - api: https://api.dummy-m1.openshiftapps.com:6443
    name: member-1
  - api: https://api.dummy-m2.openshiftapps.com:6443
    name: member-2
```

### Add ServiceAccount for cli usage

The `serviceAccounts` section contains definition of ServiceAccounts together with the granted permissions.
To add a new SA that is supposed to be used in a combination with cli commands, add the following code:

```yaml
serviceAccounts:
- name: <your-name>
  host:
    roleBindings:
    - namespace: toolchain-host-operator
      roles:
      - <roles-or-commands-to-be-granted>
    clusterRoleBindings:
      clusterRoles:
      - ...

  member:
    roleBindings:
    - namespace: toolchain-member-operator
      roles:
      - <roles-or-commands-to-be-granted>
    clusterRoleBindings:
      clusterRoles:
      - ...
```

#### ServiceAccount namespace location

By default, all `ServiceAccounts` are created in default namespaces:

* `kubesaw-admins-host` for the host cluster
* `kubesaw-admins-meber` for the member cluster

The default location can be changed in `kubesaw-admin.yaml` file:

```yaml
defaultServiceAccountsNamespace:
  host: your-host-namespace
  member: your-member-namespace
```

These two namespaces have to be named differently.

It's also possible to override the namespace location for a given `ServiceAccount`:

```yaml
serviceAccounts:
- name: in-namespace-sa
  namespace: specific-sa-namespace
  host:
    ...
  member:
    ...
```

#### Generate ksctl.yaml files

For each ServiceAccount defined in this section, the `ksctl generate cli-configs` generates a separate `ksctl.yaml` file with the corresponding cluster configuration and tokens. As an administrator of the clusters, run this command and distribute securely the generated `ksctl.yaml` files to other team members.

##### Testing the `ksctl generate cli-configs` command locally
1. Run `make install`
2. Create `kubesaw-admins.yaml` (as an example, check [kubesaw-admins.yaml](https://github.com/kubesaw/ksctl/blob/master/test-resources/dummy.openshiftapps.com/kubesaw-admins.yaml).)
3. Run `ksctl generate admin-manifests --kubesaw-admins <path>/kubesaw-admins.yaml --out-dir <admin-manifests-out-dir-path>`
4. Create resources from the `<admin-manifests-out-dir-path>` of the previous command. Please, note that you will need to create some namespaces manually (`oc create ns <namespace-name>`), such as `host-sre-namespace`, `first-component`, `second-component`, `some-component`, `member-sre-namespace`, and `crw`, for example.
- Run `oc apply -k <admin-manifests-out-dir-path>/host`
- Run `oc apply -k <admin-manifests-out-dir-path>/member`
- Run `oc apply -k <admin-manifests-out-dir-path>/member-3`
5. Run `ksctl generate cli-configs -k <kubeconfig-path> -c <path>/kubesaw-admins.yaml`


### Users

The `ksctl` command can generate  The `users` section contains definition for users, identities, and the permissions granted to them.
KubeSaw uses a suffix `-crtadmin` for the admin usernames which are blocked from signing-up as a regular users via registration service. This ensures that provisioning admin users is fully isolated from the process of the regular ones.
To add a -crtadmin user for a particular component in member cluster, update the corresponding `kubesaw-admins.yaml` file by adding the following code under the `users` section:

For an admin of the component that needs to manually approve operator updates:
```yaml
users:
- name: <your-name>-maintainer
  id:
  - <sso-identities>
  member:
    roleBindings:
    - namespace: <namespace-name>
      roles:
      - view-secrets
      clusterRoles:
      - <edit/admin>
      - some-extra-permissions
    clusterRoleBindings:
      clusterRoles:
      - some-extra-cluster-scope-permissions
```
**NOTE**: The creation of the ClusterRoles is not managed via ksctl, you need to make sure that they are created in the cluster.

For a maintainer of the component with limited permissions:
```yaml
- name: <your-name>-maintainer
  id:
  - <sso-identities>
  member:
    roleBindings:
    - namespace: <namespace-name>
      clusterRoles:
      - <edit/view>
```

If you need any permissions also in a namespace in host cluster (to be used mainly by KubeSaw maintainers), then include the host section in the user's definition as well:
```yaml
- name: <your-name>-maintainer
  id:
  - <sso-identities>
  host:
    roleBindings:
    - namespace: <namespace-name>
    ...
  member:
    roleBindings:
    - namespace: <namespace-name>
    ...
```
