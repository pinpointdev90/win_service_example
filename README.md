# C++ Windows Web Service

A minimal C++17 Windows Service with a built-in Winsock HTTP server.

## Features

- Windows Service (`services.msc`)
- C++17
- Winsock HTTP server
- Port 8080
- Graceful service shutdown
- `SERVICE_CONTROL_STOP`
- Automatic service startup
- Service recovery/restart configuration
- Console mode for development
- Install/uninstall commands

## Requirements

- Windows 10/11 or Windows Server
- Visual Studio 2022
- Desktop development with C++
- x64 build recommended

## Visual Studio project

Create an **Empty Project** and add:

```text
main.cpp
Service.cpp
Service.h
HttpServer.cpp
HttpServer.h
```

Set:

```text
Configuration Type: Application (.exe)
Platform: x64
C++ Language Standard: ISO C++17
```

`Ws2_32.lib` is linked from `HttpServer.h`.

## Run in console mode

Build Release x64, then run:

```bat
CppWindowsWebService.exe
```

Open:

```text
http://localhost:8080
```

Expected response:

```text
Hello from C++ Windows Service!
```

Press Enter to stop the server.

## Install the Windows Service

Open **Command Prompt as Administrator**.

Run:

```bat
CppWindowsWebService.exe install
```

The service name is:

```text
CppWebService
```

Start it:

```bat
sc start CppWebService
```

or open:

```text
services.msc
```

and start `CppWebService`.

## Test

Open:

```text
http://localhost:8080
```

## Stop

Using `sc`:

```bat
sc stop CppWebService
```

Or:

```text
services.msc
 -> CppWebService
 -> Stop
```

The service receives:

```text
SERVICE_CONTROL_STOP
```

and calls:

```cpp
SetEvent(stopEvent);
```

The main service thread then performs:

```cpp
server.Stop();
```

This gives the HTTP server a graceful shutdown path.

## Uninstall

Open Command Prompt as Administrator:

```bat
CppWindowsWebService.exe uninstall
```

If the service is running, the program first requests a stop and then deletes the service.

## Why Task Manager can show Access Denied

A Windows Service is controlled through the Windows Service Control Manager.

Normal service shutdown should use:

```text
services.msc
sc stop CppWebService
```

rather than terminating the process directly.

Whether Task Manager displays `Access denied` depends on the account, privileges, process security, Windows version, and how the service is configured. This sample does not use an unsafe trick to block process termination.

For stronger protection, Windows service/process security descriptors and a properly secured service account should be configured.

## Service account

The sample installs the service as:

```text
LocalSystem
```

For production, consider using a dedicated low-privilege service account instead of LocalSystem unless the service actually requires those privileges.

## Port

The server listens on:

```text
0.0.0.0:8080
```

To change the port, modify:

```cpp
HttpServer server(8080);
```

For example:

```cpp
HttpServer server(9000);
```

## Production considerations

This is intentionally a small example. A production web service should additionally consider:

- TLS/HTTPS
- authentication/authorization
- request parsing and validation
- bounded worker threads
- logging/Event Log
- configuration files
- graceful handling of existing connections
- Windows Firewall configuration
- least-privilege service account
- service ACLs
- crash handling
- health endpoint
- monitoring
- structured logging
- robust HTTP parsing

## License

Use and modify this example freely for development/testing.
