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

## Day 2

<details>
<summary> Introduction to ABI and verification Flows </summary>




