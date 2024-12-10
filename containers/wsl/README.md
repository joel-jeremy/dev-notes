# WSL Dev Notes

## Point Windows Docker CLI to WSL Docker Daemon

1. Open a bash terminal and login to WSL by running `wsl` command
2. In WSL, install Docker daemon (using preferred package manager)
3. In WSL, [allow remote access for Docker daemon](https://docs.docker.com/config/daemon/remote-access/):  
    - When using systemd:  
        a. Create an override for `docker.service` using `sudo systemctl edit docker.service` and add the following lines:

        ```conf
        [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
        # You can also set the host directly here e.g.
        # ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
        ```

        b. (Optional if host is already set in the override above) Configure Docker daemon hosts via `/etc/docker/daemon.json`:

        ```json
        { "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"] }
        ```

        c. Reload and restart Docker daemon:

        ```sh
        systemctl daemon-reload
        systemctl restart docker.service
        ```

    - When not using systemd:  
        a. Configure Docker daemon hosts via `/etc/docker/daemon.json`:

        ```json
        { "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"] }
        ```

4. In Windows, find the IP address of the WSL distribution where Docker daemon is running e.g. `wsl hostname -I`
5. Export the below variable and value (or put the export commands in `$HOME/.bash_profile` or `$HOME/.bashrc` file to avoid having to export the variables everytime)

    ```sh
    export DOCKER_HOST="tcp://$WSL_IP:2375"
    ```

6. Docker away in Windows!

    ```sh
    docker images
    ```

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

## Fix Permission Denied Error in WSL

If a permission error occurs when running docker commands in WSL, make sure to add the current WSL user to the `docker` group

`docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied.`

From [Stack Overflow](https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue)

1. Create the docker group if it does not exist:
    - `sudo groupadd docker`
2. Add your user to the docker group:
    - `sudo usermod -aG docker $USER`
3. Log in to the new docker group (to avoid having to log out and log in again; but if not enough, try to reboot):
    - `newgrp docker`
4. Check if Docker can be run without root:
    - `docker run hello-world`
5. Reboot if you still get an error:
    - `reboot`
