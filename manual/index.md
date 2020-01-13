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
  - So why Forth?
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
  - Z80 instruction set
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
main.z80                the root file of Firth (start here)
mycomputer.emu          engine configuration file for the asm80 online emulator
primitives.z80          Forth words which are written in assembly language
struct-macros.z80       macros used to enable Assembly language control structures
test-macros.z80         macros used to enable unit tests
tests.z80               unit tests to test various aspects of the Forth engine
utilities.z80           Utility subroutines used by
variables.z80           Memory locations defined in RAM
words.z80               Forth words which written in Forth
```

You can start by examining `main.z80` which includes all the other files. The first line contains the .engine directive

```
.engine mycomputer
```


