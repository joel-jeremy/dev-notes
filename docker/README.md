# Docker Dev Notes

## WSL

### Point WSL Docker CLI to WSL Minikube's Docker Daemon

- Run command: `eval $(minikube docker-env)`

### Point Windows Docker CLI to WSL Minikube's Docker Daemon

1. Open a bash terminal and login to WSL by running `wsl` command
2. Start Minikube (if not yet started)
3. Run `minikube docker-env` command. This should output something like below:

    ```sh
    export DOCKER_TLS_VERIFY="1"
    export DOCKER_HOST="tcp://127.0.0.1:32771"
    export DOCKER_CERT_PATH="/home/jay/.minikube/certs"
    ```

4. Copy output of above `minikube docker-env` command
5. Open a new bash terminal in Windows and paste the copied output

    Make sure to add `//wsl$/$DISTRIBUTION_NAME` to the `DOCKER_CERT_PATH` (wherein `$DISTRIBUTION_NAME` is the name of the WSL distribution) i.e.

    ```sh
    export DOCKER_CERT_PATH="//wsl$/Ubuntu/home/jay/.minikube/certs"
    ```

6. Docker away!
