# Configuring launch.json for C/C++ debugging

The `launch.json` file is used to configure the debugger in Visual Studio Code. 

Visual Studio Code generates a `launch.json` with almost all of the required information.  To get started
debugging you need to fill in the `program` field with the path to the executable you plan to debug. This must be specified for 
both the launch and attach (if you plan to attach to a running instance at any point) configurations.

The generated file contains two sections.  One that configures debugging for launch and a second that configures debugging for attach.

# Configure VS Code's debugging behavior

Set or change the following options to control VS Code's behavior during debugging

**program**: `Required` Specifies the full path to executable the debugger will launch or attach to.

**symbolSearchPath**: Tells the _Visual Studio Windows Debugger_ what paths to search for symbol (.pdb) files.  Separate multiple paths with a semicolon. Example `"C:\\Symbols;C:\\SymbolDir2"`

**additionalSOLibSearchPath**: Tells _GDB or LLDB_ what paths to search for .so files. Separate multiple paths with a semicolon. Example: `"/Users/user/dir1;/Users/user/dir2"`.

**externalConsole**: If set to `true`, launches an external console for the application. If `false`, no console is launched and VS Code's debugging console is used. Note this option is ignored in some cases for technical reasons.
                            
**logging**: Optional flags to determine what types of messages should be logged to the Debug Console.
  * **exceptions**: Optional flag to determine whether exception messages should be logged to the Debug Console. Defaults to true.
  * **moduleLoad**: Optional flag to determine whether module load events should be logged to the Debug Console. Defaults to true.
  * **programOutput**: Optional flag to determine whether program output should be logged to the Debug Console. Defaults to true.
  * **engineLogging**: Optional flag to determine whether diagnostic engine logs should be logged to the Debug Console. Defaults to false.
  * **trace**: Optional flag to determine whether diagnostic adapter command tracing should be logged to the Debug Console. Defaults to false.
  * **traceResponse**: Optional flag to determine whether diagnostic adapter command and response tracing should be logged to the Debug Console. Defaults to false.

**visualizerFile**: .natvis file to be used when debugging. See [Natvis syntax reference](http://aka.ms/natvis#Anchor_8) for information on how to create natvis files.

**Example:**

```json
{
   "name": "C++ Launch (Windows)",
   "type": "cppvsdbg",
   "request": "launch",
   "program": "C:\\app1\\Debug\\app1.exe",
   "symbolSearchPath": "C:\\Symbols;C:\\SymbolDir2",
   "externalConsole": true,
   "logging": {
       "moduleLoad": false,
       "trace": true
    },
   "visualizerFile": "${workspaceRoot}/my.natvis"
}
```


# Configure the target application

The following options enable you to modify the state of the target application when it is launched

**args**: JSON array of command line arguments to pass to the program when it is launched. Example `["arg1", "arg2]`.

**cwd**: Sets the the working directory of the application launched by the debugger.

**environment**: Environment variables to add to the environment for the program. Example: `[ { "name": "squid", "value": "clam" } ]`.

**Example:**

```json
{
   "name": "C++ Launch",
   "type": "cppdbg",
   "request": "launch",
   "program": "${workspaceRoot}/a.out",
   "args": ["arg1", "arg2"],
   "environment": [{"name": "squid", "value": "clam"}],
   "cwd": "${workspaceRoot}"
}
```

## Customizing GDB or LLDB 

You can change the behavior of GDB or LLDB by setting the following options.

**MIMode**: Indicates the debugger that VS Code will connect to. Must be set to `gdb` or `lldb`.  This is pre-configured on a per-operating system basis and can be changed as needed.

**miDebuggerPath**: The path to the debugger (such as gdb). When unspecified, it will search the operating system's PATH variable for a debugger (GDB on Linux and Windows, LLDB on OS X).

**stopAtEntry**: If set to true, the debugger should stop at the entrypoint of the target (ignored on attach). Default value is `false`.

**setupCommands**: JSON array of commands to execute in order to setup the GDB or LLDB. Example: `"customLaunchSetupCommands": [ { "text": "target-run", "description": "run target", "ignoreFailures": false }]`.
 
**customLaunchSetupCommands**: If provided, this replaces the default commands used to launch a target with some other commands. For example, this can be "-target-attach" in order to attach to a target process. An empty command list replaces the launch commands with nothing, which can be useful if the debugger is being provided launch options as command line options. Example: `"customLaunchSetupCommands": [ { "text": "target-run", "description": "run target", "ignoreFailures": false }]`.
      
**launchCompleteCommand**: The command to execute after the debugger is fully setup in order to cause the target process to run. Allowed values are "exec-run", "exec-continue", "None". The default value is "exec-run".

**Example:**

```json
{
   "name": "C++ Launch",
   "type": "cppdbg",
   "request": "launch",
   "program": "${workspaceRoot}/a.out",
   "stopAtEntry": false,
   "customLaunchSetupCommands": [
      { "text": "target-run", "description": "run target", "ignoreFailures": false }
   ],
   "launchCompleteCommand": "exec-run",
   "linux": {
      "MIMode": "gdb",
      "miDebuggerPath": "/usr/bin/gdb"
   },
   "osx": {
      "MIMode": "lldb"
   },
   "windows": {
      "MIMode": "gdb",
      "miDebuggerPath": "C:\\MinGw\\bin\\gdb.exe"
   }
}
```

## Debugging dump files

The C/C++ extension enables debugging dump files on Windows and core dump files Linux and OS X.

**dumpPath**: If you want to debug a Windows dump file, set this to the path to the dump file to start debugging in the `launch` configuration.

**coreDumpPath**: Full path to a core dump file to debug for the specified program.  Set this to the path to the core dump file to start debugging in the `launch` configuration. 
_Note: core dump debuging is not supported with MinGw._  

## Remote debugging or debugging with a local debugger server

**miDebuggerServerAddress**: Network address of the debugger server (e.g. gdbserver) to connect to for remote debugging (example: `localhost:1234`).

**debugServerPath**: Full path to debug server to launch.

**debugServerArgs**: Arguments for the the debugger server.
                
**serverStarted**: Server-started pattern to look for in the debug server output.

**serverLaunchTimeout**: Time in milliseconds, for the debugger to wait for the debugServer to start up. Default is 10000.

## Additional properties

**processId**: Defaults to `${command.pickProcess}` which will display a list of available processes the debugger can attach to.  It is recommended to leave this default, but the property can be explicitly set to a specific process ID for the debugger to attach to.

**request**: Indicates whether the configuration section is intended to `launch` the program or `attach` to an already running instance.

**targetArchitecture**: `Deprecated` This option is no longer needed as the target architecture is automatically detected.

**type**: Indicates the underlying debugger being used. Must be `cppvsdbg` when using the Visual Studio Windows debugger, and `cppdbg` when using GDB or LLDB.  This is automatically set to the correct value when the 
launch.json file is created.

