## Required Pre-installed Tools
* go 1.20.x (1.20.11 or higher)
* git
* operator-sdk 1.25.0 +
  NOTE: Follow the installation instructions [here](https://sdk.operatorframework.io/docs/installation/#install-from-github-release). Make sure that the download URL (specified by the `OPERATOR_SDK_DL_URL` environment variable) is set to the correct version.
* sed
* yamllint
* jq
* podman +
  NOTE: If you need to use docker, then run the make targets with this variable set: `IMAGE_BUILDER=docker`.
* opm v1.26.3 +
  NOTE: To download the Operator Registry tool use either https://github.com/operator-framework/operator-registry/releases or https://mirror.openshift.com/pub/openshift-v4/x86_64/clients/ocp/. The version should correspond with the OpenShift version you are running. To confirm that the Operator Registry tool is installed correctly: `$ opm version`