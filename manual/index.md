# Firth of Forth manual

Firth is a minimal (~4K) implementation of Forth for the Z80.

## Contents

- [Motivation](#Motivation)
- [Firth](#Firth)
  - [License](#License)
  - [Binaries](#Binaries)
  - [Building](#Building)
  - [Emulating](#emulating)
  - [Hardware requirements](#Hardware-requirements)
  - [File layout](#File-layout)
- [The Forth architecture](#Forth-architecture)
  - [Why Forth?](#Why-forth)
  - Data stack
  - Execution model
  - Forth in Assembly
- [Z80 architecture](#Z80-architecture)
  - Registers
  - Stack pointer
  - Index registers
  - Alternative registers
  - Memory addressing
  - Flags
  - I/O ports
- [Z80 assembly language](#Z80-assembly-language)
  - Asm80
  - Directives
  - Variables
  - Macros
  - Structured assembler
- [Dictionary](#Dictionary)
  - Word headers
  - Primitive words
  - Composite words
  - Immediate words
- [Compilation](#Compilation)
  - Control structures
  - Looping structures
- [Firth environment](#Firth-environment)
  - Debugging
- [Appendices](#Appendices)
  - [The Z80 instruction set](#The-Z80-instruction-set)
  - Hardware
  - Utility functions
  - Further reading

## Motivation

Back in 1983, I designed a kit with Ken Stone which we called the TEC-1. It was a single-board computer kit which we published in Talking Electronics, a Melbourne-based electronics hobbyist magazine.

The configuration of the TEC-1 was a Z80 CPU with 2K of ROM and 2K of RAM. It also included a hexidecimal keyboard and a 6 digit 7-segment display.

The kit was very successful and was released at a critical time in computer history. A few thousand TEC-1 kits were sold as we learned over the years from people who remember the kit, it introduced a great many people to computing for the first time.

The kit continues on to this day with other people creating TEC-1 boards and new TEC-1s continue to be built by hobbyists to this day. If they install the version 1 montitor ROM then they are greeted with a 37-year-old message from me:

`hello there this is the tec-1...designed by john hardy for TE!`

Looking back, I'm really happy that the TEC-1 lives on however I do have one regret. That the TEC-1 was so damned hard to program! In its original form, the TEC-1 offered very little to a beginner programmer. All code need to be hand assembled and fed in via the hexadecimal keyboard. I always wanted to provide the TEC-1 one with a proper programming environment.

After considering and rejecting building a BASIC interpreter for the TEC-1 I decided to persue another longtime interest (which also dated from that era). I decided that what the TEC-1 needed was a self-hosted programming based on Chuck Moore's Forth language.

This is where Firth comes from. It is named after the [Firth of Forth](https://en.wikipedia.org/wiki/Firth_of_Forth) an estuary on the river Forth in Scotland -- for no other reason than it's a cool-sounding name!

## License

Firth is released until the GNU General Public License Version 3. See the `LICENSE` file in the root folder.

## Binaries

TODO: Create a folder containing ROM images for various configurations.

## Building

Firth can be built using the ASM80 assembler and can be built at the [ASM80](https://www.asm80.com/) site by the following steps:

1. Press the `Import repo from GitHub` button
2. Paste the following repo name `https://github.com/jhlagado/firth` and click OK
3. Select the `main.z80` file
4. Download a .bin format binary by clicking on the "Download BIN" button. There are also buttons for downloading binaries in the .sna and .tap formats.

## Emulating

Firth can be emulated online by the following steps:

1. Press the `Import repo from GitHub` button
2. Paste the following repo name `https://github.com/jhlagado/firth` and click OK
3. Select the `main.z80` file
4. Press the `Emulator [F10]` button

This will cause ASM80 to start emulating the computer hardware specified in the `mycomputer.emu` file which is set up for a Z80 CPU which uses a Motorola 6850 ACIA serial chip mapped to ports $80 and $81.

The emulator will start up a green screen serial terminal emulation and present you with a prompt

```
Firth of Forth Z80

>
```

You can type Forth commands into it. e.g

```
> 1 2 3 * + .

7
```

You can exit the Forth interpreter by pressing the `Back to IDE` button on the top right corner.

## Hardware requirements

Firth is design to be run in ROM of a Z80 computer board like the TEC-1. It could also be easily adapted to run on a similar system such as the RC2014 homebrew Z80 single-board computer.

To run on a TEC-1 it requires additional hardware. **TODO: details of this additional hardware.** it requires a Motorola 6850 ACIA serial chip mapped to ports `$80` and `$81` (or `0x80` and `0x81`) as per the hardware arrangement designed by Grant Searle for his [7-chip Z80 computer](http://zx80.netai.net/grant/z80/SimpleZ80.html). See the circuit diagram below and note how the 6850 ACIA chip is wired up.
![Grant Searle's serial interface](Z80SbcSchematic1.2.gif)

## File layout

Here is a listing of Firth's source files with a brief description.

```
compiler-macros.z80     macros used to enable Forth control and loop structures in ROM
constants.z80           most of the constants need by Firth
dloop-macros.z80        macros used to enable Assembly language looping structures
macros.z80              macros used to implement repetitive Assembly language elements
main.z80                the main file of Firth (start here)
mycomputer.emu          engine configuration file for the asm80 online emulator
primitives.z80          Forth words which are written in assembly language
struct-macros.z80       macros used to enable Assembly language control structures
test-macros.z80         macros used to enable unit tests
tests.z80               unit tests to test various aspects of the Forth engine
utilities.z80           Utility subroutines used by
variables.z80           Memory locations defined in RAM
words.z80               Forth words which written in Forth
```

You can start by examining `main.z80` which is the root file which includes all the other files.

## Why Forth?

Forth as a programming system has many characteristics which set it apart from other programming languages. It would be wrong to simple describe it as a compiled language like C or an interpreted language BASIC. It some sits in the middle between compiler and interpreter. It is its own unique thing with its own execution model.

Forth is often described as a "bottom-up" language as opposed to a "top-down" language more easily associated with "high-level languages. Forth is a series of abstractions that are built up piece by piece from assembly language primitives. Forth can be bootstrapped to run from less than 2K of assembly language. Most of the Forth system is written in Forth itself. Forth is self-hosting.

So why Forth? Because Forth can be written from a small amount of assembly language, it becomes a relative simple task to get Forth running on diverse range of CPUs. Forth irons out the quirks and differences between instruction sets and presents the programmer with a much smaller programming surface than assembly langauge does. Forth unifies low level programming tasks.

Forth is lightweight. Even a language designed for systems programming such as C is much more high-level than Forth and adds considerably more overhead in terms of program size, memory size and computational overhead. Forth programs are extremely compact and are often smaller than the same code written in assembly language.

There is a cost to this compactness however. Forth is slower than assembly but not greatly so. Forth is pretty fast and comparable with compiled high-level languages.

Forth integrates well with assembly language and always offer the developer the ability to drop back down to assembly for performance sensitive sections. Forth does not take the developer far "from the metal" and however it does offer them control and looping structures and a unified approach to parameter passing which are features more normally associated with high-level languages. Forth brings structured programming to low-level programming.

## The Z80 instruction set
```
opcode    t-states    explanation

ADC   A,r       4 Add with carry register r to accumulator.
ADC   A,n       7 Add with carry value n to accumulator.
ADC   A,(HL)    7 Add with carry location (HL) to acccumulator.
ADC   A,(IX+d) 19 Add with carry location (IX+d) to accumulator.
ADC   A,(IY+d) 19 Add with carry location (IY+d) to accumulator.

ADC   HL,BC    15 Add with carry register pair BC to HL.
ADC   HL,DE    15 Add with carry register pair DE to HL.
ADC   HL,HL    15 Add with carry register pair HL to HL.
ADC   HL,SP    15 Add with carry register pair SP to HL.

ADD   A,r       4 Add register r to accumulator.
ADD   A,n       7 Add value n to accumulator.
ADD   A,(HL)    7 Add location (HL) to acccumulator.
ADD   A,(IX+d) 19 Add location (IX+d) to accumulator.
ADD   A,(IY+d) 19 Add location (IY+d) to accumulator.

ADD   HL,BC    11 Add register pair BC to HL.
ADD   HL,DE    11 Add register pair DE to HL.
ADD   HL,HL    11 Add register pair HL to HL.
ADD   HL,SP    11 Add register pair SP to HL.

ADD   IX,BC    15 Add register pair BC to IX.
ADD   IX,DE    15 Add register pair DE to IX.
ADD   IX,IX    15 Add register pair IX to IX.
ADD   IX,SP    15 Add register pair SP to IX.

ADD   IY,BC    15 Add register pair BC to IY.
ADD   IY,DE    15 Add register pair DE to IY.
ADD   IY,IY    15 Add register pair IY to IY.
ADD   IY,SP    15 Add register pair SP to IY.

AND   r         4 Logical AND of register r to accumulator.
AND   n         7 Logical AND of value n to accumulator.
AND   (HL)      7 Logical AND of value at location (HL) to accumulator.
AND   (IX+d)   19 Logical AND of value at location (IX+d) to accumulator.
AND   (IY+d)   19 Logical AND of value at location (IY+d) to accumulator.

BIT   b,(HL)   12 Test bit b of location (HL).
BIT   b,(IX+d) 20 Test bit b of location (IX+d).
BIT   b,(IY+d) 20 Test bit b of location (IY+d).
BIT   b,r       8 Test bit b of register r.
CALL  nn       17 Call subroutine at location.
CALL  cc,nn 17,10 Call subroutine at location nn if condition CC is true.
CCF             4 Complement carry flag.

CP    r         4 Compare register r with accumulator.
CP    n         7 Compare value n with accumulator.
CP    (HL)      7 Compare value at location (HL) with accumulator.
CP    (IX+d)   19 Compare value at location (IX+d) with accumulator.
CP    (IY+d)   19 Compare value at location (IY+d) with accumulator.

CPD            16 Comapre location (HL) and acc., decrement HL and BC,
CPDR        21,16 Perform a CPD and repeat until BC=0.
CPI            16 Compare location (HL) and acc., incr HL, decr BC.
CPIR        21,16 Perform a CPI and repeat until BC=0.
CPL             4 Complement accumulator (1's complement).
DAA             4 Decimal adjust accumulator.

DEC   r         4 Decrement register r.
DEC   (HL)     11 Decrement value at location (HL).
DEC   (IX+d)   23 Decrement value at location (IX+d).
DEC   (IY+d)   23 Decrement value at location (IY+d).

DEC   IX       10 Decrement IX.
DEC   IY       10 Decrement IY.
DEC   BC        6 Decrement register pair BC.
DEC   DE        6 Decrement register pair DE.
DEC   HL        6 Decrement register pair HL.
DEC   SP        6 Decrement register pair SP.

DI              4 Disable interrupts. (except NMI at 0066h)

DJNZ  n      13,8 Decrement B and jump relative if B<>0.

EI              4 Enable interrupts.
EX    (SP),HL  19 Exchange the location (SP) and HL.
EX    (SP),IX  23 Exchange the location (SP) and IX.
EX    (SP),IY  23 Exchange the location (SP) and IY.
EX    AF,AF'    4 Exchange the contents of AF and AF'.
EX    DE,HL     4 Exchange the contents of DE and HL.
EXX             4 Exchange the contents of BC,DE,HL with BC',DE',HL'.
HALT            4 Halt computer and wait for interrupt.
IM    0         8 Set interrupt mode 0. (instruction on data bus by int device)
IM    1         8 Set interrupt mode 1. (rst 38)
IM    2         8 Set interrupt mode 2. (vector jump)

IN    A,(n)    11 Load the accumulator with input from device n.
IN    r,(c)    12 Load the register r with input from device (C).

INC   r         4 Increment register r.
INC   (HL)     11 Increment location (HL).
INC   (IX+d)   23 Increment location (IX+d).
INC   (IY+d)   23 Increment location (IY+d).

INC   IX       10 Increment IX.
INC   IY       10 Increment IY.
INC   BC        6 Increment register pair BC.
INC   DE        6 Increment register pair DE.
INC   HL        6 Increment register pair HL.
INC   SP        6 Increment register pair SP.

IND            16 (HL)=Input from port (C). Decrement HL and B.
INDR        21,16 Perform an IND and repeat until B=0.
INI            16 (HL)=Input from port (C). HL=HL+1. B=B-1.
INIR        21,16 Perform an INI and repeat until B=0.

JP    nn       10 Unconditional jump to location nn
JP    cc,nn    10 Jump to location nn if condition cc is true.

JP    (HL)      4 Unconditional jump to location (HL).
JP    (IX)      8 Unconditional jump to location (IX).
JP    (IY)      8 Unconditional jump to location (IY).

JR    C,n    12,7 Jump relative to PC+n if carry=1.
JR    n        12 Unconditional jump relative to PC+n.
JR    NC,n   12,7 Jump relative to PC+n if carry=0.
JR    NZ,n   12,7 Jump relative to PC+n if non zero (Z=0).
JR    Z,n    12,7 Jump relative to PC+n if zero (Z=1).

LD    A,(BC)    7 Load accumulator with value at location (BC).
LD    A,(DE)    7 Load accumulator with value at location (DE).
LD    A,I       9 Load accumulator with I.(interrupt vector register)
LD    A,(nn)   13 Load accumulator with value at location nn.
LD    A,R       9 Load accumulator with R.(memory refresh register)
LD    (BC),A    7 Load location (BC) with accumulator.
LD    (DE),A    7 Load location (DE) with accumulator.
LD    (HL),n   10 Load location (HL) with value n.
LD    (IX+d),n 19 Load location (IX+d) with value n.
LD    (IY+d),n 19 Load location (IY+d) with value n.
LD    (nn),A   13 Load location (nn) with accumulator.

LD    (nn),BC  20 Load location (nn) with register pair BC.
LD    (nn),DE  20 Load location (nn) with register pair DE.
LD    (nn),HL  16 Load location (nn) with HL.
LD    (nn),SP  20 Load location (nn) with register pair SP.

LD    (nn),IX  20 Load location (nn) with IX.
LD    (nn),IY  20 Load location (nn) with IY.

LD    BC,nn    10 Load register pair BC with nn.
LD    DE,nn    10 Load register pair DE with nn.
LD    HL,nn    10 Load register pair HL with nn.
LD    SP,nn    10 Load register pair SP with nn.

LD    BC,(nn)  20 Load register pair BC with value at location (nn).
LD    DE,(nn)  20 Load register pair DE with value at location (nn).
LD    HL,(nn)  16 Load HL with value at location (nn). (L-first)
LD    SP,(nn)  20 Load register pair SP with value at location (nn).

LD    (HL),r    7 Load location (HL) with register r.
LD    (IX+d),r 19 Load location (IX+d) with register r.
LD    (IY+d),r 19 Load location (IY+d) with register r.
LD    I,A       9 Load I with accumulator.
LD    IX,nn    14 Load IX with value nn.
LD    IX,(nn)  20 Load IX with value at location (nn).
LD    IY,nn    14 Load IY with value nn.
LD    IY,(nn)  20 Load IY with value at location (nn).
LD    R,A       9 Load R with accumulator.
LD    r,(HL)    7 Load register r with value at location (HL).
LD    r,(IX+d) 19 Load register r with value at location (IX+d).
LD    r,(IY+d) 19 Load register r with value at location (IY+d).
LD    r,n       7 Load register r with value n.
LD    r,r'      4 Load register r with register r'.
LD    SP,HL     6 Load SP with HL.
LD    SP,IX    10 Load SP with IX.
LD    SP,IY    10 Load SP with IY.
LDD            16 Load location (DE) with location (HL), decrement DE,HL,BC.
LDDR        21,16 Perform an LDD and repeat until BC=0.
LDI            16 Load location (DE) with location (HL), incr DE,HL; decr BC.
LDIR        21,17 Perform an LDI and repeat until BC=0.
NEG             8 Negate accumulator (2's complement).
NOP             4 No operation.

OR    r         4 Logical OR of register r and accumulator.
OR    n         7 Logical OR of value n and accumulator.
OR    (HL)      7 Logical OR of value at location (HL) and accumulator.
OR    (IX+d)   19 Logical OR of value at location (IX+d) and accumulator.
OR    (IY+d)   19 Logical OR of value at location (IY+d) and accumulator.

OTDR        21,16 Perform an OUTD and repeat until B=0.
OTIR        21,16 Perform an OTI and repeat until B=0.
OUT   (C),r    12 Load output port (C) with register r.
OUT   (n),A    11 Load output port (n) with accumulator.
OUTD           16 Load output port (C) with (HL), decrement HL and B.
OUTI           16 Load output port (C) with (HL), incr HL, decr B.
POP   IX       14 Load IX with top of stack.
POP   IY       14 Load IY with top of stack.
POP   BC       10 Load register pair BC with top of stack.
POP   DE       10 Load register pair DE with top of stack.
POP   HL       10 Load register pair HL with top of stack.
POP   AF       10 Load register pair AF with top of stack.

PUSH  IX       15 Load IX onto stack.
PUSH  IY       15 Load IY onto stack.
PUSH  BC       11 Load register pair BC onto stack.
PUSH  DE       11 Load register pair DE onto stack.
PUSH  HL       11 Load register pair HL onto stack.
PUSH  AF       11 Load register pair AF onto stack.

RES   b,r       8 Reset bit b of register r.
RES   b,(HL)   15 Reset bit b in value at location (HL).
RES   b,(IX+d) 23 Reset bit b in value at location (IX+d).
RES   b,(IY+d) 23 Reset bit b in value at location (IY+d).

RET            10 Return from subroutine.
RET   cc     11,5 Return from subroutine if condition cc is true.
RETI           14 Return from interrupt.
RETN           14 Return from non-maskable interrupt.

RL    r         8 Rotate left through register r.
RL    (HL)     15 Rotate left through value at location (HL).
RL    (IX+d)   23 Rotate left through value at location (IX+d).
RL    (IY+d)   23 Rotate left through value at location (IY+d).
RLA             4 Rotate left accumulator through carry.

RLC   (HL)     15 Rotate location (HL) left circular.
RLC   (IX+d)   23 Rotate location (IX+d) left circular.
RLC   (IY+d)   23 Rotate location (IY+d) left circular.
RLC   r         8 Rotate register r left circular.

RLCA            4 Rotate left circular accumulator.
RLD            18 Rotate digit left and right between accumulator and (HL).

RR    r         8 Rotate right through carry register r.
RR    (HL)     15 Rotate right through carry location (HL).
RR    (IX+d)   23 Rotate right through carry location (IX+d).
RR    (IY+d)   23 Rotate right through carry location (IY+d).

RRA             4 Rotate right accumulator through carry.

RRC   r         8 Rotate register r right circular.
RRC   (HL)     15 Rotate value at location (HL) right circular.
RRC   (IX+d)   23 Rotate value at location (IX+d) right circular.
RRC   (IY+d)   23 Rotate value at location (HL+d) right circular.

RRCA            4 Rotate right circular accumulator.
RRD            18 Rotate digit right and left between accumulator and (HL).

RST   00h      11 Restart to location 0000h.
RST   08h      11 Restart to location 0008h.
RST   10h      11 Restart to location 0010h.
RST   18h      11 Restart to location 0018h.
RST   20h      11 Restart to location 0020h.
RST   28h      11 Restart to location 0028h.
RST   30h      11 Restart to location 0030h.
RST   38h      11 Restart to location 0038h.

SBC   A,r       4 Subtract register r from accumulator with carry.
SBC   A,n       7 Subtract value n from accumulator with carry.
SBC   A,(HL)    7 Subtract value at location (HL) from accu. with carry.
SBC   A,(IX+d) 19 Subtract value at location (IX+d) from accu. with carry.
SBC   A,(IY+d) 19 Subtract value at location (IY+d) from accu. with carry.
SBC   HL,BC    15 Subtract register pair BC from HL with carry.
SBC   HL,DE    15 Subtract register pair DE from HL with carry.
SBC   HL,HL    15 Subtract register pair HL from HL with carry.
SBC   HL,SP    15 Subtract register pair SP from HL with carry.

SCF             4 Set carry flag (C=1).

SET   b,r       8 Set bit b of register r.
SET   b,(HL)   15 Set bit b of location (HL).
SET   b,(IX+d) 23 Set bit b of location (IX+d).
SET   b,(IY+d) 23 Set bit b of location (IY+d).

SLA   r         8 Shift register r left arithmetic.
SLA   (HL)     15 Shift value at location (HL) left arithmetic.
SLA   (IX+d)   23 Shift value at location (IX+d) left arithmetic.
SLA   (IY+d)   23 Shift value at location (IY+d) left arithmetic.

SRA   r         8 Shift register r right arithmetic.
SRA   (HL)     15 Shift value at location (HL) right arithmetic.
SRA   (IX+d)   23 Shift value at location (IX+d) right arithmetic.
SRA   (IY+d)   23 Shift value at location (IY+d) right arithmetic.

SRL   r         8 Shift register r right logical.
SRL   (HL)     15 Shift value at location (HL) right logical.
SRL   (IX+d)   23 Shift value at location (IX+d) right logical.
SRL   (IY+d)   23 Shift value at location (IY+d) right logical.

SUB   r         4 Subtract register r from accumulator.
SUB   n         7 Subtract value n from accumulator.
SUB   (HL)      7 Subtract location (HL) from accumulator.
SUB   (IX+d)   19 Subtract location (IX+d) from accumulator.
SUB   (IY+d)   19 Subtract location (IY+d) from accumulator.

XOR   r         4 Exclusive OR register r and accumulator.
XOR   n         7 Exclusive OR value n and accumulator.
XOR   (HL)      7 Exclusive OR value at location (HL) and accumulator.
XOR   (IX+d)   19 Exclusive OR value at location (IX+d) and accumulator.
XOR   (IY+d)   19 Exclusive OR value at location (IY+d) and accumulator.
```