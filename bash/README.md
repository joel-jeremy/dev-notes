# Bash Dev Notes

## Source .bashrc from .bash_profile

1. Add below to `~/.bash_profile`:

    ```sh
    if [ -f ~/.bashrc ]; then
        source ~/.bashrc
    fi
    ```
