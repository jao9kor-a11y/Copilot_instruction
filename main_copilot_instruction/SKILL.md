---
description: Generate CAN message files (.can format) for automotive ECU testing from UDS (Unified Diagnostic Services) test specifications. Converts CSV test cases with diagnostic commands into CANoe-compatible .can files with proper timing, CAN IDs, and ISO-TP formatting. Handles session control, security access, DID read/write operations, and negative response testing.
---

# CAN File Generation from UDS Test Specifications

## Overview

This skill enables automatic generation of CAN message files (.can format) from automotive UDS diagnostic test specifications stored in CSV format. The generated files are compatible with Vector CANoe/CANalyzer and other CAN simulation tools.

## When to Use This Skill

### Primary Use Cases
- Converting UDS test specifications from CSV to executable .can files
- Generating CANoe-compatible test sequences for ECU validation
- Creating diagnostic message sequences with proper ISO-TP formatting
- Automating test case generation for automotive diagnostic testing
- Batch processing multiple test cases into individual .can files

### Specific Scenarios
- User provides CSV file with test cases containing UDS commands
- User requests .can file generation for CANoe testing
- User needs to convert diagnostic test specifications to CAN messages
- User wants to automate ECU test case creation
- User asks about "CAN file generation", "UDS testing", "CANoe import"

### When NOT to Use
- General CAN database (.dbc) creation or modification
- CAPL script development (unless integrating .can file replay)
- Real-time CAN bus monitoring or debugging
- CAN network design or architecture
- Non-automotive or non-UDS protocols

## Capabilities

### Input Processing
- Parse CSV files with UDS test specifications
- Extract test case metadata (requirement ID, function ID, purpose)
- Parse UDS command sequences from test_execution column
- Handle hex data in various formats `[0x10 0x01]` or `[10 01]`
- Support multiple test cases per CSV file

### CAN Message Generation
- Convert UDS services to CAN messages with proper formatting
- Apply correct CAN IDs (default: 0x740 Tx, 0x748 Rx)
- Generate 8-byte padded messages for standard CAN
- Calculate realistic timing between messages
- Support ISO-TP multi-frame messages for large payloads
- Handle positive and negative response generation

### UDS Services Supported
- **0x10** DiagnosticSessionControl (Default, Extended, Programming)
- **0x22** ReadDataByIdentifier (DID reads)
- **0x27** SecurityAccess (Seed-Key exchange)
- **0x2E** WriteDataByIdentifier (DID writes)
- **0x3E** TesterPresent (Session keep-alive)
- **0x7F** NegativeResponse (Error handling)

### Output Features
- CANoe-compatible .can file format
- Descriptive comments for each test step
- Test case metadata in file header
- Proper timestamp sequencing
- Configurable timing and CAN IDs
- Pass/fail criteria documentation

## How to Invoke

### Direct Generation Commands

**Generate single test case:**
```
Generate a .can file for test case TC_NoFalseAccessAttempts_NoBlockTime from test_spec_flat_all 5.csv
```

**Generate by requirement ID:**
```
Generate all .can files for requirement 2700204 from the CSV file
```

**Generate all test cases:**
```
Generate .can files for all test cases in test_spec_flat_all 5.csv
```

**Generate with custom parameters:**
```
Generate .can file for test case [name] using CAN ID 0x7E0 for Tx and 0x7E8 for Rx with 100ms delays
```

### Natural Language Prompts

Users can ask in natural language:
- "Create CAN files from my test specification CSV"
- "I need .can files for CANoe testing"
- "Convert these UDS test cases to CAN format"
- "Generate CANoe import files from the test spec"
- "Make .can files with all the diagnostic tests"

### Batch Processing

**Process multiple files:**
```
Generate .can files for all test cases in the following CSV files:
- test_spec_part1.csv
- test_spec_part2.csv
- test_spec_part3.csv
```

**Filter by criteria:**
```
Generate .can files only for test cases that include security access
Generate .can files for all DID read operations
Generate .can files for requirement IDs starting with 27002
```

## Required Input Format

### CSV Structure

CSV file must contain these columns:

| Column | Description | Example |
|--------|-------------|---------|
| req_id | Requirement identifier | 2700204 |
| fnid | Function identifier | EDI_FNID_1282 |
| object_text | Requirement description | "DID FEFC DevMsgEnable..." |
| verification_criteria | Test verification method | "Verify DID accessible..." |
| test_purpose | Test objective | "Verify block time triggered..." |
| test_scenario | Input/output table | Markdown table with scenarios |
| test_precondition | Test prerequisites | "ECU powered on..." |
| test_execution | Step-by-step UDS commands | See format below |

### Critical: test_execution Format

Must follow this structure:

```
1. [Step Description]
**Request data:** [0xXX 0xYY 0xZZ]
**Response data:** [0xAA 0xBB 0xCC]
*Note: [Optional notes]*

2. [Next Step]
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]
```

**Requirements:**
- Steps numbered with periods (1., 2., 3.)
- `**Request data:**` marker (exactly this text)
- `**Response data:**` marker (exactly this text)
- Hex data in square brackets with spaces between bytes
- Optional `*Note:*` for additional context

## Output Format

### Generated .can File Structure

```
date Wed Aug 06 00:00:00 2026
base hex timestamps absolute
internal events logged

//==============================================================================
// Test Case: TC_Name
// Requirement ID: req_id
// Function ID: fnid
// Purpose: test_purpose
// Preconditions: test_precondition_summary
//==============================================================================

// Step 1: Step description
0.000000 1 740 Tx d 3 10 01 00 00 00 00 00 00
0.010000 1 748 Rx d 2 50 01 00 00 00 00 00 00

// Step 2: Next step description
0.100000 1 740 Tx d 3 22 FE FC 00 00 00 00 00
0.110000 1 748 Rx d 4 62 FE FC 05 00 00 00 00

//==============================================================================
// End of Test Case
//==============================================================================
```

### Message Format Components

```
<timestamp> <ch> <CAN_ID> <dir> d <dlc> <data_bytes>
    ↓        ↓      ↓       ↓    ↓   ↓        ↓
0.000000    1     740     Tx   d   3    10 01 00 00 00 00 00 00
```

- **timestamp**: Seconds from start (e.g., 0.000000)
- **channel**: CAN channel number (default: 1)
- **CAN_ID**: Hexadecimal identifier (740=Tx, 748=Rx)
- **direction**: Tx (transmit) or Rx (receive)
- **d**: Standard CAN (use 'x' for extended 29-bit)
- **dlc**: Data length code (always 8 for standard CAN)
- **data_bytes**: 8 space-separated hex bytes (padded with 0x00)

### File Naming Convention

```
<req_id>_<fnid>_<test_scenario_name>.can
```

Examples:
- `2700204_EDI_FNID_1282_TC01.can`
- `5152763_EDI_FNID_1282_SecurityAccess.can`

## Customization Options

### CAN Identifier Customization

**Default IDs:**
- Tester Tx: 0x740
- ECU Rx: 0x740
- Tester Rx: 0x748
- ECU Tx: 0x748

**Prompt for custom IDs:**
```
Generate .can file using CAN ID 0x7E0 for Tx and 0x7E8 for Rx
```

### Timing Customization

**Default Timing:**
- Inter-step delay: 50ms (0.050000)
- Response wait: 10ms (0.010000)

**Prompt for custom timing:**
```
Generate .can file with 200ms delay between test steps
Generate .can file with 100ms inter-step timing and 50ms response timeout
```

### Channel Customization

**Default:** Channel 1

**Prompt for different channel:**
```
Generate .can file using CAN channel 2
```

### Extended CAN Format

**Prompt for 29-bit IDs:**
```
Generate .can file with extended CAN format (29-bit identifiers)
```

## Common Test Patterns

### Pattern 1: Simple DID Read

**CSV Input:**
```
test_execution: "1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Read DID 0xFEFC
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x62 0xFE 0xFC 0x05]"
```

**Generated .can:**
```
0.000000 1 740 Tx d 3 10 01 00 00 00 00 00 00
0.010000 1 748 Rx d 2 50 01 00 00 00 00 00 00
0.100000 1 740 Tx d 3 22 FE FC 00 00 00 00 00
0.110000 1 748 Rx d 4 62 FE FC 05 00 00 00 00
```

### Pattern 2: Security Access Sequence

**CSV Input:**
```
test_execution: "1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Request Security Seed
**Request data:** [0x27 0x01]
**Response data:** [0x67 0x01 0x12 0x34 0x56 0x78]

3. Send Security Key
**Request data:** [0x27 0x02 0x9A 0xBC 0xDE 0xF0]
**Response data:** [0x67 0x02]

4. Read Protected DID
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x62 0xFE 0xFC 0x05]"
```

**Generated .can:**
```
// Session Control
0.000000 1 740 Tx d 3 10 01 00 00 00 00 00 00
0.010000 1 748 Rx d 2 50 01 00 00 00 00 00 00

// Security Access - Seed
0.100000 1 740 Tx d 2 27 01 00 00 00 00 00 00
0.110000 1 748 Rx d 6 67 01 12 34 56 78 00 00

// Security Access - Key
0.200000 1 740 Tx d 6 27 02 9A BC DE F0 00 00
0.210000 1 748 Rx d 2 67 02 00 00 00 00 00 00

// Read Protected DID
0.300000 1 740 Tx d 3 22 FE FC 00 00 00 00 00
0.310000 1 748 Rx d 4 62 FE FC 05 00 00 00 00
```

### Pattern 3: Negative Response Test

**CSV Input:**
```
test_execution: "1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Attempt Protected Operation
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x7F 0x22 0x7E]
*Note: Expected negative response - service not supported in session*"
```

**Generated .can:**
```
0.000000 1 740 Tx d 3 10 01 00 00 00 00 00 00
0.010000 1 748 Rx d 2 50 01 00 00 00 00 00 00
0.100000 1 740 Tx d 3 22 FE FC 00 00 00 00 00
0.110000 1 748 Rx d 3 7F 22 7E 00 00 00 00 00
// Negative Response: NRC 0x7E - Sub-function not supported in active session
```

### Pattern 4: Wait/Delay Operations

**CSV Input:**
```
test_execution: "1. Read Block Time Status
**Request data:** [0x22 0xF1 0xA0]
**Response data:** [0x62 0xF1 0xA0 0x00 0x00]

2. Wait 600 seconds (10 minutes)
*Note: Simulating block time duration*

3. Read Block Time Status Again
**Request data:** [0x22 0xF1 0xA0]
**Response data:** [0x62 0xF1 0xA0 0x00 0x00]"
```

**Generated .can:**
```
0.000000 1 740 Tx d 3 22 F1 A0 00 00 00 00 00
0.010000 1 748 Rx d 5 62 F1 A0 00 00 00 00 00

// Wait 600 seconds (10 minutes)

600.000000 1 740 Tx d 3 22 F1 A0 00 00 00 00 00
600.010000 1 748 Rx d 5 62 F1 A0 00 00 00 00 00
```

## Integration with CANoe

### Import Generated Files

1. **Using Replay Block:**
   - Add Replay Block to CANoe configuration
   - Import generated .can file
   - Configure timing and trigger settings
   - Execute test sequence

2. **Using Trace Window:**
   - Load .can file into Trace window
   - View and analyze message sequence
   - Export or modify as needed

3. **Using Test Units:**
   - Import .can file as test sequence
   - Define pass/fail criteria
   - Run automated tests
   - Generate test reports

### Typical Workflow

```
CSV Test Spec → Generate .can → Import to CANoe → Execute → Analyze Results
```

## Error Handling

### Common Issues and Solutions

**Issue: Missing Request/Response Data**
- **Detection:** Parser cannot find `**Request data:**` or `**Response data:**`
- **Solution:** Alert user to check test_execution format
- **Prompt:** "Please ensure test_execution contains **Request data:** and **Response data:** markers"

**Issue: Malformed Hex Data**
- **Detection:** Hex bytes not in correct format
- **Solution:** Clean and parse various formats (0xXX, XX, 0xX, X)
- **Prompt:** "Some hex values appear malformed. I'll clean them during generation."

**Issue: Undefined DID**
- **Detection:** DID reference in test but not in object_text
- **Solution:** Use DID as specified in test_execution
- **Prompt:** "DID 0xFEFC referenced but not defined in object_text. Proceeding with test specification."

**Issue: Session/Security Mismatch**
- **Detection:** Protected operation without security access
- **Solution:** Generate as-is but add warning comment
- **Comment:** "// WARNING: This operation may require security access"

## Best Practices

### For Users

1. **Ensure CSV Format:** Verify all required columns are present
2. **Check Hex Data:** Ensure hex values are valid and properly formatted
3. **Review Test Logic:** Verify test sequence makes sense before generation
4. **Specify Parameters:** Provide CAN IDs and timing if different from defaults
5. **Test Incrementally:** Generate and test one file before batch processing

### For Implementation

1. **Parse Robustly:** Handle various hex formats (0xXX, XX, 0x, etc.)
2. **Validate Output:** Ensure all messages are 8 bytes padded
3. **Preserve Context:** Include descriptive comments from test specifications
4. **Handle Errors Gracefully:** Provide clear messages when parsing fails
5. **Support Customization:** Allow overrides for IDs, timing, channel

## Technical Specifications

### UDS Service Response Mapping

| Request Service | Response Service | Format |
|-----------------|------------------|--------|
| 0x10 | 0x50 | Session Control Positive |
| 0x22 | 0x62 | Read DID Positive |
| 0x27 | 0x67 | Security Access Positive |
| 0x2E | 0x6E | Write DID Positive |
| 0x3E | 0x7E | Tester Present Positive |
| Any | 0x7F + Service + NRC | Negative Response |

### Negative Response Codes (NRC)

| NRC | Description |
|-----|-------------|
| 0x11 | Service Not Supported |
| 0x12 | Sub-Function Not Supported |
| 0x13 | Incorrect Message Length |
| 0x22 | Conditions Not Correct |
| 0x31 | Request Out Of Range |
| 0x33 | Security Access Denied |
| 0x7E | Sub-Function Not Supported In Active Session |
| 0x7F | Service Not Supported In Active Session |

### Timing Constants

| Parameter | Default | Adjustable | Notes |
|-----------|---------|------------|-------|
| Inter-step delay | 50ms | Yes | Time between test steps |
| Response timeout | 10ms | Yes | Wait for ECU response |
| Session timeout | 5000ms | Yes | Keep session alive |
| Extended response | 5000ms | Yes | P2* timing for long operations |

## Example Usage Scenarios

### Scenario 1: Basic DID Read Test Generation

**User Request:**
```
Generate a .can file for the DID read test from my CSV file
```

**Expected Action:**
1. Parse CSV file for test case
2. Extract DID read operation
3. Generate .can file with session control and DID read
4. Apply default CAN IDs and timing
5. Save with appropriate filename

### Scenario 2: Complete Test Suite Generation

**User Request:**
```
Generate .can files for all test cases in test_spec_flat_all 5.csv
```

**Expected Action:**
1. Parse entire CSV file
2. Identify all unique test cases
3. Generate separate .can file for each
4. Apply consistent naming convention
5. Report generation summary

### Scenario 3: Custom Configuration

**User Request:**
```
Generate .can files for requirement 2700204 using CAN ID 0x7E0/0x7E8 with 100ms delays
```

**Expected Action:**
1. Filter test cases by requirement ID
2. Apply custom CAN IDs to all messages
3. Apply custom 100ms timing
4. Generate files with custom parameters
5. Verify and save

## Related Files and Resources

- **`.copilot-instructions.md`** - Detailed generation instructions
- **`README.md`** - User guide and documentation
- **`INPUT_EXAMPLES.md`** - Input format examples and workflows
- **`CANOE_UPLOAD_GUIDE.md`** - CANoe integration instructions
- **`QUICK_REFERENCE.md`** - Quick command reference
- **Example files:** `example_*.can` - Sample generated files

## Limitations

### Current Limitations

1. **UDS Only:** Focused on UDS diagnostic protocol, not general CAN messaging
2. **Standard CAN:** Default is 8-byte standard CAN (CAN-FD support via customization)
3. **CSV Input:** Requires specific CSV format with test_execution column
4. **Seed-Key:** Security key calculation is project-specific, not automated
5. **Multi-Frame:** ISO-TP multi-frame supported but not for all edge cases

### Not Supported

- CAN database (.dbc) file generation or modification
- CAPL script generation (separate skill)
- ODX parsing or generation
- Real-time CAN bus communication
- CAN network simulation or node emulation
- Automatic security key calculation (project-specific algorithms)

## Future Enhancements

- Support for ODX input format
- Automatic security key calculation (configurable algorithms)
- CAN-FD format by default
- CAPL script generation integration
- Test report generation from execution results
- Integration with test management systems

## Support and Troubleshooting

### Getting Help

Users can ask:
- "How do I format my CSV for .can generation?"
- "What CAN IDs should I use?"
- "How do I upload generated files to CANoe?"
- "Why is my .can file not importing correctly?"
- "Can you regenerate with different timing?"

### Common Questions

**Q: What if my ECU uses different CAN IDs?**
A: Specify custom IDs in your generation prompt: "Generate .can file using CAN ID 0xXXX for Tx and 0xYYY for Rx"

**Q: How do I handle security key calculation?**
A: Security key calculation is project-specific. Generate .can file with placeholder key, then manually update or use CAPL for runtime calculation.

**Q: Can I batch generate with different parameters?**
A: Yes, but parameters apply to all files in batch. For different parameters per file, generate individually.

**Q: How do I verify generated .can files are correct?**
A: Review generated file comments, check CAN IDs match your ECU, verify timing is appropriate, import to CANoe and test.

---

## Skill Metadata

- **Version:** 1.0
- **Last Updated:** 2026-08-06
- **Domains:** Automotive, ECU Testing, UDS Diagnostics, CAN Communication
- **Languages:** English
- **Tools:** CANoe, CANalyzer, Vector Hardware
- **Protocols:** ISO 14229 (UDS), ISO 15765 (ISO-TP), ISO 11898 (CAN)
- **File Formats:** .can (CANoe ASCII), .csv (input), .dbc (optional)

## Keywords

UDS, CAN, CANoe, CANalyzer, diagnostic, test generation, automotive, ECU, ISO-TP, DID, session control, security access, test automation, Vector, test specification, message sequence, replay block, trace analysis

---

**End of Skill Definition**
