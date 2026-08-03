# CAPL Code Generator - Input Examples

This file contains example user inputs/prompts that demonstrate how to use the CAPL skill.

---

## Basic CAN Communication

### Example 1: Simple Message Sender
```
Create a CAPL script that sends a CAN message with ID 0x123 every 100ms
```

### Example 2: Message Receiver
```
Write CAPL code to receive CAN message 0x456 and print its data bytes
```

### Example 3: CAN FD Communication
```
Generate a CAPL script for CAN FD message transmission with ID 0x200, 64 bytes payload, sent every 50ms
```

---

## Timer-Based Scripts

### Example 4: Periodic Timer
```
Create a CAPL timer that triggers every 500ms and prints a message
```

### Example 5: Watchdog Monitor
```
Implement a watchdog timer that monitors CAN message 0x100 and reports timeout after 1000ms of no reception
```

### Example 6: Multiple Timers
```
Create CAPL code with 3 different timers: 100ms, 500ms, and 1000ms intervals
```

---

## Signal Processing

### Example 7: Signal Reading
```
Read signal "EngineSpeed" from CAN message "EngineData" and display its value
```

### Example 8: Signal Calculation
```
Create CAPL code that reads two signals, calculates their sum, and sends the result in another message
```

### Example 9: Signal Threshold Detection
```
Monitor signal "Temperature" and trigger an alert when it exceeds 80 degrees
```

---

## UDS Diagnostics

### Example 10: Diagnostic Session Control
```
Generate UDS diagnostic session control service (0x10) with extended session request
```

### Example 11: Read Data By Identifier
```
Create CAPL code for UDS service ReadDataByIdentifier (0x22) to read VIN number
```

### Example 12: Security Access
```
Implement UDS security access (0x27) with seed-key mechanism
```

### Example 13: ECU Reset
```
Generate CAPL code for UDS ECU reset service (0x11) with hard reset
```

### Example 14: Tester Present
```
Create a periodic tester present (0x3E) implementation that sends every 2 seconds
```

---

## State Machine Implementations

### Example 15: Basic State Machine
```
Create a state machine with INIT, IDLE, RUNNING, and ERROR states
```

### Example 16: ECU Startup Sequence
```
Implement a startup state machine: INIT -> WAIT_FOR_POWER -> READY -> OPERATIONAL
```

### Example 17: State Transitions with Timers
```
Create a state machine where each state transition is triggered by a timer event
```

---

## ECU Simulation

### Example 18: Engine ECU Simulation
```
Generate an engine ECU simulation that sends RPM, speed, and temperature messages periodically
```

### Example 19: Gateway ECU
```
Create a gateway ECU that receives messages on CAN1 and forwards them to CAN2
```

### Example 20: Sensor Simulation
```
Simulate a temperature sensor that sends values increasing from 20 to 100 degrees
```

---

## Error Handling

### Example 21: Bus-Off Recovery
```
Implement CAPL code that detects bus-off condition and attempts recovery
```

### Example 22: Error Frame Handling
```
Create error frame handler that counts and logs all CAN error frames
```

### Example 23: Invalid Message Detection
```
Monitor for messages with invalid DLC and log them
```

---

## Advanced Features

### Example 24: CRC Calculation
```
Implement CRC16 calculation for CAN message payload
```

### Example 25: Rolling Counter
```
Create a rolling counter implementation that increments from 0 to 15
```

### Example 26: Message Filtering
```
Filter and process only CAN messages with IDs in range 0x100 to 0x1FF
```

### Example 27: Periodic Message Scheduler
```
Create a scheduler that sends 5 different messages with different periods
```

---

## J1939 Protocol

### Example 28: J1939 Message Transmission
```
Generate J1939 PGN 0xF004 (Electronic Engine Controller 1) transmission
```

### Example 29: J1939 Address Claiming
```
Implement J1939 address claiming procedure
```

---

## ISO-TP Communication

### Example 30: ISO-TP Multi-Frame
```
Create ISO-TP multi-frame transmission for diagnostic messages longer than 8 bytes
```

### Example 31: Flow Control
```
Implement ISO-TP flow control handling for consecutive frames
```

---

## Debugging and Error Fixing

### Example 32: Fix Compilation Errors
```
Fix the compilation errors in this CAPL code:
[paste your code here]
```

### Example 33: Remove Duplicate Functions
```
My CAPL file has duplicate function errors, please fix:
[paste your code here]
```

### Example 34: Format Specifier Issues
```
I'm getting format specifier warnings, please correct:
[paste your code here]
```

---

## Event Handlers

### Example 35: Key Press Handler
```
Create CAPL code that triggers on pressing 'a' key and sends a CAN message
```

### Example 36: Environment Variable Handler
```
Implement handler for environment variable "TestMode" and perform actions on change
```

### Example 37: Multiple Message Handlers
```
Create handlers for 3 different CAN messages: 0x100, 0x200, 0x300
```

---

## File Operations

### Example 38: Log to File
```
Create CAPL script that logs all received CAN messages to a text file
```

### Example 39: Read Configuration
```
Read configuration values from a file and use them to configure message transmission
```

---

## Network Management

### Example 40: Wake-up Pattern
```
Implement network wake-up pattern transmission
```

### Example 41: Sleep Mode Handling
```
Create logic to detect network idle and enter sleep mode after 5 seconds
```

---

## Testing and Validation

### Example 42: Message Counter Validation
```
Validate that message counter increments correctly and detect any gaps
```

### Example 43: Timeout Detection
```
Monitor 5 different messages and detect which ones timeout after 200ms
```

### Example 44: Checksum Verification
```
Verify checksum in received CAN messages and report errors
```

---

## Complete Example Request Format

```
Task: Create CAN message sender
Message ID: 0x123
DLC: 8 bytes
Period: 100ms
Data: [0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08]
Network: CAN1
Additional: Include start/stop event handlers
```

---

## Tips for Writing Good Input Prompts

1. **Be Specific**: Include CAN IDs, periods, byte values
2. **Mention Protocol**: Specify if CAN FD, UDS, J1939, etc.
3. **State Requirements**: Mention error handling, logging needs
4. **Provide Context**: Describe the use case or test scenario
5. **Include Details**: DLC, data patterns, timeout values
6. **Reference Standards**: Mention ISO standards if applicable

---

## What NOT to Ask

❌ "Write me a C++ program for CAN"
❌ "Create a Python script to parse messages"
❌ "Generate Java code for diagnostics"
❌ "Make a partial code snippet with TODO comments"

✅ Ask for complete CAPL .can files
✅ Request specific CAPL features
✅ Provide automotive protocol details
✅ Ask for compilation error fixes
