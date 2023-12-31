# iiitb-RISCV_ISA
This github repositary contains all the progress made during RISC-V based MYTH

[Day 1](#day-1)

[Day 2](#day-2)

[Day 3](#day-3)

[Day 4](#day-4)

[Day 5](#day-5)

## Day 1 - Introduction to RSIC-V ISA and GNU compiler toolchain

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



## Day 2 - Introduction to Application Binary Interface And Basic Error Flow

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

## Day 3 - Digital Logic with TL verilog and Makerchip

<details>

<summary> Introduction </summary>

In here we will learn about **TL verilog** and **makerchip** and how to implement and visualize the logic gates and other circuits
The topics covered are as follows:

- Logic gates
- MakerChip platform(IDE)
- Combinational Logic
- Sequential Logic
- Piplining logic
- Validity

**Logic Gates** - Logic gates are fundamental building blocks of digital circuits. They are electronic devices that perform basic logical operations on one or more 
binary inputs (usually 0 or 1) to produce a single binary output. These gates are the foundation of all digital systems, including computers, microcontrollers, and 
other digital devices. Logic gates are typically implemented using electronic components such as transistors.

The gates commonly used are:

- NOT
- AND
- OR
- NAND
- NOR
- XOR
- XNOR

![risc day 3 ](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/86b2e0e3-8356-4fd2-a8bf-0d94282c26e1)
Although we can implement all the gates using *NAND* or *NOR* gates (prefers NAND gate with its low-cost, high-density, high-speed 
program/erase applications, for file storage in consumer applications.)

The following provides booliean logic for the above gates
![Screenshot from 2023-08-26 18-22-56](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/57e4e26e-900a-4a1a-af16-f516e469539f)

**Introduction to makerchip**
Makerchip is a free online environment for developing high-quality integrated circuits. You can code, compile, simulate, and debug Verilog 
designs, all from your browser. Your code, block diagrams, and waveforms are tightly integrated.TL-Verilog was used as the HDL of choice 
for this project. Projects on Makerchip can be completely designed using TL-Verilog. Transaction Level - Verilog standard is an extension 
of Verilog which has various advantages like simpler syntax, shorter codes and easy pipelining.

*Loading the Pythagorean example on the makerchip*
![Screenshot from 2023-08-21 14-31-33](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f5a732b9-3721-46cb-bd18-e256a226ae4b)

*Loading invertor logic on makerchip*
![Screenshot from 2023-08-21 14-48-26](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/097ecb0e-b85c-49f4-a89e-fda7fb6a8282)

</details>
<details>
<summary>Combinational Logic</summary>
  
Under this section, we will go over a few lab examples using Makerchip to have a firm grasp.
  
*Lab On Understanding Usage Of Vector*
![Screenshot from 2023-08-21 14-54-24](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/100c5fda-0752-46b7-ad5f-d07be9eb7315)

*Lab on making MUX using makerchip IDE*
![Screenshot from 2023-08-26 18-35-17](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4b39ecd7-e2fd-412d-a4bb-529e92531740)

**Implementation of the calculator using MakerchipIDE**
Now a lab on combinational calculator is implemented that can perform +, -, *, / on two input values. The snapshot of the code, waveform and diagram is as shown below.
![Screenshot from 2023-08-21 15-43-43](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/d98025c3-e023-4654-b853-92a1434aaa93)

</details>
<details>
<summary>Sequential logic</summary>  
Under this section, we will look into the implementation of sequential logic circuits on Makerchip IDE. Sequential logic integrates a clock 
that defines the flow and transition of data. The cicuit also integrates a reset which upon activation will reset the output to a pre-
defined value. The most common flipflop used is D flip flop.

![Screenshot from 2023-08-26 18-41-40](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/14d3b3f6-f9f4-42b3-b0a8-376bd3360841)
- The circuits can be refered as a state machine as well, the flops are followed by the combinational logic.

*Fibonacci sequence implementaion*
![Screenshot from 2023-08-26 18-43-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/be0e38f8-8b30-4fbe-913f-67df284f0081)

*Implementing free running counter*
![Screenshot from 2023-08-21 15-52-17](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f6b85424-e0a6-4433-88fe-12accc5510d1)

*Sequential calculator using makerchipIDE*
Here we will use what e learnt during the counter and fibonacci series an apply it on the calculator that we implemented before.
We implement a sequential calculator that updates on each clock cycle.
- The circuit diagram
![Screenshot from 2023-08-26 18-48-07](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/8bb98cfe-1996-4673-9877-b2cbdd1e5182)

*makerchipIDE squential calculator*
![Screenshot from 2023-08-22 01-37-17](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4cacb86a-a1a3-4025-ade3-f3da38e4a2f1)

</details>

<details>
<summary>Pipelining</summary>

Pipelining is a technique used in digital design and computer architecture to improve the efficiency and performance of processing by 
breaking down a task into 
smaller stages that can be executed concurrently. Here are some of the benefits of pipelining -
- Increased throughput
- reduced latency
- Better resource utilization
- improved parallelism
- Smoother Performance
- Scalability
- Faster clock speeds

*Pipelined Pythegorean Imoplementation*
![Screenshot from 2023-08-22 14-15-12](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/268f9da4-5140-46bd-8670-7775af9688db)

*Fibonacci sequence in pipeline*
![Screenshot from 2023-08-26 21-28-17](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/7d2213de-a780-4a29-9502-13f974dfcc62)

![Screenshot from 2023-08-26 21-29-53](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/aab3dbff-c89d-494f-a5a1-a3151660ee8e)

*Creating the given piplined circuit in makerchipIDE*

- Under this, we are given a pipelined structure and asked to recreate it on Makerchip using TLverilog
![Screenshot from 2023-08-26 21-32-22](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6443b423-1f2b-4425-8035-14d9985cb935)

*Counter and Calculator in pipeline*
![Screenshot from 2023-08-26 21-55-39](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/e15763a4-375e-401e-a9cf-671737289f03)


- In here we will apply pipeline to our counter and sequential calculator

![Screenshot from 2023-08-26 21-47-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/8cd1e3e2-a945-4887-947c-707cfe73ba69)

- Implementation on Makerchip IDE is shown as below.
```bash
$reset = *reset;
   
   |calc
      @1
         $val1[31:0] = >>2$out[31:0];
         $val2[31:0] = $rand2[3:0];

         $sum[31:0] = $val1+$val2;
         $dif[31:0] = $val1-$val2;
         $mul[31:0] = $val1*$val2;
         $div[31:0] = $val1/$val2;
         $valid[1:0] = $reset ? 0 : >>1$valid + 1'b1;
         
      @2
         $out[31:0] = !($reset &&  !($valid))? 1 :($op[1] ? ($op[0] ? $div : $mul):($op[0] ? $dif : $sum));
```
- Implementation using Makerchip IDE
![Screenshot from 2023-08-22 23-09-28](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/f411f259-7a93-418b-a225-de9b968ad012)

</details>

<details>
<summary>Validity</summary>

Validity is another feature in TL verilog which is asserted if a particular transactions in a pipeline is valid or true. A new scope, called “when” scope is introduced for this and it is denoted as ?$valid. This new scope has many advantages - easier design, cleaner debug, better error checking and automated clock gating. Validity provides :

- Easier debug
- Cleaner design
- Better error checking
- Automated Clock gating

**Clock Gating**

- Clock signals are distributed to EVERY flip-flop.
- Clocks toggle twice per cycle. This consumes power.
- Clock gating avoids toggling clock signals.
- TL-Verilog can produce fine-grained gating (or enables).

Thus, in TLverilog, we don't have to look into clock gating individually. It gets considered 
and covered with the Validity concept.

*Distance Accumulator using Makerchip IDE*
- The pipelined block diagram for the accumulator

![Screenshot from 2023-08-26 22-06-01](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/24e6996b-9d1a-4102-bae4-b3c54021b27c)

- Code for the design on TLverilog
```bash
|calc
      
      @1
         $reset = *reset;    
      
      ?$valid
         @1
            $aa_sq[31:0] = $aa[3:0] ** 2;
            $bb_sq[31:0] = $bb[3:0] ** 2;
          @2
            $cc_sq[31:0] = $aa_sq + $bb_sq;
          @3
            $cc[31:0] = sqrt($cc_sq);
            
      @4
         $tot_dis[63:0] = 
                   $reset ? '0 :
                   $valid ? >>1$tot_dis + $cc :
                            >>1$tot_dis;
```

![Screenshot from 2023-08-23 02-08-56](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/d7c2c04a-f60e-4373-b779-5207dd74ec4c)

*2-Cycle Calculator with Validity*

Under this lab work we design a 2-cycle calculator along with the validity functionality.

- Pipelined Logic Design to be implemented

![Screenshot from 2023-08-27 01-56-59](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/8737bd4d-c9db-4aef-a675-d3bf370a435a)

Code on TLverilog
```bash
|calc
      
      @0
         $reset = *reset;
      @1
         $val1[31:0] = >>2$out[31:0];
         $val2[31:0] = $rand2[3:0];
         
         $valid = $reset ? 1'b0 : >>1$valid + 1'b1;
         $valid_or_reset = $valid || $reset;

      ?$valid_or_reset   
         @1


            $sum[31:0] = $val1+$val2;
            $dif[31:0] = $val1-$val2;
            $mul[31:0] = $val1*$val2;
            $div[31:0] = $val1/$val2;
            

         @2
            $out[31:0] = ($reset)? 1 :($op[1] ? ($op[0] ? $div : $mul):($op[0] ? $dif : $sum));
```
-Implementation on Makerchip IDE.
![Screenshot from 2023-08-23 02-40-04](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/323c5c26-d261-4386-ab8b-7aadfb1fc431)

*Calculator with Single-value Memory*

Under this lab work, we design a calculator with a memory component.

- Pipelined design to be implemented.

![Screenshot from 2023-08-27 02-03-38](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/a5ad008f-5b8b-4840-9257-20d589fdeb54)

- Code on TLverilog
```bash
|calc
      @0
         $reset = *reset;
         
      @1
         $val1 [31:0] = >>2$out[31:0];
         $val2 [31:0] = $rand1[3:0];
         
         $valid = $reset ? 1'b0 : >>1$valid + 1'b1 ;
         $valid_or_reset = $valid || $reset;
         
      ?$vaild_or_reset
         @1   
            $sum[31:0] = $val1 + $val2;
            $dif[31:0] = $val1 - $val2;
            $mul[31:0] = $val1 * $val2;
            $div[31:0] = $val1 / $val2;
            
         @2   
            $mem[31:0] = $reset ? 32'b0 :
                         ($op[2:0] == 3'b101) ? $val1 : >>2$mem ;
            
            $out [31:0] = $reset ? '1 :
                          ($op[2:0] == 3'b000) ? $sum :
                          ($op[2:0] == 3'b001) ? $dif :
                          ($op[2:0] == 3'b010) ? $mul :
                          ($op[2:0] == 3'b011) ? $div :
                          ($op[2:0] == 3'b100) ? >>2$mem : >>2$out ;
```
- Implementation on Makerchip IDE.
![Screenshot from 2023-08-27 02-05-46](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/7bea8535-b8e0-4f76-a46b-d1b88ff8d345)
</details>

<details>
<summary>Wrap up</summary>
</details>

## Day 4- Basic RISC-V CPU micro-architecture

<details>
<summary>Introduction to simple RISC-V micro-architecture</summary>
The block diagram of a basic RISC-V microarchitecture is as shown in figure below. Now, using the Makerchip platform the implementation of the RISC-V microarchitecture or core is done. For starting the implementation a starter code present in reference is used. The starter code consist of -

- A simple RISC-V assembler.
- An instruction memory containing the sum 1..9 test program.
- Commented code for register file and memory.
- Visualization.
![Screenshot from 2023-08-27 02-14-19](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/38f8f12d-5cd1-4a79-9d3c-f5e8420ab8b4)

It's important to note that RISC-V is an instruction set architecture, and microarchitectures 
based on RISC-V can vary widely depending on the design goals of the processor manufacturer. 
Different companies and research institutions may develop their own microarchitectures that 
implement the RISC-V ISA in unique ways, tailored to specific use cases and performance goals.

Here we are designing the basic processor of 3 stages fetch, decode and execute based on 
RISC-V ISA. For starting the implementation a starter code is present in the github repository 
provided.
```bash
 https://github.com/stevehoover/RISC-V_MYTH_Workshop
```
We will follow a test driven development, ie. develop first and then test functionality.

- Template for starting point code of RISC-V CPU.
```bash
\m4_TLV_version 1d: tl-x.org
\SV
   // This code can be found in: https://github.com/stevehoover/RISC-V_MYTH_Workshop
   
   m4_include_lib(['https://raw.githubusercontent.com/BalaDhinesh/RISC-V_MYTH_Workshop/master/tlv_lib/risc-v_shell_lib.tlv'])

\SV
   m4_makerchip_module   // (Expanded in Nav-TLV pane.)
\TLV

   // /====================\
   // | Sum 1 to 9 Program |
   // \====================/
   //
   // Program for MYTH Workshop to test RV32I
   // Add 1,2,3,...,9 (in that order).
   //
   // Regs:
   //  r10 (a0): In: 0, Out: final sum
   //  r12 (a2): 10
   //  r13 (a3): 1..10
   //  r14 (a4): Sum
   // 
   // External to function:
   m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
   // Function:
   m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
   m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
   m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
   // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program
   
   // Optional:
   // m4_asm(JAL, r7, 00000000000000000000) // Done. Jump to itself (infinite loop). (Up to 20-bit signed immediate plus implicit 0 bit (unlike JALR) provides byte address; last immediate bit should also be 0)
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)

   |cpu
      @0
         $reset = *reset;



      // YOUR CODE HERE
      // ...

      // Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
      //       be sure to avoid having unassigned signals (which you might be using for random inputs)
      //       other than those specifically expected in the labs. You'll get strange errors for these.

   
   // Assert these to end simulation (before Makerchip cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
   
   // Macro instantiations for:
   //  o instruction memory
   //  o register file
   //  o data memory
   //  o CPU visualization
   |cpu
      //m4+imem(@1)    // Args: (read stage)
      //m4+rf(@1, @1)  // Args: (read stage, write stage) - if equal, no register bypass is required
      //m4+dmem(@4)    // Args: (read/write stage)
      //m4+myth_fpga(@0)  // Uncomment to run on fpga

   //m4+cpu_viz(@4)    // For visualisation, argument should be at least equal to the last stage of CPU logic. @4 would work for all labs.
\SV
   endmodule
```

</details>

<details>
<summary>Fetch and decode</summary>

Here we gonna design RiscV Cpu Core for which block diagram is given below :
![Screenshot from 2023-08-27 03-10-48](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/3a142599-75f1-494c-9728-a43dbaac02fb)

**Program Counter Logic**

The Program Counter, often referred to as the "PC," is a fundamental component of a processor 
that keeps track of the address of the next instruction to be executed. In the RISC-V 
architecture, the PC is typically called "pc" or "pc_reg." Overall, the PC logic is crucial 
for the control flow of a program. It determines which instruction will be executed next and 
how the program progresses. RISC-V, as a RISC (Reduced Instruction Set Computer) architecture, 
emphasizes simplicity and regularity in its design, which extends to its PC handling 
mechanisms.

![Screenshot from 2023-08-27 03-15-02](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/0e38ef87-0613-46c4-b023-11dfe176787a)

```bash
|cpu
      @0
         $reset = *reset;
         
         $pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;
```
- program counter implementation on makerchipIDE
![Screenshot from 2023-08-27 03-30-13](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/871b396b-03a1-4b3f-98e4-a04bf91ba7e0)

**Fetch implementation Logic**
During the fetch stage, processors fetches the instruction from the memory to the address 
pointed by the program counter. The program counters holds the address of the next stage, in 
our case it is after 4 cycle and the instruction memory holds the set of instruction to be 
executed. The snapshot of the fetch stage is shown below.
         
- logic diagram for Fetch
![Screenshot from 2023-08-27 03-33-51](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6fd351cd-a681-48c9-8c26-b720ad06502e)

```bash
|cpu
      @0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;

// Assert these to end simulation (before Makerchip cycle limit).
*passed = *cyc_cnt > 40;
*failed = 1'b0;

|cpu
      m4+imem(@1)    // Args: (read stage)

m4+cpu_viz(@4)
```
- Implementation of Fetch Logic on Makerchip along with Diagram and Visualisation.
![Screenshot from 2023-08-27 03-35-59](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/28ac652b-4c95-4139-990e-13cd7958ca39)

The current implementations have errors such as the variables are not being used. Fetch Logic 
to be implemented

![Screenshot from 2023-08-27 03-37-54](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/490e6212-26c6-4205-ab1b-08a3095da51e)

- code for implementation of Fetch
```bash
@0
         $reset = *reset;
         $pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;
      @1
         $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
         $imem_rd_en = !$reset;
         $instr[31:0] = $imem_rd_data[31:0];
      
      ?$imem_rd_en
         @1
            $imem_rd_data[31:0] = /imem[$imem_rd_addr]$instr;          
```

- Final implementation of Fetch Logic

![Screenshot from 2023-08-27 03-48-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/c0e496de-bae6-4f3d-bb90-16307576cda3)

![Screenshot from 2023-08-27 03-56-06](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/81578a5d-ba09-46e8-94a8-33298b261f9e)

**Decode Logic**

Under this section, we look into how to decode the instruction code we fetched from memory.

- Logic Diagram for Decode stage.

![Screenshot from 2023-08-27 03-54-11](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/253b7b53-f04f-4bb2-8d34-c6ebd181689e)

Before moving on to Decode logic implementation, it is important to understand how the 
instruction set and opcode are defined in RISC-V. We have dicussed before the different types 
of the instruction types. The various types of instrutcion types are summarised in the table 
along with the binary code. There are 6 instructions type in RISC-V :

- Register (R) type
- Immediate (I) type
- Store (S) type
- Branch (B) type
- Upper immediate (U) type
- Jump (J) type

![Screenshot from 2023-08-27 04-01-21](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/bfb64c18-904d-47b0-afb8-a1c8dc13348f)




-code for decode logic
```bash
@1
         $is_i_instr = $instr[6:2] ==? 5'b0000x || 
                       $instr[6:2] ==? 5'b001x0 || 
                       $instr[6:2] == 5'b11001;
         $is_r_instr = $instr[6:2] ==? 5'b011x0 || 
                       $instr[6:2] == 5'b01011 || 
                       $instr[6:2] == 5'b10100;
         $is_s_instr = $instr[6:2] ==? 5'b0100x;
         $is_b_instr = $instr[6:2] ==? 5'b11000;
         $is_j_instr = $instr[6:2] ==? 5'b11011;
         $is_u_instr = $instr[6:2] ==? 5'b0x101;
```
- Implementation for fetch logic
![Screenshot from 2023-08-27 04-16-14](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/92c1a303-4a0d-405a-b409-3a4b2ee994e7)

![Screenshot from 2023-08-27 04-18-11](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/a58e4ca9-e7da-4907-a7b2-cd5a2fc94f7a)

*Lab on instruction immediate code*
![Screenshot from 2023-08-27 04-20-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6c8d10ae-0370-487a-9906-1c400932d6ab)

- Code or determining *immediate* for decode logic implementaion
```bash
		      $imm[31:0] = $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                      $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                      $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], 1'b0} :
                      $is_u_instr ? {$instr[31:12], 12'b0} :
                      $is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0} :
                                    32'b0;
```
- Implementaion of the immediate instruction set
![Screenshot from 2023-08-27 12-26-21](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/47d3164e-0b42-4973-89be-1df41a2b9d4c)
- VIZ
![Screenshot from 2023-08-27 12-27-15](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/db38cf29-4fea-4133-beee-d1a3ff66313d)

*Lab on instruction decode*
![Screenshot from 2023-08-27 12-28-27](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/c17f274f-74fa-45af-b4f0-842fc0bc8570)

- Code fo nstruction code implmentaion
```bash
	 $rs2[4:0] = $instr[24:20];
         $rs1[4:0] = $instr[19:15];
         $rd[4:0]  = $instr[11:7];
         $opcode[6:0] = $instr[6:0];
         $func7[6:0] = $instr[31:25];
         $func3[2:0] = $instr[14:12];
```

- Output
![Screenshot from 2023-08-27 12-30-28](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/e8294787-79e5-4ae9-8579-f2996c9c1dfc)
- VIZ
![Screenshot from 2023-08-27 12-33-05](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/ff34f021-6438-4b7e-b398-f91c2566cdb6)

*Lab on Decoding Instruction Field Set*
![Screenshot from 2023-08-27 12-42-12](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/bf1d779f-11a3-4f62-a076-ff38b434d9cb)

-code for instruction based field set
```bash
	$rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20];
            
         $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15];
         
         $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
            
         $funct7_valid = $is_r_instr ;
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];
            
         $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         ?$rd_valid
            $rd[4:0] = $instr[11:7];
```     
- Output
![Screenshot from 2023-08-27 12-45-20](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/e5dce8c8-5908-4ba8-8e2d-a6037abb57ed)
- VIZ
![Screenshot from 2023-08-27 12-46-28](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4944ec3f-59d2-4b9d-9f98-65e7fb7e86d5)

*Lab on individual instruction decode*

- code
```bash
	 $dec_bits [10:0] = {$funct7[5], $funct3, $opcode};
         $is_beq = $dec_bits ==? 11'bx_000_1100011;
         $is_bne = $dec_bits ==? 11'bx_001_1100011;
         $is_blt = $dec_bits ==? 11'bx_100_1100011;
         $is_bge = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
         $is_addi = $dec_bits ==? 11'bx_000_0010011;
         $is_add = $dec_bits ==? 11'b0_000_0110011;
```
![Screenshot from 2023-08-27 13-00-19](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/7cbae990-ae41-433c-85a6-b469aba03946)

- Implementaion of individual instruction decode
![Screenshot from 2023-08-27 13-01-46](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/50f23fbf-f79f-441b-92c0-dc65764ae77e)
- VIZ
![Screenshot from 2023-08-27 13-02-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/2a58ceda-1f52-4817-8f3f-ec8533ee41d8)

</details>

<details>
<summary>RISC-V control logic</summary>
	
Under this section, we will look into the implementation of RISC-V CPU from register file read 
onwards.	
**Execute and Register file read/write**

- Pipelined Logic diagram for implementation.
![Screenshot from 2023-08-27 14-02-47](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/dd050004-605a-4bcf-a471-dafdae24fab3)

- Structure of the register design for implementation. Two read operations and one write operation to be performed.
![Screenshot from 2023-08-27 14-04-09](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/bffed054-73d1-4f63-bfcb-44770b63ce70)

- Code for implementaion
```bash
	 $rf_wr_en = 1'b0;
         $rf_wr_index[4:0] = 5'b0;
         $rf_wr_data[31:0] = 32'b0;
         $rf_rd_en1 = $rs1_valid;
         $rf_rd_en2 = $rs2_valid;
         
         $rf_rd_index1[4:0] = $rs1;
         $rf_rd_index2[4:0] = $rs2;
```
- Now, we have read the register files, we will connect up the values we have read to the signals we will send to the ALU.
- Code for connection.
```bash
	 $src1_value[31:0] = $rf_rd_data1;
         $src2_value[31:0] = $rf_rd_data2;
```

*ALU Imlimentaion*
We move to the next stage of implementation, ie. ALU. The logic diagram.

![Screenshot from 2023-08-27 14-07-54](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/8a8e7fdb-c2ee-47c9-b613-f85b762eace2)

- code to implement the arithmatic and logic functionalities of the ALU.
```bash
	$result[31:0] = $is_addi ? $src1_value + $imm :
			$is_add ? $src1_value + $src2_value :
			32'bx ;
```

- Implementation upto ALU on Makerchip IDE
![Screenshot from 2023-08-27 14-10-04](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/02654228-d6bf-49ff-8750-4ad6ff33810f)

*Register File Write Implementation*

Under this we go over the implementation of Write funcction after the ALU has performed.

- Logic Diagram
![Screenshot from 2023-08-27 14-11-35](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4200d704-3398-4377-9e78-2b7ca0802338)

- Code for writing register file.
```bash
	$rf_wr_en = $rd_valid && $rd != 5'b0;
	$rf_wr_index[4:0] = $rd;
	$rf_wr_data[31:0] = $result;
```
- Implementaion of the register file logic
![Screenshot from 2023-08-27 14-22-34](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/d7909254-49e0-4a29-8484-5afb95cc20b8)

**Note** : We will look into arrays under RISC-V.

- Arrays are collection of data of same datatypes.
- RISC-V processors support arrays through their memory access instructions and addressing modes. In the RISC-V architecture, arrays are commonly managed using a combination of memory locations and registers.
- Arrays are represented as contiguous blocks of memory in RISC-V.
- Pointers are used to track the memory address where the array starts.
- Arrays are accessed using pointers and offsets calculated from the index and element size.
- Load and store instructions are used to manipulate array elements.
- Pointer arithmetic involves adding offsets to pointers for accessing different elements.
- Pointers are initialized with the memory address of the array's first element.
- Array operations are performed through load-store instructions and pointer manipulation.

![Screenshot from 2023-08-27 14-25-01](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/a8ec4da7-5c92-4cba-81c9-3f36a54fcc9c)

*Branch Instruction Imlementaion*
We will look into the implementations for the various branch instructions, we have decoded earlier.

- Logic Diagram
![Screenshot from 2023-08-27 14-34-59](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/30a2f80a-111e-479a-9c84-b9a19c3f2b9e)

- All instr with B initials are branch statements. Logic for the branches are as follow
![Screenshot from 2023-08-27 14-38-09](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/9812d296-5cc9-411f-85c4-8b83e9ef37ab)

- Code for implementing when to take a branch.
```bash
$taken_branch = $is_beq ? ($src1_value == $src2_value):
		$is_bne ? ($src1_value != $src2_value):
		$is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])):
		$is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])):
		$is_bltu ? ($src1_value < $src2_value):
		$is_bgeu ? ($src1_value >= $src2_value):
		1'b0;
```

- Now, we will look into how to figure out where to branch to.
- Code to determine the path to Branch to
```bash
$br_target_pc[31:0] = $pc + $imm;
```

- implemenataion of branch instructions
![Screenshot from 2023-08-27 14-40-48](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/316a3825-837f-47fd-836b-b84a82f7d87c)

*Creating a test bench*
- code
```bash
*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9) ;
```

![Screenshot from 2023-08-27 14-42-36](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/3ebd4ec2-62f8-496b-9dd1-5a2affb2d370)

</details>

## Day 5 - Complete Pipelined RISC-V CPU Micro-Architecture

<details>
<summary>Pipelining the CPU</summary>
Pipelining of the CPU core is going to be implemented, streamlining the process of retiming and considerably reducing the occurrence of 
functional errors. This technique enables faster computational tasks. As previously explained, establishing the pipeline is a 
straightforward process of incorporating stages labelled as @1, @2, and so on. A visual representation of the pipelining setup is provided 
below. In TL Verilog, it's important to note that there is no strict requirement to define the pipeline stages in a specific systematic 
order, providing an extra layer of benefit.

*LAB on Cycle valid signal*

We are required to get implement the logic in the following block diagram:
![Screenshot from 2023-08-27 15-41-10](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/8e74d03c-07c5-426a-953b-1791c78f5563)

- the following code is used for implementaion
```bash

$valid = $reset ? 1'b0 : ($start) ? 1'b1 : (>>3$valid) ;
         $start_int = $reset ? 1'b0 : 1'b1;
         $start = $reset ? 1'b0 : ($start_int && !>>1$start_int);
```
The following output is obtained on MakerChip.
![Screenshot from 2023-08-27 15-50-02](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/a3fd6b6e-d524-4a88-a8bf-cce7dbe0a56c)

*LAB to take care of invalid cycles*

![Screenshot from 2023-08-27 15-52-26](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4fe735c8-ebcd-4f36-b6d2-cfdab206e5a2)

- The code snippet required to implement is.
```bash
 $pc[31:0] = (>>1$reset) ? 32'b0 : (>>3$valid_taken_branch) ? (>>3$br_tgt_pc) :  (>>3$int_pc)  ;
 $valid_taken_branch = $valid && $taken_br;
```
![Screenshot from 2023-08-27 15-59-51](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/6cd66100-a2cc-42e7-8e7f-b86f6de9ae3a)

*LAB to distribute logic*

Pipelining is done in this step. Code is distributed and output is obtained.
![Screenshot from 2023-08-27 16-01-42](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/3ebabe6d-2cae-4ef5-9168-a815d1ca9f72)

- below is the gievn output on makerchip

![Screenshot from 2023-08-27 16-03-34](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/59b51783-7778-4333-8080-42f37fc75197)

</details>

<details>
<summary>Solutions to pipeline hazzards</summary>

**Lab for register file Bypass to address rd-after-wr hazard**

We are required to implement the logic as per the given figure.

![Screenshot from 2023-08-27 16-56-28](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/4371f568-2336-4e19-894c-23748e4fd4a6)

The logic code required is given below.
```bash
$src1_value[31:0] = ((>>1$rf_wr_en) && (>>1$rd == $rs1 )) ? (>>1$result): $rf_rd_data1; 
$src2_value[31:0] = ((>>1$rf_wr_en) && (>>1$rd == $rs2 )) ? (>>1$result) : $rf_rd_data2;
```
- there would be no noticible changes
![Screenshot from 2023-08-27 16-58-45](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/348e2e5f-97b3-4030-80c6-358e0e9df5c4)

*Lab for branches to correct the branch target path*

The logic snippet required is given below.
```bash
 $pc[31:0] = (>>1$reset) ? 32'b0 : (>>3$valid_taken_br) ? (>>3$br_tgt_pc) :  (>>3$int_pc)  ;
         //$valid = $reset ? 1'b0 : ($start) ? 1'b1 : (>>3$valid) ; no need for this
```

![Screenshot from 2023-08-27 17-01-34](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/73ebe28c-d743-4840-ba62-465a0fc29809)


**Lab for complete RV32I instruction set (except FENCE, ECALL, EBREAK)**

Added some more instructions to the existing ones and removed bogus codes and added some real values.

![Screenshot from 2023-08-27 17-03-00](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/b763829f-78db-4f63-a85e-46441b07d22b)

</details>

<details>
<summary>Load and store data memory & Wrap up</summary>

Similar to branch,load will also have 3 cycle delay. So, added a Data Memory 1 write/read 
memory. Added test case to check the functionality of load/store.

```bash
	uncomment enable m4+dmem(@4)    // Args: (read/write stage)
 	connect interface signals using address bits[5:2] to perform load and store (when valid)
	Additionally Incorporation of Jump feature (JAL and JALR instructions).
```

Below is the output Makerchip after including load/store instructions:

![Screenshot from 2023-08-27 17-09-20](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/cc0b6389-583c-4c2a-9b7a-463e9d307bb4)

Finally the jump instruction is added and the decode instuction and the ALU for the entire RV32I base integer set is completed. 
The final code:
```bash
\m4_TLV_version 1d: tl-x.org
\SV
   // Template code can be found in: https://github.com/stevehoover/RISC-V_MYTH_Workshop
   
   m4_include_lib(['https://raw.githubusercontent.com/BalaDhinesh/RISC-V_MYTH_Workshop/master/tlv_lib/risc-v_shell_lib.tlv'])

\SV
   m4_makerchip_module   // (Expanded in Nav-TLV pane.)
\TLV

   // /====================\
   // | Sum 1 to 9 Program |
   // \====================/
   //
   // Add 1,2,3,...,9 (in that order).
   //
   // Regs:
   //  r10 (a0): In: 0, Out: final sum
   //  r12 (a2): 10
   //  r13 (a3): 1..10
   //  r14 (a4): Sum
   // 
   // External to function:
   m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
   // Function:
   m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
   m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
   m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
   // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program
   m4_asm(SW, r0, r10, 10000)           // Store r10 result in dmem
   m4_asm(LW, r17, r0, 10000)           // Load contents of dmem to r17
   m4_asm(JAL, r7, 00000000000000000000) // Done. Jump to itself (infinite loop). (Up to 20-bit signed immediate plus implicit 0 bit (unlike JALR) provides byte address; last immediate bit should also be 0)
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)

   |cpu
      @0
         $reset = *reset;
         
         //PC fetch - branch, jumps and loads introduce 2 cycle bubbles in this pipeline
         $pc[31:0] = >>1$reset ? '0 : (>>3$valid_taken_br ? >>3$br_tgt_pc :
                                       >>3$valid_load     ? >>3$inc_pc[31:0] :
                                       >>3$jal_valid      ? >>3$br_tgt_pc :
                                       >>3$jalr_valid     ? >>3$jalr_tgt_pc :
                                                     (>>1$inc_pc[31:0]));
         // Access instruction memory using PC
         $imem_rd_en = ~ $reset;
         $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
         
         
      @1
         //Getting instruction from IMem
         $instr[31:0] = $imem_rd_data[31:0];
         
         //Increment PC
         $inc_pc[31:0] = $pc[31:0] + 32'h4;
         
         //Decoding I,R,S,U,B,J type of instructions based on opcode [6:0]
         //Only [6:2] is used here because this implementation is for RV64I which does not use [1:0]
         $is_i_instr = $instr[6:2] ==? 5'b0000x ||
                       $instr[6:2] ==? 5'b001x0 ||
                       $instr[6:2] == 5'b11001;
         
         $is_r_instr = $instr[6:2] == 5'b01011 ||
                       $instr[6:2] ==? 5'b011x0 ||
                       $instr[6:2] == 5'b10100;
         
         $is_s_instr = $instr[6:2] ==? 5'b0100x;
         
         $is_u_instr = $instr[6:2] ==? 5'b0x101;
         
         $is_b_instr = $instr[6:2] == 5'b11000;
         
         $is_j_instr = $instr[6:2] == 5'b11011;
         
         //Immediate value decode
         $imm[31:0] = $is_i_instr ? { {21{$instr[31]}} , $instr[30:20]} :
                      $is_s_instr ? { {21{$instr[31]}} , $instr[30:25] , $instr[11:8] , $instr[7]} :
                      $is_b_instr ? { {20{$instr[31]}} , $instr[7] , $instr[30:25] , $instr[11:8] , 1'b0} :
                      $is_u_instr ? { $instr[31] , $instr[30:12] , { 12{1'b0}} } :
                      $is_j_instr ? { {12{$instr[31]}} , $instr[19:12] , $instr[20] , $instr[30:21] , 1'b0} :
                      >>1$imm[31:0];
         
         //Generate valid signals for each instruction fields
         $rs1_or_funct3_valid    = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         $rs2_valid              = $is_r_instr || $is_s_instr || $is_b_instr;
         $rd_valid               = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         $funct7_valid           = $is_r_instr;
         
         //Decode other fields of instruction - source and destination registers, funct, opcode
         ?$rs1_or_funct3_valid
            $rs1[4:0]    = $instr[19:15];
            $funct3[2:0] = $instr[14:12];
         
         ?$rs2_valid
            $rs2[4:0]    = $instr[24:20];
         
         ?$rd_valid
            $rd[4:0]     = $instr[11:7];
         
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];
         
         $opcode[6:0] = $instr[6:0];
         
         //Decode instruction in subset of base instruction set based on RISC-V 32I
         $dec_bits[10:0] = {$funct7[5],$funct3,$opcode};
         
         //Branch instructions
         $is_beq   = $dec_bits ==? 11'bx_000_1100011;
         $is_bne   = $dec_bits ==? 11'bx_001_1100011;
         $is_blt   = $dec_bits ==? 11'bx_100_1100011;
         $is_bge   = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu  = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu  = $dec_bits ==? 11'bx_111_1100011;
         
         //Jump instructions
         $is_auipc = $dec_bits ==? 11'bx_xxx_0010111;
         $is_jal   = $dec_bits ==? 11'bx_xxx_1101111;
         $is_jalr  = $dec_bits ==? 11'bx_000_1100111;
         
         //Arithmetic instructions
         $is_addi  = $dec_bits ==? 11'bx_000_0010011;
         $is_add   = $dec_bits ==  11'b0_000_0110011;
         $is_lui   = $dec_bits ==? 11'bx_xxx_0110111;
         $is_slti  = $dec_bits ==? 11'bx_010_0010011;
         $is_sltiu = $dec_bits ==? 11'bx_011_0010011;
         $is_xori  = $dec_bits ==? 11'bx_100_0010011;
         $is_ori   = $dec_bits ==? 11'bx_110_0010011;
         $is_andi  = $dec_bits ==? 11'bx_111_0010011;
         $is_slli  = $dec_bits ==? 11'b0_001_0010011;
         $is_srli  = $dec_bits ==? 11'b0_101_0010011;
         $is_srai  = $dec_bits ==? 11'b1_101_0010011;
         $is_sub   = $dec_bits ==? 11'b1_000_0110011;
         $is_sll   = $dec_bits ==? 11'b0_001_0110011;
         $is_slt   = $dec_bits ==? 11'b0_010_0110011;
         $is_sltu  = $dec_bits ==? 11'b0_011_0110011;
         $is_xor   = $dec_bits ==? 11'b0_100_0110011;
         $is_srl   = $dec_bits ==? 11'b0_101_0110011;
         $is_sra   = $dec_bits ==? 11'b1_101_0110011;
         $is_or    = $dec_bits ==? 11'b0_110_0110011;
         $is_and   = $dec_bits ==? 11'b0_111_0110011;
         
         //Store instructions
         $is_sb    = $dec_bits ==? 11'bx_000_0100011;
         $is_sh    = $dec_bits ==? 11'bx_001_0100011;
         $is_sw    = $dec_bits ==? 11'bx_010_0100011;
         
         //Load instructions - support only 4 byte load
         $is_load  = $dec_bits ==? 11'bx_xxx_0000011;
         
         $is_jump = $is_jal || $is_jalr;
         
      @2
         //Get Source register values from reg file
         $rf_rd_en1 = $rs1_or_funct3_valid;
         $rf_rd_en2 = $rs2_valid;
         
         $rf_rd_index1[4:0] = $rs1[4:0];
         $rf_rd_index2[4:0] = $rs2[4:0];
         
         //Register file bypass logic - data forwarding from ALU to resolve RAW dependence
         $src1_value[31:0] = $rs1_bypass ? >>1$result[31:0] : $rf_rd_data1[31:0];
         $src2_value[31:0] = $rs2_bypass ? >>1$result[31:0] : $rf_rd_data2[31:0];
         
         //Branch target PC computation for branches and JAL
         $br_tgt_pc[31:0] = $imm[31:0] + $pc[31:0];
         
         //RAW dependence check for ALU data forwarding
         //If previous instruction was writing to reg file, and current instruction is reading from same register
         $rs1_bypass = >>1$rf_wr_en && (>>1$rd == $rs1);
         $rs2_bypass = >>1$rf_wr_en && (>>1$rd == $rs2);
         
      @3
         //ALU
         $result[31:0] = $is_addi  ? $src1_value +  $imm :
                         $is_add   ? $src1_value +  $src2_value :
                         $is_andi  ? $src1_value &  $imm :
                         $is_ori   ? $src1_value |  $imm :
                         $is_xori  ? $src1_value ^  $imm :
                         $is_slli  ? $src1_value << $imm[5:0]:
                         $is_srli  ? $src1_value >> $imm[5:0]:
                         $is_and   ? $src1_value &  $src2_value:
                         $is_or    ? $src1_value |  $src2_value:
                         $is_xor   ? $src1_value ^  $src2_value:
                         $is_sub   ? $src1_value -  $src2_value:
                         $is_sll   ? $src1_value << $src2_value:
                         $is_srl   ? $src1_value >> $src2_value:
                         $is_sltu  ? $sltu_rslt[31:0]:
                         $is_sltiu ? $sltiu_rslt[31:0]:
                         $is_lui   ? {$imm[31:12], 12'b0}:
                         $is_auipc ? $pc + $imm:
                         $is_jal   ? $pc + 4:
                         $is_jalr  ? $pc + 4:
                         $is_srai  ? ({ {32{$src1_value[31]}} , $src1_value} >> $imm[4:0]) :
                         $is_slt   ? (($src1_value[31] == $src2_value[31]) ? $sltu_rslt : {31'b0, $src1_value[31]}):
                         $is_slti  ? (($src1_value[31] == $imm[31]) ? $sltiu_rslt : {31'b0, $src1_value[31]}) :
                         $is_sra   ? ({ {32{$src1_value[31]}}, $src1_value} >> $src2_value[4:0]) :
                         $is_load  ? $src1_value +  $imm :
                         $is_s_instr ? $src1_value + $imm :
                                    32'bx;
         
         $sltu_rslt[31:0]  = $src1_value <  $src2_value;
         $sltiu_rslt[31:0] = $src1_value <  $imm;
         
         //Jump instruction target PC computation
         $jalr_tgt_pc[31:0] = $imm[31:0] + $src1_value[31:0]; 
         
         //Branch resolution
         $taken_br = $is_beq ? ($src1_value == $src2_value) :
                     $is_bne ? ($src1_value != $src2_value) :
                     $is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])) :
                     $is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])) :
                     $is_bltu ? ($src1_value < $src2_value) :
                     $is_bgeu ? ($src1_value >= $src2_value) :
                     1'b0;
         
         //Current instruction is valid if one of the previous 2 instructions were not (taken_branch or load or jump)
         $valid = ~(>>1$valid_taken_br || >>2$valid_taken_br || >>1$is_load || >>2$is_load || >>2$jump_valid || >>1$jump_valid);
         
         //Current instruction is valid & is a taken branch
         $valid_taken_br = $valid && $taken_br;
         
         //Current instruction is valid & is a load
         $valid_load = $valid && $is_load;
         
         //Current instruction is valid & is jump
         $jump_valid = $valid && $is_jump;
         $jal_valid  = $valid && $is_jal;
         $jalr_valid = $valid && $is_jalr;
         
         //Destination register update - ALU result or load result depending on instruction
         $rf_wr_en = (($rd != '0) && $rd_valid && $valid) || >>2$valid_load;
         $rf_wr_index[4:0] = $valid ? $rd[4:0] : >>2$rd[4:0];
         $rf_wr_data[31:0] = $valid ? $result[31:0] : >>2$ld_data[31:0];
         
      @4
         //Data memory access for load, store
         $dmem_addr[3:0]     =  $result[5:2];
         $dmem_wr_en         =  $valid && $is_s_instr;
         $dmem_wr_data[31:0] =  $src2_value[31:0];
         $dmem_rd_en         =  $valid_load;
         
      
         //Write back data read from load instruction to register
         $ld_data[31:0]      =  $dmem_rd_data[31:0];
         
      
      

      // Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
      //       be sure to avoid having unassigned signals (which you might be using for random inputs)
      //       other than those specifically expected in the labs. You'll get strange errors for these.

   
   // Assert these to end simulation (before Makerchip cycle limit).
   //Checks if sum of numbers from 1 to 9 is obtained in reg[17] and runs 10 cycles extra after this is met
   *passed = |cpu/xreg[17]>>10$value == (1+2+3+4+5+6+7+8+9);
   //Run for 200 cycles without any checks
   //*passed = *cyc_cnt > 200;
   *failed = 1'b0;
   
   // Macro instantiations for:
   //  o instruction memory
   //  o register file
   //  o data memory
   //  o CPU visualization
   |cpu
      m4+imem(@1)    // Args: (read stage)
      m4+rf(@2, @3)  // Args: (read stage, write stage) - if equal, no register bypass is required
      m4+dmem(@4)    // Args: (read/write stage)
   
   m4+cpu_viz(@4)    // For visualisation, argument should be at least equal to the last stage of CPU logic
                       // @4 would work for all labs
\SV
   endmodule
```
- The Final Diagram is shown below

![Screenshot from 2023-08-27 17-10-09](https://github.com/simarthethi/iiitb-RISCV_ISA/assets/140998783/895f1120-f666-422d-aaa7-27eb3ec3bc9b)

</detalis>











