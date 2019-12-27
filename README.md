Firth is a minimal (~4K) implementation of Forth for the Z80.

It uses the ASM80 assembler and can be emulated at the [ASM80](https://www.asm80.com/) site by the following steps:

1. Press the `Import repo from GitHub` button
2. Paste the following repo name `https://github.com/jhlagado/firth` and click OK
3. Select the `main.z80` file
4. Press the `Emulator [F10]` button

This will cause ASM80 to start emulating the computer hardware specified in the mycomputer.emu file which includes a Z80 CPU and a Motorola 6850 serial chip mapped to ports $80 and $81.

The emulator will start up a green screen serial terminal emulation and present you with a prompt

```
Firth of Forth Z80
by John Hardy (c) 2019

>
```
You can type Forth commands into it. e.g
```
> 1 2 3 * + .

7
```
You can exit the Forth interpreter by pressing the `Back to IDE` button on the top right corner.

You can run the unit tests which exercise all the important functions of the Forth interpreter by setting line 3 to:
```
TESTMODE        equ     1
```
I you are interesting in seeing how the code executes, ASM80 gives you the ability to single-step through it an instruction at a time. The easiest way to do that is to comment out line 1:
```
; .engine mycomputer
```
This turns off the emulator and shows you the internal state of the CPU. Step through code by pressing `Single step(F8)` and `Step over (F7)`.
