# Module 4: Peripheral Interfacing with 8051

> **Exam Focus**: Interfacing theory + programs. Expect 1 × 12-mark (a/b) + 1 × 8-mark.
> Must know: Pin connections, explain how each device works, and write complete assembly programs with comments.
> Key devices: LED, 7-Segment Display, ADC, DAC, Keyboard (4×4), LCD.

---

## 📋 Quick Exam Cheatsheet

| What to Study | Marks Likely | Type |
|---|---|---|
| LED interfacing (circuit + program) | 8 marks | Program + explain |
| 7-Segment Display (look-up table, common anode/cathode) | 8-12 marks | Program + explain |
| ADC interfacing (0808 steps + program) | 12 marks | Explain pins + program |
| DAC interfacing (waveform generation) | 8 marks | Program |
| Keyboard 4×4 matrix (scanning technique) | 12 marks | Explain + program |
| LCD interfacing (pins, commands, data routine) | 8-12 marks | Explain pins + program |
| Port Configuration | 8 marks | Theory |

---

## 0. 8051 Port Configuration (Prerequisite) 🔥

> **Always configure ports before using them!**

```asm
; To configure Port 1 as INPUT port:
MOV P1, #0FFH        ; Write all 1s → port becomes input
                      ; (8051 ports need 1s to read inputs)

; To configure Port 1 as OUTPUT port:
MOV P1, #00H          ; Write all 0s → port becomes output
```

> 💡 **Why write FFH for input?** In 8051, port pins have internal pull-ups. Writing 1 makes the pin "float high" and allows external devices to pull it LOW. If you write 0, the pin is forced LOW and can't read external signals.

---

## 1. LED Interfacing 🔥

### Concept:
- Connect LEDs to a port (e.g., Port 2)
- Writing 1 = LED ON, Writing 0 = LED OFF (for common cathode / active-high wiring)
- To alternate LEDs: Send `AAH` (10101010) and `55H` (01010101) with delay

### Circuit (Conceptual):
```
8051 Port 2                    LEDs
Pin P2.7 ──── [R] ──── LED7 ──── GND
Pin P2.6 ──── [R] ──── LED6 ──── GND
Pin P2.5 ──── [R] ──── LED5 ──── GND
Pin P2.4 ──── [R] ──── LED4 ──── GND
Pin P2.3 ──── [R] ──── LED3 ──── GND
Pin P2.2 ──── [R] ──── LED2 ──── GND
Pin P2.1 ──── [R] ──── LED1 ──── GND
Pin P2.0 ──── [R] ──── LED0 ──── GND

[R] = Current limiting resistor (~330Ω)
```

### Program — Alternate LEDs ON/OFF 🔥:

```asm
        ORG  0000H             ; Start address

L1:     MOV  P2, #0AAH         ; Send 10101010 to Port 2
                                ; LEDs: ON OFF ON OFF ON OFF ON OFF
        LCALL D1               ; Call delay subroutine

        MOV  P2, #55H          ; Send 01010101 to Port 2
                                ; LEDs: OFF ON OFF ON OFF ON OFF ON
        LCALL D1               ; Call delay subroutine

        SJMP L1                ; Jump back to L1 → infinite loop

; ──────────────────────────────────────
; Delay Subroutine
; ──────────────────────────────────────
D1:     MOV  R6, #200          ; Outer loop counter = 200
DL1:    MOV  R7, #255          ; Inner loop counter = 255
DL2:    DJNZ R7, DL2           ; Decrement R7; loop until R7 = 0
        DJNZ R6, DL1           ; Decrement R6; loop until R6 = 0
        RET                    ; Return from subroutine

        END
```

---

## 2. 7-Segment Display Interfacing 🔥🔥

### What is a 7-Segment Display?
```
         ┌─── a ───┐
         │         │
        f│         │b
         │         │
         ├─── g ───┤
         │         │
        e│         │c
         │         │
         └─── d ───┘   • dp (decimal point)
```

### Two Types:

| Type | To Turn ON Segment | To Turn OFF |
|---|---|---|
| **Common Anode** | Send **0** (LOW) | Send **1** (HIGH) |
| **Common Cathode** | Send **1** (HIGH) | Send **0** (LOW) |

### 7-Segment Codes (Common Cathode) 🔥:

| Digit | Binary (pgfedcba) | Hex |
|---|---|---|
| **0** | 0 0 1 1 1 1 1 1 | **3FH** |
| **1** | 0 0 0 0 0 1 1 0 | **06H** |
| **2** | 0 1 0 1 1 0 1 1 | **5BH** |
| **3** | 0 1 0 0 1 1 1 1 | **4FH** |
| **4** | 0 1 1 0 0 1 1 0 | **66H** |
| **5** | 0 1 1 0 1 1 0 1 | **6DH** |
| **6** | 0 1 1 1 1 1 0 1 | **7DH** |
| **7** | 0 0 0 0 0 1 1 1 | **07H** |
| **8** | 0 1 1 1 1 1 1 1 | **7FH** |
| **9** | 0 1 1 0 1 1 1 1 | **6FH** |

### Connection:
```
8051 Port 2 → 7-Segment Display
P2.0 → a
P2.1 → b
P2.2 → c
P2.3 → d
P2.4 → e
P2.5 → f
P2.6 → g
P2.7 → dp (decimal point)
```

### Program — Display Digits 0-9 Using Look-up Table 🔥🔥:

```asm
        ORG  0000H             ; Start of program memory

        MOV  P2, #00H          ; Clear Port 2 (all segments OFF)
        MOV  DPTR, #TAB        ; Load DPTR with address of look-up table
        MOV  R0, #0            ; R0 = counter for digits (starts at 0)

BACK:
        MOV  A, R0             ; A = current digit number (0-9)
        MOVC A, @A+DPTR        ; A = 7-seg code from table at address DPTR + A
                                ; This is the INDEXED ADDRESSING mode
        MOV  P2, A             ; Output segment pattern to Port 2
                                ; → corresponding digit lights up on display
        ACALL DELAY            ; Wait so human can see the digit
        INC  R0                ; Move to next digit
        CJNE R0, #10, BACK    ; If R0 ≠ 10, loop back (digits 0-9 only)
        SJMP $                 ; Stop program (infinite loop at this line)

; ──────────────────────────────────────
; Delay Subroutine
; ──────────────────────────────────────
DELAY:
        MOV  R1, #200          ; Outer loop: 200 iterations
L1:     MOV  R2, #255          ; Inner loop: 255 iterations
L2:     DJNZ R2, L2            ; Inner loop countdown
        DJNZ R1, L1            ; Outer loop countdown
        RET                    ; Return to caller

; ──────────────────────────────────────
; Look-up Table for digits 0-9 (Common Cathode)
; ──────────────────────────────────────
TAB:    DB 3FH, 06H, 5BH, 4FH, 66H   ; Codes for 0, 1, 2, 3, 4
        DB 6DH, 7DH, 07H, 7FH, 6FH   ; Codes for 5, 6, 7, 8, 9

        END
```

### BCD to 7-Segment (Using Port 3 as Input):
```asm
        MOV  A, P3             ; Read BCD value from Port 3
        ANL  A, #0FH           ; Mask upper nibble (keep only lower 4 bits)
                                ; This ensures we only get digits 0-9 (0000-1001)
GET_CODE:
        MOV  DPTR, #TAB        ; Point to look-up table
        MOVC A, @A+DPTR        ; Get 7-seg code for this BCD digit
        MOV  P1, A             ; Display on Port 1
```

---

## 3. ADC (Analog to Digital Converter) Interfacing 🔥🔥

> 🔥 **High probability 12-mark question** — Explain ADC interface steps + program.

### What is ADC?
- Converts **analog voltage** (from sensors like temperature, light) to **digital value** (binary number)
- Example: ADC0808 — 8-bit, 8-channel ADC

### Pin Connections (ADC0808 to 8051):
```
ADC0808                    8051
────────                   ────
D0-D7 (Data Out)  ←──→    P1.0-P1.7 (configured as INPUT)
A, B, C (Channel)  ←──    P2.0-P2.2 (channel select)
ALE               ←──     P2.4
SOC (Start)       ←──     P2.5
EOC (End)          ──→    P2.6
OE (Output Enable)←──     P2.7
```

### ADC Conversion Steps 🔥:

```
Step 1: Configure data port as INPUT
        ↓
Step 2: Set EOC pin HIGH (waiting for high-to-low signal)
        Clear ALE, SOC, OE pins
        ↓
Step 3: Select ADC channel (send 3-bit channel address)
        Example: Channel 2 = 010 → P2.2=0, P2.1=1, P2.0=0
        ↓
Step 4: Latch the address with ALE pulse (HIGH then LOW)
        ↓
Step 5: Start conversion with SOC pulse (HIGH then LOW)
        ↓
Step 6: Wait for EOC to go LOW (conversion in progress)
        ↓
Step 7: Wait for EOC to go HIGH (conversion complete!)
        ↓
Step 8: Enable output with OE pulse → Read data from P1
        ↓
Repeat from Step 3
```

### ADC Interface Program (Channel 2) 🔥🔥:

```asm
        ORG  0000H             ; Program start

; ─── STEP 1: Configure Port 1 as Input ───
        MOV  P1, #0FFH         ; P1 = input port (for ADC data lines D0-D7)

; ─── STEP 2: Initialize control pins ───
        SETB P2.6              ; EOC pin = HIGH (will go LOW when conversion starts)
        CLR  P2.4              ; ALE = LOW (no address latch yet)
        CLR  P2.5              ; SOC = LOW (no conversion yet)
        CLR  P2.7              ; OE = LOW (output disabled)

; ─── STEP 3: Select ADC Channel 2 (binary 010) ───
BACK:
        CLR  P2.2              ; A (MSB of channel) = 0
        SETB P2.1              ; B (middle bit) = 1
        CLR  P2.0              ; C (LSB of channel) = 0
                                ; Channel = 010 = Channel 2

; ─── STEP 4: Latch address with ALE pulse ───
        SETB P2.4              ; ALE = HIGH → address is now on the bus
        CLR  P2.4              ; ALE = LOW → address latched into ADC

; ─── STEP 5: Start conversion with SOC pulse ───
        SETB P2.5              ; SOC = HIGH → tell ADC to start converting
        CLR  P2.5              ; SOC = LOW → pulse sent

; ─── STEP 6: Wait for EOC to go LOW (conversion started) ───
HERE:   JB   P2.6, HERE        ; If EOC is still HIGH, keep waiting
                                ; (JB = Jump if Bit is set)

; ─── STEP 7: Wait for EOC to go HIGH (conversion complete) ───
HERE1:  JNB  P2.6, HERE1       ; If EOC is still LOW, keep waiting
                                ; (JNB = Jump if Bit is NOT set)

; ─── STEP 8: Read converted data ───
        SETB P2.7              ; OE = HIGH → enable ADC output
        MOV  A, P1             ; Read 8-bit digital value from ADC into A
        CLR  P2.7              ; OE = LOW → disable output

        SJMP BACK              ; Repeat conversion

        END
```

### Alternative for Steps 6-7 (Using Delay):
```asm
; Instead of polling EOC, use a fixed delay
        LCALL DELAY            ; Wait ~100µs for conversion
                                ; (ADC0808 needs about 100µs)
WAIT_EOC:
        MOV  C, P2.6           ; Read EOC bit into carry flag
        JNC  WAIT_EOC          ; If carry = 0 (EOC = LOW), keep waiting
                                ; When carry = 1 (EOC = HIGH), conversion done
```

---

## 4. DAC (Digital to Analog Converter) Interfacing 🔥

### What is DAC?
- Converts **digital value** (binary number) to **analog voltage**
- Used to generate waveforms: square wave, triangular wave, sawtooth, staircase

### Connection:
```
8051 Port 1 → DAC (8-bit input)
DAC output → Oscilloscope / Speaker / Motor
```

### Square Wave Generation 🔥:
```
Voltage
  FFH ─── ┌───────┐         ┌───────┐
          │       │         │       │
  00H ────┘       └─────────┘       └─────
          ← delay →← delay →
```

### Program — Square Wave on Port 1:

```asm
        ORG  0000H             ; Program start

REPEAT:
        MOV  P1, #00H          ; Output LOW (0V) → DAC outputs 0V
        LCALL D1               ; Wait for half period

        MOV  P1, #0FFH         ; Output HIGH (max) → DAC outputs max voltage
        LCALL D1               ; Wait for half period

        SJMP REPEAT            ; Loop forever → continuous square wave

; ──────────────────────────────────────
; Delay Subroutine (controls pulse width)
; ──────────────────────────────────────
D1:     MOV  R6, #200
DL1:    MOV  R7, #255
DL2:    DJNZ R7, DL2
        DJNZ R6, DL1
        RET

        END
```

### Triangular Wave (Concept):
```asm
; Ramp UP: Send 00H, 01H, 02H, ... FFH to DAC
; Ramp DOWN: Send FFH, FEH, FDH, ... 00H to DAC
; Result: Triangular waveform

UP:     MOV  A, #00H
AGAIN1: MOV  P1, A             ; Output to DAC
        INC  A                 ; Increment value
        CJNE A, #0FFH, AGAIN1  ; Until max reached

DOWN:   MOV  A, #0FFH
AGAIN2: MOV  P1, A             ; Output to DAC
        DEC  A                 ; Decrement value
        CJNE A, #00H, AGAIN2   ; Until min reached
        SJMP UP                ; Repeat
```

---

## 5. Keyboard (4×4 Matrix) Interfacing 🔥🔥

### Concept:
- 16 keys arranged in a **4×4 matrix** (4 rows × 4 columns)
- Instead of 16 individual pins, we need only **8 pins** (4 rows + 4 columns)
- **Scanning technique**: Place 0 on one row at a time, read columns

### Circuit:
```
              Columns (Input)
              P1.3  P1.2  P1.1  P1.0
               │     │     │     │
    P1.7 ──┬───┤─────┤─────┤─────┤
    (Row 0) │  0     1     2     3
            │  │     │     │     │
    P1.6 ──┬───┤─────┤─────┤─────┤
    (Row 1) │  4     5     6     7
            │  │     │     │     │
    P1.5 ──┬───┤─────┤─────┤─────┤
    (Row 2) │  8     9     A     B
            │  │     │     │     │
    P1.4 ──┬───┤─────┤─────┤─────┤
    (Row 3) │  C     D     E     F
            │  │     │     │     │
```

### Scanning Steps:
1. Ground **Row 0** (send 0 to P1.7, keep others HIGH): `P1 = #EFH` (1110 1111)
2. Read columns (P1.0–P1.3)
3. If any column is **LOW** → that key at Row 0, Column x is pressed
4. If no key → ground **Row 1**: `P1 = #DFH` (1101 1111)
5. Repeat for all 4 rows
6. Use **debounce delay** (10ms) to avoid multiple reads from one press

### Keyboard Scan Program 🔥:

```asm
; ──────────────────────────────────────
; Row 0 Scan: Send 0 to P1.7 (Row 0), read columns P1.0-P1.3
; ──────────────────────────────────────
SCAN:
        MOV  P1, #0EFH         ; P1 = 1110 1111 → Row 0 = LOW, others HIGH
        JNB  P1.0, DB_0        ; If P1.0 = LOW → key '0' pressed
SCAN1:  JNB  P1.1, DB_1        ; If P1.1 = LOW → key '1' pressed
SCAN2:  JNB  P1.2, DB_2        ; If P1.2 = LOW → key '2' pressed
SCAN3:  JNB  P1.3, DB_3        ; If P1.3 = LOW → key '3' pressed

; ──────────────────────────────────────
; Row 1 Scan: Send 0 to P1.6 (Row 1)
; ──────────────────────────────────────
SCAN4:
        MOV  P1, #0DFH         ; P1 = 1101 1111 → Row 1 = LOW
        JNB  P1.0, DB_4        ; Key '4'
        ; ... continue for keys 5, 6, 7
        
; ──────────────────────────────────────
; Row 2 and Row 3 scans similar...
; ──────────────────────────────────────

; ──────────────────────────────────────
; Debounce and Key Identification
; ──────────────────────────────────────
DB_0:
        LCALL WT_10MS           ; Wait 10ms for debounce
        JB   P1.0, SCAN1       ; If key released (bounced), skip to next
        MOV  A, #0              ; Key confirmed! A = key number 0
        LJMP GET_CODE           ; Jump to look-up table

DB_1:
        LCALL WT_10MS           ; Debounce delay
        JB   P1.1, SCAN2       ; If released, skip
        MOV  A, #1              ; A = key number 1
        LJMP GET_CODE           ; Get ASCII code

; ... repeat for all 16 keys ...

; ──────────────────────────────────────
; Convert key number to ASCII using look-up table
; ──────────────────────────────────────
GET_CODE:
        MOV  DPTR, #KEY_TAB    ; Point to ASCII table
        MOVC A, @A+DPTR        ; A = ASCII code for this key
        LJMP SCAN              ; Go back and scan again

KEY_TAB: DB '0123456789ABCDEF' ; ASCII codes for keys 0-F

        END
```

### Key debounce explanation:
> When you press a button, the metal contacts **bounce** several times before settling. This can register as multiple presses. The 10ms delay waits for bouncing to stop, then re-checks if the key is still pressed.

---

## 6. LCD Interfacing 🔥🔥

### LCD Pin Description (Standard 16×2 LCD) 🔥:

| Pin | Name | Function |
|---|---|---|
| 1 | **VSS** | Ground |
| 2 | **VCC** | +5V power |
| 3 | **VEE** | Contrast adjustment |
| 4 | **RS** | Register Select: **0 = Command**, **1 = Data** |
| 5 | **R/W** | Read/Write: **0 = Write**, 1 = Read |
| 6 | **E** | Enable: Data latched on **HIGH→LOW** transition |
| 7-14 | **D0-D7** | Bidirectional data/command pins (ASCII format) |

### Key LCD Commands:

| Hex Code | Command |
|---|---|
| 01H | Clear display |
| 02H | Return cursor home |
| 06H | Increment cursor (shift right) |
| 0CH | Display ON, Cursor OFF |
| 0EH | Display ON, Cursor ON |
| 38H | 2-line, 5×7 matrix, 8-bit mode |
| 80H | Force cursor to beginning of 1st line |
| C0H | Force cursor to beginning of 2nd line |

### Connection:
```
8051                 LCD Module
─────                ──────────
P1.0-P1.7  ───→      D0-D7    (8-bit data bus)
P3.3       ───→      E        (Enable)
P3.4       ───→      R/W      (Read/Write)
P3.5       ───→      RS       (Register Select)
```

### LCD Write Routines 🔥🔥:

```asm
; ══════════════════════════════════════
; DATA WRITE: Send a character to LCD
; ══════════════════════════════════════
; Input: A = ASCII character to display
DATA_WRITE:
        MOV  P1, A             ; Put ASCII data on data bus (P1)
        SETB P3.5              ; RS = 1 → selecting DATA register
                                ; (we want to display a character, not a command)
        CLR  P3.4              ; R/W = 0 → write mode
        SETB P3.3              ; E = HIGH → begin enable pulse
        CLR  P3.3              ; E = LOW → data latched on falling edge
        RET

; ══════════════════════════════════════
; COMMAND WRITE: Send a command to LCD
; ══════════════════════════════════════
; Input: A = command byte
CMD_WRITE:
        MOV  P1, A             ; Put command on data bus (P1)
        CLR  P3.5              ; RS = 0 → selecting COMMAND register
                                ; (we want to send an instruction, not a character)
        CLR  P3.4              ; R/W = 0 → write mode
        SETB P3.3              ; E = HIGH → begin enable pulse
        CLR  P3.3              ; E = LOW → command latched on falling edge
        RET
```

### Complete LCD Example — Display "AB" 🔥:

```asm
        ORG  0000H

; ─── Initialize LCD ───
        MOV  A, #38H           ; Command: 2-line, 5×7 matrix, 8-bit mode
        ACALL CMD_WRITE        ; Send command to LCD
        ACALL DELAY            ; Wait for LCD to process

        MOV  A, #0EH           ; Command: Display ON, Cursor ON
        ACALL CMD_WRITE
        ACALL DELAY

        MOV  A, #01H           ; Command: Clear display
        ACALL CMD_WRITE
        ACALL DELAY

        MOV  A, #06H           ; Command: Increment cursor (move right)
        ACALL CMD_WRITE
        ACALL DELAY

        MOV  A, #80H           ; Command: Cursor to line 1, position 0
        ACALL CMD_WRITE
        ACALL DELAY

; ─── Display Characters ───
        MOV  A, #'A'           ; ASCII of 'A' = 41H
        ACALL DATA_WRITE       ; Display 'A' on LCD
        ACALL DELAY

        MOV  A, #'B'           ; ASCII of 'B' = 42H
        ACALL DATA_WRITE       ; Display 'B' on LCD
        ACALL DELAY

        SJMP $                 ; Stop (loop here forever)

; ─── Subroutines (defined above) ───
; DATA_WRITE and CMD_WRITE would go here
; DELAY subroutine would go here

        END
```

### RS Pin Logic — The Key Concept 🔥:
```
RS = 0  →  LCD interprets data bus as a COMMAND
           (like "clear screen", "move cursor", "set mode")

RS = 1  →  LCD interprets data bus as CHARACTER DATA
           (like 'A', 'B', '1', '$' — displayed on screen)
```

---

## 💡 Conceptual Corner

### Q: Why does Port 0 need external pull-up resistors but other ports don't?
**A**: Port 0 is an **open-drain** port — its output transistors can only pull the pin LOW but cannot drive it HIGH. The external 10KΩ pull-up resistors provide the HIGH voltage. Ports 1, 2, 3 have **internal pull-ups**, so they don't need external ones.

### Q: Why do we need debounce in keyboard scanning?
**A**: Mechanical switches have metal contacts that physically bounce when pressed, causing rapid ON-OFF-ON transitions for about 5-20ms. Without debounce, the controller reads multiple presses instead of one. A 10ms delay lets the bouncing settle.

### Q: What happens if you send data to LCD with RS=0?
**A**: The LCD treats it as a **command**, not a character. For example, sending ASCII 'A' (41H) with RS=0 would be interpreted as command 41H (which isn't a standard command), producing unexpected behavior instead of displaying the letter 'A'.

### Q: How does the look-up table work in 7-segment display?
**A**: The table stores pre-calculated segment patterns. When you want to display digit `n`, you use `n` as an offset into the table. `MOVC A, @A+DPTR` fetches the byte at address `DPTR + A`, which is the segment pattern for digit `n`. This is much faster than calculating patterns with IF statements.

### Q: Why use MOVC instead of MOV for look-up tables?
**A**: `MOVC` reads from **program memory** (ROM/Flash where your code and data tables live). Regular `MOV` reads from **data memory** (RAM). Look-up tables defined with `DB` directive are stored in program memory, so `MOVC` must be used to access them.

---

## 📝 Likely Exam Questions

### 8-Mark Questions
1. Explain LED interfacing with 8051. Write a program to blink LEDs alternately.
2. Explain the pin diagram of an LCD module. Write the command and data write routines.
3. Explain how a DAC is interfaced with 8051. Write a program for square wave generation.
4. Explain the concept of 7-segment display (common anode vs common cathode) with look-up table.

### 12-Mark Questions
1. (a) Explain ADC interfacing with 8051 — draw the connection diagram, explain each step, and write the complete assembly program with comments.
   OR
   (b) Explain 4×4 keyboard matrix interfacing with 8051 — scanning technique, debouncing, and write the scanning program.

2. (a) Explain LCD interfacing with 8051 — pin functions, commands, and write a program to display a message.
   OR
   (b) Explain 7-segment display interfacing with 8051. Write a program to display digits 0-9 using a look-up table.
