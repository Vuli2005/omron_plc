# Omron PLC Code Generation for Manual Pneumatic Controls

This document serves as a guide and template for generating PLC mnemonic code and I/O lists for Omron PLCs. The focus is on creating standardized manual controls for single and double-acting pneumatic cylinders.

The structured and repetitive nature of this code is ideal for generation and modification in a text editor like Visual Studio Code, using features like multi-cursor editing and find/replace to rapidly create code for large numbers of devices.

## 1. PLC Mnemonic Logic Explained

The code is written in **Mnemonic code** (also known as Statement List).

*   **`LD` (Load):** Starts a new line of logic.
*   **`AND`:** Performs a logical AND operation.
*   **`OUT`:** Writes the result of the logic to a bit.
*   **`KEEP(011)`:** A latching relay (Set/Reset flip-flop). The top line is the **Set** condition, and the bottom line is the **Reset** condition.

### Master Control Logic

All manual controls are enabled by a master condition to ensure they are only active when the machine is in a safe, manual state. This logic is stored in a temporary relay (`TR0`) for efficiency.

```MNEMONIC
LD      MCR
AND     ManualOn
OUT     TR0
```

## 2. Mnemonic Code Templates

The following templates are used to build the complete logic. The full program, following these patterns for all 20 cylinders, should be maintained in a separate file (e.g., `manual_logic.txt`).
Never add comments and never leave empty lines anywhere inside mnemonic code blocks — no leading, trailing, or in-between blank lines are allowed. Each mnemonic line must follow immediately after the previous line.

### Single Solenoid Control Template
Used for spring-return cylinders.
*   **`_MOn` button:** SETS (latches ON) the output `PCx_A`.
*   **`_MOff` button:** RESETS (latches OFF) the output `PCx_A`.
```MNEMONIC
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC1_A_MOn
LD      TR0
AND     PC1_A_MOff
KEEP(011) PC1_A
```

### Double Solenoid Control Template
Used for cylinders with both an extend (`_A`) and retract (`_B`) solenoid.
*   **`_MOn` button:** SETS `PCx_A` and RESETS `PCx_B`.
*   **`_MOff` button:** RESETS `PCx_A` and SETS `PCx_B`.
```MNEMONIC
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC11_A_MOn
LD      TR0
AND     PC11_A_MOff
KEEP(011) PC11_A
LD      TR0
AND     PC11_A_MOff
LD      TR0
AND     PC11_A_MOn
KEEP(011) PC11_B
```

## 3. I/O List Structure and Examples

The full I/O lists should be maintained in separate, tab-separated files. These examples show the required format and naming conventions.

**Format:** `Name<tab>Type<tab>Address<tab><tab>Initial_Value`

### Input List (Manual Pushbuttons)
These are the momentary inputs from the HMI. The full list can be found in `current_io_list.csv`.

**Example:**
```
PC1_A_MOn	BOOL	54.06		0	
PC1_A_MOff	BOOL	54.07		0	
PC2_A_MOn	BOOL	54.08		0	
PC2_A_MOff	BOOL	54.09		0
```

### Input List (Cylinder Sensors)
These are the physical sensor inputs that confirm cylinder position. `_R` is Retracted, `_E` is Extended. The full list can be found in `current_io_list.csv`.

**Example:**
```
PC1_R	BOOL	1.00		0	
PC1_E	BOOL	1.01		0	
PC2_R	BOOL	1.02		0	
PC2_E	BOOL	1.03		0
```

### Output List (Solenoid Valves)
These are the physical outputs that energize the solenoid coils. Double solenoid will have both `_A` and `_B` outputs defined. The full list can be found in `current_io_list.csv`.

**Example:**
```
PC1_A	BOOL	3.00		0	
PC1_B	BOOL	3.01		0	
...
PC2_A	BOOL	3.08		0	
PC3_A	BOOL	3.09		0
```

## 4. HMI (Human-Machine Interface) Correlation

The `omron_hmi.xml` file defines the graphical screens (`MANUAL_1`, `MANUAL_2`) that an operator uses. The link between the HMI and the PLC logic is the **I/O Address**.

### 4.1. How HMI Buttons Work

Each button has two key functions tied to PLC addresses in the **CIO memory area**:
1.  **Write Address (The Command):** When a button is pressed, the HMI writes to a specific PLC bit. This bit corresponds to a variable in the `current_io_list.csv` list (e.g., `PC1_A_MOn` at `54.06`) and triggers the PLC logic.
2.  **Read Address (The Feedback):** The button reads a separate PLC bit to change its appearance (e.g., light up). This provides visual confirmation to the operator that the action was successful.

### 4.2. Validating HMI Feedback (Read Addresses)

For reliable feedback, the HMI button's `Read` address **must** be mapped to the appropriate physical sensor from the `current_io_list.csv` list.

*   **Correct:** The "PC1 ON" button reads address `1.01`, which is the `PC1_E` (Extended) sensor. The button lights up only when the cylinder is physically extended.
*   **Incorrect:** A button reads its own command bit (e.g., `54.06`). This is unreliable, as it only confirms the command was sent, not that the action was completed.

**Action:** The HMI file should be audited to ensure all manual buttons on the `MANUAL_1` and `MANUAL_2` screens use the correct sensor addresses for their `Read` function.

### 4.3. CRUCIAL: How to Modify HMI Addresses

If you change an address in any of the I/O lists, you **must** update the corresponding address in the `omron_hmi.xml` file.

**Scenario:** Change the sensor for `PC1_E` from `1.01` to a new address, `80.05`.

**Instructions:**
1.  **Open `omron_hmi.xml`** in VS Code.
2.  **Find the button** by searching for its label (e.g., `PC1$_enter_$ON`).
3.  **Modify the `Read` Address:** Locate the `<Address Function="Read">` block for that button and change the `<AddressValue>` from `1.01` to `80.05`.
4.  **Modify the `Write` Address:** If you change a button command address (e.g., `PC1_A_MOn`), locate the `<Address Function="Write">` block and update its `<AddressValue>`.
5.  **Save the file.** Repeat for all changed addresses.