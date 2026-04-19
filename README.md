# 🚀 MPMC Exam Super-Cheat Sheet & Navigation Hub

This hub contains comprehensive, exam-oriented study notes for the Microprocessors and Microcontrollers (MPMC) course. Use the links below to dive into specific modules, or use this README as a last-minute revision guide.

---

## 📂 Navigation Index

| Module | Title | Focus Area |
|---|---|---|
| [**Module 1**](file:///c:/Users/ankit/Downloads/mpmc/Module_1_Computer_Systems_and_Microprocessor_Basics.md) | **Basics & History** | CPU components, µP evolution, Dev tools (Linker/Loader). |
| [**Module 2**](file:///c:/Users/ankit/Downloads/mpmc/Module_2_8086_Architecture_and_Programming.md) | **8086 Architecture** | BIU/EU, Pipelining, Addressing Modes, Instruction Set. |
| [**Module 3**](file:///c:/Users/ankit/Downloads/mpmc/Module_3_8051_Microcontroller.md) | **8051 Microcontroller** | Memory Map, SFRs, Timers, Interrupts, Serial Comm. |
| [**Module 4**](file:///c:/Users/ankit/Downloads/mpmc/Module_4_Peripheral_Interfacing.md) | **Interfacing** | ADC 0808, DAC, LCD, Keyboard, 7-Segment (Programs). |
| [**Module 5**](file:///c:/Users/ankit/Downloads/mpmc/Module_5_ARM_Processors.md) | **ARM Processors** | RISC vs CISC, ARM7/9 Pipelining, Modes, AMBA Bus. |

---

## 🧠 The "Remember Everything" Cheat Sheet

### 1. Crucial Architectures at a Glance
| Feature | 8086 (µP) | 8051 (µC) | ARM (µC) |
|---|---|---|---|
| **Width** | 16-bit | 8-bit | 32-bit |
| **Philosophy** | CISC | CISC (Harvard) | RISC (Load/Store) |
| **Address Bus** | 20-bit (1 MB) | 16-bit (64 KB) | 32-bit (4 GB) |
| **Registers** | 14 (mostly 16-bit) | 34 (mostly 8-bit) | 37 (all 32-bit) |
| **Pipelining** | 2-stage (BIU/EU) | Simple | 3 or 5 stage |

---

### 2. Physical Address Calculations 🔥
- **8086**: `Segment Register × 10H + Offset Register`
  - *Example*: CS=1000H, IP=2000H → `10000 + 2000 = 12000H`
- **8051 (Timer)**: `65536 - (Delay / 1.085 µs)` for 11.0592MHz XTAL.

---

### 3. Comparison Goldmines (12-Mark Favorites) 🔥
| **Microprocessor vs Microcontroller** | **RISC vs CISC** | **AHB vs APB (AMBA)** |
|---|---|---|
| µP: CPU only. Needs external RAM/ROM. | RISC: Simple instructions, 1 cycle. | AHB: High Perf, Frequent Access. |
| µC: All-in-one chip. Cheap, compact. | CISC: Complex instructions, many cycles. | APB: Low Power, Slow Peripherals. |

---

### 4. Special Pins You CANNOT Forget
- **8086 ALE**: High = Address is on bus; Low = Data is on bus.
- **8051 EA̅**: Low = Use external ROM (Program Memory).
- **ADC 0808 EOC**: High = Conversion Finished.
- **LCD RS**: 0 = Command; 1 = Data.

---

### 5. Interrupt Vector Cheat Table
| Source | 8086 Type | 8051 Address | 8051 Flag |
|---|---|---|---|
| **Reset** | N/A | 0000H | — |
| **External 0** | Type 2 (NMI) | 0003H | IE0 |
| **Timer 0** | N/A | 000BH | TF0 |
| **External 1** | N/A | 0013H | IE1 |
| **Timer 1** | N/A | 001BH | TF1 |
| **Serial** | N/A | 0023H | RI/TI |

---

### 6. ARM 7 Processor Modes (FUIS-UAb)
1. **User**: Standard apps.
2. **FIQ**: Fast Interrupt.
3. **IRQ**: Normal Interrupt.
4. **SVC**: Supervisor (Reset/SWI).
5. **Abort**: Memory violation.
6. **Undef**: Bad instruction.
7. **System**: Privileged User mode.

---

### 7. Pipelining Stages
- **ARM7 (3-stage)**: FETCH → DECODE → EXECUTE (Execute at `PC-8`)
- **ARM9 (5-stage)**: FETCH → DECODE → EXEC → MEM → WRITE

---

## 💡 Quick Mnemonics
- **8086 Flags**: **C**arry **P**arked **A** **Z**ebra, **S**itting **T**owards **I**ce **D**uring **O**ctober (CF, PF, AF, ZF, SF, TF, IF, DF, OF).
- **8051 Ports**: Port 0 (Address/Data, Pull-ups), Port 2 (Address Upper), Port 3 (Special Fn).
- **ARM-TDMI**: **T**humb, **D**ebug, **M**ultiplier, **I**CE.

---

## 📝 Final Exam Tips
1. **Draw Diagrams**: Even if not asked, draw the BIU/EU or 8051 Block diagram for 12-mark questions.
2. **Comment Code**: In Module 4 and 3 programs, every line should have a `; comment`.
3. **Table Width**: For "Difference" questions, aim for 10 points for 12 marks.
4. **Time Management**: 30 mins per 12-mark question, 15 mins per 8-mark question.

Good luck for your exam! 🎯
