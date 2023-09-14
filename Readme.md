# EvoClock
## Project Overview
In the "Interface Technology" course, I learned about the section involving the use of the 8253 programmable timing counter to create an electronic clock. Currently, the design of microcomputer-based electronic clocks on the network has significant limitations and cannot meet user needs. Therefore, I aim to enhance the functionality of the electronic clock by incorporating the knowledge acquired this semester, specifically by using the 8259 interrupt controller.

The 8259 has fixed interrupt priorities ranging from ir0 to ir7, from high to low, with the master chip's priority being adjustable relative to the slave chip. The operation control word (ocw1) can be configured to mask interrupts from ir0 to ir7 on an 8259 chip. My idea is to use the interrupt service routine of the master chip to set the interrupt mask word (ocw1) of the slave chip. This approach ensures that, during counting, the slave chip only accepts interrupts from the 8253 to update timing. Additionally, timing adjustments can be made while counting is paused. Furthermore, I employ two 6264 chips to simulate parity memory, which stores hours, minutes, and seconds of immediate timing. The corresponding CPU memory address range is f0000h-f3fffh.
## Project Functions
The system accomplishes the following functions:

- Implements cyclic timing from 00:00:00 to 23:59:59, automatically resetting to 00:00:00 when the next second begins.
- Enables timing pauses.
- During a pause, allows modification of the set number of minutes and seconds.
- Supports automatic increment modification (carry) of minutes and seconds when selecting during a pause.
- Enables automatic decrement modification (borrow) of minutes and seconds when selecting during a pause.
- Provides options for speeding up timing.
- Offers options for slowing down timing.
- Allows for the resumption of timing at the initial speed.

The above functions are interacted with through buttons on the interface.

- The console end is equipped with 9 buttons, 1 switch, a two-bit binary input, and an eight-bit binary input.
- The switch controls the overall counting state and must be turned off before starting. When off, counting is forcibly stopped. Each button's function is detailed in the circuit diagram.
- Two binary inputs are used to control the selection of operation bits. "00" controls the second bit, "01" controls the minute bit, "10" controls the hour bit, and "11" is an invalid control. By default, the second bit is not set in "00", "01", and "10" in the software.
- The eight-digit binary input is used for setting numeric values. To facilitate display, user-entered numbers are processed according to the decimal system. For non-decimal numbers, decimal adjustment is performed in the software. For decimal numbers greater than 59, they are forcibly converted to 59H in the software.

The above functions are interactive through the keys in the interface.
> 1. The console end is equipped with 9 buttons, 1 switch, a two bit binary input and an eight bit binary input.
> 2. The switch controls the total counting state. It needs to be off before starting. If it is off, the counting will be forcibly stopped. The functions of each button have been marked in detail in the circuit diagram.
> 3. Two binary inputs are used to control the selection of operation bits. 00 controls the second bit, 01 controls the minute bit, 10 controls the time bit, and 11 is invalid control. Here, the control second bit is not set in 00,01,10 by default in the software.
> 4. Eight digit binary is used for setting number input. In order to facilitate display, the number entered by the user is processed according to decimal system. For non decimal number, decimal adjustment is carried out in the software. For decimal number greater than 59, it is forcibly converted to 59H in the software.

## Software Design
The program defines a data segment in which two bytes are stored. The "Flag" is the flag bit used to display the current counting status. "00h" indicates that counting is currently in progress, "01h" indicates that counting is paused, and the default status in the program is paused counting, i.e., "01h". "CLOCK_Speed" is used to store the initial counting value of the current 8253 (which can also be understood as counting speed). The default value in the program is "0ah." Its value is modified during speed increase, speed decrease, and recovery. The allowable range of modification is "05H" to "0Fh."

This project employs two 8255 chips, two 8259 chips, and one 8253 chip.

- The address of "8255_1" is "8000h", "8002h", "8004h", "8006h", while the address of "8255_2" is "9000h", "9002h", "9004h", "9006h".
- "8259_1" has the address "a000h", "a002h", and "8259_2" has the address "b000h", "b002h".
- The addresses of the "8253" are "c000h", "c002h", "c004h", and "c006h".
- The hours, minutes, and seconds data are stored in 6264, with two bytes allocated for each (to avoid accessing parity memory errors). The first storage address is "8000h:0100h", the second storage address is "8000h:0200h", and the hour storage address is "8000h:0300h".


