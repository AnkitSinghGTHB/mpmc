# Module 2: 8086 Microprocessor — Architecture & Programming

> **Exam Focus**: Mostly theory, less chance of programming. No programming from 8086.
> Expect 1 × 12-mark (a/b) + 1 × 8-mark question.
> Heavy on: Architecture (BIU/EU), Registers, Flags, Addressing Modes, Instruction Set, Directives.

---

## 📋 Quick Exam Cheatsheet

| What to Study | Marks Likely | Type |
|---|---|---|
| 8086 Architecture (BIU + EU) with diagram | 12 marks | Explain with diagram |
| Pin diagram (Min/Max mode signals) | 8-12 marks | Explain key pins |
| Registers table (all types + special functions) | 8 marks | Table |
| Flag Register (all 9 flags) | 8-12 marks | Explain each flag |
| Addressing Modes (all 12) with examples | 12 marks | Explain with examples |
| Instruction Set (6 categories) | 12 marks | Table + examples |
| Assembler Directives | 8 marks | Explain each |
| Interrupts & IVT | 8-12 marks | Explain + diagram |
| Procedures & Macros | 8 marks | Difference + example |

---

## 1. 8086 Overview

| Feature | Value |
|---|---|
| **Manufacturer** | Intel |
| **Year** | 1978 |
| **Generation** | 3rd Generation |
| **Technology** | HMOS (now HMOS III) |
| **Package** | 40-pin DIP |
| **Supply** | +5V |
| **Data Bus** | 16-bit |
| **Address Bus** | 20-bit → can address 2²⁰ = **1 MB** memory |
| **Transistors** | ~29,000 |
| **Clock** | No internal clock; needs external 8284A clock generator with **33% duty cycle** |
| **Instruction Queue** | 6 bytes (FIFO) |

---

## 2. Microprocessor Generations (Quick Reference)

| Gen | Period | Technology | Example | Processor Width |
|---|---|---|---|---|
| 1st | 1971-73 | PMOS | Intel 4004 | 4-bit (16 pins) |
| 2nd | 1973 | NMOS | Intel 8085 | 8-bit (40 pins) |
| 3rd | 1978 | HMOS | Intel 8086 | 16-bit (40/48/64 pins) |
| 4th | 1980s | HCMOS | Intel 80386 | 32-bit |
| 5th | 1990s+ | — | Pentium | 32/64-bit |

---

## 3. 8086 Architecture — BIU & EU 🔥🔥

> 🔥 **Almost certain 12-mark question**. Draw and explain this diagram.

Intel split the 8086 into two independent units that work **simultaneously** (pipelining):

```
┌──────────────────────────────────────────────────────────────────────┐
│                        8086 MICROPROCESSOR                           │
│                                                                      │
│  ┌─────────────────────────────┐ ┌────────────────────────────────┐  │
│  │    EXECUTION UNIT (EU)      │ │  BUS INTERFACE UNIT (BIU)      │  │
│  │                             │ │                                │  │
│  │  ┌─────┐   ┌──────────┐     │ │  ┌──────────────────────────┐  │  │
│  │  │ AH  │AL │ General  │     │ │  │ Segment Registers        │  │  │
│  │  ├─────┤   │ Purpose  │     │ │  │ ┌────┐ ┌────┐ ┌────┐     │  │  │
│  │  │ BH  │BL │ Regs     │     │ │  │ │ CS │ │ DS │ │ SS │     │  │  │
│  │  ├─────┤   │ (16-bit) │     │ │  │ ├────┤ ├────┤ ├────┤     │  │  │
│  │  │ CH  │CL │          │     │ │  │ │ ES │ │    │ │    │     │  │  │
│  │  ├─────┤   └──────────┘     │ │  │ └────┘ └────┘ └────┘     │  │  │
│  │  │ DH  │DL                  │ │  └──────────────────────────┘  │  │
│  │  └─────┘                    │ │                                │  │
│  │                             │ │  ┌────────────────────┐        │  │
│  │  ┌──────┐  ┌──────┐         │ │  │ Instruction        │        │  │
│  │  │  SP  │  │  SI  │         │ │  │ Pointer (IP)       │        │  │
│  │  ├──────┤  ├──────┤         │ │  └────────────────────┘        │  │
│  │  │  BP  │  │  DI  │         │ │                                │  │
│  │  └──────┘  └──────┘         │ │  ┌────────────────────┐        │  │
│  │  Pointer    Index           │ │  │ Instruction Queue  │        │  │
│  │  Registers  Registers       │ │  │ (6-byte FIFO)      │        │  │
│  │                             │ │  └────────────────────┘        │  │
│  │  ┌─────────────────────┐    │ │                                │  │
│  │  │ Flag Register       │    │ │  ┌────────────────────┐        │  │
│  │  │ (16-bit)            │    │ │  │  20-bit Adder      │        │  │
│  │  └─────────────────────┘    │ │  │ (Segment×16+Offset)│        │  │
│  │                             │ │  └────────────────────┘        │  │
│  │  ┌─────────────────────┐    │ │              │                 │  │
│  │  │  16-bit ALU         │    │ │              ▼                 │  │
│  │  └─────────────────────┘    │ │     20-bit Address Bus         │  │
│  │                             │ │     16-bit Data Bus            │  │
│  │  ┌─────────────────────┐    │ │     Control Bus                │  │
│  │  │  EU Control System  │    │ │                                │  │
│  │  │  (Instruction       │    │ │                                │  │
│  │  │   Decoder)          │    │ │                                │  │
│  │  └─────────────────────┘    │ │                                │  │
│  └─────────────────────────────┘ └────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────┘
```

### Bus Interface Unit (BIU) — "The Postman"
- **Fetches** instructions from memory
- **Reads/writes** data to memory and I/O ports
- Contains:
  - **Segment registers** (CS, DS, SS, ES) — each 16-bit
  - **Instruction Pointer (IP)** — 16-bit, points to next instruction
  - **Instruction Queue** — 6-byte FIFO buffer for pre-fetched instructions
  - **20-bit Adder** — generates physical address: `Segment × 16 + Offset`

### Execution Unit (EU) — "The Worker"
- **Decodes** and **executes** instructions from the queue
- Contains:
  - **General Purpose Registers**: AX, BX, CX, DX (each 16-bit, split into high/low 8-bit)
  - **Pointer Registers**: SP, BP (16-bit)
  - **Index Registers**: SI, DI (16-bit)
  - **Flag Register** (16-bit, 9 flags used)
  - **16-bit ALU**
  - **EU Control System** (instruction decoder)

> 💡 **Think of a restaurant**: BIU is the waiter bringing orders (instructions) from the kitchen (memory). EU is the chef who cooks (executes) the orders. Both work simultaneously → **pipelining**.

---

## 4. Pipelining in 8086

- BIU **fetches** the next instruction while EU **executes** the current one
- Instruction Queue holds up to **6 bytes** of pre-fetched instructions
- This overlapping = **pipelining** → faster execution

```
Time:     T1      T2      T3      T4      T5
BIU:    [FETCH1][FETCH2][FETCH3][FETCH4][FETCH5]
EU:            [EXEC 1][EXEC 2][EXEC 3][EXEC 4]
          ↑ BIU and EU work simultaneously
```

---

## 5. Memory Segmentation in 8086 🔥

### The Problem:
- 8086 has **20-bit address bus** → can address 2²⁰ = 1 MB
- But all registers are only **16-bit** → max value = 2¹⁶ = 64 KB
- **How does a 16-bit register address 1 MB?** → **Segmentation!**

### The Solution:
- Divide 1 MB memory into **segments** of up to **64 KB** each
- Use a **Segment Register** (base) + **Offset** (within segment) to form 20-bit address

### Physical Address Calculation:
```
Physical Address = Segment Register × 16₁₀ + Offset

                   (Same as shifting segment left by 4 bits)

Example:  Segment = 89ABH, Offset = F012H

    89AB × 10H  =  89AB0H   (shift left by 1 hex digit)
  + F012H       =  0F012H
                  ─────────
                   98AC2H   ← 20-bit Physical Address
```

> 💡 **Think of it like a building**: Segment = Floor number, Offset = Room number on that floor.
> Floor 89AB, Room F012 → Actual room location in the whole building = 98AC2.

### 16 bytes of contiguous memory = 1 Paragraph

### Four Segments Active at Any Time:

```
┌──────────────────────────┐  ← 1 MB (FFFFFH)
│                          │
│   Extra Segment (ES)     │  64 KB max
│                          │
├──────────────────────────┤
│   Stack Segment (SS)     │  64 KB max
│                          │
├──────────────────────────┤
│   Data Segment (DS)      │  64 KB max
│                          │
├──────────────────────────┤
│   Code Segment (CS)      │  64 KB max
│                          │
└──────────────────────────┘  ← 00000H
```

---

## 6. 8086 Registers — Complete Reference 🔥🔥

### Register Categories:

| # | Type | Width | Registers |
|---|---|---|---|
| 1 | General Purpose | 16-bit (or 2×8-bit) | AX, BX, CX, DX |
| 2 | Pointer | 16-bit | SP, BP |
| 3 | Index | 16-bit | SI, DI |
| 4 | Instruction Pointer | 16-bit | IP |
| 5 | Segment | 16-bit | CS, DS, SS, ES |
| 6 | Flag (PSW) | 16-bit | Flag Register |

### 8-bit Split of General Purpose Registers:
```
AX = [ AH | AL ]     (Accumulator)
BX = [ BH | BL ]     (Base)
CX = [ CH | CL ]     (Counter)
DX = [ DH | DL ]     (Data)
```

### Register Special Functions 🔥

| Register | Name | Special Function |
|---|---|---|
| **AX/AL** | Accumulator | Stores results of arithmetic/logic ops. Used in I/O, MUL, DIV |
| **BX** | Base Register | Only GP register usable for memory addressing. Uses DS as default segment |
| **CX** | Count Register | Used as counter in SHIFT, ROTATE, and LOOP instructions |
| **DX** | Data Register | Holds data for MUL and DIV operations. Upper word of 32-bit results |
| **SP** | Stack Pointer | Offset of top of stack memory (used with SS) |
| **BP** | Base Pointer | Base value for based addressing using SS |
| **SI** | Source Index | Index of source operand for string instructions |
| **DI** | Destination Index | Index of destination operand for string operations |

### Segment Registers:

| Register | Points To | Offset Comes From |
|---|---|---|
| **CS** | Code Segment (where instructions live) | IP (Instruction Pointer) |
| **DS** | Data Segment (where data variables live) | SI, DI, BX, or 16-bit displacement |
| **SS** | Stack Segment (function call stack) | SP (stack ops) or BP (based addressing) |
| **ES** | Extra Segment (extra data, string destination) | DI (for string destination) |

---

## 7. Flag Register 🔥🔥

> 🔥 **Very likely 8-12 mark question**. Know all 9 flags.

The Flag Register is 16-bit, but only **9 bits are used** (6 status + 3 control flags):

```
Bit:  15  14  13  12  11  10   9   8   7   6   5   4   3   2   1   0
      --  --  --  --  OF  DF  IF  TF  SF  ZF  --  AF  --  PF  --  CF
                       ↑   ↑   ↑   ↑   ↑   ↑       ↑       ↑       ↑
                     Control Flags    Status Flags
```

### Status Flags (set automatically by ALU operations):

| Flag | Bit | Name | When is it SET (=1)? | Example |
|---|---|---|---|---|
| **CF** | 0 | Carry Flag | Carry out of MSB (addition) or borrow (subtraction) | 255 + 1 → CF=1 |
| **PF** | 2 | Parity Flag | Lower byte of result has **even** number of 1s | Result = 00110011 (four 1s) → PF=1 |
| **AF** | 4 | Auxiliary Carry | Carry from bit 3 to bit 4 (lower nibble to upper nibble) | Used in BCD arithmetic |
| **ZF** | 6 | Zero Flag | Result is **zero** | 5 - 5 = 0 → ZF=1 |
| **SF** | 7 | Sign Flag | Result is **negative** (MSB = 1) | Result = FFH (-1) → SF=1 |
| **OF** | 11 | Overflow Flag | Signed result too large for destination | 127 + 1 → OF=1 (8-bit signed overflow) |

### Control Flags (set/cleared by programmer):

| Flag | Bit | Name | When SET (=1) | When CLEARED (=0) |
|---|---|---|---|---|
| **TF** | 8 | Trap Flag | Single-step mode (interrupt after each instruction) | Normal execution |
| **IF** | 9 | Interrupt Flag | Recognizes external maskable interrupts (INTR) | Disables INTR interrupts |
| **DF** | 10 | Direction Flag | String operations process **high→low** address (auto-decrement) | Process **low→high** (auto-increment) |

> 💡 **Mnemonic for flags**: "**C**arry **P**arked **A** **Z**ebra, **S**itting **T**owards **I**ce **D**uring **O**ctober"
> CF, PF, AF, ZF, SF, TF, IF, DF, OF

---

## 8. 8086 Pin Diagram — Key Signals 🔥

### Common Signals (both modes):

| Pin | Name | Description |
|---|---|---|
| **AD0-AD15** | Address/Data Bus | Bidirectional, multiplexed — carry address in T1, data in T2-T4 |
| **A16/S3–A19/S6** | High Address/Status | Upper 4 address bits multiplexed with status |
| **BHE̅/S7** | Bus High Enable | Enables upper data bus (D8-D15) for 8-bit devices |
| **MN/MX̅** | Min/Max Mode | HIGH = Minimum mode; LOW = Maximum mode |
| **RD̅** | Read | Active low; signals a read operation |
| **READY** | Ready | Acknowledgement from slow device — data transfer complete |
| **RESET** | Reset | Active HIGH for ≥4 clock cycles; terminates all activity |
| **CLK** | Clock | External clock from 8284A; asymmetric, 33% duty cycle |
| **INTR** | Interrupt Request | Active HIGH, level-triggered, maskable interrupt |

### Minimum Mode Signals:

| Pin | Name | Description |
|---|---|---|
| **DT/R̅** | Data Transmit/Receive | Controls direction of data through transceivers |
| **DEN̅** | Data Enable | Output enable for 8286 transceivers; separates data from address/data bus |
| **ALE** | Address Latch Enable | Positive pulse at start of each operation; latches address from AD lines |
| **M/IO̅** | Memory/IO | HIGH = memory access; LOW = I/O access (IN/OUT) |
| **WR̅** | Write | Active LOW during write to memory or I/O |
| **INTA̅** | Interrupt Acknowledge | LOW when interrupt request is accepted |
| **HOLD** | Hold Request | Input from DMA controller requesting bus control |
| **HLDA** | Hold Acknowledge | 8086 acknowledges HOLD; releases the bus |

### Maximum Mode Signals:

| Pin | Name | Description |
|---|---|---|
| **S0̅, S1̅, S2̅** | Status | Decoded by 8288 bus controller to generate bus signals |
| **QS0, QS1** | Queue Status | Indicates current state of instruction queue |

### Min vs Max Mode:
| Feature | Minimum Mode (MN/MX̅ = HIGH) | Maximum Mode (MN/MX̅ = LOW) |
|---|---|---|
| **Coprocessor** | No coprocessor | Can work with 8087 coprocessor |
| **Multiprocessor** | Single processor only | Multi-processor system possible |
| **Bus Controller** | Control signals generated by 8086 itself | 8288 bus controller generates control signals |
| **DMA** | Uses HOLD/HLDA directly | Uses Request/Grant protocol |

---

## 9. Addressing Modes (All 12) 🔥🔥

> 🔥 **Almost guaranteed exam question**. Know at least 7-8 modes with examples.

### Group I: Register & Immediate Data

#### 1. Register Addressing
- Operand is in a **register**
- Example: `MOV CL, DH` → (CL) ← (DH)

#### 2. Immediate Addressing
- Data is **part of the instruction** itself
- Example: `MOV DL, 08H` → (DL) ← 08H
- Example: `MOV AX, 0A9FH` → (AX) ← 0A9FH

### Group II: Memory Data Addressing

**Physical Address Formula**:
```
Physical Address = Base Address + Effective Address
                 = (Segment Register × 16) + Offset
```

#### 3. Direct Addressing
- Effective address is **written directly** in the instruction
- Example: `MOV BX, [1354H]` → BX ← contents of memory at address DS×16 + 1354H

#### 4. Register Indirect Addressing
- Register holds the **EA** (Effective Address); registers: BX, BP, DI, SI
- Example: `MOV CX, [BX]`
  ```
  EA = (BX)
  BA = (DS) × 16
  Physical Address = BA + EA
  (CX) ← contents at Physical Address
  ```

#### 5. Based Addressing
- BX or BP holds base + signed displacement
- Example: `MOV AX, [BX + 08H]`
  ```
  08H → sign extended to 0008H
  EA = (BX) + 0008H
  If BX used → BA = (DS) × 16
  If BP used → BA = (SS) × 16
  ```

#### 6. Indexed Addressing
- SI or DI holds index + displacement
- Example: `MOV CX, [SI + 0A2H]`
  ```
  A2H → sign extended to FFA2H (negative)
  EA = (SI) + FFA2H
  BA = (DS) × 16
  ```

#### 7. Based Index Addressing
- Base (BX/BP) + Index (SI/DI) + displacement
- Example: `MOV DX, [BX + SI + 0AH]`
  ```
  EA = (BX) + (SI) + 000AH
  BA = (DS) × 16
  ```

#### 8. String Addressing
- For string operations; SI = source EA, DI = destination EA
- DS for source, ES for destination
- Example: `MOVS BYTE`
  ```
  Source:  MA = (DS)×16 + (SI)
  Dest:   MAE = (ES)×16 + (DI)
  (MAE) ← (MA)
  If DF=0: SI++, DI++    (auto-increment)
  If DF=1: SI--, DI--    (auto-decrement)
  ```

### Memory Addressing Combinations:
```
Valid combinations inside [ ]:
┌──────────────────────────────────────────────────────┐
│   [BX + SI]        [BX + SI + d8]    [BX + SI + d16] │
│   [BX + DI]        [BX + DI + d8]    [BX + DI + d16] │
│   [BP + SI]        [BP + SI + d8]    [BP + SI + d16] │
│   [BP + DI]        [BP + DI + d8]    [BP + DI + d16] │
│   [SI]             [SI + d8]         [SI + d16]      │
│   [DI]             [DI + d8]         [DI + d16]      │
│   d16 (direct)     [BP + d8]         [BP + d16]      │
│   [BX]             [BX + d8]         [BX + d16]      │
└──────────────────────────────────────────────────────┘
d8 = 8-bit displacement, d16 = 16-bit displacement
```

### Group III: I/O Port Addressing

#### 9. Direct I/O Port Addressing
- 8-bit port address directly in instruction
- Example: `IN AL, [09H]` → (AL) ← contents of port 09H

#### 10. Indirect I/O Port Addressing
- DX register holds port address (16-bit)
- Example: `OUT [DX], AX` → port at address (DX) ← (AX)

### Group IV: Program Control

#### 11. Relative Addressing
- EA relative to IP by 8-bit signed displacement
- Example: `JZ 0AH`
  ```
  If ZF=1: IP = (IP) + 000AH → jump forward 10 bytes
  If ZF=0: continue to next instruction
  ```

### Group V: Implied

#### 12. Implied Addressing
- No operands; instruction implies the data
- Example: `CLC` → clears carry flag to 0

---

## 10. Instruction Set (6 Categories) 🔥🔥

### Category 1: Data Transfer Instructions

| Mnemonic | Operation | Example |
|---|---|---|
| `MOV dst, src` | Copy src to dst | `MOV AX, BX` → AX ← BX |
| `XCHG dst, src` | Exchange (swap) | `XCHG AX, BX` → AX ↔ BX |
| `PUSH reg16` | Push onto stack | SP←SP-2, then store reg at SS:SP |
| `POP reg16` | Pop from stack | Load reg from SS:SP, then SP←SP+2 |
| `IN A, port` | Input from port | `IN AL, [09H]` |
| `OUT port, A` | Output to port | `OUT [DX], AX` |

### Category 2: Arithmetic Instructions

| Mnemonic | Operation | Notes |
|---|---|---|
| `ADD dst, src` | dst ← dst + src | Affects CF, PF, AF, ZF, SF, OF |
| `ADC dst, src` | dst ← dst + src + CF | Add with carry |
| `SUB dst, src` | dst ← dst - src | Subtraction |
| `SBB dst, src` | dst ← dst - src - CF | Subtract with borrow |
| `INC dst` | dst ← dst + 1 | Does NOT affect CF |
| `DEC dst` | dst ← dst - 1 | Does NOT affect CF |
| `MUL src` | Unsigned multiply | 8-bit: AX ← AL × src; 16-bit: DX:AX ← AX × src |
| `IMUL src` | Signed multiply | Same as MUL but signed |
| `DIV src` | Unsigned divide | 8-bit: AL=AX÷src (quotient), AH=remainder; 16-bit: AX=DX:AX÷src, DX=rem |
| `IDIV src` | Signed divide | Same as DIV but signed |
| `CMP dst, src` | Compare (subtract without storing) | Sets flags: if dst>src → CF=0,ZF=0; if dst<src → CF=1; if dst=src → ZF=1 |

### CMP Flag Results (Important!) 🔥

| Condition | CF | ZF | SF |
|---|---|---|---|
| dst **>** src | 0 | 0 | 0 |
| dst **<** src | 1 | 0 | 1 |
| dst **=** src | 0 | 1 | 0 |

### Category 3: Logical Instructions

| Mnemonic | Operation | Notes |
|---|---|---|
| `AND dst, src` | Bitwise AND | CF=0, OF=0 after AND |
| `OR dst, src` | Bitwise OR | CF=0, OF=0 after OR |
| `XOR dst, src` | Bitwise XOR | CF=0, OF=0. `XOR AX,AX` = fastest way to zero a register |
| `NOT dst` | One's complement | Inverts all bits |
| `TEST dst, src` | AND without storing result | Same as AND but dst is NOT modified; only flags change |
| `SHL/SAL` | Shift Left | MSB → CF, 0 → LSB |
| `SHR` | Logical Shift Right | 0 → MSB, LSB → CF |
| `SAR` | Arithmetic Shift Right | Sign bit preserved, LSB → CF |
| `ROL` | Rotate Left | MSB → CF and LSB |
| `ROR` | Rotate Right | LSB → CF and MSB |
| `RCL` | Rotate Left through Carry | MSB → CF, old CF → LSB |
| `RCR` | Rotate Right through Carry | LSB → CF, old CF → MSB |

> 💡 **TEST vs AND**: Both do AND operation, but TEST does **NOT change** the destination. Use TEST when you just want to check a condition (like even/odd) without modifying the register.

### Category 4: String Manipulation Instructions

Key rules:
- **SI** = source offset (with **DS**)
- **DI** = destination offset (with **ES**)
- **DF=0** → SI/DI auto-increment; **DF=1** → auto-decrement
- Increment/decrement by **1 for byte**, **2 for word**

| Mnemonic | Operation |
|---|---|
| `MOVS/MOVSB/MOVSW` | Move string byte/word from source to destination |
| `CMPS/CMPSB/CMPSW` | Compare string byte/word |
| `SCAS/SCASB/SCASW` | Scan (compare) string with AL/AX |
| `LODS/LODSB/LODSW` | Load string byte/word into AL/AX |
| `STOS/STOSB/STOSW` | Store AL/AX into string |
| `REP` | Repeat while CX ≠ 0, decrement CX |
| `REPZ/REPE` | Repeat while CX≠0 AND ZF=1 |
| `REPNZ/REPNE` | Repeat while CX≠0 AND ZF=0 |

### Category 5: Processor Control Instructions

| Mnemonic | Operation |
|---|---|
| `STC` | Set CF ← 1 |
| `CLC` | Clear CF ← 0 |
| `CMC` | Complement CF (flip) |
| `STD` | Set DF ← 1 |
| `CLD` | Clear DF ← 0 |
| `STI` | Set IF ← 1 (enable interrupts) |
| `CLI` | Clear IF ← 0 (disable interrupts) |
| `NOP` | No operation |
| `HLT` | Halt after interrupt is set |
| `WAIT` | Wait for TEST pin active |
| `LOCK` | Lock bus during next instruction |
| `ESC` | Pass instruction to coprocessor |

### Category 6: Control Transfer Instructions

**Unconditional:**

| Mnemonic | Description |
|---|---|
| `CALL` | Call subroutine (push IP, jump) |
| `RET` | Return from subroutine (pop IP) |
| `JMP` | Unconditional jump |

**Conditional (Signed):**

| Mnemonic | Meaning | Condition |
|---|---|---|
| `JE/JZ` | Jump if Equal / Zero | ZF=1 |
| `JNE/JNZ` | Jump if Not Equal / Not Zero | ZF=0 |
| `JG/JNLE` | Jump if Greater | ZF=0 AND SF=OF |
| `JGE/JNL` | Jump if Greater or Equal | SF=OF |
| `JL/JNGE` | Jump if Less | SF≠OF |
| `JLE/JNG` | Jump if Less or Equal | ZF=1 OR SF≠OF |

**Conditional (Unsigned):**

| Mnemonic | Meaning | Condition |
|---|---|---|
| `JA/JNBE` | Jump if Above | CF=0 AND ZF=0 |
| `JAE/JNB` | Jump if Above or Equal | CF=0 |
| `JB/JNAE` | Jump if Below | CF=1 |
| `JBE/JNA` | Jump if Below or Equal | CF=1 OR ZF=1 |

**Flag-based jumps:**

| Mnemonic | Condition |
|---|---|
| `JC/JNC` | CF=1 / CF=0 |
| `JP/JNP` | PF=1 / PF=0 |
| `JO/JNO` | OF=1 / OF=0 |
| `JS/JNS` | SF=1 / SF=0 |

**Iteration:**

| Mnemonic | Operation |
|---|---|
| `LOOP label` | Decrement CX; if CX≠0, jump to label |
| `LOOPE/LOOPZ` | Decrement CX; if CX≠0 AND ZF=1, jump |
| `LOOPNE/LOOPNZ` | Decrement CX; if CX≠0 AND ZF=0, jump |
| `JCXZ` | Jump if CX = 0 |

---

## 11. Assembler Directives 🔥

> These are instructions to the **assembler**, not the CPU. No machine code is generated.

| Directive | Purpose | Example |
|---|---|---|
| **DB** | Define Byte (8-bit variable) | `LIST DB 7FH, 42H, 35H` |
| **DW** | Define Word (16-bit variable) | `ALIST DW 6512H, 0F251H` |
| **SEGMENT/ENDS** | Mark start/end of a segment | `DATA SEGMENT ... DATA ENDS` |
| **ASSUME** | Tell assembler which segment register maps to which segment | `ASSUME CS:CODE, DS:DATA` |
| **ORG** | Set starting effective address | `ORG 1000H` — start storing at EA 1000H |
| **END** | Terminate program | Statements after END are ignored |
| **EVEN** | Align next data/code to even address | Ensures word-aligned access |
| **EQU** | Assign value to a variable | `COUNT EQU 10FEH` |
| **PROC/ENDP** | Define start/end of procedure | `ADD64 PROC NEAR ... RET ... ADD64 ENDP` |
| **NEAR** | Intrasegment call (same segment) | Only IP changes |
| **FAR** | Intersegment call (different segment) | Both CS and IP change |
| **SHORT** | Reserve 1 byte for 8-bit signed displacement | `JMP SHORT AHEAD` |
| **MACRO/ENDM** | Define start/end of macro | Macros expand inline at every call |
| **OFFSET** | Get 16-bit offset address of a label | `MOV SI, OFFSET LIST` |
| **LENGTH** | Get number of elements in data item | `MOV CX, LENGTH STRING1` |
| **LEA** | Load Effective Address | `LEA AX, Test` — loads address, not value |

### NEAR vs FAR Procedures:

| Feature | NEAR | FAR |
|---|---|---|
| Location | Same code segment as caller | Different code segment |
| What changes | Only **IP** | Both **CS and IP** |
| CALL action | Push IP, load new IP | Push CS, push IP, load new CS and IP |
| RET action | Pop IP | Pop IP, pop CS |
| Stack usage | 2 bytes | 4 bytes |

### Procedure vs Macro:

| Feature | Procedure | Macro |
|---|---|---|
| Definition | `PROC ... RET ... ENDP` | `MACRO ... ENDM` |
| Invocation | `CALL procname` | Just use `macroname` |
| Code in memory | Stored ONCE; jumped to | Expanded INLINE at every use |
| Memory usage | Efficient (one copy) | Uses more memory (duplicated) |
| Speed | Slower (CALL/RET overhead) | Faster (no call overhead) |
| Parameters | Through registers/stack | Directly via arguments |

---

## 12. 8086 Interrupts 🔥🔥

### Three Sources of Interrupts:
1. **External hardware signal** on NMI or INTR pin → Hardware Interrupt
2. **INT n** instruction (n = 00H–FFH) → Software Interrupt
3. **Error conditions** during execution → Internal Interrupt

### Interrupt Processing Steps 🔥
When an interrupt is received, the 8086:
1. **Pushes** Flag Register onto stack (SP ← SP - 2)
2. **Clears IF** (disables further INTR interrupts)
3. **Clears TF** (disables single-step mode)
4. **Pushes CS** onto stack (SP ← SP - 2)
5. **Pushes IP** onto stack (SP ← SP - 2)
6. **Loads new CS:IP** from Interrupt Vector Table → jumps to ISR

```
  Main Program          ISR (Interrupt Service Routine)
  ┌──────────┐          ┌──────────────┐
  │   ...    │          │   ...        │
  │   ...    │ ──INT──► │   ...        │
  │   ...    │          │   IRET       │
  │   ...    │ ◄─IRET── └──────────────┘
  │   ...    │
  └──────────┘
```

### Interrupt Vector Table (IVT) 🔥

- Located at addresses **00000H to 003FFH** (first 1 KB of memory)
- Contains **256** interrupt vectors (Type 0 to Type 255)
- Each vector = **4 bytes** (2 for IP, 2 for CS)
- **Offset in IVT** = Type Number × 4

```
Memory Map of IVT:
┌────────────────────────────────┐ 003FFH
│  Type 255 (Available)          │  ← CS:IP for INT FFH
│  ...                           │
│  Type 32 (Available - User)    │  ← First user-available
├────────────────────────────────┤ 00080H
│  Type 5-31 (Reserved)          │  ← For higher processors
├────────────────────────────────┤ 00014H
│  Type 4: Overflow (INTO)       │  00010H-00013H
│  Type 3: Breakpoint (INT 3)    │  0000CH-0000FH
│  Type 2: NMI                   │  00008H-0000BH
│  Type 1: Single Step (Trap)    │  00004H-00007H
│  Type 0: Divide by Zero        │  00000H-00003H
└────────────────────────────────┘ 00000H
```

**Example**: INT 02H → Offset = 02 × 4 = 08 = 00008H
At 00008H: IP (2 bytes), at 0000AH: CS (2 bytes)

### Three Groups of 256 Interrupts:

| Group | Types | Count | Purpose |
|---|---|---|---|
| **Dedicated** | 0-4 | 5 | Fixed operations |
| **Reserved** | 5-31 | 27 | For higher processors (80286, 80386) |
| **Available** | 32-255 | 224 | User-defined (H/W via INTR or S/W via INT n) |

### Dedicated Interrupts:

| Type | Name | Trigger |
|---|---|---|
| **Type 0** | Divide by Zero | Quotient too large or divide by zero |
| **Type 1** | Single Step (Trap) | When TF=1, interrupt after every instruction |
| **Type 2** | NMI (Non-Maskable) | Positive edge on NMI pin; cannot be disabled |
| **Type 3** | Breakpoint | One-byte INT 3 instruction; used for breakpoints |
| **Type 4** | Overflow | Triggered by INTO instruction when OF=1 |

---

## 💡 Conceptual Corner

### Q: Why are address and data lines multiplexed (AD0-AD15)?
**A**: The 8086 has only 40 pins. To fit 20 address lines + 16 data lines + control signals into 40 pins, Intel multiplexed the lower 16 address lines with the 16 data lines. **ALE** (Address Latch Enable) tells external circuits when address is valid vs when data is being transferred.

### Q: How does the 8086 access more than 64KB with 16-bit registers?
**A**: Through **segmentation**. A segment register provides the base address (shifted left by 4 bits) and an offset register provides the distance within the segment. Together: 16-bit segment × 16 + 16-bit offset = 20-bit physical address = 1 MB addressable.

### Q: What's the difference between NEAR and FAR calls?
**A**: NEAR CALL = within the same code segment → only IP changes (2 bytes on stack). FAR CALL = to a different code segment → both CS and IP change (4 bytes on stack). Use NEAR when the subroutine is in the same segment; FAR when it's in a different segment.

### Q: Why does 8086 need an external clock (8284A)?
**A**: 8086 does NOT have an on-chip clock generator. The 8284A provides the asymmetric square wave with 33% duty cycle required by the 8086's internal timing.

### Q: What happens during RESET?
**A**: The processor terminates ALL current activity. The RESET signal must be HIGH for at least 4 clock cycles. After RESET, IP = 0000H, CS = FFFFH → execution starts at FFFF0H (reset vector).

---

## 📝 Likely Exam Questions

### 8-Mark Questions
1. Explain the flag register of 8086 with all 9 flags.
2. List and explain any 6 addressing modes of 8086 with examples.
3. Explain the register set of 8086 with a table showing special functions.
4. Differentiate between NEAR and FAR procedures. Write an example.
5. Explain the assembler directives DB, DW, SEGMENT, ASSUME, ORG, EQU.
6. Explain the interrupt processing steps in 8086.

### 12-Mark Questions
1. (a) Draw and explain the architecture of 8086 showing BIU and EU with all components.
   OR
   (b) Explain all 12 addressing modes of 8086 with examples.

2. (a) Explain the complete instruction set of 8086 with categories and examples.
   OR
   (b) Explain the interrupt system of 8086 — types, IVT, processing steps, and dedicated interrupts.
