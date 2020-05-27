# Start qemu gdb
To debug with QEMU and to start a GDB server and wait for a remote connect, run the following inside the build directory of an application:

```bash
cd build_dir
make debugserver
```

# Vscode setup
Create `.vscode/launch.json` with following content:
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "RiscV debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "/workspace/zephyr/build/hello_world/zephyr/zephyr.elf",
            "miDebuggerServerAddress": "localhost:1234",
            "miDebuggerPath": "/opt/toolchains/zephyr-sdk-0.11.2/riscv64-zephyr-elf/bin/riscv64-zephyr-elf-gdb",
            "args": [],
            "stopAtEntry": true,
            "cwd": "/workspace/zephyr",
            "environment": [],
            "externalConsole": true,
            "linux": {
              "MIMode": "gdb"
            },
          }
    ]
}
```

Update field: 
+ **program**: path to elf target debug file.
+ **miDebuggerServerAddress**: gdbserver.
+ **miDebuggerPath**: path to gdb tool. In this case, `riscv64-zephyr-elf-gdb`. With docker dev environmen path is: */opt/toolchains/zephyr-sdk-0.11.2/riscv64-zephyr-elf/bin/riscv64-zephyr-elf-gdb*.
+ **cwd**: working dir.

# Vscode debug.
1. Select debug button or press `Ctrl+Shift+D`.
2. Select debug target: `RiscV debug`
5. Press `F5` to start debug client.

# Links:
https://docs.zephyrproject.org/1.13.0/application/application.html#id3
https://medium.com/@spe_/debugging-c-c-programs-remotely-using-visual-studio-code-and-gdbserver-559d3434fb78
https://code.visualstudio.com/docs/editor/debugging#_launch-configurations
https://code.visualstudio.com/docs/cpp/cpp-debug