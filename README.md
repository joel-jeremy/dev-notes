# dotnet-development

# VS Code

## Download Shared Runtime Symbols for Debugging

(See: https://github.com/dotnet/symstore/blob/main/src/dotnet-symbol/README.md)

1. Install dotnet-symbol tool
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
    dotnet symbol --symbols --output /temp/symbols /usr/share/dotnet/shared/Microsoft.NETCore.App/7.0.0/*
    ```
    ```sh
    dotnet symbol --symbols --output /temp/symbols /usr/share/dotnet/shared/Microsoft.AspNetCore.App/7.0.0/*
    ```
3. Copy symbol files (PDB)s to runtime directory so the native debuggers like lldb or gdb can find them, but the copy needs to be superuser
    ```sh
    sudo cp /tmp/symbols/* /usr/share/dotnet/shared/Microsoft.NETCore.App/7.0.0
    ```
    ```sh
    sudo cp /tmp/symbols/* /usr/share/dotnet/shared/Microsoft.AspNetCore.App/7.0.0
    ```
 4. Debug away!
