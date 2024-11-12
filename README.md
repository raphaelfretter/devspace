# DevSpacesExample

- [DevSpacesExample](#devspacesexample)
  - [Devfile registry](#devfile-registry)
  - [Access](#access)
    - [Git](#git)
    - [Image Registry](#image-registry)
        - [Option 1 - via DevSpaces UI:](#option-1---via-devspaces-ui)
        - [Option 2 - via YAML:](#option-2---via-yaml)
  - [Usage](#usage)
    - [Build Images and Run Containers](#build-images-and-run-containers)


## Devfile registry
[Devfile Registry](https://registry.devfile.io/viewer) - List of `devfile.yaml` that are tailored to specific images (java, python, universal, etc.)

## Access

### Git
If the [devfile.yaml](devfile.yaml) is hosted on a private repo or if you need to clone some other git repo, ensure either:

  1. Personal Access Token(PAT) is added to DevSpaces: https://YOURDEVSPACESURL/dashboard/#/user-preferences?tab=PersonalAccessTokens
      - You can reference [git-credentials-secret.yaml](git-credentials-secret.yaml) if you want to do this via yaml. I recommend you do this via DevSpaces UI instead since it will auto merge it into the container's `/.git-credentials/credentials` file that will be picked up by the `git` cli per `/etc/gitconfig`.
      - Official steps: https://eclipse.dev/che/docs/stable/end-user-guide/using-a-git-provider-access-token/
      - If using GitHub, a Fine Grained Token for PAT is restricted to a specific Owner. Use Classis Token if you want access to repos that are owned by multiple owners/organizations. 
  2. OR an SSH Key is added to: https://YOURDEVSPACESURL/dashboard/#/user-preferences?tab=SshKeys


`/etc/gitconfig` will be mounted by the auto created configMap of the devworkspace-operator. You can modify the content of it if needed.


-----

### Image Registry

If the image within the devfile.yaml is hosted on a private registry, ensure you setup the authentication:

##### Option 1 - via DevSpaces UI:

1. Go to below URL, make sure to replace YOURDEVSPACESURL with yours:
https://YOURDEVSPACESURL/dashboard/#/user-preferences?tab=ContainerRegistries

   - This will create a secret in Kubernetes/OpenShift and mount it to the DevSpace/Container.

2. Optional - if you plan on using `podman` to authenticate externally, then include the path of the mounted secret:
   - ```
      - name: REGISTRY_AUTH_FILE ## https://docs.podman.io/en/latest/markdown/podman-login.1.html#authfile-path
        value: /etc/secret/TheGeneratedSecretName/.dockerconfigjson
     ```

##### Option 2 - via YAML:

1. Apply the "RegistryAuthTokenSecret.yaml" yaml. This will create a secret called `devworkspace-container-registry-dockercfg`

2. Update the auth token / json values.

3. If using `podman` to authenticate externally, make sure the [devfile.yaml](devfile.yaml) is using the below value:
   - ```
      - name: REGISTRY_AUTH_FILE ## https://docs.podman.io/en/latest/markdown/podman-login.1.html#authfile-path
        value: /etc/secret/devworkspace-container-registry-dockercfg/.dockerconfigjson
      ```

## Usage

### Build Images and Run Containers

1. Enable Kubedock in [devfile.yaml env block](devfile.yaml)
```
- name: KUBEDOCK_ENABLED ## https://eclipse.dev/che/docs/stable/end-user-guide/running-containers-with-kubedock/
  value: 'true'
```

2. Utilize podman
```
# enables "podman run" within container

# The podman build -t <localImage> . && podman run <localImage> command will FAIL. 

# image to be tagged/pushed to external registry for it to work.
# Use podman build -t <localImage> . && podman tag <localImage> <ExtenalRegistry> && podman push <ExtenalRegistry> && podman run <image> instead.

   ## To open bash session to container, -i with podman run won't work, so you will need to:
      # podman run -d bitnami/nginx:latest
      # podman ps -a
      # podman exec -it 98504TheContainerID /bin/bash
```