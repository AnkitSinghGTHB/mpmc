# Module 1: Computer Systems & Microprocessor Basics

> **Exam Focus**: Total theory module. Expect 1 × 12-mark (a/b choice) + 1 × 8-mark question.
> No programming from this module. Focus on definitions, comparisons, and history.

---

## 📋 Quick Exam Cheatsheet

| What to Study | Marks Likely | Type |
|---|---|---|
| Computer system components (CPU, Memory, I/O, Bus) | 8 marks | Describe/Explain |
| Microprocessor history (Intel 4004 → Core i9) | 8-12 marks | Table / Timeline |
| Assembly vs High-level language | 8 marks | Comparison |
| Development tools (Assembler, Linker, Loader, Debugger, Emulator) | 12 marks | Explain each |
| Linking and Relocation | 8 marks | Conceptual |

---

## 1. Computer System Components

A computer system has **4 main components** that work together:

```
┌─────────────────────────────────────────────────────────────────────┐
│                        COMPUTER SYSTEM                              │
│                                                                     │
│   ┌──────────┐     ┌──────────────────────────┐     ┌──────────┐    │
│   │          │     │         CPU              │     │          │    │
│   │  MEMORY  │◄───►│  ┌─────┐  ┌────────────┐ │◄───►│   I/O    │    │
│   │ (RAM/ROM)│     │  │ ALU │  │ Control    │ │     │(Keyboard,│    │
│   │          │     │  └─────┘  │ Unit       │ │     │ Display) │    │
│   └──────────┘     │  ┌─────┐  └────────────┘ │     └──────────┘    │
│        ▲           │  │Regs │                 │           ▲         │
│        │           │  └─────┘                 │           │         │
│        │           └──────────────────────────┘           │         │
│        │                      ▲                           │         │
│        │                      │                           │         │
│        ▼──────────────────────▼───────────────────────────▼         │
│   ═══════════════════ SYSTEM BUS ══════════════════════════         │
│   [Address Bus]      [Data Bus]       [Control Bus]                 │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.1 Memory
- **Stores** both instructions and data
- Contents are addressable by memory address, regardless of data type
- Instructions execute **sequentially** unless explicitly altered (like by a jump)

### 1.2 Input/Output (I/O)
- Also called **peripherals**
- Used to input and output instructions and data
- Examples: keyboard, monitor, printer, mouse

### 1.3 Arithmetic and Logic Unit (ALU)
- Performs **arithmetic** operations: Addition, Subtraction, Increment (+1), Decrement (-1)
- Performs **logical** operations: AND, OR, XOR, NOT (complement), SHIFT, ROTATE
- **Accumulator** = special 8-bit register tied to ALU
  - One input to ALU always comes from Accumulator
  - Result of operation is placed back in Accumulator
  - In 8085, Register **'A'** is the accumulator

### 1.4 Control Unit 🔥
- **Brain of the computer system**
- Generates control signals for instruction execution
- Connects registers to the bus
- Controls data flow between CPU and peripherals (including memory)
- Provides **status, control & timing signals** for memory and I/O devices
- All actions related to **fetching, decoding, and executing** instructions

### 1.5 System Bus
A **bus** = a group of lines used to transfer bits between components.

> **Key Rule**: Only ONE transmitter at a time; only the ADDRESSED device responds.

| Bus Type | Direction | Purpose |
|---|---|---|
| **Address Bus** | CPU → Memory/IO | Carries the address of memory location or I/O device |
| **Data Bus** | Bidirectional | Carries actual data between components |
| **Control Bus** | Bidirectional | Carries control signals (Read, Write, Clock, Interrupts) |

### 1.6 CPU Registers
- Hold data, instructions, or other items
- Come in various sizes
- **Program Counter** and **Memory Address Register** must be same width as Address Bus
- **Data registers** must be same width as memory words

---

## 2. CPU Performance Metrics

- Short **response time** for a given piece of work
- High **throughput** (rate of processing work)
- Low **utilization** of computing resources
- High **availability** of the computing system
- Fast data **compression/decompression**
- High **bandwidth**
- Short data **transmission time**

---

## 3. What is a Microprocessor? 🔥

A microprocessor is a **semiconductor device** consisting of electronic logic circuits, manufactured using various fabrication schemes, capable of performing computing functions and transporting data.

### Three Segments of a Microprocessor:
```
┌──────────────────────────────────────┐
│           MICROPROCESSOR             │
│                                      │
│   ┌──────────┐  ┌──────────────┐     │
│   │   ALU    │  │ Register     │     │
│   │(Compute) │  │ Unit (Store) │     │
│   └──────────┘  └──────────────┘     │
│   ┌─────────────────────────────┐    │
│   │    Control Unit (Manage)    │    │
│   └─────────────────────────────┘    │
└──────────────────────────────────────┘
```

---

## 4. Historical Background of Intel Microprocessors 🔥🔥

> **This is VERY likely to appear as a 12-mark question** — "Explain the evolution of Intel microprocessors" or as a table.

| Processor | Year | Bits | Clock Speed | Transistors | Address Bus | Memory | Key Features |
|---|---|---|---|---|---|---|---|
| **4004** | 1971 | 4-bit | 740 KHz | 2,300 | — | 4 KB | 1st µP by Intel, PMOS, 45 instructions, used in calculators |
| **8008** | 1972 | 8-bit | 500 KHz | — | — | 16 KB | 1st 8-bit µP, 48 instructions, PMOS, slow |
| **8080** | 1974 | 8-bit | 2 MHz | 6,000 | — | 64 KB | NMOS, 10× faster than 8008, needed 3 power supplies |
| **8085** | 1976 | 8-bit | 3 MHz | 6,500 | 16-bit | 64 KB | NMOS, single +5V supply, 246 instructions, 100M+ copies sold |
| **8086** | 1978 | 16-bit | 4.77–10 MHz | 29,000 | 20-bit | 1 MB | 3rd gen, HMOS, MUL/DIV instructions, 22K instructions |
| **8088** | 1979 | 16-bit | — | — | 20-bit | 1 MB | Cheaper 8086, 8-bit external bus. Used in IBM PC |
| **80186/80188** | 1982 | 16-bit | 6 MHz | — | — | — | Built-in interrupt controller, clock gen, counters |
| **80286** | 1982 | 16-bit | 8 MHz | 134,000 | 24-bit | 16 MB | Protected mode |
| **80386** | 1986 | 32-bit | 16–33 MHz | 275,000 | 32-bit | 4 GB | 1st 32-bit µP, best-selling µP in history, DX/SX/SL versions |
| **80486** | 1989 | 32-bit | 16–100 MHz | 1.2M | 32-bit | 4 GB | 8 KB cache, internal clock 2× external bus speed |
| **Pentium** | 1993 | 32-bit | 66 MHz | — | 32-bit | 4 GB | Originally 80586, 2 instructions simultaneously, 8KB+8KB cache |
| **Pentium Pro** | 1995 | 32-bit | — | 21M | — | — | For servers, L2 cache 256 KB |
| **Pentium II** | 1997 | 32-bit | 233–500 MHz | — | — | — | MMX technology, L2+processor on one circuit |
| **Pentium II Xeon** | 1998 | 32-bit | 400–450 MHz | 7.5M | — | — | For servers, L2: 512KB/1MB/2MB, 4 Xeons in one system |
| **Pentium III** | 1999 | 32-bit | 500 MHz–1.4 GHz | 9.5M | — | — | — |
| **Pentium IV** | 2000 | 32-bit | 1.3–3.8 GHz | 42M | — | — | L1=32KB, L2=256KB, aluminium→copper connections |
| **Dual Core** | 2006 | 32/64-bit | — | — | — | — | 2 cores, own L1 cache, shared L2 cache, SMT |
| **Core 2** | 2006 | 64-bit | 1.2–3 GHz | 291M | — | — | 64KB L1/core, 4MB L2, Duo/Quad/Extreme versions |
| **Core i3** | 2010 | 64-bit | 2.93–3.33 GHz | 781M | — | — | 2 physical cores, 64KB L1, 512KB L2, 4MB L3 |
| **Core i5** | 2009 | 64-bit | 2.40–3.60 GHz | 781M | — | — | 4 cores, 8MB L3, good for multitasking/gaming |
| **Core i7** | 2008 | 64-bit | 2.66–3.33 GHz | 731M | — | — | 4 cores, 8MB L3 |
| **Core i9** | 2017 | 64-bit | 3.33–5.2 GHz | 4.3B | — | — | 10 cores, 1MB L2/core, 13.75MB L3, for 3D/AI |

### Key Patterns to Remember:
- **Technology progression**: PMOS → NMOS → HMOS → HCMOS
- **Bit width**: 4-bit → 8-bit → 16-bit → 32-bit → 64-bit
- **Cache evolution**: No cache → 8KB → split I/D cache → L1+L2 → L1+L2+L3
- **Power supply**: Multiple → single +5V (from 8085 onwards)

---

## 5. Assembly Language, Machine Language & High-Level Language 🔥

### What are they?

| Level | Example | Tool to Translate | Runs on Any CPU? |
|---|---|---|---|
| **Machine Language** | `7E` or `0111 1110` | None (CPU directly understands) | No |
| **Assembly Language** | `MOV A, M` | **Assembler** | No (processor-specific) |
| **High-Level Language** | `x = a + b;` (C, Python) | **Compiler** | Yes (portable) |

> Both Assembly and Machine language are called **low-level languages**.

### Assembly vs High-Level Language — Full Comparison 🔥

| Feature | Assembly Language | High-Level Language |
|---|---|---|
| **Processor dependency** | Written for ONE specific processor; won't run on another | Runs independently of processor type |
| **Performance** | Better performance and accuracy | Slightly lower due to extra overhead |
| **Extra instructions** | Minimal overhead | Needs extra instructions to run |
| **Readability** | Difficult to understand and debug | Easy to read and debug |
| **Code expansion** | 1 line does 1 thing | 1-2 HLL lines = many assembly lines |
| **Hardware communication** | Can directly access hardware (registers, ports) | Cannot directly access hardware |
| **Pointer/Address access** | Can read pointers at physical address: `MOV AX, [1234H]` | Not possible directly |
| **Bit manipulation** | Easy to work with individual bits | More complex |
| **Translation tool** | **Assembler** | **Compiler** |
| **Executable size** | Small and tight | Larger (takes longer to execute) |
| **Efficiency** | More efficient | Less efficient |
| **Hardware knowledge needed** | Must know registers, addressing, etc. | No hardware details needed |
| **Conversion** | — | Most HLL code is first auto-converted to assembly |

---

## 6. Microprocessor Development Tools 🔥🔥

> 🔥 **Likely 12-mark question**: "Explain the software and hardware tools used in microprocessor development."

### Software Tools

#### 6.1 Assembler
- Translates **assembly language** (mnemonics) into **machine code** (object code)
- Input: `.asm` file → Output: `.obj` file

#### 6.2 Compiler
- Translates **high-level language** (C, Pascal) into **machine code**
- Does the same job as assembler but for HLL

#### 6.3 Linker 🔥
- A special program that **combines object files** (from compiler/assembler) and other code pieces to create an **executable file** (`.exe`)
- Searches and appends all **libraries** needed for execution
- Regulates **memory space** for code from each module
- Merges two or more separate object programs and establishes **links** among them

```
  ┌──────────┐   ┌──────────┐   ┌──────────┐
  │ Object   │   │ Object   │   │ Library  │
  │ File 1   │   │ File 2   │   │ Files    │
  └────┬─────┘   └────┬─────┘   └────┬─────┘
       │              │              │
       ▼──────────────▼──────────────▼
       │         LINKER              │
       └──────────┬──────────────────┘
                  ▼
         ┌────────────────┐
         │ Executable     │
         │ File (.exe)    │
         └────────────────┘
```

#### 6.4 Loader
- Takes **object code from linker**, loads it into **main memory**, and prepares it for execution
- **Allocates memory space** to the program
- Settles symbolic references between objects
- In charge of loading programs and libraries in the operating system

#### 6.5 Locator
- Assigns **specific addresses** of where segments of object code are to be loaded into memory

#### 6.6 Debugger 🔥
A debugger lets you:
- **Load** your object code program into system memory
- **Execute** the program and troubleshoot/debug it
- **View** contents of registers and memory locations after the program runs
- **Change** contents of registers and memory locations and re-run the program
- **Single-step** execution: Stop after each instruction to check/alter register contents
- **Set breakpoints**: Stop execution at any point in your program; the program runs UP TO that instruction and then stops

> 💡 **Conceptual**: If your program produces a wrong result, how would you debug it?
> 1. Load the program into the debugger
> 2. Set a breakpoint before the suspected faulty instruction
> 3. Run the program — it stops at the breakpoint
> 4. Check register/memory values — are they what you expected?
> 5. Single-step through the faulty section, checking values after each instruction
> 6. Identify where the value goes wrong → that's your bug

#### 6.7 Simulator
- A **software** program that imitates the behavior of the target processor
- Debuggers may use **instruction-set simulators** instead of running directly on the processor
- Provides higher level of control over execution
- **Drawback**: Decreases execution speed

### Hardware Tools

#### 6.8 Emulator 🔥
- A **mixture of hardware and software**
- Used to test and debug the hardware and software of an external system (e.g., prototype of a µP-based instrument)
- A multi-wire cable connects the host system to the system being developed
- Examples: FPGAs, USRPs

#### 6.9 In-Circuit Emulator (ICE) 🔥
- Provides a **window into the embedded system**
- Programmer uses ICE to:
  - Load programs into the embedded system
  - Run them
  - Step through them slowly
  - View and change data used by the system's software
- Works by **replacing the processor temporarily** with a more powerful hardware emulator
- Uses a **bond-out processor** which has internal signals brought out for debugging

#### 6.10 Logic Analyzer
- Hardware tool for monitoring and capturing digital signals
- Used to observe timing relationships and data on bus lines

---

## 7. Linking and Relocation 🔥

### What is Linking?
- Programs are composed of **multiple modules/parts**
- These parts may be in **different object files**
- They refer to each other using **symbols** (addresses into other modules)
- The **linker** combines these files into a **unified executable**, resolving symbols
- **Library linking** is iterative: some modules require additional modules

### What is Relocation?
- The process of **assigning load addresses** for position-dependent code
- **Adjusting** the code and data to reflect the assigned addresses
- In early systems, addresses were **absolute** starting at location 0
- In modern multi-process systems, **position-independent code** is needed
- Relocation can happen at:
  - **Link time** (by the linker) — most common
  - **Load time** (by a relocating loader)
  - **Run time** (by the program itself)

> 💡 **Think of it like this**: Relocation is like assigning seat numbers to passengers AFTER they've booked tickets. The linker handles the seating chart; the loader actually places people in their seats.

### Code Compilation Flow
```
┌─────────────┐
│ Source Code │  (C, Pascal)
└──────┬──────┘
       ▼
┌──────────────────┐
│ Preprocessing &  │
│ Compiling        │
└──────┬───────────┘
       ▼
┌─────────────┐
│ Assembly    │  (Assembly Language)
│ Code        │
└──────┬──────┘
       ▼
┌──────────────┐
│ Assembly     │  (Assembler translates)
└──────┬───────┘
       ▼
┌─────────────┐     ┌──────────┐
│ Object Code │ ◄── │  DLLs    │
└──────┬──────┘     └──────────┘
       ▼
┌──────────────┐
│ Linking      │
└──────┬───────┘
       ▼
┌──────────────────┐
│ Executable Code  │  (.exe)
└──────────────────┘
```

### What is Disassembly?
- The **reverse** of the above process
- Going from **executable/object code** back to **assembly code**
- Useful for reverse engineering or debugging when source code is unavailable

---

## 💡 Conceptual Corner

### Q: What happens if you skip the linker step?
**A**: Each object file contains **unresolved references** (external function calls, library calls). Without linking, the program won't know WHERE those functions are in memory. You'll get "undefined reference" errors.

### Q: Why does a debugger with a simulator run slower?
**A**: A simulator **emulates** each instruction in software rather than executing it on real hardware. Each real instruction may take hundreds of software instructions to simulate, hence the slowdown.

### Q: What's the difference between an Emulator and a Simulator?
| Feature | Simulator | Emulator |
|---|---|---|
| **Nature** | Pure software | Hardware + Software |
| **Speed** | Slower | Closer to real speed |
| **Hardware testing** | Cannot test real hardware | Can test actual hardware |
| **Connection** | No physical connection | Connected via cable to target |

### Q: Why do we need a Locator separate from a Linker?
**A**: The linker resolves references and combines files, but it may use **relative addresses**. The locator assigns **actual physical memory addresses** — this is critical in embedded systems where code must go to specific ROM/RAM locations.

---

## 📝 Likely Exam Questions

### 8-Mark Questions
1. Explain the components of a computer system with a block diagram.
2. Differentiate between assembly language and high-level language (10 points).
3. Explain the roles of Assembler, Linker, and Loader in program development.
4. What is a debugger? Explain its features (breakpoints, single-stepping, etc.).

### 12-Mark Questions
1. (a) Explain the evolution of Intel microprocessors from 4004 to Core i9 with a table showing key features.
   OR
   (b) Explain all the software and hardware development tools used in microprocessor development with diagrams.
2. (a) What is linking and relocation? Explain with the code compilation flow diagram.
   OR
   (b) Compare Assembly language and High-Level language. Also explain the code flow from source code to executable.
