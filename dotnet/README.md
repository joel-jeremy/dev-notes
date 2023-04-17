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

    (See: [Automated Script](scripts/downloadDotnetRuntimeSymbols.sh))

    1. Install `dotnet-symbol` tool

        ```sh
        dotnet tool install -g dotnet-symbol
        ```

    2. Download symbol files (PDB)s for the shared runtime

        ```sh
        dotnet symbol --symbols --output $OUTPUT_DIR $SHARED_RUNTIME_DIR
        ```

        For Windows, Shared Runtime directory is usually `C:\Program Files\dotnet\shared\`  
        For Linux, Shared Runtime directory is usually `/usr/share/dotnet/shared/.`

        e.g.

        ```sh
        dotnet symbol --symbols --output /temp/symbols /usr/share/dotnet/shared/Microsoft.NETCore.App/7.0.0/
        ```

        ```sh
        dotnet symbol --symbols --output /temp/symbols /usr/share/dotnet/shared/Microsoft.AspNetCore.App/7.0.0/
        ```

    3. Copy symbol files (PDB)s to runtime directory so the native debuggers like lldb or gdb can find them, but the copy needs to be superuser

        ```sh
        sudo cp /tmp/symbols/* /usr/share/dotnet/shared/Microsoft.NETCore.App/7.0.0
        ```

        ```sh
        sudo cp /tmp/symbols/* /usr/share/dotnet/shared/Microsoft.AspNetCore.App/7.0.0
        ```

    4. Debug away!
