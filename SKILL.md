--
description: Generate production-ready CAPL .can files for Vector CANoe/CANalyzer with expertise in automotive communication protocols, ECU simulation, diagnostics (UDS, ISO-TP, J1939), and automated testing. Creates compile-ready code following CAPL syntax rules.
instructions: .prompt.md
---

# CAPL Code Generator Skill

This skill enables generation of production-ready CAPL `.can` files for Vector CANoe/CANalyzer.

## When to Use This Skill

Use this skill when you need to:

- Generate CAPL code for CAN/CAN FD communication
- Create ECU simulation scripts
- Implement diagnostic protocols (UDS, ISO-TP, OBD-II, KWP2000, J1939)
- Build automated test scripts for automotive networks
- Fix compilation errors in CAPL files
- Create event handlers for CAN messages, signals, timers
- Implement state machines for ECU behavior
- Generate CRC/checksum calculations
- Create network management implementations

## Example User Requests

### Basic CAN Communication
```
Create a CAPL script that sends a CAN message with ID 0x123 every 100ms
```

### Diagnostic Services
```
Generate UDS diagnostic session control implementation with timeout handling
```

### ECU Simulation
```
Create an engine ECU simulation that sends periodic messages and responds to diagnostic requests
```

### Error Fixing
```
Fix the compilation errors in my CAPL file [paste code or filename]
```

### State Machine Implementation
```
Create a state machine with INIT, IDLE, RUNNING, and ERROR states with proper transitions
```

### Timer-Based Logic
```
Implement a watchdog timer that monitors CAN message reception and triggers timeout after 500ms
```

### Signal Processing
```
Create a CAPL script that reads signal values from CAN messages and performs calculations
```

## What This Skill Does

1. **Generates Complete CAPL Files**: Creates full, compile-ready `.can` source files
2. **Follows CAPL Syntax**: Ensures Vector CANoe/CANalyzer compatibility
3. **Prevents Common Errors**: Avoids duplicate functions, invalid C++ syntax, format specifier issues
4. **Creates Workspace Files**: Automatically saves generated code as `.can` files
5. **Maintains Structure**: Follows proper CAPL file organization (includes, variables, functions, events)

## What This Skill Does NOT Do

- Does NOT generate C, C++, C#, Java, or Python code
- Does NOT create partial or incomplete code snippets
- Does NOT use placeholders like TODO or FIXME
- Does NOT generate pseudocode
- Does NOT use unsupported C/C++ constructs (pointers, structs, typedef, malloc, etc.)

## Output Format

Every generation creates:
1. A complete `.can` file in your workspace with a descriptive name
2. The CAPL source code displayed in the response

## Tips for Best Results

- Specify CAN IDs, message names, and signal details when known
- Mention if you need CAN FD vs. Classical CAN
- Indicate required diagnostic services (UDS, J1939, etc.)
- Describe the desired behavior clearly
- For error fixes, provide the error messages or problematic code

## Supported Features

### Communication Protocols
- Classical CAN & CAN FD
- LIN, FlexRay, Ethernet
- J1939
- XCP
- UDS, ISO-TP, OBD-II, KWP2000

### Implementation Capabilities
- Message transmission/reception (periodic, event-driven)
- Signal processing and manipulation
- Diagnostic services
- State machines
- Timers and event handlers
- CRC/Checksum calculations
- Network management
- Error handling and recovery
- ECU simulation

### Code Quality Features
- Compiler warning prevention
- Proper format specifier usage
- Duplicate function detection
- Valid CAPL data types
- Event-driven architecture
- Modular and maintainable code
