# WSL Dev Notes

## Point Windows Docker CLI to WSL Minikube's Docker Daemon

1. Open a bash terminal and login to WSL by running `wsl` command
2. Start Minikube (if not yet started)
3. Inside WSL, run `minikube docker-env` command. The output should be something like:

    ```sh
    export DOCKER_TLS_VERIFY="1"
    export DOCKER_HOST="tcp://127.0.0.1:32771"
    export DOCKER_CERT_PATH="/home/jay/.minikube/certs"
    ```

4. In Windows, open a new bash terminal and export the above variables and values (or put the export commands in `$HOME/.bash_profile` or `$HOME/.bashrc` file to avoid having to export the variables everytime)

    **IMPORTANT: Make sure to prefix `DOCKER_CERT_PATH` with `//wsl$/$DISTRIBUTION_NAME` (where `$DISTRIBUTION_NAME` is the name of the WSL distribution that is running Minikube) otherwise Docker will not be able to find the directory from Windows** i.e.

    ```sh
    export DOCKER_CERT_PATH="//wsl$/Ubuntu/home/jay/.minikube/certs"
    ```

5. Docker away in Windows!

    ```sh
    docker images
    ```

## Point Windows Kubernetes CLI (kubectl) to WSL Minikube's Kubernetes Cluster

1. Open a bash terminal and login to WSL by running `wsl` command
2. Start Minikube (if not yet started) i.e. `minikube start`
3. Inside WSL, run the following command:

    ```sh
    # Set $WINDOWS_USER.
    WINDOWS_USER=Jay
    # Set $KUBECONFIG.
    KUBECONFIG=~/.kube/config:/mnt/c/Users/$WINDOWS_USER/.kube/config
    # Merge WSL ~/.kube/config and Windows /c/Users/$WINDOWS_USER/.kube/config and save to Windows /c/Users/$WINDOWS_USER/.kube/mergedkubeconfig.
    minikube kubectl -- config view --merge --flatten > /mnt/c/Users/$WINDOWS_USER/.kube/mergedkubeconfig
    ```

    (Update the `$WINDOWS_USER` variable accordingly.)

    The resulting `mergedkubeconfig` file should look something like: [mergedkubeconfig](examples/mergedkubeconfig)

4. Backup the contents of `/c/Users/$WINDOWS_USER/.kube/config` and replace it with contents of `mergedkubeconfig` e.g.

    From inside WSL:

    ```sh
    # Set $WINDOWS_USER.
    WINDOWS_USER=Jay
    # Backup and replace config.
    mv /mnt/c/Users/$WINDOWS_USER/.kube/config /mnt/c/Users/$WINDOWS_USER/.kube/config.$(date +%s).bak
    cp /mnt/c/Users/$WINDOWS_USER/.kube/mergedkubeconfig /mnt/c/Users/$WINDOWS_USER/.kube/config
    ```

    Alternatively, the commands can be executed in Windows:

    ```sh
    mv $HOME/.kube/config $HOME/.kube/config.$(date +%s).bak
    cp $HOME/.kube/mergedkubeconfig $HOME/.kube/config
    ```

5. Kubectl away in Windows!

    - Make sure to switch to the correct context before running any commands i.e.

        ```sh
        kubectl config use-context $MINIKUBE_CONTEXT 
        ```

        (Where `$MINIKUBE_CONTEXT` is the name of the context we have copied from Minikube. In this case it is `minikube`.)

    - If switching contexts is not desired, the `--context` option may be used to specify which context should be used by the kubectl command i.e.

        ```sh
        kubectl --context $MINIKUBE_CONTEXT get pods
        ```

        (Where `$MINIKUBE_CONTEXT` is the name of the context we have copied from Minikube. In this case it is `minikube`.)
