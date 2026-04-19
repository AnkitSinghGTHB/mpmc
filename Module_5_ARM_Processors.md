# Module 5: ARM Processors & Advanced Architectures

> **Exam Focus**: Total theory module. Expect 1 × 12-mark (a/b) + 1 × 8-mark question.
> Key topics: RISC vs CISC, ARM architecture (ATDMI-T), Pipelining (3 or 5 stage), 7 Modes, Controller comparisons.
> ❌ No addressing modes or instructions from this module.

---

## 📋 Quick Exam Cheatsheet

| What to Study | Marks Likely | Type |
|---|---|---|
| RISC vs CISC comparison | 8-12 marks | Table (10+ points) |
| ARM architecture — ATDMI-T | 8-12 marks | Explain acronym + features |
| 7 Processor Modes of ARM | 8 marks | List & explain |
| Pipelining (3-stage ARM7 vs 5-stage ARM9) | 12 marks | Diagram + explanation |
| Pipeline hazards | 8 marks | Table + explain |
| Difference between 8051, PIC, AVR, ARM | 12 marks | Comparison table |
| AMBA Bus Architecture (AHB vs APB) | 8 marks | Explain + compare |

---

## 1. Introduction to ARM

- **ARM** = originally **Acorn RISC Machine**, developed by British company **Acorn Computers** in the 1980s
- Official project started **October 1983**, with VLSI Technology as silicon partner
- Designed by **Wilson and Furber**
- ARM is a **32-bit RISC** (Reduced Instruction Set Computing) load/store architecture
- Now widely used in: mobile phones, tablets, multimedia players, gaming systems, hard drives, PDAs

### ARM Advantages:
- **Cheaper** compared to other processors (like Intel)
- Designed for **low power consumption** → better battery life
- Performs **one operation at a time** → works faster
- Wide **availability** and application support
- Memory is not accessed directly — handled through **registers**

---

## 2. RISC vs CISC 🔥🔥

> 🔥 **VERY HIGH probability exam question**. Write this as a full 10-12 point comparison.

| # | Feature | RISC (Reduced Instruction Set) | CISC (Complex Instruction Set) |
|---|---|---|---|
| 1 | **Full Form** | Reduced Instruction Set Computing | Complex Instruction Set Computing |
| 2 | **Instruction Set** | Small, simple set of instructions | Large, complex set of instructions |
| 3 | **Instruction Size** | Fixed length (32-bit in ARM) | Variable length |
| 4 | **Execution Time** | Most execute in 1 clock cycle | May take multiple clock cycles |
| 5 | **Addressing Modes** | Few, simple modes | Many, complex modes |
| 6 | **Architecture** | Load/Store — only LOAD and STORE access memory | Any instruction can access memory directly |
| 7 | **Registers** | Large number of general-purpose registers (37 in ARM) | Fewer registers |
| 8 | **Pipelining** | Highly efficient pipelining | Pipelining is difficult due to variable-length instructions |
| 9 | **Hardware Complexity** | Simple hardware, complex compiler | Complex hardware, simpler compiler |
| 10 | **Power Consumption** | Low power (ideal for mobile/embedded) | Higher power consumption |
| 11 | **Code Size** | More lines of code needed | Fewer lines (complex instructions do more) |
| 12 | **Examples** | ARM, MIPS, SPARC | Intel x86, 8086, Motorola 680x0 |

### Easy Way to Remember:
> **RISC** = "**R**eally **I**s **S**imple and **C**heap" — simple instructions, low power, many registers
> **CISC** = "**C**omplex **I**nstructions, **S**low **C**ycles" — complex instructions, fewer registers, more powerful per instruction

---

## 3. ARM Architecture — ATDMI-T 🔥🔥

> 🔥 The acronym **ARM → ATDMI-T** breaks down the architecture's key feature extensions.

### What does ATDMI-T stand for?

```
A R M 7 T D M I - T

A  →  ARM architecture (base)
T  →  Thumb instruction set support (16-bit compressed instructions)
D  →  Debug support (on-chip debugging via JTAG)
M  →  Enhanced Multiplier (fast 32×32 multiply, 64-bit result)
I  →  EmbeddedICE (hardware breakpoints and watchpoints)

The trailing -T  →  Thumb support confirmed
```

### Detailed Breakdown:

| Letter | Feature | What It Means |
|---|---|---|
| **A** | ARM | Base 32-bit RISC architecture |
| **T** | Thumb | Supports 16-bit compressed instruction set (reduces code size by ~35%) |
| **D** | Debug | Built-in debug support; allows external debugger to control processor via JTAG |
| **M** | Multiplier | Enhanced hardware multiplier — 32-bit × 32-bit → 64-bit result |
| **I** | EmbeddedICE | Hardware breakpoint and watchpoint unit built into the chip |

> 💡 **Think of it like a phone model name**: ARM7**TDMI** means the ARM7 core with **T**humb + **D**ebug + **M**ultiplier + embedded**I**CE features.

### Main Features of ARM Instruction Set:
- All instructions are **32 bits long**
- Most instructions execute in a **single cycle**
- **Every instruction can be conditionally executed** (unique to ARM!)
- **Load/Store architecture** — data processing acts ONLY on registers
  - Three operand format
  - Combined ALU and shifter for high-speed bit manipulation
- Specific memory access instructions with powerful **auto-indexing** addressing modes
- Supports 32-bit, 16-bit, and 8-bit data types
- Flexible **multiple register load/store** instructions
- Instruction set extension via **coprocessors**

---

## 4. ARM Registers 🔥

ARM has **37 registers**, all **32-bits long**:

```
┌─────────────────────────────────────────────────┐
│               ARM REGISTER SET                  │
│                                                 │
│  30 × General Purpose Registers (r0-r14 banks)  │
│   1 × Program Counter (r15 / PC)                │
│   1 × Current Program Status Register (CPSR)    │
│   5 × Saved Program Status Registers (SPSRs)    │
│                                                 │
│  Total = 30 + 1 + 1 + 5 = 37 registers          │
└─────────────────────────────────────────────────┘
```

### Special Registers:

| Register | Alias | Purpose |
|---|---|---|
| **R15** | **PC** (Program Counter) | Points to instruction being FETCHED (not executed). Any instruction with PC as destination = branch. |
| **R14** | **LR** (Link Register) | Saves return address when executing `BL` (Branch with Link) or entering interrupt. Copied back to PC on return. |
| **R13** | **SP** (Stack Pointer) | No hardware stack in ARM! R13 is conventionally used as stack pointer by software. |
| **CPSR** | — | Current Program Status Register — holds flags (N, Z, C, V) + mode bits + interrupt masks |
| **SPSR** | — | Saved PSR — saves copy of CPSR when entering exception/interrupt. Restored on return. NOT available in User/System modes. |

### Register Banking:
- The current **processor mode** determines which bank of registers is accessible
- Each mode can access:
  - A particular set of **r0-r12**
  - Its own **r13 (SP)** and **r14 (LR)**
  - **r15 (PC)** and **CPSR** (shared)
  - **SPSR** (privileged modes only, except System)

---

## 5. Seven Processor Modes of ARM 🔥🔥

> 🔥 **Definite exam question**. Know all 7 modes with one-line descriptions.

| # | Mode | Type | When Does It Activate? |
|---|---|---|---|
| 1 | **User** | Unprivileged | Normal mode — most tasks/applications run here |
| 2 | **FIQ** | Privileged | **Fast Interrupt** — entered on high-priority interrupt |
| 3 | **IRQ** | Privileged | **Normal Interrupt** — entered on low-priority interrupt |
| 4 | **SVC** (Supervisor) | Privileged | Entered on **Reset** and when **SWI** (Software Interrupt) instruction executes |
| 5 | **Abort** | Privileged | Handles **memory access violations** (invalid address) |
| 6 | **Undef** (Undefined) | Privileged | Handles **undefined/illegal instructions** |
| 7 | **System** | Privileged | Uses **same registers as User mode** — a privileged version of User |

### Mnemonics to Remember (FUIS-UAb):
> **F**IQ, **U**ser, **I**RQ, **S**VC, **U**ndef, **A**bort, **S**ystem
> Or think: "**FUNSIUS**" — **F**IQ **U**ser **N**(IRQ) **S**VC **I**(Undef) **U**(System) **S**(Abort)

### Key Differences:
- **User** is the ONLY unprivileged mode — cannot directly access hardware or change modes
- **FIQ** has more **banked registers** (r8-r14) than IRQ — that's what makes it "fast" (no need to save/restore as many registers)
- **System** mode is special — it's privileged but uses User's register bank (useful for OS tasks that need privileges but user-mode register layout)

---

## 6. ARM Pipelining 🔥🔥

> 🔥 **Very likely 12-mark question**: "Explain 3-stage and 5-stage pipelining in ARM processors."

### What is Pipelining?
- Instead of completing one instruction before starting the next, **overlap** multiple instructions
- Like a factory assembly line: while one product is being painted, the next is being assembled

### 6.1 ARM7 — 3-Stage Pipeline

```
Cycle:     1     2     3     4     5     6
          ┌─────┬─────┬─────┬─────┬─────┬─────┐
Instr 1:  │FETCH│DECOD│EXEC │     │     │     │
          ├─────┼─────┼─────┼─────┼─────┼─────┤
Instr 2:  │     │FETCH│DECOD│EXEC │     │     │
          ├─────┼─────┼─────┼─────┼─────┼─────┤
Instr 3:  │     │     │FETCH│DECOD│EXEC │     │
          ├─────┼─────┼─────┼─────┼─────┼─────┤
Instr 4:  │     │     │     │FETCH│DECOD│EXEC │
          └─────┴─────┴─────┴─────┴─────┴─────┘
```

**Three stages:**

| Stage | What Happens | PC Points Here |
|---|---|---|
| **FETCH** | Instruction fetched from memory into internal register | **PC** |
| **DECODE** | Registers used in instruction are decoded; register bank read | **PC - 4** |
| **EXECUTE** | Shift + ALU operation; write results back to register bank | **PC - 8** |

> **Key Point**: PC always points to the instruction being **FETCHED**, not the one being executed. The executing instruction is at **PC - 8**.

- Simple instructions like `ADD` complete at rate of **one per cycle**
- Complex instructions like `STR` (Store) take **2 effective cycles** (+1 extra)

### 6.2 ARM9 — 5-Stage Pipeline

```
Cycle:     1     2     3     4     5     6     7
          ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐
Instr 1:  │FETCH│DECOD│EXEC │ MEM │WRITE│     │     │
          ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
Instr 2:  │     │FETCH│DECOD│EXEC │ MEM │WRITE│     │
          ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
Instr 3:  │     │     │FETCH│DECOD│EXEC │ MEM │WRITE│
          └─────┴─────┴─────┴─────┴─────┴─────┴─────┘
```

**Five stages:**

| Stage | What Happens |
|---|---|
| **FETCH** | Instruction fetched from memory |
| **DECODE** | Instruction decoded + Thumb→ARM decompression + register selection |
| **EXECUTE** | Register read + Shift + ALU operation |
| **MEMORY** | Memory access (for Load/Store instructions) |
| **WRITE** | Write results back to register bank |

### ARM7 (3-stage) vs ARM9 (5-stage) Comparison:

| Feature | ARM7 (3-stage) | ARM9 (5-stage) |
|---|---|---|
| Pipeline stages | 3 (Fetch, Decode, Execute) | 5 (Fetch, Decode, Execute, Memory, Write) |
| Throughput | Good | Better — higher clock speeds possible |
| Hazard risk | Lower | Higher — more stages = more potential stalls |
| Complexity | Simpler | More complex controller needed |
| Thumb decompression | In Decode stage | Separate step in Decode stage |
| Memory access | Done in Execute stage | Separate Memory stage |

### Beyond 5-Stage: ARM10 and ARM11

- **ARM10**: 6 stages — adds Branch Prediction and separates Multiply stage
- **ARM11**: 8 stages — further splits Fetch (2 stages), adds Saturate, MAC (3 stages for multiply-accumulate), Data Cache (2 stages)

> **Pattern**: More stages → higher clock frequency possible → but more complex hazard management needed

---

## 7. Pipeline Hazards 🔥

| Hazard Type | Cause | Example |
|---|---|---|
| **Data Hazard** | Instruction depends on result of a previous instruction | `ADD R1,R2,R3` followed by `SUB R4,R1,R5` — R1 not ready yet |
| **Control Hazard** | Branch instruction changes program flow — pipeline has already fetched wrong instructions | `BNE label` — pipeline fetched next 2 instructions, but branch jumps elsewhere |
| **Structural Hazard** | Two instructions need the same hardware resource at the same time | Two instructions both need memory access in the same cycle |

---

## 8. Comparison: 8051 vs PIC vs AVR vs ARM 🔥🔥

> 🔥 **Very likely 12-mark question**. This is the "difference between various controllers" question.

### 8051 Microcontroller
- Developed by **Intel in 1981**
- **8-bit** processor, called "System on a Chip"
- 128 bytes RAM, 4KB ROM, 2 Timers, 1 Serial port, 4 ports (32 I/O pins)
- CPU works with 8 bits at a time; larger data broken into parts
- ROM can be extended up to 64KB

### PIC Microcontroller
- Developed by **Microchip**
- **PIC** = Peripheral Interface Controller
- Fast and simple to program compared to 8051
- Contains RAM, ROM, CPU, Timer, Counter, ADC, DAC
- Supports protocols: **CAN, SPI, UART**
- Uses **RISC** architecture
- Popular for hardware interfacing

### AVR Microcontroller
- Developed by **Atmel Corporation in 1996**
- Designers: Alf-Egil Bogen and Vegard Wollan
- **AVR** = **A**lf-egil **V**egard **R**ISC (also called Advanced Virtual RISC)
- First chip: AT90S8515; First commercial: AT90S1200 (1997)
- Three categories:
  - **TinyAVR**: Less memory, small size, simple applications
  - **MegaAVR**: Good memory (up to 256KB), most popular, moderate-complex apps
  - **XmegaAVR**: Commercial, complex applications, large memory, high speed

### ARM Microcontroller
- Developed by **ARM Holdings** (British)
- 32-bit RISC architecture
- Used in mobile phones, tablets, multimedia players
- Low power, high performance
- Load/store architecture with 37 registers

### Full Comparison Table 🔥

| # | Feature | 8051 | PIC | AVR | ARM |
|---|---|---|---|---|---|
| 1 | **Developer** | Intel (1981) | Microchip | Atmel (1996) | ARM Holdings |
| 2 | **Data Width** | 8-bit | 8/16/32-bit | 8-bit | 32-bit |
| 3 | **Architecture** | CISC (Harvard) | RISC (Harvard) | RISC (Harvard) | RISC (Load/Store) |
| 4 | **Memory** | 128B RAM, 4KB ROM | Varies | Up to 256KB | Large (varies) |
| 5 | **Speed** | Slow (12 clocks/cycle) | Moderate | Fast (1 clock/cycle many instr) | Very fast |
| 6 | **Power** | Moderate | Low | Low | Very low |
| 7 | **Cost** | Very cheap | Cheap | Moderate | Moderate-High |
| 8 | **Peripherals** | Basic (Timer, Serial, I/O) | ADC, DAC, CAN, SPI, UART | ADC, SPI, I2C, UART | Full SoC features |
| 9 | **Applications** | Industrial control, simple embedded | Hardware interfacing | Robotics, automation | Mobile, tablets, IoT, AI |
| 10 | **Registers** | 8B banks (R0-R7) | Few | 32 GPRs | 37 registers |
| 11 | **Instruction Set** | 255 instructions | 35-80 instructions | 120-130 instructions | ~45 core instructions |
| 12 | **Popularity** | Educational, legacy | Hobbyist, industry | Arduino platform | Dominant in mobile |

---

## 9. ARM vs Intel Processors

| Feature | ARM | Intel |
|---|---|---|
| **Design Philosophy** | Simple, low power | Complex, high compatibility |
| **Power Consumption** | Very low — long battery life | Higher |
| **History in Mobile** | Used in portables for long time | Entered mobile market later |
| **Pricing** | Low cost power chips | Higher priced |
| **Architecture** | RISC | CISC (x86) |
| **Market** | Dominates mobile/embedded | Dominates desktop/laptop |

---

## 10. AMBA — Advanced Microcontroller Bus Architecture 🔥

> **AMBA** is ARM's bus architecture standard for connecting components in an ARM-based system.

### System Overview:
```
┌──────────────────────────────────────────────────────────────┐
│                    ARM-based AMBA System                     │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────────┐   │
│  │   ARM    │  │ DMA Bus  │  │ High-BW  │  │ External    │   │
│  │ Process- │  │ Master   │  │ On-chip  │  │ Memory      │   │
│  │   or     │  │          │  │  RAM     │  │ Interface   │   │
│  └────┬─────┘  └─────┬────┘  └────┬─────┘  └──────┬──────┘   │
│       │              │            │               │          │
│  ═════╧══════════════╧════════════╧═══════════════╧════════  │
│              AHB (Advanced High-performance Bus)             │
│        High Performance | Pipelined | Burst Support          │
│        Multiple Bus Masters                                  │
│  ════════════════════════╤═══════════════════════════════════│
│                          │                                   │
│                   ┌──────┴──────┐                            │
│                   │ APB Bridge  │                            │
│                   └──────┬──────┘                            │
│                          │                                   │
│  ════════════════════════╧═══════════════════════════════════│
│              APB (Advanced Peripheral Bus)                   │
│        Low Power | Non-pipelined | Simple Interface          │
│  ════════════════════════════════════════════════════════════│
│       │         │         │          │                       │
│  ┌────┴───┐ ┌───┴───┐ ┌──┴───┐ ┌───┴───┐                     │
│  │ Timer  │ │Keypad │ │ UART │ │  PIO  │                     │
│  └────────┘ └───────┘ └──────┘ └───────┘                     │
└──────────────────────────────────────────────────────────────┘
```

### AHB vs APB Comparison 🔥

| Feature | AHB (Advanced High-performance Bus) | APB (Advanced Peripheral Bus) |
|---|---|---|
| **Performance** | High performance | Low power |
| **Access Frequency** | Frequently accessed modules | Infrequently accessed modules |
| **Timing** | Pipelined timing | Non-pipelined |
| **Bus Masters** | Multiple bus masters supported | Simple interface |
| **Bus Width** | Maximum address and data widths | Only as wide as required |
| **Connected Modules** | ARM processor, DMA, RAM, memory interface | Timer, Keypad, UART, PIO |
| **Design Goal** | Maximize system performance | Minimize power consumption |
| **Module Count** | Keep to minimum (reduce loading) | Bulk of design modules placed here |

### Design Partitioning Rule:
> - Put **high-performance, frequently-accessed** modules on **AHB**
> - Put **low-power, infrequently-accessed** modules on **APB**
> - APB modules don't consume power when there's no APB activity

---

## 11. Simulation vs Synthesis

| Concept | Simulation | Synthesis |
|---|---|---|
| **What** | Verify functional correctness | Translate design to logic gates |
| **Input** | HDL model (Verilog/VHDL) | HDL model (Verilog/VHDL) |
| **Output** | Waveforms, functional verification | Gate-level implementation |
| **Tool** | Simulator software | Synthesis tool software |

---

## 💡 Conceptual Corner

### Q: Why ARM uses Load/Store architecture?
**A**: By only allowing LOAD and STORE to access memory, all other operations work on registers which are MUCH faster. This simplifies the instruction set and makes pipelining very efficient — the processor knows exactly when memory will be accessed.

### Q: Why does FIQ have more banked registers than IRQ?
**A**: FIQ (Fast Interrupt) banks registers r8-r14, meaning the ISR doesn't need to save/restore these on the stack. This saves precious cycles, making it "fast." IRQ only banks r13 and r14, so it must save/restore r8-r12 manually.

### Q: What happens if a pipeline hazard occurs?
**A**: The pipeline may need to **stall** (insert "bubbles" — wasted cycles) until the hazard is resolved. For data hazards, techniques like **forwarding** can help. For control hazards, **branch prediction** helps.

### Q: Why is ARM dominant in mobile but Intel dominates desktops?
**A**: ARM's RISC architecture is simple and low-power — perfect for battery-powered devices. Intel's x86 CISC architecture is complex but highly optimized for raw computing performance — ideal for desktops/servers that have constant power supply.

---

## 📝 Likely Exam Questions

### 8-Mark Questions
1. List and explain the 7 processor modes of ARM.
2. Compare AHB and APB in AMBA architecture.
3. Explain the ARM7TDMI architecture with the ATDMI-T acronym.
4. What are pipeline hazards? Explain the three types.
5. Compare ARM and 8051 microcontrollers (8 points).

### 12-Mark Questions
1. (a) Differentiate between RISC and CISC architectures with at least 10 points.
   OR
   (b) Explain the 3-stage pipeline of ARM7 and 5-stage pipeline of ARM9 with diagrams. Compare them.

2. (a) Compare 8051, PIC, AVR, and ARM microcontrollers in detail.
   OR
   (b) Explain the ARM register set, special registers, and AMBA bus architecture.
