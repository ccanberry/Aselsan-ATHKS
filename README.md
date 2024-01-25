# ATHKS - Automated Test Havuzu Konumlandırma Sistemi

Control software for acoustic test pool positioning system (ATHKS) developed for Aselsan/Robotek.

## Overview

ATHKS is a C# application that coordinates multi-carrier motion control in an acoustic test environment using **Modbus TCP** fieldbus protocol.

The system manages **4 carriers**, each with **4 axes** (X, Y, Z, and C/rotation), for a total of 16 controllable axes. It provides:

- Real-time carrier positioning and motion control
- Speed/acceleration override for all axes
- Digital I/O handling (relay coils, input states)
- Concurrent multi-carrier motion
- Position verification and referencing
- Test pool synchronization via Modbus TCP

## Architecture

### Core Components

- **ATHKS.cs**: Main system controller managing 4 carriers, Modbus connections (PLC + CNC), and system-level I/O
- **Carrier.cs**: Individual carrier control (4 axes per carrier) with position tracking and motion commands
- **ModbusClientTCP.cs**: Modbus TCP client for PLC/CNC communication
- **Program.cs**: Functional test suite demonstrating system capabilities

### Hardware Target

- **Network Target**: Modbus TCP server on 192.168.0.10 (configurable)
  - CNC port: 1502 (motion control)
  - PLC port: 502 (I/O and logic)
- **Physical**: 4-carrier positioning system with proportional servo control

## Build & Run

### Requirements

- .NET Framework 4.5+ or .NET Core 3.0+
- Visual Studio 2019+ or command-line C# compiler

### Build

```bash
# Via Visual Studio
msbuild SAL2.sln

# Or via .NET CLI
dotnet build SAL2.csproj
```

### Run

```bash
dotnet run
# or
./bin/Debug/SAL2.exe
```

Default target: Modbus server at `192.168.0.10` (override in ATHKS constructor)

## Usage Example

```csharp
// Create system instance
ATHKS system = new ATHKS();

// Connect to PLC and CNC
if (!system.Connect()) { /* error */ }

// Enable all carriers
system.Enable();

// Reference all axes
system.Reference();

// Move carrier 0 to position (x=100, y=200, z=300, c=400)
Vector4D target = new Vector4D(100, 200, 300, 400);
system.carrier0.Move(target, true);  // blocking move

// Read current position
Vector4D currentPos = system.carrier0.ReadPos();

// Set digital I/O
system.SetCoil(800, true);
bool coilState = system.GetCoil(800);

// Disconnect
system.Disconnect();
```

## Carrier Axes & Addressing

Each carrier has 4 axes with Modbus register offsets:

| Carrier | X | Y | Z | C |
|---------|-------|-------|-------|--------|
| 0 (PLC A) | 0 | 1 | 3 | 2 |
| 1 (PLC B) | 0 | 4 | 6 | 5 |
| 2 (CNC A) | 7 | 8 | 10 | 9 |
| 3 (CNC B) | 7 | 11 | 13 | 12 |

## Key Features

- **Concurrent Motion**: Non-blocking moves allow simultaneous carrier operation
- **Speed Override**: Global velocity scaling (0 = pause, 1.0 = default, >1.0 = faster)
- **Position Verification**: Read and validate actual vs. target positions
- **Safety Height**: Configurable Z-axis safety move before other axes
- **I/O Control**: Digital coils and discrete inputs via Modbus

## Testing

The included test suite (Program.cs) validates:

- Modbus connection establishment
- Carrier enable/disable
- Axis referencing and homing
- Digital I/O read/write
- Individual and grouped carrier motions
- Speed and acceleration configuration
- Position verification

Run tests:
```bash
dotnet run
```

## License

Licensed per LICENSE.txt. Project for Aselsan/Robotek.

---

**Creator**: Cansın Canberi  
**Date**: February 2024  
**Contact**: Robotek Motion Control Systems

