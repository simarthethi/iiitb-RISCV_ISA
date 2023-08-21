# iiitb-RISCV_ISA
This github repositary contains all the progress made during RISC-V based MYTH

[Day 1](#day-1)

[Day 2](#day-2)

## Day 1

<details>
<summary> Installation of tools </summary>
To install the RISC-V toolchain on Ubuntu, follow these steps:
- Install Prerequisites:
Before you can build the RISC-V toolchain, you'll need to install some software dependencies like spike, pk, gcc etc:
  
``` bash
sudo apt update
sudo apt install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev git
```
-  Clone the RISC-V GNU Toolchain Repository:
```bash
git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
```
- Build and install the tool chain
```bash
git clone https://github.com/kunalg123/riscv_workshop_collaterals.git
cd riscv_workshop_collaterals
chmod +x run.sh
./run.sh
```
Once you run it you may get make error. ignore it  and type the following command
```bash
cd ~/riscv_toolchain/iverilog/
git checkout --track -b v10-branch origin/v10-branch
git pull 
chmod 777 autoconf.sh 
./autoconf.sh 
./configure 
make
sudo make install 
```
to set the PATH variable in .bashrc
```bash
gedit .bashrc
export PATH="/home/simar-thethi/riscv_toolchain/riscv64-unknown-elf-gcc-8.3.0-2019.08.0-x86_64-linux-ubuntu14/bin:$PATH" #Type at last line #
close the bashrc and type
source .bashrc
```
</details>
<details>
<summary> Introduction to RISC-V ISA and basic keywords </summary>

RISC-V is an open-source instruction set architecture (ISA) for computer processors.
An instruction set architecture defines the set of instructions that a processor can execute and the organization and behaviour of those instructions. RISC-V is unique in that any single company or organization does not own it. and it is freely available for anyone to use, modify, and implement without the need for licensing fees or proprietary restrictions.
![Screenshot from 2023-08-21 01-05-30](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f7f9e295-496b-43bd-affd-042211d74f89)
Application software (apps) and hardware are linked by 'system software'.There are various layers of **system software**. This includes major components like Compiler and Assembler.

The compiler compiles high-level codes like C and C++ to Instructions(eg: the codes inside .exe files) that can be read by the Assembler.
The Assembler converts it into binary codes which the machine can understand. The instructions act as an interface between the high-level language and the machine language.

The converted binary is then given to an RTL snippet that understands the instruction. This is done by a Hardware Description Language (HDL).
This is basically called RTL implementation and a netlist is being generated. with this, a physical design implementation of the design is generated.

The RISC-V project began at the University of California, Berkeley in 2010, and it has since gained significant traction in both academia and industry. Its open nature has led to a growing ecosystem of hardware and software developers collaborating to create a wide range of products, from simple embedded devices to high-performance supercomputers. 
</details>

<details>
<summary> Lab 1 : RISC-V software toolchain | GCC Compile and Disassemble </summary>
- sumt of 1 to n integers on sum1ton.c
  
  First, let us write a basic C program to find the sum of n numbers using *gedit*.
  ```bash
gedit sum1ton.c
```
```bash
  #include <stdio.h>
int main(){
int n = 100,sum=0,i;
for(i=0;i<=n;i++)
{
    sum= sum +i;
}
printf("The sum of %d consecutive numbers is :%d \n",n,sum);
return 0;
}
```
after writing the code we can execute it and also compile the .c file using RISC-V compiler tool
```bash
./a.out
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
```
To view the assembly code for the same, do the following command.
```bash
riscv64-unknown-elf-objdump -d sum1ton.o
```
![Screenshot from 2023-08-21 01-30-09](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/434602a0-5a1e-42e6-8f89-536d0c527a50)
To view the detailed code do the following command.
```bash
riscv64-unknown-elf-objdump -d sum1ton.o | less
```
![Screenshot from 2023-08-21 01-34-06](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/16e6e3a7-eb22-424d-b9c4-708d13385477)

In the above screenshot, we can see the memory address for 
the instructions. Where it starts and where another one 
begins.
If we subtract '00000000000101c0'(end of main) and 
'0000000000010184'(beginning of main) and then divide by 4 
we get 15. Which is the number of instructions within that 
particular block(main).

Now let's execute the below commands:
```bash
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
riscv64-unknown-elf-objdump -d sum1ton.o | less
```
![Screenshot from 2023-08-21 01-38-40](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/84e3d5b3-b3ae-4e94-9c0d-fe3a5939b1a9)

</details>

<details>
<summary> Lab 2 : Spike Simulation and Debug </summary>

In this lab, we are going to Debug the '.o' file that we 
generated using the RISC-V compiler.
For that, we use the following command: 
```bash
spike pk sum1ton.o
spike -d pk sum1ton.o
```
The debugger mode will be open.
We use the until command to move to a particular address.
Here we are debugging the highlighted instructions.
![Screenshot from 2023-08-21 01-38-40](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/84e3d5b3-b3ae-4e94-9c0d-fe3a5939b1a9)
The following commands are used in the debugger:
```bash
until pc 0 100b0 //moves the program counter(PC) to the address
reg 0 a2    //views content of the address
**We press enter to go to the next instruction**
```
![risc day_1 debugging in Ofast directory](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/59e0a415-f1c0-4397-a968-07300f1f52ae)

In the above screenshot, we can see the register pertaining to a particular instruction getting updated.
</details>

<details>
<summary> Integer number representation </summary>
The RISC-V architecture defines several different data types and number systems to represent and manipulate data. Here, I'll explain the basic number systems used in RISC-V:
  
![Screenshot from 2023-08-21 09-55-55](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/fc09b089-ab31-4f9f-8199-4a1d2c3341d6)


- Binary Number System: RISC-V, like most digital systems,
primarily operates on binary data. In the binary number
system, numbers are represented using only two symbols: 0
and 1. Each digit in a binary number represents a power of
2. For example, the binary number "1101" represents (1 *
2^3) + (1 * 2^2) + (0 * 2^1) + (1 * 2^0) = 13 in decimal.
- Integer Representation: RISC-V supports different integer
data types with varying sizes. The most common are 32-bit
and 64-bit integers, denoted as "RV32" and "RV64"
respectively. Integers are typically represented in two's
complement form, which allows both positive and negative
values to be stored and manipulated using the same hardware.
- Floating-Point Representation: RISC-V also supports
floating-point operations for real numbers. Floating-point
numbers are represented using a sign bit, an exponent, and a
fraction (also known as mantissa). RISC-V defines different
formats for floating-point numbers, including the IEEE 754
standard formats (single precision, double precision, etc.).
These formats allow a wide range of values to be represented
with varying levels of precision.
- Hexadecimal Notation: While binary is the fundamental
representation in RISC-V, hexadecimal (base-16) notation is
often used to represent binary numbers in a more human-
readable form. Each hexadecimal digit represents four bits.
For example, the binary number "11011010" can be represented
as "DA" in hexadecimal.
- Memory Addressing: RISC-V CPUs use memory addresses to
access data stored in memory. Memory addresses are typically
represented in hexadecimal form. The exact memory addressing
scheme depends on the specific RISC-V implementation and the
memory model being used. Overall, the RISC-V architecture
provides a flexible framework for representing and
manipulating different types of numbers, allowing software
developers and hardware designers to efficiently perform
arithmetic and logical operations on various data types
within the context of RISC-V-based systems.

In computer architecture, the terms "bit," "byte," "word," and "double word" refer to different units of data storage and manipulation. These terms are used to describe the size of data that a computer's memory and processing units can handle. The specific sizes of these units can vary based on the architecture and implementation, but I'll provide you with some common interpretations:

- Bit: A bit is the smallest unit of data in computing. It
can represent one of two values: 0 or 1. Bits are the
building blocks of all digital information and are used to
represent various types of data and instructions in a
computer's memory and processing units.

- Byte: A byte is a group of 8 bits. It is the basic
addressable unit of memory storage in most computer
architectures. Bytes are commonly used to represent
characters, numbers, and other small data elements. For
example, the ASCII code for the letter 'A' is 65, which can
be represented as a byte with the binary value 01000001.

- Word: The term "word" refers to the natural data size that
a computer's central processing unit (CPU) can process in a
single operation. The size of a word can vary between
different computer architectures. In the context of x86 and
x86-64 architectures, a word is typically 16 bits, while in
other architectures like RISC-V, a word can be 32 bits or 64
bits. The size of a word determines the maximum amount of
data that the CPU can manipulate at once, which can impact
the efficiency of data processing.

- Double Word (Dword): The term "double word" (often
abbreviated as "dword") is used to describe a data unit that
is twice the size of a standard word. In x86 and x86-64
architectures, a double word is 32 bits, while in some other
architectures, it can refer to a 64-bit value. The term
"dword" is often used in the x86 family of processors to
describe a 32-bit data value. It's important to note that
the exact sizes of these units can vary based on the
computer architecture and implementation. Total Number of
pattern by RV64 will be 2^64 RISC- doubleword can represent
'0' to '(2^64 - 1)' unsigned numbers or positive numbers
</details>
<details>
<summary> Lab for signed and un signed integers </summary>
  
Here we are going to execute the following code for unsigned numbers.
The output is given for unsigned numbers and we are just viewing if it is within the range or 
it goes out in which case displays either the minimum or maximum values.

  ```bash

#include <stdio.h>
#include <math.h>
int main()
{
    unsigned long long int max = (unsigned long long int)(pow(2,64)-1);       //statement 1  //will display the max number
    //unsigned long long int max = (unsigned long long int)(pow(2,127)-1);    //statement 2  //(out of range) will display the max number(within range)
    //unsigned long long int max = (unsigned long long int)(pow(2,10)-1);     //statement 3  //will display number (since it is within range)
    //unsigned long long int max = (unsigned long long int)(pow(2,64) * -1);  //statement 4  //will display 0 since it is a -ve number
    printf("highest number represented by unsigned long long int is %llu\n",max);
    return 0;
}
```
The below screenshot shows the output of the same.
![risc day_1 unsignednumber](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/1ad854c1-0f7a-4e5e-8761-250520cf8307)

- Next we execute the code for signed intgger

```bash
#include <stdio.h>
#include <math.h>
int main()
{
    long long int max = (long long int)(pow(2,63)-1);        //will display the max number
    long long int min = (long long int)(pow(2,63) * -1);     //will display the min number
    printf("highest number represented by long long int is %lld\n",max);
    printf("lowest number represented by long long int is %lld\n",min);
    return 0;
}
```
The below screenshot shows the output of the same.
![riscv day_1 signednumber](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/e5759cdc-2334-4fc4-b88a-1daf95c8e119)
</details>



## Day 2

<details>
<summary> Introduction to ABI and verification Flows </summary>


- The application program can directly access the registers of the RISC V architecture using something known as system calls. The ABI (also known as system call interface enables the application to access the hardware resources via registers.

- In RISC V architecture, the width of the register is defined as XLEN. For RV64 and RV32, the widths are 64 bits and 32 bits, respectively.

- RISC V belongs to the little endian memory addressing system, which means that the least significant byte of a word is stored in the smallest memory address.

An Application Binary Interface is a set of rules enforced by the operating system on a specific architecture. So, Linker converts relocatable machine code to absolute machine code via ABI interface specific to the architecture of machine. Just like how application program interface (API) is used by application programs to access the standard libraries, an application binary interface or system call interface is utilised to access hardware resources. The ISA is inherently divided into two parts: User & System ISA and User ISA the latter is available to the user directly by system calls.

Now, how does the ABI access the hardware resources?

- It uses different registers(32 in number) which are each of width XLEN = 32 bit for RV32 (~XLEN = 64 for RV64) . On a higher level of abstraction these registers are accessed by their respective ABI names.

    For base integer instructions there are broadly 3 types of of such registers:
        I-type : For instructions having immediate values as operands.
        R-type : For instructions having only registers as operands.
        S-type : For instructions used for storing operations.

So, it is system call interface used by the application program to access the registers specific to architecture. Overhere the architecture is RISC-V, so to access 32 registers of RISC-V below is the table which shows the calling convention (ABI name) given to registers for the application programmer to use.

## Load,Add And Store Instructions
```bash
ld x8,16(x23)
```
here ld is for load doubleword,x8 shows destination register (rd),16 is offset,x23 is source register . This is I type Instructions :
![Screenshot from 2023-08-21 11-20-40](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/831b8e92-5443-46cc-adef-331f1427c12b)
```
add x8,x29,x8
```
here add is function,x8 is destination register (rd),x29 & x8 is source register. This is R type Instructions :
![Screenshot from 2023-08-21 11-24-27](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/dcfaefc5-a12b-46ef-8c08-748c8db539f1)
```bash
sd x8,8(x23)
```
here store is store doubleword,x8 is data registers,8 tell offset(immediate) ,x23 is source register. This is S type Instructions :
![Screenshot from 2023-08-21 11-34-28](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/bd67289f-f016-434f-bb8f-d821e0ff2cc2)
Here in each Instructions set we can see register are of 5 bits so total number of register = 2^5 = 32 registers
RISC-V
![Screenshot from 2023-08-21 11-37-03](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f17b3c11-77c8-491c-a875-14bec724912a)

![Screenshot from 2023-08-21 11-39-30](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/57c3343c-4cd5-4253-bad6-c4cc6053fa0f)
</details>
<details>
<summary> Illustration of ABI </summary>
For verification of the RISC-V CPU the C code will be converted into HEX file and it will be given to the RISC-V CPU and the output will be displayed and verified. The block diagram is shown below : 

Consider the .c file for sum from 1 to 9
![Screenshot from 2023-08-21 12-28-24](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6fc3a2e6-b3b8-40bc-a787-ed2c127ae1a4)

Consider the assembly code (ASM) given below :
![Screenshot from 2023-08-21 12-28-35](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/c8894186-6b6e-44de-a286-83dbc1071d42)

The flow chart of the function performed by ASM code is shown below :
![Screenshot from 2023-08-21 12-31-23](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/a59b2a81-9f7e-47ab-94a2-eb6ec9b624ec)

To illustrate the ABI the C code shown above will send the values to the ASM code through the function load and the ASM code will perform the function and return the value to C code and the value is displayed by the C code.

- Perform the following steps
```bash
riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o custom_1_to_9.o custom_1_to_9.c load.S
riscv64-unknown-elf-objdump -d custom_1_to_9.o | less
spike pk custom_1_to_9.o
```
![Screenshot from 2023-08-21 11-52-15](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/2d4cc791-c8c7-482f-a48e-ef99d6bb0669)

![Screenshot from 2023-08-21 11-50-55](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/0a9fbf4c-2aa5-48be-b543-a3226bbb9406)
</details>

<details>
<summary> RISC-V Basic Verification flow using iverilog demo </summary>
  
  ![Screenshot from 2023-08-21 12-20-50](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4a1926ab-0d60-45ad-a38b-965c08c9bc29)
  For demo go to the lab directory using the command given below :
  ```bash
cd ~/riscv_workshop_collaterals/labs/
chmod 777 rv32im.sh
./rv32im.sh  # Contains necessary commands to convert C to hex
```
**Output, Script(rv32im.sh) and firmare.hex**
![Screenshot from 2023-08-21 12-23-14](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/449d6aff-7cea-4ad2-9a07-988ec818bad9)

![Screenshot from 2023-08-21 12-24-27](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f74e2dda-8a01-4d2d-b8a4-3f06361f2c66)

![Screenshot from 2023-08-21 12-37-51](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6dd9e186-2adf-46e3-b0d4-23cc88881d09)

![Screenshot from 2023-08-21 12-41-29](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6e8a2180-4186-4b98-976f-d155a88efd50)


</details>





