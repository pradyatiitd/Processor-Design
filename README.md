# Processor-Design

## Part - 1
### Aim
This assignment aims at designing ARM datapath modules and implementing these on the BASYS3 board. The following subset of ARM instructions has been chosen for implementation.

Arithmetic: <add|sub|rsb|adc|sbc|rsc> {cond} {s}<br/>
Logical: <and | orr | eor | bic> {cond} {s}<br/>
Test: <cmp | cmn | teq | tst> {cond}<br/>
Move: <mov | mvn> {cond} {s}<br/>
Branch: <b | bl> {cond}<br/>
Multiply: <mul | mla> {cond} {s}<br/>
Load/store: <ldr | str> {cond} {b | h | sb | sh }<br/>
cond: <EQ|NE|CS|CC|MI|PL|VS|VC|HI|LS|GE|LT|GT|LE|AL><br/>
<br/>
Instructions excluded are as follows:<br/>
Co-processor: cdp, mcr, mrc, ldc, stc<br/>
Branch and exchange: bx<br/>
Load/Store multiple: ldm, stm<br/>
Software interrupt: swi <br/>
Atomic swap: swp<br/>
PSR transfer: mrs, msr<br/>
Multiply long: mull, mlal<br/>

Instruction swi will be added later. Following are the building blocks :-

### ALU
Inputs : two 32-bit operands, operation to be performed, carry<br/>
Outputs : 32-bit result of arithmetic/logical operation, next flag values<br/>

It is a combinational circuit that performs the arithmetic/logical operations for the DP instructions. The “operation to be performed” input can come from the opcode field of DP instructions. Its ability to add/subtract is also used by other instructions by giving appropriate inputs. This includes address offset addition/subtraction for DT and b|bl instructions as well as addition required in mla instruction. The ALU need not inherently know whether the addition it is doing is for add or ldr or bl or mla. Apart from the 32-bit result, the next values for the 4 flags are also output. Whether the flag values actually need to change is decided elsewhere.

### Shifter
Inputs : 32-bit data to be shifted, shift type (LSL|LSR|ASR|ROR) and shift amount<br/>
Outputs : 32-bit data after shifting, shifter carry<br/>

It is a combinational circuit that performs the required shift operation by the specified number of bits. This serves the purpose for DP and DT instructions requiring shift. It also outputs shifter carry. Which carry (ALU or shifter) is to be used for updating the carry flag is decided elsewhere.

### Multiplier
Inputs : Two 32-bit operands<br/>
Outputs : One 32-bit result<br/>

It is a combinational circuit that performs multiplication operation for mul and mla instructions. The addition required for mla instruction is done by ALU. For this assignment, a detailed design for multiplier is not required. Simply use multiply symbol in VHDL and leave the rest to the synthesizer.

### Register File
Inputs : 32-bit data to be written, two 4-bit read addresses, one 4-bit write address, clock, reset, write enable<br/>
Outputs : two 32-bit data outputs, PC output<br/>

It has an array of sixteen 32-bit registers accessible through two read ports and one write port. It maintains a copy of register 15 out side the array for easy access as program counter. Clock and write enable have their usual meaning. Reset is used to initialize PC.

### Processor-Memory Path
Inputs : 32-bit data from processor, 32-bit data from memory, type of DT instruction, byte offset in memory address<br/>
Outputs : 32-bit data to processor, 32-bit data to memory, memory write enable signals<br/>

It is a combinational circuit that provides byte manipulation and sign/zero extension needed for ldrb, ldrh, ldrsb, ldrsh, strb and strh instructions. For byte addressable memory, byte level write enable signals are required. These are also generated by this module.
