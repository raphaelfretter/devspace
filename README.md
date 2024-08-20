# DevSpacesExample

## Devfile registry
[Devfile Registry](https://registry.devfile.io/viewer) - List of `devfile.yaml` that are tailored to specific images (java, python, universal, etc.)

## Access
### Git
If the `devfile.yaml` is hosted on a private repo, ensure the Personal Access Token(PAT) is added to DevSpaces: https://YOURDEVSPACESURL/dashboard/#/user-preferences?tab=PersonalAccessTokens

### Image Registry
```
If the image within the devfile.yaml is hosted on a private registry, ensure you setup the authentication:

Option 1 - via DevSpaces UI:

A) Go to below URL, make sure to replace YOURDEVSPACESURL with yours:
https://YOURDEVSPACESURL/dashboard/#/user-preferences?tab=ContainerRegistries

   - This will create a secret in Kubernetes/OpenShift and mount it to the DevSpace/Container.

B) Optional - if you plan on using `podman` within the devspace/container, then include the path of the mounted secret:
   - name: REGISTRY_AUTH_FILE ## https://docs.podman.io/en/latest/markdown/podman-login.1.html#authfile-path
     value: /etc/secret/TheGeneratedSecretName/.dockerconfigjson

-----

Option 2 - via YAML:

A) Apply the "RegistryAuthTokenSecret.yaml" yaml. This will create a secret called `devworkspace-container-registry-dockercfg`

B) Update the auth token / json values.

B) If using `podman` within devspace/container, make sure the `devfile.yaml` is using the below value.
    - name: REGISTRY_AUTH_FILE ## https://docs.podman.io/en/latest/markdown/podman-login.1.html#authfile-path
      value: /etc/secret/devworkspace-container-registry-dockercfg/.dockerconfigjson


```