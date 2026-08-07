# Input Examples and File Generation Guide

## Table of Contents
1. [CSV Input Format](#csv-input-format)
2. [Example Test Cases](#example-test-cases)
3. [How to Generate .CAN Files](#how-to-generate-can-files)
4. [Batch Generation](#batch-generation)
5. [Customization Options](#customization-options)
6. [Upload to CANoe](#upload-to-canoe)

---

## CSV Input Format

Your CSV file must contain these columns:

| Column Name | Required | Description | Example |
|-------------|----------|-------------|---------|
| req_id | Yes | Requirement identifier | 2700204 |
| fnid | Yes | Function identifier | EDI_FNID_1282 |
| object_text | Yes | Requirement description with DID specs | "DID FEFC - DevMsgEnable..." |
| verification_criteria | Yes | How to verify the test | "Verify DID can be accessed..." |
| test_purpose | Yes | Purpose of the test | "Verify block time triggered..." |
| test_scenario | Yes | Test scenario with I/O table | "\| Input \| Output \|..." |
| test_precondition | Yes | Prerequisites | "ECU powered on..." |
| test_execution | Yes | Step-by-step execution with UDS commands | "1. Establish Default Session..." |

### Critical: test_execution Format

The `test_execution` column must follow this format:

```
1. [Step Description]
**Request data:** [0xXX 0xYY 0xZZ]
**Response data:** [0xAA 0xBB 0xCC]
*Note: [Optional notes]*

2. [Next Step Description]
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]
```

**Key Requirements:**
- ✅ Steps must be numbered (1., 2., 3., etc.)
- ✅ Request data must have `**Request data:**` marker
- ✅ Response data must have `**Response data:**` marker
- ✅ Hex data must be in square brackets `[0xXX 0xYY]`
- ✅ Use `0x` prefix for hex values
- ✅ Separate hex bytes with spaces

---

## Example Test Cases

### Example 1: Simple DID Read Test

**CSV Row:**
```csv
req_id,fnid,object_text,verification_criteria,test_purpose,test_scenario,test_precondition,test_execution
5152763,EDI_FNID_1282,"DID FEFC DevMsgEnable Read/Write access in Extended session","Verify DID can be accessed in defined session","Verify DID FEFC is readable in extended diagnostic session","| Input: Session | Output: Access | | Extended | Allowed |","ECU powered on, diagnostic tool connected","1. Establish Extended Diagnostic Session
**Request data:** [0x10 0x03]
**Response data:** [0x50 0x03]

2. Read DID 0xFEFC
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x62 0xFE 0xFC 0x05]
*Note: Response data byte indicates DevMsgEnable state*"
```

### Example 2: Security Access Test

**CSV Row:**
```csv
req_id,fnid,object_text,verification_criteria,test_purpose,test_scenario,test_precondition,test_execution
5153105,EDI_FNID_1282,"Security Level: Bosch, InCar1","Verify DID accessible at Bosch security level","Test Bosch security access for protected DID","| Input: Security | Output: Access | | Bosch | Allowed |","ECU powered on, diagnostic tool connected","1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Request Security Seed (Bosch Level 0x01)
**Request data:** [0x27 0x01]
**Response data:** [0x67 0x01 0x12 0x34 0x56 0x78]
*Note: Seed received: 0x12345678*

3. Send Security Key (calculated from seed)
**Request data:** [0x27 0x02 0x9A 0xBC 0xDE 0xF0]
**Response data:** [0x67 0x02]
*Note: Security access granted*

4. Read Protected DID
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x62 0xFE 0xFC 0x05]"
```

### Example 3: Negative Response Test

**CSV Row:**
```csv
req_id,fnid,object_text,verification_criteria,test_purpose,test_scenario,test_precondition,test_execution
2700205,EDI_FNID_1283,"DID access denied in wrong session","Verify DID cannot be accessed outside defined session","Test that DID is protected in default session","| Input: Session | Output: Access | | Default | Denied |","ECU powered on, diagnostic tool connected","1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Attempt to read protected DID
**Request data:** [0x22 0xFE 0xFC]
**Response data:** [0x7F 0x22 0x7E]
*Note: Negative response - Sub-function not supported in active session (NRC 0x7E)*"
```

### Example 4: Block Time Test with Wait

**CSV Row:**
```csv
req_id,fnid,object_text,verification_criteria,test_purpose,test_scenario,test_precondition,test_execution
2700204,EDI_FNID_1282,"Block time triggered after 2 false attempts","Verify 10 minute block time after 2 failed attempts","Test block time mechanism with false access attempts","| Input: Attempts | Output: Block Time | | 2 | 10 minutes |","ECU powered on, no previous false attempts","1. Establish Default Session
**Request data:** [0x10 0x01]
**Response data:** [0x50 0x01]

2. Read Block Time Status before attempts
**Request data:** [0x22 0xF1 0xA0]
**Response data:** [0x62 0xF1 0xA0 0x00 0x00]
*Note: No block time active*

3. Wait 600 seconds (10 minutes)
*Note: Simulating time passage to verify block time duration*

4. Read Block Time Status after wait
**Request data:** [0x22 0xF1 0xA0]
**Response data:** [0x62 0xF1 0xA0 0x00 0x00]
*Note: Block time cleared after duration*"
```

---

## How to Generate .CAN Files

### Method 1: Generate Single Test Case

**Prompt to Copilot:**
```
Generate a .can file for test case TC_ReadDID_FEFC from test_spec_flat_all 5.csv
```

**Expected Output:**
- File: `5152763_EDI_FNID_1282_TC_ReadDID_FEFC.can`
- Contains: Properly formatted CAN messages with timing

### Method 2: Generate by Requirement ID

**Prompt to Copilot:**
```
Generate all .can files for requirement ID 2700204 from the CSV
```

**Expected Output:**
- Multiple .can files for all test cases under that requirement
- Each file contains one complete test case

### Method 3: Generate All Test Cases

**Prompt to Copilot:**
```
Generate .can files for all test cases in test_spec_flat_all 5.csv
```

**Expected Output:**
- One .can file per test case
- Files organized by requirement ID and function ID

### Method 4: Generate with Custom Parameters

**Prompt to Copilot:**
```
Generate .can file for test case TC_NoFalseAccessAttempts_NoBlockTime using:
- CAN ID 0x7E0 for Tx (tester to ECU)
- CAN ID 0x7E8 for Rx (ECU to tester)
- 100ms delay between test steps
- Channel 2 instead of channel 1
```

**Expected Output:**
- Customized .can file with specified parameters

---

## Batch Generation

### Generate by Function ID

**Prompt:**
```
Generate all .can files for function ID EDI_FNID_1282
```

### Generate with Filtering

**Prompt:**
```
Generate .can files only for test cases that include security access from the CSV
```

### Generate with Naming Convention

**Prompt:**
```
Generate .can files with naming format: TEST_<testcase_name>_<date>.can
```

---

## Customization Options

### Option 1: Custom CAN IDs

Different ECUs use different CAN identifiers. Customize as needed:

**Common CAN ID Mappings:**
| ECU Type | Tester Tx | ECU Rx | Tester Rx | ECU Tx |
|----------|-----------|--------|-----------|--------|
| Generic | 0x7DF | 0x7DF | 0x7E8-7EF | 0x7E8-7EF |
| Physical (Default) | 0x740 | 0x740 | 0x748 | 0x748 |
| Custom ECU 1 | 0x7E0 | 0x7E0 | 0x7E8 | 0x7E8 |
| Custom ECU 2 | 0x700 | 0x700 | 0x708 | 0x708 |

**Prompt Example:**
```
Generate .can files using CAN ID 0x7E0 for requests and 0x7E8 for responses
```

### Option 2: Custom Timing

Adjust timing based on ECU response characteristics:

**Timing Profiles:**
| Profile | Inter-step Delay | Response Wait | Use Case |
|---------|------------------|---------------|----------|
| Fast | 10ms | 5ms | Quick regression tests |
| Standard | 50ms | 10ms | Normal testing (default) |
| Slow | 200ms | 50ms | Slow ECU or CAN bus |
| Real-time | 1000ms | 100ms | Simulating real conditions |

**Prompt Example:**
```
Generate .can file with 200ms delay between steps and 50ms wait for responses
```

### Option 3: Channel Assignment

For multi-channel CAN setups:

**Prompt Example:**
```
Generate .can files using CAN channel 2 for all messages
```

### Option 4: Extended CAN Format

For 29-bit CAN identifiers:

**Prompt Example:**
```
Generate .can file with extended CAN format (29-bit identifiers)
```

---

## Upload to CANoe

### Step 1: Prepare CANoe Environment

1. **Open Vector CANoe**
   - Launch CANoe application
   - Create or open your test configuration

2. **Configure CAN Channels**
   - Go to `Configuration` → `Hardware Configuration`
   - Ensure CAN channels match your .can file (default: Channel 1)
   - Set correct baudrate (typically 500 kbit/s for automotive)

3. **Create Replay Block**
   - In CANoe configuration window, add a `Replay Block`
   - Right-click in measurement setup → `Insert` → `Replay Block`

### Step 2: Import .CAN File

#### Method A: Using Replay Block

1. **Open Replay Block Configuration**
   - Double-click the Replay Block in your configuration
   - Click `Add` or `Import` button

2. **Select .CAN File**
   - Browse to your generated .can file location
   - Example: `c:\Users\JAO9KOR\Desktop\new_prompt\2700204_EDI_FNID_1282_TC01.can`
   - Click `Open`

3. **Configure Replay Settings**
   - **Play Mode**: Sequential (plays messages in order)
   - **Timing**: Use original timing (from .can file)
   - **Loop**: Enable if you want continuous replay
   - **Trigger**: Set start trigger (e.g., manual, key press, or automatic)

4. **Verify Import**
   - Preview messages in the Replay Block window
   - Check that all CAN IDs and data bytes are correct
   - Verify timing looks appropriate

#### Method B: Using Trace Window

1. **Open Trace Window**
   - Go to `Analysis` → `Trace`
   - Or press `Ctrl + T`

2. **Load .CAN File**
   - Right-click in Trace window
   - Select `Load Trace File...`
   - Browse to your .can file
   - Click `Open`

3. **View Messages**
   - All messages displayed in chronological order
   - You can filter, search, and analyze
   - Export or copy messages as needed

#### Method C: Using CAPL Script

1. **Create CAPL Test Module**
   ```
   // File: TestReplay.can
   includes
   {
     // Add necessary includes
   }
   
   on start
   {
     // Load and replay .can file
     replayFile("c:\\path\\to\\your\\file.can");
   }
   ```

2. **Add to Configuration**
   - Add Test Module to CANoe configuration
   - Configure to run on measurement start

### Step 3: Execute Test in CANoe

#### Using Replay Block

1. **Start Measurement**
   - Click `Start Measurement` button (F5)
   - Or go to `Measurement` → `Start`

2. **Trigger Replay**
   - If manual trigger: Click `Start` in Replay Block window
   - If automatic: Replay starts automatically with measurement

3. **Monitor Execution**
   - Watch Trace window for transmitted and received messages
   - Verify responses match expected values
   - Check timing is as expected

4. **Observe Results**
   - Tx messages appear with configured CAN ID (e.g., 0x740)
   - Rx messages should come from ECU (e.g., 0x748)
   - Green = successful transmission
   - Red = errors or no response

#### Using Interactive Generator

1. **Open Interactive Generator Panel**
   - Go to `Measurement` → `Interactive Generator`
   - Or add to configuration as a panel

2. **Import Messages**
   - Click `Import` → `From File`
   - Select your .can file
   - Messages loaded into generator

3. **Send Messages**
   - Select message to send
   - Click `Send` button
   - Or enable cyclic sending with time interval

### Step 4: Verify Test Results

1. **Check Trace Window**
   ```
   Time      Channel  ID   Dir  DLC  Data
   0.000000  1        740  Tx   3    10 01 00 00 00 00 00 00
   0.010000  1        748  Rx   2    50 01 00 00 00 00 00 00  ✓ Positive Response
   0.100000  1        740  Tx   3    22 FE FC 00 00 00 00 00
   0.110000  1        748  Rx   4    62 FE FC 05 00 00 00 00  ✓ Data Received
   ```

2. **Analyze Responses**
   - ✅ Positive responses (e.g., 0x50, 0x62, 0x67) = PASS
   - ❌ Negative responses (0x7F) = Check NRC code
   - ⚠️ No response = Timeout or communication issue

3. **Save Test Report**
   - Go to `Analysis` → `Logging` → `Save Log`
   - Export trace to file
   - Generate test report with results

### Step 5: Advanced CANoe Integration

#### Create Test Units

1. **Open Test Feature Set**
   - Go to `Test` → `Test Feature Set`
   - Or add Test Units to configuration

2. **Import Test Sequence**
   - Create XML test specification from .can file
   - Define pass/fail criteria
   - Add to automated test suite

3. **Run Automated Tests**
   - Execute test sequence
   - Generate test report automatically
   - Log results for traceability

#### Use with vTESTstudio

1. **Import .CAN File**
   - Open Vector vTESTstudio
   - Create new test case
   - Import .can file as test sequence

2. **Define Test Steps**
   - Each CAN message becomes a test step
   - Set expected responses
   - Define timing constraints

3. **Execute and Report**
   - Run test in CANoe via vTESTstudio
   - Automatic pass/fail evaluation
   - Generate test documentation

---

## Complete Workflow Example

### From CSV to CANoe Test Execution

**Step 1: Prepare CSV File**
```
test_spec_flat_all 5.csv (your input file)
```

**Step 2: Generate .CAN File**

**Prompt to Copilot:**
```
Generate a .can file for test case TC_NoFalseAccessAttempts_NoBlockTime from test_spec_flat_all 5.csv with CAN ID 0x740/0x748 and 50ms timing
```

**Step 3: Verify Generated File**

Check output file: `2700204_EDI_FNID_1282_TC01.can`

```
date Wed Aug 06 00:00:00 2026
base hex timestamps absolute
internal events logged

// Test Case: TC_NoFalseAccessAttempts_NoBlockTime
// Requirement: 2700204

0.000000 1 740 Tx d 3 10 01 00 00 00 00 00 00
0.010000 1 748 Rx d 2 50 01 00 00 00 00 00 00
...
```

**Step 4: Import to CANoe**

1. Open CANoe
2. Add Replay Block
3. Import `2700204_EDI_FNID_1282_TC01.can`
4. Configure channel and timing
5. Connect to ECU (real or simulated)

**Step 5: Execute Test**

1. Start measurement (F5)
2. Trigger replay
3. Monitor trace window
4. Verify responses

**Step 6: Analyze Results**

1. Check all responses are positive (0x50, 0x62, etc.)
2. Verify data values match expected
3. Check timing is correct
4. Save test log

**Step 7: Generate Report**

1. Export trace log
2. Create test report
3. Document pass/fail status
4. Archive results

---

## Troubleshooting Upload Issues

### Issue 1: CAN IDs Not Recognized

**Problem:** CANoe doesn't show messages or shows wrong ECU
**Solution:**
- Verify CAN database (.dbc file) includes your CAN IDs
- Check that 0x740 (Tx) and 0x748 (Rx) are defined
- Regenerate .can file with correct IDs for your ECU

**Prompt:**
```
Regenerate .can file with CAN ID 0x7E0 for Tx and 0x7E8 for Rx
```

### Issue 2: Timing Issues

**Problem:** ECU doesn't respond or messages overlap
**Solution:**
- Increase delay between messages
- Check ECU response time requirements
- Adjust timing in .can file

**Prompt:**
```
Regenerate .can file with 200ms delay between steps
```

### Issue 3: File Format Errors

**Problem:** CANoe can't parse .can file
**Solution:**
- Check file header is correct
- Verify all messages are 8 bytes (padded)
- Ensure timestamps are in correct format

**Prompt:**
```
Regenerate .can file with strict CANoe format compliance
```

### Issue 4: Channel Mismatch

**Problem:** Messages appear on wrong channel
**Solution:**
- Check CANoe channel configuration
- Update .can file to use correct channel

**Prompt:**
```
Regenerate .can file using channel 2 instead of channel 1
```

### Issue 5: No ECU Response

**Problem:** Tx messages sent but no Rx received
**Solution:**
1. Verify ECU is powered and connected
2. Check CAN baudrate (500 kbit/s typical)
3. Verify termination resistors
4. Check ECU is in correct mode (ignition on, etc.)
5. Try extended timing with longer response waits

---

## Quick Command Reference

### Generate Single File
```
Generate .can file for test case [name] from [CSV file]
```

### Generate Multiple Files
```
Generate all .can files for requirement [req_id] from [CSV file]
```

### Custom CAN IDs
```
Generate .can file with CAN ID [0xXXX] for Tx and [0xYYY] for Rx
```

### Custom Timing
```
Generate .can file with [XXX]ms delay between steps
```

### Custom Channel
```
Generate .can file using channel [N]
```

### Batch with Options
```
Generate all .can files from [CSV file] using CAN ID 0x7E0/0x7E8 with 100ms timing on channel 2
```

---

## Additional Resources

### File Locations
- **Input CSV:** `test_spec_flat_all 5.csv`
- **Generated .CAN files:** `c:\Users\JAO9KOR\Desktop\new_prompt\*.can`
- **Instructions:** `.copilot-instructions.md`
- **Examples:** `example_*.can`

### Reference Files
- `README.md` - Complete documentation
- `QUICK_REFERENCE.md` - Quick reference guide
- `.copilot-instructions.md` - Generation instructions
- `SKILL.md` - Copilot skill definition

### Need Help?

Ask Copilot:
```
How do I upload .can files to CANoe?
What CAN IDs should I use for my ECU?
How do I fix timing issues in generated .can files?
Explain the CANoe replay block configuration
Generate .can file with detailed comments for CANoe import
```

---

**Document Version:** 1.0  
**Last Updated:** 2026-08-06  
**Author:** CAN File Generator System
