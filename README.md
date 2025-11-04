Of course. This is an excellent and crucial step for making the documentation truly useful. Tying the HMI file back to the PLC logic and I/O lists is what makes the project maintainable.

I have updated the `readme.md` file to include a new section, "5. HMI (Human-Machine Interface) Correlation," which describes the manual control screens and provides clear instructions on how to modify the HMI addresses if the PLC I/O list changes.

---

# Omron PLC Code Generation for Manual Pneumatic Controls

This document serves as a guide and template for generating PLC mnemonic code and I/O lists for Omron PLCs. The focus is on creating standardized manual controls for single and double-acting pneumatic cylinders.

The structured and repetitive nature of this code is ideal for generation and modification in a text editor like Visual Studio Code, using features like multi-cursor editing and find/replace to rapidly create code for large numbers of devices.

## 1. Understanding the PLC Mnemonic Logic

The code is written in **Mnemonic code** (also known as Statement List), a text-based programming language for PLCs. The logic uses a few core instructions.

*   **`LD` (Load):** Starts a new line (or rung) of logic.
*   **`AND`:** Performs a logical AND operation with the previous result.
*   **`OUT`:** Writes the result of the logic to a bit or coil.
*   **`KEEP(011)`:** A latching relay, equivalent to a Set/Reset flip-flop. It has two inputs: the top line is the **Set** condition, and the bottom line is the **Reset** condition.

### Master Control Logic

All manual controls are enabled by a master condition. This ensures that manual buttons are only active when the machine is in a safe, manual state. This logic is stored in a temporary relay (`TR0`) for efficiency.

```MNEMONIC
LD      MCR
AND     ManualOn
OUT     TR0
```

This means `TR0` is ON only when the `MCR` (Master Control Relay) is active AND the `ManualOn` input is ON.

## 2. Mnemonic Code Templates

Below are the standard building blocks for controlling pneumatic cylinders.

### Single Solenoid Control

This logic is for a spring-return cylinder. The `KEEP` instruction latches the output, so the operator does not need to hold the button down.

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

### Double Solenoid Control

This logic is for a cylinder with two solenoids: one to extend (`_A`) and one to retract (`_B`). The logic is complementary to ensure that both solenoids cannot be energized at the same time.

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

## 3. Complete Mnemonic Code (PC1-PC20)

Here is the complete manual control program for 20 cylinders, where **PC9, PC10, PC11, and PC12 are double solenoids** and the rest are single.

```MNEMONIC
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC1_A_MOn
LD      TR0
AND     PC1_A_MOff
KEEP(011) PC1_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC2_A_MOn
LD      TR0
AND     PC2_A_MOff
KEEP(011) PC2_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC3_A_MOn
LD      TR0
AND     PC3_A_MOff
KEEP(011) PC3_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC4_A_MOn
LD      TR0
AND     PC4_A_MOff
KEEP(011) PC4_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC5_A_MOn
LD      TR0
AND     PC5_A_MOff
KEEP(011) PC5_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC6_A_MOn
LD      TR0
AND     PC6_A_MOff
KEEP(011) PC6_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC7_A_MOn
LD      TR0
AND     PC7_A_MOff
KEEP(011) PC7_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC8_A_MOn
LD      TR0
AND     PC8_A_MOff
KEEP(011) PC8_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC9_A_MOn
LD      TR0
AND     PC9_A_MOff
KEEP(011) PC9_A
LD      TR0
AND     PC9_A_MOff
LD      TR0
AND     PC9_A_MOn
KEEP(011) PC9_B
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC10_A_MOn
LD      TR0
AND     PC10_A_MOff
KEEP(011) PC10_A
LD      TR0
AND     PC10_A_MOff
LD      TR0
AND     PC10_A_MOn
KEEP(011) PC10_B
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
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC12_A_MOn
LD      TR0
AND     PC12_A_MOff
KEEP(011) PC12_A
LD      TR0
AND     PC12_A_MOff
LD      TR0
AND     PC12_A_MOn
KEEP(011) PC12_B
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC13_A_MOn
LD      TR0
AND     PC13_A_MOff
KEEP(011) PC13_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC14_A_MOn
LD      TR0
AND     PC14_A_MOff
KEEP(011) PC14_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC15_A_MOn
LD      TR0
AND     PC15_A_MOff
KEEP(011) PC15_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC16_A_MOn
LD      TR0
AND     PC16_A_MOff
KEEP(011) PC16_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC17_A_MOn
LD      TR0
AND     PC17_A_MOff
KEEP(011) PC17_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC18_A_MOn
LD      TR0
AND     PC18_A_MOff
KEEP(011) PC18_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC19_A_MOn
LD      TR0
AND     PC19_A_MOff
KEEP(011) PC19_A
LD      MCR
AND     ManualOn
OUT     TR0
AND     PC20_A_MOn
LD      TR0
AND     PC20_A_MOff
KEEP(011) PC20_A
```

## 4. I/O List Configuration

The I/O lists map the symbolic variable names used in the code (`PC1_A_MOn`, etc.) to physical hardware addresses (`Word.Bit`). The format is a tab-separated list: `Name Type Address Comment Initial_Value`.

### Input List (Manual Pushbuttons)

This list defines the addresses for the On/Off pushbuttons for all 20 cylinders, starting at address `54.06`.

```
PC1_A_MOn	BOOL	54.06		0	
PC1_A_MOff	BOOL	54.07		0	
PC2_A_MOn	BOOL	54.08		0	
PC2_A_MOff	BOOL	54.09		0	
PC3_A_MOn	BOOL	54.10		0	
PC3_A_MOff	BOOL	54.11		0	
PC4_A_MOn	BOOL	54.12		0	
PC4_A_MOff	BOOL	54.13		0	
PC5_A_MOn	BOOL	54.14		0	
PC5_A_MOff	BOOL	54.15		0	
PC6_A_MOn	BOOL	55.00		0	
PC6_A_MOff	BOOL	55.01		0	
PC7_A_MOn	BOOL	55.02		0	
PC7_A_MOff	BOOL	55.03		0	
PC8_A_MOn	BOOL	55.04		0	
PC8_A_MOff	BOOL	55.05		0	
PC9_A_MOn	BOOL	55.06		0	
PC9_A_MOff	BOOL	55.07		0	
PC10_A_MOn	BOOL	55.08		0	
PC10_A_MOff	BOOL	55.09		0	
PC11_A_MOn	BOOL	55.10		0	
PC11_A_MOff	BOOL	55.11		0	
PC12_A_MOn	BOOL	55.12		0	
PC12_A_MOff	BOOL	55.13		0	
PC13_A_MOn	BOOL	55.14		0	
PC13_A_MOff	BOOL	55.15		0	
PC14_A_MOn	BOOL	56.00		0	
PC14_A_MOff	BOOL	56.01		0	
PC15_A_MOn	BOOL	56.02		0	
PC15_A_MOff	BOOL	56.03		0	
PC16_A_MOn	BOOL	56.04		0	
PC16_A_MOff	BOOL	56.05		0	
PC17_A_MOn	BOOL	56.06		0	
PC17_A_MOff	BOOL	56.07		0	
PC18_A_MOn	BOOL	56.08		0	
PC18_A_MOff	BOOL	56.09		0	
PC19_A_MOn	BOOL	56.10		0	
PC19_A_MOff	BOOL	56.11		0	
PC20_A_MOn	BOOL	56.12		0	
PC20_A_MOff	BOOL	56.13		0	
```

### Output List (Solenoid Valves)

This list defines the addresses for the solenoid outputs based on the physical hardware layout.

*   **Card 1 (starts at address `3.00`):** Contains PC9-12 (double), followed by PC1-8 (single).
*   **Card 2 (starts at address `4.00`):** Contains PC13-20 (single).

```
PC9_A	BOOL	3.00		0	
PC9_B	BOOL	3.01		0	
PC10_A	BOOL	3.02		0	
PC10_B	BOOL	3.03		0	
PC11_A	BOOL	3.04		0	
PC11_B	BOOL	3.05		0	
PC12_A	BOOL	3.06		0	
PC12_B	BOOL	3.07		0	
PC1_A	BOOL	3.08		0	
PC2_A	BOOL	3.09		0	
PC3_A	BOOL	3.10		0	
PC4_A	BOOL	3.11		0	
PC5_A	BOOL	3.12		0	
PC6_A	BOOL	3.13		0	
PC7_A	BOOL	3.14		0	
PC8_A	BOOL	3.15		0	
PC13_A	BOOL	4.00		0	
PC14_A	BOOL	4.01		0	
PC15_A	BOOL	4.02		0	
PC16_A	BOOL	4.03		0	
PC17_A	BOOL	4.04		0	
PC18_A	BOOL	4.05		0	
PC19_A	BOOL	4.06		0	
PC20_A	BOOL	4.07		0	
```

---

## 5. HMI (Human-Machine Interface) Correlation

The `omron_hmi.xml` file defines the graphical screens an operator uses to interact with the PLC. The link between the HMI and the PLC logic is the **I/O Address**.

### 5.1. Overview of Manual Control Screens

The HMI project contains two primary screens for manual control:
*   **`MANUAL_1` (ProjectID="11"):** Contains buttons for PC1 through PC10.
*   **`MANUAL_2` (ProjectID="12"):** Contains buttons for PC11 through PC20.

These screens feature momentary pushbuttons that allow an operator to manually turn the pneumatic cylinder solenoids ON and OFF, provided the master `ManualOn` and `MCR` conditions are met.

### 5.2. How the HMI Buttons Work (The Correlation)

Each button on the manual screens is configured to **write** to a specific bit in the PLC's **CIO (Core I/O) memory area**. This is the trigger for our PLC logic.

Let's examine the **PC1 ON** button from the `MANUAL_1` screen as an example.

1.  **The Button in the XML:**
    Inside the `<Window ProjectID="11" Name="MANUAL_1">` section, you will find a `<Part Type="Switch">` with the label `PC1$_enter_$ON`.

2.  **The Critical `Write` Address:**
    Within that `<Part>` block, the `Write` address is defined. This is the memory location the HMI will turn ON when the button is pressed.
    ```xml
    <Part Type="Switch" Name="SW0" ... >
      ...
      <Address Function="Write" ... >
        <AddressType ...>CIO_IR_bit</AddressType>
        <AddressValue ...>54.06</AddressValue>
      </Address>
      ...
    </Part>
    ```    *   **`AddressType CIO_IR_bit`**: Specifies the memory area is the main **Core I/O** area, and the target is a single **bit**.
    *   **`AddressValue 54.06`**: This is the exact PLC address the button controls.

3.  **The Link to the I/O List:**
    This address `54.06` directly corresponds to our PLC Input List:
    ```
    PC1_A_MOn    BOOL    54.06        0
    ```
    When the operator presses this button, bit `54.06` in the PLC turns ON, making the `PC1_A_MOn` variable in our mnemonic code TRUE and firing the logic.

### 5.3. CRUCIAL: How to Modify HMI Addresses

If you need to change an address in the PLC I/O list, you **must** update the corresponding address in the `omron_hmi.xml` file. Otherwise, the HMI button will not control the correct PLC logic.

**Scenario:** Let's say you change the input for `PC1_A_MOn` from `54.06` to a new address, `70.00`.

**Step-by-Step Instructions:**

1.  **Update the PLC I/O List:**
    First, change the address in your I/O list documentation. This is your project's source of truth.
    *   **Before:** `PC1_A_MOn    BOOL    54.06        0`
    *   **After:**  `PC1_A_MOn    BOOL    70.00        0`

2.  **Find the Button in the XML:**
    *   Open the `omron_hmi.xml` file in VS Code.
    *   Use the "Find" feature (Ctrl+F). The easiest way to find the correct button is to search for its label, such as **`PC1$_enter_$ON`**.

3.  **Modify the `Write` Address Value:**
    *   Once you have located the `<Part Type="Switch">` block for that button, find the `<Address Function="Write">` section within it.
    *   Change the value inside the `<AddressValue>` tag from the old address to the new one.

    **Before:**
    ```xml
    <Address Function="Write" ...>
      <AddressValue ...>54.06</AddressValue>
    </Address>
    ```

    **After:**
    ```xml
    <Address Function="Write" ...>
      <AddressValue ...>70.00</AddressValue>
    </Address>
    ```

4.  **Save and Repeat:**
    Save the `omron_hmi.xml` file. Repeat this find-and-replace process for every I/O address that has been changed. Both the "ON" and "OFF" buttons for a cylinder will need to be updated if their addresses change.