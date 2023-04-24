# .NET Dev Notes

## VS Code

### Debugging

- Disable `justMyCode` to allow stepping into 3rd party library source

    ```json
    // launch.json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": ".NET Core Launch (console)",
                "type": "coreclr",
                "request": "launch",
                "preLaunchTask": "build",
                "justMyCode": false
            }
        ]
    }
    ```

- Disable `suppressJITOptimizations`

    ```json
    // launch.json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": ".NET Core Launch (console)",
                "type": "coreclr",
                "request": "launch",
                "preLaunchTask": "build",
                "suppressJITOptimizations": true
            }
        ]
    }
    ```

- Configure Symbol Servers

    This will allow debugger to download symbols from Microsoft, NuGet or any internal symbol servers.

    ```json
    // launch.json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": ".NET Core Launch (console)",
                "type": "coreclr",
                "request": "launch",
                "preLaunchTask": "build",
                "symbolOptions": {
                    "searchPaths": [ "/can/be/a/directory", "https://canbeaurl.symbols.server.com" ],
                    "searchMicrosoftSymbolServer": true,
                    "searchNuGetOrgSymbolServer": true,
                    "cachePath": "C:\\Temp\\SymbolCache"
                }
            }
        ]
    }
    ```

- Enable Source Link

    ```json
    // launch.json
    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": ".NET Core Launch (console)",
                "type": "coreclr",
                "request": "launch",
                "preLaunchTask": "build",
                "sourceLinkOptions": {
                    "*": {
                        "enabled": true
                    }
                }
            }
        ]
    }
    ```

- Download shared runtime symbols to step into .NET source

    (See: <https://github.com/dotnet/symstore/blob/main/src/dotnet-symbol/README.md>)

    1. Install `dotnet-symbol` tool

        ```sh
        dotnet tool install -g dotnet-symbol
        ```

    2. Download symbol files (PDB)s for the shared runtime

        (Administrator privilege is required)

        ```sh
        dotnet symbol --symbols --recurse-subdirectories $SHARED_RUNTIME_ROOT_DIR
        ```

        For Windows, Shared Runtime root directory is usually `/c/Program Files/dotnet/shared/`  
        For Linux, Shared Runtime root directory is usually `/usr/share/dotnet/shared/`

        e.g.

        ```sh
        dotnet symbol --symbols --recurse-subdirectories /usr/share/dotnet/shared/
        ```

        ```sh
        dotnet symbol --symbols --recurse-subdirectories /c/Program\ Files/dotnet/shared/
        ```

    3. Debug away!

- Download solution/project symbols to step into source

    1. Install `dotnet-symbol` tool

        ```sh
        dotnet tool install -g dotnet-symbol
        ```

    2. Download symbol files for a dotnet solution

        ```sh
        dotnet symbol --symbols --recurse-subdirectories $SOLUTION_DIR
        ```

        e.g.

        ```sh
        dotnet symbol --symbols --recurse-subdirectories .
        ```
