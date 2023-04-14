# Docker Dev Notes

## WSL

### Point WSL Docker CLI to WSL Minikube's Docker Daemon

- Run command: `eval $(minikube docker-env)`

### Point Windows Docker CLI to WSL Minikube's Docker Daemon

1. Open a bash terminal and login to WSL by running `wsl` command
2. Start Minikube (if not yet started)
3. Run `minikube docker-env` command. The output should be something like:

    ```sh
    export DOCKER_TLS_VERIFY="1"
    export DOCKER_HOST="tcp://127.0.0.1:32771"
    export DOCKER_CERT_PATH="/home/jay/.minikube/certs"
    ```

4. Open a new bash terminal running in Windows and export the above variables (or put the export commands in `$HOME/.bash_profile` or `$HOME/.bashrc` file to avoid having to export the variables everytime)

    Make sure to prefix `DOCKER_CERT_PATH` with `//wsl$/$DISTRIBUTION_NAME` (where `$DISTRIBUTION_NAME` is the name of the WSL distribution Minikube is running on) otherwise Docker will not be able to find the directory when run outside of WSL i.e.

    ```sh
    export DOCKER_CERT_PATH="//wsl$/Ubuntu/home/jay/.minikube/certs"
    ```

5. Docker away in Windows!

    ```sh
    docker images
    ```

### Point Windows Kubernetes CLI (kubectl) to WSL Minikube's Kubernetes Cluster

1. Open a bash terminal and login to WSL by running `wsl` command
2. Start Minikube (if not yet started)
3. Run `minikube kubectl -- config view`. The output should be something like:

    ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority: /home/jay/.minikube/ca.crt
        extensions:
        - extension:
            last-update: Thu, 06 Apr 2023 14:06:32 PDT
            provider: minikube.sigs.k8s.io
            version: v1.29.0
        name: cluster_info
        server: https://127.0.0.1:32769
      name: dev
    contexts:
    - context:
        cluster: dev
        extensions:
        - extension:
            last-update: Thu, 06 Apr 2023 14:06:32 PDT
            provider: minikube.sigs.k8s.io
            version: v1.29.0
        name: context_info
        namespace: default
        user: dev
      name: dev
    current-context: dev
    kind: Config
    preferences: {}
    users:
    - name: dev
    user:
        client-certificate: /home/jay/.minikube/profiles/dev/client.crt
        client-key: /home/jay/.minikube/profiles/dev/client.key
    ```

4. Copy the output of above command and save to a file e.g. `wslminikubeconfig` (or add it `$HOME/.kube/config` file to avoid having to pass a `--kubeconfig` to `kubectl` everytime)

    Make sure to prefix all the directories in the config (such as `certificate-authority`, `client-certificate`, `client-key`) with `//wsl$/$DISTRIBUTION_NAME` (where `$DISTRIBUTION_NAME` is the name of the WSL distribution Minikube is running on) otherwise `kubectl` will not be able to find the directories when run outside of WSL i.e.

    ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority: //wsl$/Ubuntu/home/jay/.minikube/ca.crt
        extensions:
        - extension:
            last-update: Thu, 06 Apr 2023 14:06:32 PDT
            provider: minikube.sigs.k8s.io
            version: v1.29.0
        name: cluster_info
        server: https://127.0.0.1:32769
      name: dev
    contexts:
    - context:
        cluster: dev
        extensions:
        - extension:
            last-update: Thu, 06 Apr 2023 14:06:32 PDT
            provider: minikube.sigs.k8s.io
            version: v1.29.0
        name: context_info
        namespace: default
        user: dev
      name: dev
    current-context: dev
    kind: Config
    preferences: {}
    users:
    - name: dev
    user:
        client-certificate: //wsl$/Ubuntu/home/jay/.minikube/profiles/dev/client.crt
        client-key: //wsl$/Ubuntu/home/jay/.minikube/profiles/dev/client.key
    ```

5. If config is added to `$HOME/.kube/config`, make sure to switch to the correct context before running any commands i.e.

    ```sh
    kubectl config use-context $MINIKUBE_CONTEXT 
    ```

    (Where `$MINIKUBE_CONTEXT` is the name of the context we have copied from Minikube. In this case it is `dev`.)

6. Kubectl away in Windows!

    ```sh
    kubectl --kubeconfig ./path/to/minikubeconfig get pods
    ```

    (`--kubeconfig` is only needed if Minikube config wasn't added to `$HOME/.kube/config`)
