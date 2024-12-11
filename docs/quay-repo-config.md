## Configure your Quay account for dev deployment

There is a set of images that are built and pushed to quay repositories while deploying local versions of KubeSaw
operators to OpenShift cluster. Please make sure that the repositories exist in your quay.io account.

### Repositories

1. Register for a quay.io account if you don't have one
2. Make sure you have set the _QUAY_NAMESPACE_ variable: `export QUAY_NAMESPACE=<quay-username>`
3. Log in to quay.io using +
   `docker login quay.io`
4. Make sure that these repositories exist on quay.io and the visibility is set to `public` for all of them:
    - `https://quay.io/repository/<quay-username>/host-operator`
    - `https://quay.io/repository/<quay-username>/host-operator-bundle`
    - `https://quay.io/repository/<quay-username>/host-operator-index`
    - `https://quay.io/repository/<quay-username>/member-operator`
    - `https://quay.io/repository/<quay-username>/member-operator-webhook`
    - `https://quay.io/repository/<quay-username>/member-operator-bundle`
    - `https://quay.io/repository/<quay-username>/member-operator-index`
    - `https://quay.io/repository/<quay-username>/registration-service`

### Public visibility

All aforementioned repositories have to be public, so make sure that the visibility is set to `public` for all of them:

* `https://quay.io/repository/<quay-username>/host-operator?tab=settings`
* `https://quay.io/repository/<quay-username>/host-operator-bundle?tab=settings`
* `https://quay.io/repository/<quay-username>/host-operator-index?tab=settings`
* `https://quay.io/repository/<quay-username>/member-operator?tab=settings`
* `https://quay.io/repository/<quay-username>/member-operator-webhook?tab=settings`
* `https://quay.io/repository/<quay-username>/member-operator-bundle?tab=settings`
* `https://quay.io/repository/<quay-username>/member-operator-index?tab=settings`
* `https://quay.io/repository/<quay-username>/registration-service?tab=settings`