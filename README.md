# TRS-80 Diagnostic ROMs

![Normal Operation](https://github.com/misterblack1/trs80-diagnosticrom/blob/main/documentation/Normal%20Operation%2016K%20Model%203.jpg?raw=true)

## Introduction from Adrian

This projet was born out of a broken TRS-80 Model 3 that I was working on. I could not tell if the system was even "executing code," so I used an early version of this ROM to help diagnose the problem.

Please know that the main goal of this ROM is to test the functionality of the video RAM (VRAM) and the dynamic RAM (DRAM, system memory.) It does not test any other component unrelated to those two subsystems. If a TRS-80 has good VRAM and DRAM, it should boot into basic where you can then run further tests. 

You should familiarize yourself with the system schematics and design of the TRS-80 before using this ROM since problems in other areas of the system can sometimes manifest themselves of a RAM problem. 

<< Insert video links here when live >>

In addition, most (all?) RAM tests contained inside diagnostic ROMs on various systems use a very rudamentary RAM test that is inadequate to detect subtle RAM problems. While the test in this ROM isn't the end-all, be-all of RAM tests, we feel it is better than the normal simple bit pattern tests used elsewhere. The RAM test implemented here is a "march" test, which is widely viewed as one of the best tests there is.

## Feature List

- One ROM image for both Model 1 and 3 and all DRAM sizes
- Audio feedback via the cassette port, so you can tell what's happening even if you have no video display
- Testing the VRAM using a march test, then Using the VRAM as the stack so the ROM can fully work without any DRAM installed in the system *1
- Auto detection of VRAM type (The Model 1 comes with 7-bit VRAM as stock, so it cannot be used as a stack)
- Auto detection of bank size (4k or 16k) *2
- Testing up to 48k of DRAM, looping continually 
- Fits within 2K so the ROM can be used on a Level 1 machine

*1: The stack can only be used if all 8-bits of VRAM are available and working. If there is a VRAM fault, the system will make a tonoe and halt. If there is only 7-bit of VRAM (stock Model 1) the RAM test will test and use the first vank of DRAM for the stack. 

*2: When testing, if the ROM detects 4K, it will only test 4k and stop. This is the mazimum allowed RAM in a 4K configuration. When 16k is detected, it will attempt to test all 3 banks oF DRAM, even if only 1 is installed.


## Future improvements

- Porting the ROM to other TRS-80 systems like the Model 2 and Model 4
- Porting the diagnostic routines to other Z80 systems



# Running this diagnostic ROM on a TRS-80 Model 1 or Model 3

To use this diagnostic ROM on a TRS-80 Model 3, you must first make or buy an adapter to allow use of an EPROM in the U104 ROM socket. This socket is designed for a 2364 which does not have a compatible pinout with a 2764 EPROM. Adapter PCBs are widely available on the usual sources, or you can make some PCBs at this link:

[PCBway Project Link for EPROM adapter](https://www.pcbway.com/project/shareproject/Adapter_2364___27128__by_Bobbel_.html
)
  
One you have a programmed 2764 or 28B64C, insert that into the adapter and install it into U104 on the Model 3. This is the boot ROM that the CPU starts to execute code from at power-up. (Address $0000)
  
On a TRS-80 Model 1 with Level II ROM upgrade, the main boot rom is the left most chip. On the Model 1, the main ROM is a 2332 ROM chip is a 2332, so a 2732 should work in place of it. (Unconfirmed. I used my 2364 to 2764 adapter in this socket and it mostly worked, except I had to write the ROM into the second top half of the 28b64. Load the ROM image into address $1000 in your EPROM software, then write to the chip.)
  
You can leave the othe ROM chips installed in the system. 

The VRAM test detects if the machine is a 7-bit VRAM Model 1 by testing the VRAM. If bit 6 (data line 6) comes back as bad, it assumes it's a model 1.
  
If you have a Model 3 or a 8-bit VRAM Model 1 (this is also known as the lower-case mode, where one extra VRAM chip is added in) the Diagnostic screen will say it's using the VRAM for the processor stack. If you areon a Model 3 or 8-bit VRAM Model 1 and the ROM says it is using DRAM for the stack, **then bit 6 of the VRAM has a problem and you must fix that. **

When bad bits are detected in VRAM or DRAM, you will hear a beep code telling you which bit is bad. The beeping goes from Bit 7 (MSB) to Bit 0 (LSB.) So if you hear, high high high low high high high high, then the bad bit is BIT 4 (of 7). You will not hear a VRAM beep code for a problem in bit-6 because it assumes it is a Model 1. (See above.)

4K DRAM machinse can only ever have 4K of RAM, so only the first 4K of ram is tested and no more.

On the Model 3, you **must** have a working connection on JP2A and JP2B to run this diagnostic. Both the cassette port (for audio output) but more importantly the video subsystem is across this interconnect. Bits 0 and bit 1 of this interconnection are required for the cassette port audio, but all 8 bits are needed for video to work. 

On the Model 3, the cassette port output is the pin closest to the keyboard connector. (Connector J3) On the Model 1, you can either clip a test lead onto the cassette port, or use the cassette DIN cable to get audio output. 

You do not need any DRAM installed in the machine for the diagnostic to run. If you have good working VRAM but no working DRAM, you should see it trying to test the DRAM, but all banks will come back as bad. Keep in mand a stuck or bad DRAM bus transciever can trash the entire bus, causing the VRAM test to also fail.

You do not need the keyboard connected for the system to run the diagnostic. The keyboard is not used during the test at all.

The diagnostic ROM _must_ be installed into U104 on the TRS-80 Model 3. You must use a 2364 to 27XXX adapter. The one I used is made for 27128, but it works just fine with 2764 and more importantly 28B64C (EEPROMs.) You can also use this same adapter in U105 (for testing replacment of that ROM.) You can use a normal 2716 in U106 if you need to test replacing that ROM.

You do not need to have any ROM installed in U105 or U106 during the test. A bad ROM in one of those sockets could cause the computer to not work.

You do not need the interconnect between JP1A and JP1B. This is only to connect the floppy and serial board. The system will operate fine without the interconnect, but you will not be able to use the floppy or serial port. 

## Building

This repository will contain the assembled ROM image.  To assemble, you will need
to use the `zmac` assembler from George Phillips, located here: http://48k.ca/zmac.html.  Many thanks to George also for his excellent emulator, `trs80gp` (located here: http://48k.ca/trs80gp.html) which includes integrated debugging facilities which dramatically reduced the time necessary to develop and debug these diagnostics.