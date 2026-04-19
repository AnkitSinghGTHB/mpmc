# Module 3: 8051 Microcontroller

> **Exam Focus**: Theory + one optional assembly program. Expect 1 × 12-mark (a/b) + 1 × 8-mark.
> One 12-mark option will likely be a program (8051 assembly). The other will be theory.
> Key topics: Architecture, Memory Map, SFRs, Addressing Modes, Instructions, Timers, Interrupts.

---

## 📋 Quick Exam Cheatsheet

| What to Study | Marks Likely | Type |
|---|---|---|
| 8051 Features & Block Diagram | 8 marks | Explain |
| Microprocessor vs Microcontroller | 8 marks | Comparison |
| Memory Organization (RAM map, Register Banks) | 8-12 marks | Diagram + explain |
| SFRs (PSW, Accumulator, DPTR, SP, Ports) | 8 marks | Table |
| Addressing Modes (all 8) | 8-12 marks | Examples |
| Instruction Set (5 categories with examples) | 12 marks | Explain + code |
| Timers (Mode 0, 1, 2 — TMOD, TCON) | 12 marks | Explain + program |
| Interrupts (IE, IP, vectors, priorities) | 8-12 marks | Explain + diagram |
| Serial Communication (basics) | 8 marks | Compare parallel vs serial |
| Assembly Program (timer delay / LED / counter) | 12 marks | Write + explain |

---

## 1. What is a Microcontroller?

A microcontroller is a **"smaller computer"** with everything on **a single chip**:

```
┌─────────────────────────────────────┐
│       MICROCONTROLLER (Single Chip) │
│                                     │
│   ┌─────┐  ┌─────┐  ┌──────────┐    │
│   │ CPU │  │ RAM │  │   ROM    │    │
│   └─────┘  └─────┘  └──────────┘    │
│   ┌──────────┐  ┌───────┐  ┌─────┐  │
│   │ I/O Port │  │ Timer │  │UART │  │
│   └──────────┘  └───────┘  └─────┘  │
└─────────────────────────────────────┘
```

Examples: Intel 8051, Motorola 6811, Zilog Z8, PIC 16X

---

## 2. Microprocessor vs Microcontroller 🔥

| # | Feature | Microprocessor | Microcontroller |
|---|---|---|---|
| 1 | **Components** | CPU only (standalone) | CPU + RAM + ROM + I/O + Timer on single chip |
| 2 | **External Parts** | RAM, ROM, I/O, Timer are all separate chips | All integrated on one chip |
| 3 | **RAM/ROM** | Designer decides amount | Fixed amount on-chip |
| 4 | **Bit Addressability** | Limited | Highly bit-addressable |
| 5 | **Cost** | Expensive (many external chips) | Cheap (single chip solution) |
| 6 | **Space** | Large PCB needed | Compact |
| 7 | **Power** | Higher power consumption | Lower power |
| 8 | **Versatility** | General-purpose (can build anything) | Single-purpose (specific application) |
| 9 | **Applications** | PCs, Laptops, Servers | Washing machines, remotes, toys, alarms |
| 10 | **Examples** | Intel x86, Motorola 680x0 | Intel 8051, PIC, AVR, ARM |

> 💡 **Think of it this way**: A **Microprocessor** is like a brain — it needs a body (external RAM, ROM, I/O) to function. A **Microcontroller** is a complete mini-person — brain AND body all in one.

---

## 3. 8051 — Key Features 🔥

| Feature | Value |
|---|---|
| ROM (Program Memory) | 4 KB on-chip |
| RAM (Data Memory) | 128 bytes |
| I/O Ports | 4 ports × 8 bits = **32 I/O pins** |
| Timers | 2 × 16-bit timers (Timer 0 and Timer 1) |
| Serial Port | 1 (Full-duplex UART) |
| External Code Memory | Up to 64 KB |
| External Data Memory | Up to 64 KB |
| Interrupts | 6 sources (5 in basic + Reset) |
| Package | 40-pin DIP |
| Architecture | **Harvard** (separate code and data memory) |

### MCS-51 Family Comparison:

| Feature | 8031 | 8051 | 8052 | 8751 |
|---|---|---|---|---|
| ROM | None | 4 KB | 8 KB | 4 KB UV EPROM |
| RAM (bytes) | 128 | 128 | 256 | 128 |
| Timers | 2 | 2 | 3 | 2 |
| I/O Pins | 32 | 32 | 32 | 32 |
| Serial Ports | 1 | 1 | 1 | 1 |
| Interrupt Sources | 6 | 6 | 8 | 6 |

---

## 4. 8051 Architecture — Block Diagram 🔥🔥

```
┌────────────────────────────────────────────────────────────────────┐
│                        8051 MICROCONTROLLER                        │
│                                                                    │
│  ┌──────────────┐   ┌──────────┐   ┌──────────────────────────┐    │
│  │  Oscillator  │   │  4 KB    │   │   128 Bytes              │    │
│  │  & Timing    │   │  Program │   │   Data Memory (RAM)      │    │
│  │              │   │  Memory  │   │   ┌────────────────────┐ │    │
│  └──────────────┘   │  (ROM)   │   │   │ R0-R7 (4 banks)    │ │    │
│                     └──────────┘   │   │ Bit-addressable    │ │    │
│  ┌──────────────┐                  │   │ Scratch pad        │ │    │
│  │   8051 CPU   │                  │   └────────────────────┘ │    │
│  │  ┌────────┐  │                  └──────────────────────────┘    │
│  │  │  ALU   │  │                                                  │
│  │  └────────┘  │   ┌──────────────────────────┐                   │
│  │  ┌────────┐  │   │  Two 16-bit              │                   │
│  │  │  ACC   │  │   │  Timer/Event Counters    │                   │
│  │  │   B    │  │   │  (Timer 0, Timer 1)      │                   │
│  │  │  PSW   │  │   └──────────────────────────┘                   │
│  │  │  DPTR  │  │                                                  │
│  │  │  SP    │  │   ┌──────────────────────────┐                   │
│  │  └────────┘  │   │  Programmable Serial     │                   │
│  └──────────────┘   │  Port (Full-Duplex UART) │                   │
│                     └──────────────────────────┘                   │
│  ┌──────────────────────────────────────────────┐                  │
│  │          Programmable I/O Ports              │                  │
│  │   ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐         │                  │
│  │   │ P0  │  │ P1  │  │ P2  │  │ P3  │         │                  │
│  │   │8-bit│  │8-bit│  │8-bit│  │8-bit│         │                  │
│  │   └─────┘  └─────┘  └─────┘  └─────┘         │                  │
│  └──────────────────────────────────────────────┘                  │
│                                                                    │
│  ┌────────────┐  ┌────────────┐                                    │
│  │ 64 KB Bus  │  │ Interrupt  │                                    │
│  │ Expansion  │  │ Subsystem  │                                    │
│  │ Control    │  │            │                                    │
│  └────────────┘  └────────────┘                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Important Pins:

| Pin | Name | Function |
|---|---|---|
| **Pin 30** | ALE/PROG | Address Latch Enable — separates multiplexed address/data |
| **Pin 31** | EA̅/VPP | External Access Enable — LOW = use external program memory |
| **PSEN̅** | Program Store Enable | LOW when reading external program memory |
| **P0** (pins 32-39) | Port 0 | Dual-purpose; needs **10K pull-up resistors** (open drain) |
| **P1** (pins 1-8) | Port 1 | General-purpose I/O |
| **P2** (pins 21-28) | Port 2 | I/O or upper address byte (A8-A15) |
| **P3** (pins 10-17) | Port 3 | I/O with **alternate functions** (see below) |

### Port 3 Alternate Functions 🔥:

| Pin | Alternate Function | Description |
|---|---|---|
| P3.0 | **RXD** | Serial data input |
| P3.1 | **TXD** | Serial data output |
| P3.2 | **INT0̅** | External interrupt 0 |
| P3.3 | **INT1̅** | External interrupt 1 |
| P3.4 | **T0** | Timer 0 external input |
| P3.5 | **T1** | Timer 1 external input |
| P3.6 | **WR̅** | External data memory write |
| P3.7 | **RD̅** | External data memory read |

---

## 5. Memory Organization 🔥🔥

### Von Neumann vs Harvard Architecture:

| Feature | Von Neumann | Harvard (8051 uses this) |
|---|---|---|
| Memory | Single memory for code + data | **Separate** memory for code and data |
| Bus | Single address/data bus | Separate buses for fetching and data |
| Speed | Slower (bus contention) | Faster (simultaneous access) |

### 8051 RAM Memory Map (128 Bytes: 00H–7FH) 🔥:

```
┌────────────────────────────────────┐ 7FH
│                                    │
│   Scratch Pad RAM                  │  80 locations (30H - 7FH)
│   (General Purpose)                │  No special function
│                                    │
├────────────────────────────────────┤ 2FH
│                                    │
│   Bit-Addressable RAM              │  16 bytes (20H - 2FH)
│   128 individually addressable     │  = 128 bits total
│   bits (bit 00 to bit 7F)          │  Both bit AND byte addressable
│                                    │
├────────────────────────────────────┤ 1FH
│   Register Bank 3  (18H - 1FH)     │  R0-R7
├────────────────────────────────────┤ 17H
│   Register Bank 2  (10H - 17H)     │  R0-R7
├────────────────────────────────────┤ 0FH
│   Register Bank 1  (08H - 0FH)     │  R0-R7
├────────────────────────────────────┤ 07H
│   Register Bank 0  (00H - 07H)     │  R0-R7 ← DEFAULT
└────────────────────────────────────┘ 00H
```

- **4 Register Banks**, each with 8 registers (R0–R7)
- **Default bank** = Bank 0 (selected by RS1, RS0 bits in PSW)
- Only **one bank active** at a time

### Special Function Registers (SFRs): 80H–FFH 🔥
- **Direct addressing** is used to access SFRs
- Key SFRs:

| SFR | Address | Purpose |
|---|---|---|
| **ACC (A)** | E0H | Accumulator — used in almost all ALU operations |
| **B** | F0H | Used with ACC in MUL and DIV operations |
| **PSW** | D0H | Program Status Word (Flag Register) |
| **SP** | 81H | Stack Pointer (default = 07H after reset) |
| **DPTR** | 82H-83H | Data Pointer (16-bit: DPL + DPH) |
| **P0-P3** | 80H,90H,A0H,B0H | I/O Port latches |
| **TMOD** | 89H | Timer Mode register |
| **TCON** | 88H | Timer Control register |
| **TH0/TL0** | 8CH/8AH | Timer 0 high/low byte |
| **TH1/TL1** | 8DH/8BH | Timer 1 high/low byte |
| **IE** | A8H | Interrupt Enable register |
| **IP** | B8H | Interrupt Priority register |
| **SCON** | 98H | Serial Control register |
| **SBUF** | 99H | Serial Buffer |
| **PCON** | 87H | Power Control register |

---

## 6. PSW — Program Status Word (Flag Register) 🔥

```
Bit:   7     6     5     4     3     2     1     0
     ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
     │  CY │  AC │  F0 │ RS1 │ RS0 │ OV  │  —  │  P  │
     └─────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘
```

| Bit | Flag | Name | Description |
|---|---|---|---|
| PSW.7 | **CY** | Carry Flag | Set if carry from bit 7 (add) or borrow (sub) |
| PSW.6 | **AC** | Auxiliary Carry | Carry from bit 3 to bit 4 (used in BCD) |
| PSW.5 | **F0** | Flag 0 | General-purpose flag for user |
| PSW.4 | **RS1** | Register Bank Select bit 1 | See table below |
| PSW.3 | **RS0** | Register Bank Select bit 0 | See table below |
| PSW.2 | **OV** | Overflow Flag | Set if signed overflow |
| PSW.1 | **—** | Reserved | — |
| PSW.0 | **P** | Parity Flag | Set if ACC has **odd** number of 1s |

### Register Bank Selection:

| RS1 | RS0 | Active Bank | Address Range |
|---|---|---|---|
| 0 | 0 | Bank 0 | 00H – 07H |
| 0 | 1 | Bank 1 | 08H – 0FH |
| 1 | 0 | Bank 2 | 10H – 17H |
| 1 | 1 | Bank 3 | 18H – 1FH |

> ⚠️ **Important difference from 8086**: In 8051, Parity flag is set for **ODD** number of 1s. In 8086, it's set for **EVEN** number of 1s.

---

## 7. Stack in 8051

- Stack Pointer (SP) default value after reset = **07H**
- Stack grows **upward** (SP increments on PUSH)
- **PUSH**: SP ← SP + 1, then store data at SP
- **POP**: Read data at SP, then SP ← SP - 1

> This is opposite to 8086! In 8086, stack grows downward (SP decrements on PUSH).

---

## 8. Addressing Modes of 8051 (8 Modes) 🔥🔥

### 1. Immediate Addressing
- Data is **part of the instruction** (prefixed with `#`)
```asm
ADD A, #30H       ; Add 8-bit value 30H to accumulator
MOV DPTR, #0FE00H ; Load 16-bit value FE00H into DPTR
```

### 2. Register Addressing
- Operand is in a **register** (R0–R7)
```asm
MOV R0, A         ; Copy accumulator content to R0
```

### 3. Direct Addressing
- Specify the **actual memory address** or SFR name
```asm
MOV A, P3         ; Copy Port 3 contents to ACC
MOV A, 020H       ; Copy contents of RAM location 20H to ACC
```

### 4. Indirect Addressing
- A **pointer register** holds the address (R0, R1, or DPTR)
- R0/R1 = 8-bit address, DPTR = 16-bit address
```asm
MOV @R0, A        ; Store ACC into memory location pointed by R0
MOVX A, @DPTR     ; Load ACC from external memory pointed by DPTR
```

### 5. Indexed Addressing
- Used for **look-up tables** in program memory
- Base (DPTR or PC) + offset (ACC)
```asm
MOV A, #08H            ; Offset = 08H
MOV DPTR, #01F00H      ; Table start address
MOVC A, @A+DPTR        ; A ← code memory at address 1F00H + 08H = 1F08H
```

### 6. Relative Addressing
- Used with **SJMP** and conditional jumps (JNZ, JZ, etc.)
- Destination = PC + signed 8-bit offset (range: -128 to +127)
```asm
GoBack: DEC A          ; Decrement A
        JNZ GoBack     ; If A ≠ 0, jump back (relative)
```

### 7. Absolute Addressing
- Used with **ACALL** and **AJMP** (2-byte instructions)
- 11-bit address → destination must be within **same 2 KB page**
```asm
ACALL PORT_INIT        ; Call subroutine within 2K range
```

### 8. Long Addressing
- Used with **LCALL** and **LJMP** (3-byte instructions)
- 16-bit address → can reach **any location in 64 KB**
```asm
LCALL TIMER_INIT       ; Call subroutine anywhere in 64K
```

### Quick Comparison:

| Mode | Range | Instruction Size | Example |
|---|---|---|---|
| Relative (SJMP) | -128 to +127 bytes | 2 bytes | `SJMP label` |
| Absolute (AJMP/ACALL) | Within 2 KB page | 2 bytes | `AJMP label` |
| Long (LJMP/LCALL) | Full 64 KB | 3 bytes | `LJMP label` |

---

## 9. Instruction Set (5 Categories) 🔥🔥

### Category 1: Arithmetic Operations

```asm
ADD  A, R2         ; A = A + R2 (affects CY, AC, OV)
ADDC A, #30H       ; A = A + 30H + CY (add with carry)
SUBB A, R1         ; A = A - R1 - CY (subtract with borrow, ALWAYS uses CY)
INC  R1            ; R1 = R1 + 1
INC  DPTR          ; DPTR = DPTR + 1 (only 16-bit INC available)
DEC  A             ; A = A - 1 (00H underflows to FFH)
MUL  AB            ; A × B → result: B = high byte, A = low byte
                   ; If result > 255, OV = 1. CY always cleared.
DIV  AB            ; A ÷ B → A = quotient, B = remainder
                   ; If B = 0 before DIV: result undefined, OV = 1
DA   A             ; Decimal Adjust: corrects BCD result after ADD/ADDC
```

> 🔥 **MUL AB Example**: If A = 85 (55H) and B = 23 (17H):
> 85 × 23 = 1955 = 07A3H → B = 07H, A = A3H, OV = 1 (result > 255)

> 🔥 **DIV AB Example**: If A = 90 (5AH) and B = 5 (05H):
> 90 ÷ 5 = 18 remainder 0 → A = 12H (18), B = 00H

### Category 2: Logical Operations

```asm
ANL A, R2          ; A = A AND R2 (bitwise)
ORL A, R2          ; A = A OR R2
XRL A, R0          ; A = A XOR R0
CLR A              ; A = 00H (clear all bits)
CPL A              ; A = one's complement of A (flip all bits)
RL  A              ; Rotate A left (bit 7 → bit 0)
RLC A              ; Rotate left through carry (bit 7 → CY, CY → bit 0)
RR  A              ; Rotate A right (bit 0 → bit 7)
RRC A              ; Rotate right through carry (bit 0 → CY, CY → bit 7)
SWAP A             ; Swap upper and lower nibbles of A
                   ; Example: A = C3H (1100 0011) → A = 3CH (0011 1100)
```

### Category 3: Data Transfer Operations

```asm
MOV  A, R1         ; A = R1 (internal register)
MOV  A, 020H       ; A = contents of RAM address 20H (direct)
MOV  A, @R0        ; A = contents of address pointed by R0 (indirect)
MOV  A, #55H       ; A = 55H (immediate)
MOV  DPTR, #1032H  ; DPTR = 1032H (16-bit immediate)
MOVC A, @A+DPTR    ; A = code memory at A + DPTR (look-up table)
MOVC A, @A+PC      ; A = code memory at A + PC
MOVX A, @DPTR      ; A = external data memory at DPTR (16-bit address)
MOVX A, @R0        ; A = external data memory at R0 (8-bit address)
PUSH 22H           ; SP++, then stack[SP] = contents of address 22H
POP  DPH           ; DPH = stack[SP], then SP--
XCH  A, @R0        ; Swap A with contents at address pointed by R0
XCHD A, @R0        ; Swap ONLY LOWER NIBBLES of A and @R0
```

> 🔥 **PUSH/POP difference from 8086**: In 8051, PUSH **increments SP first**, then stores. In 8086, PUSH decrements SP first, then stores.

### Category 4: Boolean (Bit) Variable Operations

```asm
CLR  C             ; Clear carry flag (CY = 0)
CLR  P2.7          ; Clear bit 7 of Port 2
SETB C             ; Set carry flag (CY = 1)
SETB P2.0          ; Set bit 0 of Port 2
CPL  P2.1          ; Complement bit 1 of Port 2
ANL  C, P2.7       ; CY = CY AND P2.7
ANL  C, /OV        ; CY = CY AND (NOT OV) — slash means complement of bit
ORL  C, P2.7       ; CY = CY OR P2.7
MOV  C, P2.0       ; CY = P2.0
MOV  P2.3, C       ; P2.3 = CY
JC   label         ; Jump if CY = 1
JNC  label         ; Jump if CY = 0
JB   bit, label    ; Jump if bit = 1
JNB  bit, label    ; Jump if bit = 0
JBC  bit, label    ; Jump if bit = 1, then CLEAR the bit
```

### Category 5: Program Branching

```asm
ACALL addr11       ; Absolute call (within 2K page)
LCALL addr16       ; Long call (anywhere in 64K)
RET                ; Return from subroutine (pop PC from stack)
RETI               ; Return from interrupt (pop PC + re-enable interrupts)
AJMP  addr11       ; Absolute jump (within 2K)
LJMP  addr16       ; Long jump (anywhere in 64K)
SJMP  rel          ; Short jump (+127/-128 bytes)
JMP   @A+DPTR      ; Indirect jump: PC = A + DPTR (jump table)
JZ    rel          ; Jump if A = 0
JNZ   rel          ; Jump if A ≠ 0
CJNE  A, #50H, rel ; Compare A with 50H; jump if not equal
                   ; Also sets CY if A < 50H
DJNZ  R5, rel      ; Decrement R5; jump if R5 ≠ 0 (loop counter)
NOP                ; No operation (1 machine cycle — useful for timing)
```

> 🔥 **RETI vs RET**: Both pop PC from stack. But **RETI** also tells the interrupt system that the ISR is done, allowing it to accept new interrupts. Using RET instead of RETI in an ISR would prevent future interrupts from being serviced.

---

## 10. Timers in 8051 🔥🔥

> 🔥 **Very likely 12-mark question** — either timer theory or a timer programming question.

### Basics:
- 8051 has **2 timers**: Timer 0 and Timer 1
- Both are **16-bit** (split into THx and TLx, each 8-bit)
- Can be used as **timers** (internal clock) or **counters** (external pulses)

### TMOD Register (Timer Mode — 8-bit, NOT bit-addressable):

```
 Bit:   7      6      5      4      3      2      1      0
      ┌──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┐
      │ GATE │ C/T̅  │  M1  │  M0  │ GATE │ C/T̅  │  M1  │  M0  │
      └──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┘
      ◄──── Timer 1 ────────────►  ◄──── Timer 0 ────────────►
```

| Bit | Name | Function |
|---|---|---|
| **GATE** | Gate | 0 = Timer controlled by software (TRx). 1 = Timer controlled by hardware (INTx̅ pin AND TRx) |
| **C/T̅** | Counter/Timer | 0 = Timer mode (internal clock). 1 = Counter mode (external pulses on T0/T1 pin) |
| **M1, M0** | Mode Select | 00 = Mode 0, 01 = Mode 1, 10 = Mode 2, 11 = Mode 3 |

### Timer Modes:

| Mode | M1 | M0 | Description |
|---|---|---|---|
| **Mode 0** | 0 | 0 | 13-bit timer (8-bit TH + 5-bit TL) — legacy MCS-48 compatible |
| **Mode 1** | 0 | 1 | **16-bit timer** (TH + TL, full range 0000–FFFF) |
| **Mode 2** | 1 | 0 | **8-bit auto-reload** (TL counts; when overflow, TH value auto-copied to TL) |
| **Mode 3** | 1 | 1 | Split timer (Timer 0 only: TL0 is 8-bit timer, TH0 is 8-bit timer using Timer 1's controls) |

### TCON Register (Timer Control — 8-bit, bit-addressable):

```
 Bit:   7      6      5      4      3      2      1      0
      ┌──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┐
      │ TF1  │ TR1  │ TF0  │ TR0  │ IE1  │ IT1  │ IE0  │ IT0  │
      └──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┘
      ◄── Timer 1 ──┤◄── Timer 0 ──┤◄── Ext Int 1 ─┤◄── Ext Int 0
```

| Bit | Name | Function |
|---|---|---|
| **TF1/TF0** | Timer Flag | Set to 1 when timer overflows. Cleared by hardware (interrupt) or software. |
| **TR1/TR0** | Timer Run | SETB TRx = start timer; CLR TRx = stop timer |
| **IE1/IE0** | Interrupt Edge Flag | Set when external interrupt edge detected |
| **IT1/IT0** | Interrupt Type | 1 = edge-triggered; 0 = level-triggered |

### Mode 1 Programming Steps (16-bit Timer) 🔥:
1. Load TMOD (select timer and mode)
2. Load TH and TL with initial count
3. Start timer: `SETB TRx`
4. Monitor TF flag: `JNB TFx, $` (wait loop)
5. Stop timer: `CLR TRx`
6. Clear flag: `CLR TFx`
7. Reload TH/TL and repeat

### Timer Value Calculation 🔥🔥:
For XTAL = 11.0592 MHz:
- Machine cycle = 12 / 11.0592 MHz = **1.085 µs**

```
Step 1: Count needed = Desired delay / 1.085 µs

Step 2: Load value = 65536 - Count  (for Mode 1, 16-bit)
                    = 256 - Count    (for Mode 2, 8-bit)

Step 3: Convert to hex → TH = high byte, TL = low byte
```

**Example**: Generate 5 ms delay with Timer 0, Mode 1:
```
Count = 5 ms / 1.085 µs = 4608
Load value = 65536 - 4608 = 60928 = EE00H
→ TH0 = EEH, TL0 = 00H
```

### Timer Mode 1 — Example Program (Toggle P1.5 every delay) 🔥:

```asm
      ORG  0000H              ; Program starts at address 0000H

MAIN:
      MOV  TMOD, #01H         ; Timer 0, Mode 1 (16-bit timer)
                               ; GATE=0, C/T=0, M1=0, M0=1

AGAIN:
      MOV  TL0, #00H          ; Load low byte of timer
      MOV  TH0, #0EEH         ; Load high byte (EE00H for ~5ms delay)

      SETB TR0                ; Start Timer 0
                               ; Timer begins counting up from EE00H

WAIT:
      JNB  TF0, WAIT          ; Stay in loop until TF0 = 1
                               ; (Timer overflows from FFFFH to 0000H)

      CLR  TR0                ; Stop Timer 0
      CLR  TF0                ; Clear overflow flag for next round

      CPL  P1.5               ; Toggle (complement) pin P1.5
                               ; Creates a square wave

      SJMP AGAIN              ; Repeat: reload timer and do it again

      END                     ; End of program
```

### Mode 2 Programming (8-bit Auto-Reload) 🔥:
- Only TL counts (0→FF); when overflows, **TH value auto-copied to TL**
- No need to manually reload — saves code and time

```asm
      MOV  TMOD, #02H         ; Timer 0, Mode 2 (8-bit auto-reload)
      MOV  TH0, #03H          ; Load reload value (auto-copied to TL0)
                               ; Timer counts: 03→04→...→FE→FF→00(TF set)→03(auto-reload)
                               ; That's 253 counts (FFH - 03H + 1 = 253)
      SETB TR0                ; Start timer
BACK:
      JNB  TF0, BACK          ; Wait for overflow
      CLR  TF0                ; Clear flag (NO need to reload TH0!)
      CPL  P1.0               ; Toggle output pin
      SJMP BACK               ; Repeat
```

### Counter Mode:
- When C/T̅ = 1, timer counts **external pulses** on pin T0 (pin 14) or T1 (pin 15)
- Everything else is same as timer programming
- External pulse must be held HIGH for 1 machine cycle and LOW for 1 machine cycle (minimum)

---

## 11. Serial Communication 🔥

### Parallel vs Serial:

| Feature | Parallel | Serial |
|---|---|---|
| **Data Transfer** | Multiple bits at a time (8+ lines) | One bit at a time (single line) |
| **Distance** | Short distance | Long distance |
| **Cost** | Expensive (more wires) | Cheap |
| **Speed** | Fast | Slower |
| **Example** | PCI | SPI, I2C, UART |

---

## 12. 8051 Interrupts 🔥🔥

### Interrupt Sources (6 total):

| # | Source | Vector Address | Flag |
|---|---|---|---|
| 1 | Reset | 0000H | — |
| 2 | External Interrupt 0 (INT0̅) | **0003H** | IE0 |
| 3 | Timer 0 Overflow | **000BH** | TF0 |
| 4 | External Interrupt 1 (INT1̅) | **0013H** | IE1 |
| 5 | Timer 1 Overflow | **001BH** | TF1 |
| 6 | Serial Port (RI + TI) | **0023H** | RI or TI |
| 7 | Timer 2 (8052 only) | **002BH** | TF2 |

> 💡 **Memorize the vectors**: 03, 0B, 13, 1B, 23 → each is 8 apart (03 + 8 = 0B, 0B + 8 = 13, etc.)

### Interrupt Execution Steps:
1. Finish current instruction
2. Save PC on stack (push PC low byte, then high byte)
3. Jump to interrupt vector address
4. Execute ISR until **RETI**
5. Pop PC from stack → resume main program

### IE Register (Interrupt Enable — bit-addressable) 🔥:

```
 Bit:    7      6     5     4     3     2     1     0
       ┌──────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
       │  EA  │  —  │ ET2 │  ES │ ET1 │ EX1 │ ET0 │ EX0 │
       └──────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘
```

| Bit | Name | Function |
|---|---|---|
| **EA** | Enable All | **Master switch** — must be 1 for ANY interrupt to work |
| **ET2** | Enable Timer 2 | Timer 2 interrupt (8052 only) |
| **ES** | Enable Serial | Serial port interrupt |
| **ET1** | Enable Timer 1 | Timer 1 overflow interrupt |
| **EX1** | Enable External 1 | External interrupt 1 |
| **ET0** | Enable Timer 0 | Timer 0 overflow interrupt |
| **EX0** | Enable External 0 | External interrupt 0 |

**Enable Timer 0 and External 0 interrupts:**
```asm
; Method 1: Bit operations (recommended mid-program)
SETB EA              ; Enable all interrupts (master switch)
SETB ET0             ; Enable Timer 0 interrupt
SETB EX0             ; Enable External interrupt 0

; Method 2: MOV instruction (recommended at program start)
MOV IE, #10000011B   ; EA=1, ET0=1, EX0=1
```

### Default Priority Order (Power-on):

| Priority | Source |
|---|---|
| Highest | External Interrupt 0 (INT0) |
| ↓ | Timer 0 (TF0) |
| ↓ | External Interrupt 1 (INT1) |
| ↓ | Timer 1 (TF1) |
| Lowest | Serial Port (RI + TI) |

### IP Register (Interrupt Priority — bit-addressable):

```
 Bit:    7      6     5     4     3     2     1     0
       ┌──────┬─────┬─────┬─────┬─────┬─────┬─────┬─────┐
       │  —   │  —  │ PT2 │  PS │ PT1 │ PX1 │ PT0 │ PX0 │
       └──────┴─────┴─────┴─────┴─────┴─────┴─────┴─────┘
```

- Set bit to **1** = High priority
- Set bit to **0** = Low priority (default)
- A **high-priority** interrupt CAN interrupt a **low-priority** ISR
- A low-priority interrupt **CANNOT** interrupt a high-priority ISR

**Example**: `MOV IP, #00000100B` (set PX1 = 1)
→ Priority order: **INT1** > INT0 > Timer0 > Timer1 > Serial

---

## 13. Polling vs Interrupts

| Feature | Polling | Interrupts |
|---|---|---|
| **Method** | CPU continuously checks device status | Device signals CPU when ready |
| **CPU utilization** | Wastes CPU time checking | CPU free to do other work |
| **Response time** | Depends on polling frequency | Immediate (after current instruction) |
| **Complexity** | Simple to implement | More complex (ISR, IE, IP) |
| **Best for** | Few, predictable events | Multiple, unpredictable events |

---

## 💡 Conceptual Corner

### Q: Why does 8051 stack grow upward while 8086 stack grows downward?
**A**: It's a design choice. In 8051, the stack starts at 07H (top of Register Bank 0) and grows upward into general RAM. This means you must be careful not to let the stack overwrite your data in RAM locations 08H-7FH.

### Q: What happens if you use Timer 0 in Mode 1 without reloading TH/TL?
**A**: After the first overflow (TF0 = 1), the timer keeps counting from 0000H. The delay will be maximum (65536 machine cycles ≈ 71 ms at 11.0592 MHz) — NOT your desired delay. You MUST reload TH/TL for correct timing.

### Q: Why Mode 2 (auto-reload) is preferred for baud rate generation?
**A**: Mode 2 automatically reloads TH value into TL on overflow — no software intervention needed. This guarantees a perfectly constant baud rate without any timing jitter from reload instructions.

### Q: Difference between RET and RETI?
**A**: Both pop PC from stack. But RETI additionally informs the interrupt priority logic that the current ISR is finished. If you use RET instead of RETI, the interrupt system will think you're still in the ISR and won't service any same-or-lower-priority interrupts.

---

## 📝 Likely Exam Questions

### 8-Mark Questions
1. Compare Microprocessor and Microcontroller (10 points).
2. Explain the memory organization of 8051 with diagram.
3. Explain the PSW register of 8051 with all flag bits.
4. Explain any 5 addressing modes of 8051 with examples.
5. Explain the IE and IP registers of 8051 with interrupt priority.
6. Compare parallel and serial communication.

### 12-Mark Questions
1. (a) Draw and explain the architecture of 8051 with pin descriptions and features.
   OR
   (b) Write an 8051 assembly program to generate a square wave using Timer 0 in Mode 1. Explain each line.

2. (a) Explain the Timer modes (0, 1, 2) of 8051 with TMOD and TCON registers.
   OR
   (b) Explain the interrupt system of 8051 — sources, vectors, IE register, IP register, and priority.
