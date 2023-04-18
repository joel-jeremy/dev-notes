# Minikube Dev Notes

## Point Docker CLI to Minikube's Docker Daemon

1. Run shell command:

    ```sh
    eval $(minikube docker-env)
    ```

## Make `mk` alias to for Minikube

1. Add the below alias to `~/.bashrc` or `~/.bash_profile`:

    ```sh
    alias mk="minikube"
    ```

## Make `kubectl` alias to use Minikube's kubectl

1. Add the below alias to `~/.bashrc` or `~/.bash_profile`:

    ```sh
    alias kubectl="minikube kubectl --"
    ```

## Minikube and Minikube kubectl bash completion

1. Add the below to `~/.bashrc` or `~/.bash_profile`:

    ```sh
    if command -v minikube &>/dev/null
    then
        # Minikube bash completion
        eval "$(minikube completion bash)"
        # Kubectl bash completion
        eval "$(minikube kubectl -- completion bash)"
        # Make completion work with mk alias
        complete -o default -F __start_minikube mk
        # Make completion work with kubectl alias
        complete -o default -F __start_kubectl kubectl
    fi
    ```
