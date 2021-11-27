MARS can be run from a command interpreter to assemble and execute a MIPS program in a batch fashion. The format for running MARS from a command line is:

```
java -jar ./Mars4_5.jar [options] program.asm [more files...] [ pa arg1 [more args...]]
```
Items in [ ] are optional. Valid options (not case sensitive, separated by spaces) are:

Option	Description	Since
a	assemble only, do not simulate	1.0
aen	terminate MARS with integer exit code n if assembly error occurs	4.1
ascii	display memory or register contents interpreted as ASCII codes. (alternatives are dec and hex)	4.1
b	brief - do not display register/memory address along with contents	2.2
d	display MARS debugging statements (of interest mainly to MARS developer)	1.0
db	MIPS delayed branching is enabled.	3.3
dec	display memory or register contents in decimal. (alternatives are ascii and hex)	2.2
dump	dump memory contents to file. Option has 3 arguments, e.g. dump <segment> <format> <file>. Current supported segments are .text and .data. Also supports an address range (see m-n below). Current supported dump formats are Binary, HexText, BinaryText, AsciiText. See examples below.	3.4
hex	display memory or register contents in hexadecimal - this is the default. (alternatives are ascii and dec)	2.2
h	display this help. Use this option by itself and with no filename.	1.0
mc	set memory configuration. Option has 1 argument, e.g. mc <config>. Argument <config> is case-sensitive and its possible values are Default for the default 32-bit address space, CompactDataAtZero for a 32KB address space with data segment at address 0, or CompactTextAtZero for a 32KB address space with text segment at address 0.	3.7
nc	copyright notice will not be displayed. Useful if redirecting or piping program output.	3.5
np	pseudo-instructions or extended instruction formats are not permitted.	3.0
p	project option - will assemble the specified file and all other assembly files (*.asm; *.s) in its directory.	3.1
sen	terminate MARS with exit code n if simulate (run) error occurs	4.1
sm	start execution at statement having global label 'main' if defined	3.8
we	assembler warnings will be considered errors.	3.5
n	where n is an integer maximum count of execution steps to simulate. If 0, negative or not specified, there is no maximum.	1.0
$reg	where reg is number or name (e.g. 5, t3, f10) of register whose content to display at end of run. Even-numbered float register displays both float and double. Option may be repeated.	2.2
reg_name	where reg_name is the name (e.g. t3, f10) of register whose content to display at end of run. Even-numbered float register displays both float and double. Option may be repeated. $ not required.	2.2
m-n	memory address range from m to n whose contents to display at end of run. m and n may be decimal or hexadecimal (starts with 0x), m <= n, both must be on word boundary. Option may be repeated.	2.2
pa	program arguments - all remaining space-separated items are argument values provided to the MIPS program via $a0 (argc - argument count) and $a1 (argv - address of array containing pointers to null-terminated argument strings). The count is also at the top of the runtime stack ($sp), followed by the array.This option and its arguments must be the last items in the command!	3.5
Example: 
````
java -jar ./Mars4_5.jar h
````
>Displays command options and explanations.

Example: 
````
java -jar ./Mars4_5.jar $s0 $s1 0x10010000-0x10010010 ./sample/Fibonacci.asm
````
>Assemble and run ./sample/Fibonacci.asm.  
At the end of the run, display the contents of registers $s0 and $s1, and the contents of memory locations 0x10010000 through 0x10010010. The contents are displayed in hexadecimal format.

Example: java -jar ./Mars4_5.jar a ./sample/Fibonacci.asm
>Assemble ./sample/Fibonacci.asm. Does not attempt to run the program, and the assembled code is not saved.

Example: 
````
java -jar ./Mars4_5.jar 100000 infinite.asm
````
Assemble and run infinite.asm for a maximum of 100,000 execution steps.

Example: 
````
java -jar ./Mars4_5.jar p major.asm
````
>Assemble major.asm and all other files in the same directory, link the assembled code, and run starting with the first instruction in major.asm.

Example: 
````
java -jar ./Mars4_5.jar major.asm minor.asm sub.asm
````
>Assemble and link major.asm, minor.asm and sub.asm. If successful, execution will begin with the first instruction in major.asm.

Example: 
````
java -jar ./Mars4_5.jar a dump .text HexText hexcode.txt ./sample/./sample/Fibonacci.asm
````
>Assemble ./sample/./sample/Fibonacci.asm without simulating (note use of 'a' option). At end of assembly, dump the text segment (machine code) to file hexcode.txt in hexadecimal text format with one instruction per line.

Example: 
````
java -jar ./Mars4_5.jar dump 0x10010000-0x10010020 HexText hexcode.txt ./sample/./sample/Fibonacci.asm
````
>Assemble and simulate ./sample/./sample/Fibonacci.asm. At end of simulation, dump the contents of addresses 0x1001000 to 0x10010020 to file hexdata.txt in hexadecimal text format with one word per line.

Example: 
````
java -jar ./Mars4_5.jar t0 process.asm pa counter 10
````
>Assemble and run process.asm with two program argument values, "counter" and "10". It may retrieve the argument count (2) from $a0, and the address of an array containing pointers to the strings "count" and "10", from $a1. At the end of the run, display the contents of register $t0.

The ability to run MARS from the command line is useful if you want to develop scripts (macros) to exercise a given MIPS program under multiple scenarios or if you want to run a number of different MIPS programs such as for grading purposes.



#NASM-MIPS Translations
This is a WIP. Please notify me of any mistakes or possible improvements.  
I have ignored any 64-bit differences due to Moore seeming to be a 32-bit system.



#Registers
| Purpose of Register                |       MIPS       |                             NASM                             |
| :--------------------------------- | :--------------: | :----------------------------------------------------------: |
| Stack Pointer                      |       $sp        |                             esp                              |
| Stack Frame Pointer / Base Pointer |       $fp        |                             ebp                              |
| Return Value                       |     $v0-$v1      |                             eax                              |
| Return Value (floating point)      |       $f0        |                             st0                              |
| Arguments                          |     $a0-$a3      |                       NONE - In stack                        |
| Free (32-bit integer)              | $t0-$t9, $s0-$s8 | eax, ecx, ebx, edx, esi, edi(ebx if not in a shared library) |
| Free (16-bit integer)              |       NONE       |                       ax,  bx, cd, dx                        |
| Free (8-bit integer)               |       NONE       |                ah, al, bh, bl, ch, cl, dh, dl                |
| Free (floating point)              |     $f0-$f31     |                       st0,st1,...,st?                        |
| Return Address                     |       $ra        |                       NONE - In stack                        |
| Product and Remainder              |     $LO, $HI     |                           eax, edx                           |
| Quotient and High 32 Bits          |     $LO, $HI     |                           eax, edx                           |





#Basic Instructions
Arbitrarily chosen names are shown in _italics_.  
There are several more pseduoinstructions that are combinations of the ones shown here.  

| Description                              |                  MIPS                   |                                NASM                                 |
| :--------------------------------------- | :-------------------------------------: | :-----------------------------------------------------------------: |
| Load immediate value into register.      |               li _$t0, 4_               |                             mov _eax,4_                             |
| Move value of one register into another. |             move _$t0, $t1_             |                            mov _eax,ecx_                            |
| Add 2 registers.                         |           add _$t0, $t1, $t2_           |             add _eax,ebx_ (Sum saved in first register)             |
| Add register and immediate.              |           addi _$t0, $t1, 4_            |              add _eax,4_ (Sum saved in first register)              |
| Subtract 2 registers.                    |           sub _$t0, $t1, $t2_           |         sub _eax,ebx_ (Difference  saved in first register)         |
| Subtract register and immediate.         |           subi _$t0, $t1, 4_            |          sub _eax,4_ (Difference  saved in first register)          |
| Multiply.                                | mult _$t1, $t2_ (Result in $HI and $LO) | mul _ebx_ (Multiply __eax__ by _ebx_ and put product into __eax__.) |
| Multiply by an immediate.                | multi _$t1, 3_ (Result in $HI and $LO)  |  imul _ebx, 3_ (Multiply _ebx_ by _3_ and put product into _ebx_.)  |
| Divide.                                  | div _$t1, $t2_ (Result in $HI and $LO)  | div _ebx_ (Divide __eax__ by _ebx_ and put quotient into __eax__.)  |
| Divide by an immediate.                  |  divi _$t1, 3_ (Result in $HI and $LO)  |  idiv _ebx, 3_ (Divide _ebx_ by _3_ and put quotient into _ebx_.)   |
| Add 1 to a register.                     |          addi _$t1_, _$t1_, 1           |                              inc _eax_                              |
| Subtract 1 from a register.              |          subi _$t1_, _$t1_, 1           |                              dec _eax_                              |
| Call a function.                         |       jalr _address of function_        |                       call _name of function_                       |
| Import an external function.             |       .global _name of function_        |                      extern _name of function_                      |
| Return                                   |                 jr $ra                  |         ret (Returns to the last jump instruction called.)          |

#Data Instructions

| Description                             |                      MIPS                       |                       NASM                       |
| :-------------------------------------- | :---------------------------------------------: | :----------------------------------------------: |
| Push a value into the stack.            |        subi $sp, $sp, 4; sw _$t0_, ($sp)        |                     push eax                     |
| Pop a value from the stack.             |        lw _$t0_, ($sp); addi $sp, $sp, 4        |                     pop eax                      |
| Declare initialized variable. (Word)    |            _variableName_: .word _3_            |              _variableName_: dw _3_              |
| Declare uninitialized variable. (Word)  |             NONE - Must initialize.             |   _variableName_: resd _1_ (_1_ is # of words)   |
| Declare initialized variable. (Byte)    |            _variableName_: .byte _3_            |              _variableName_: db _3_              |
| Declare uninitialized variable. (Byte)  |             NONE - Must initialize.             |   _variableName_: resb _1_ (_1_ is # of bytes)   |
| Declare initialized variable. (Array)   |            NONE - Initialize after.             |              _variableName_: db _3_              |
| Declare uninitialized variable. (Array) | _variableName_: .space 10 (Array of _10_ words) | _variableName_: resq _10_  (Array of _10_ reals) |

#Bitwise Instructions
| Description            |          MIPS           |       NASM       |
| :--------------------- | :---------------------: | :--------------: |
| Shift logical left <<  |  sll _$t0_, _$t0_, _4_  |   shl _eax, 4_   |
| Shift logical right >> |  srl _$t0_, _$t0_, _4_  |   shr _eax, 4_   |
| Bitwise NOT            |    not _$t0_, _$t0_     |    not _eax_     |
| Bitwise AND            | not _$t0_, _$t0_, _$t1_ | not _eax_, _ebx_ |
| Bitwise OR             | xor _$t0_, _$t0_, _$t1_ | xor _eax_, _ebx_ |
| Bitwise XOR            | or _$t0_, _$t0_, _$t1_  | or _eax_, _ebx_  |

#Logic Instructions
##MIPS
|        Instruction        | Description                                     |
| :-----------------------: | :---------------------------------------------- |
|         b _label_         | Unconditional branch.                           |
| beq __$t0, $t1__, _label_ | Branch if $t0 = $t1                             |
|   bgez __$t0__, _label_   | Branch if $t0 >= 0                              |
|  bgezal __$t0__, _label_  | Branch if $t0 >= 0 and set $ra                  |
|   bgtz __$t0__, _label_   | Branch if $t0 > 0                               |
|   bgtz __$t0__, _label_   | Branch if $t0 < 0                               |
|  bgtzal __$t0__, _label_  | Branch if $t0 < 0 and set $ra                   |
| bne __$t0, $t1__, _label_ | Branch if $t0 != $t1                            |
|        j _address_        | Jump to address.                                |
|         jr _$t0_          | Jump to address stored in register.             |
|       jal _address_       | Jump to address and set $ra.                    |
|        jalr _$t0_         | Jump to address stored in register and set $ra. |
|       _labelname_:        | Define a label.                                 |

##NASM
|  Instruction   | Description                          |
| :------------: | :----------------------------------- |
| cmp _eax, ebx_ | Set comparison flags for two values. |
|   jl _label_   | Jump to label if _eax_ < _ebx_       |
|  jle _label_   | Jump to label if _eax_ <= _ebx_      |
|   je _label_   | Jump to label if _eax_ = _ebx_       |
|  jge _label_   | Jump to label if _eax_ >= _ebx_      |
|   jg _label_   | Jump to label if _eax_ > _ebx_       |
|  jne _label_   | Jump to label if _eax_ != _ebx_      |
|  _labelname_:  | Define a label.                      |



#Appendix
##A - Getting the program counter in MIPS.
```
  bgezal $zero, getpc
getpc:  
  move $v0, $ra  
  j $ra
```

##B - NASM setup instructions.
| Instruction | Definition                                                    |
| :---------: | :------------------------------------------------------------ |
|    pusha    | Push all registers.                                           |
|    popa     | Pop all registers.                                            |
|    enter    | Create the stack frame. (Gives you a place to push)           |
|    leave    | Destroy the stack frame. (Frees up the memory taken by enter) |

##C - NASM Calling Procedure
###Caller
```
push ARGUMENT#N  ;Push arguments
;...
push ARGUMENT#3
push ARGUMENT#2
push ARGUMENT#1
call FUNCTION         ;Call function
add esp, 4*N          ;Essentially pops arguments.
```  

##Callee
```
enter 0,0 ;Create stack frame

mov ARGUMENT#1, dword [ebp+8]       ;Load arguments.
mov ARGUMENT#2, dword [ebp+12]
mov ARGUMENT#3, dword [ebp+16]
;...
mov ARGUMENT#N, dword [ebp+4+N*4]

;YOUR FUNCTION CODE HERE!

leave   ;Destroy stack frame
ret     ;Return to caller
```  


#Sources

https://github.com/akainth015/mars/wiki/Command-Line-Interface
https://gist.github.com/t-mullen/02969915753a8684ed6f#basic-instructions
http://www.nasm.us/doc/nasmdoc3.html  
https://www.csee.umbc.edu/courses/undergraduate/CMSC313/fall04/burt_katz/lectures/  
http://www.eecg.toronto.edu/~amza/www.mindsec.com/files/x86regs.html  
https://msdn.microsoft.com/en-us/library/ms253512(VS.80).aspx  
http://www.mrc.uidaho.edu/mrc/people/jff/digital/MIPSir.html  
https://www.cs.uaf.edu/2006/fall/cs301/support/x86/  
http://home.myfairpoint.net/fbkotler/nasmdocc.html  
https://en.wikibooks.org/wiki/X86_Assembly/Other_Instructions  
http://www.nasm.us/doc/nasmdoc9.html  
https://en.wikipedia.org/wiki/MIPS_instruction_set#Pseudo_instructions