# MTRX2700-CLAB G4


###################### Acknowledgement ############################# 
 
Some parts of the code were developed based on the 2022 Tuesday 
MTRX2700 weekly lecture example code demonstration. 

##########################  Group member: ########################## 
Name:		 SID:

Hugh Henry       490410688

Teddy Yixun Yan	 490438626 

Peter Le	 TODO

##########################  Detail about the project #########################

This project primarily utilises the serial ports on the 68HCS12 dragonboard to
perform actions, including taking in messages from the serial ports and storing them (done using interrupts), and parsing the input messages into a series of 
possible actions.

The message which is received requires a specific syntax (defined below in instructions
for the user); this tells the dragonboard what actions and specific parameters for those actions.
There are also outputs of two types of errors: firstly, error in syntax (incorrect message 
sent, based on definitions) and secondly, errors of buffer overflow (where too many characters
are typed such that they cannot be stored). 

Two actions modules have been implemented.
The first module is the LED module: this module turns on between 1 and 3 LEDs, as well 
as determining (based on the input messages) whether to make them blink or not. In addition, there
is a third parameter for how many seconds the lights stay on (or blink) for.
 
Additionally, the most extensive module in the program is the music module, whereby a tune 
is played. This module can play a combination of 5 notes to form a tune (from 7 different
notes - ABCDEF) and between 1-5 seconds for each note to sustain. This information
is based off the message the user inputs.

##########################  High Level Information About The Code #########################

The code was broken down into three main tasks, corresponding to the exercises, each with 
multiple functions.

INTERUPTS AND SERIAL PORT
The first task is utilising interrupts via the serials port to read and write data. 
This uses the init_serial function to set all the required bits in the control register
along with baud rate. Then interrupts are enabled and the use of isr_vectors to trigger 
serial port interrupts in the banked model. When the interrupt is triggered, program enters
the function called serial_isr (serial interrupt service routine). This uses serial_read and
serial_write to read and write. These two functions work based off similar principles, these are
the following.

SERIAL READ/WRITE
This function takes in a pointer to a character buffer of length 64 and a serial port, along with external pointers to  
where to store the data. Each time the interrupt is triggered, the “stage” flag of receiving or transmitting
is checked. Then the data is stored or transmitted, and the external pointer is incremented, until 
it reaches the ascii value for the enter key, in which case it reverses the transmission/receive flag
to know it is at the end of the input. This is the basic functionality as to the use of the serial port.


/*
WRITING PREFILLED BUFFER TO SERIAL PORT
Although not requested by the marking criteria, a function that takes a pre-filled buffer of 8 bit values
and send them to a serial port is required by the lab instructions; therefore, this function is also 
written and able to be called, although not being fully integrated into the final program.
This function takes in a prefilled buffer of string "Welcome!\n", and outputs it to the serial port, 
making it able to be displayed on the terminal. However calling this function will leave the prefilled
buffer content in the serial port, causing a disruption of the following tasks which detects input messages
from the serial port; therefore this function is currently commented out, but can be called independently
when required.
*/

GENERATION OF OUTPUT (MUSIC and LED MODULE)
The second task is processing those messages. This is done using the message_generator function,
this checks for errors in the messages along with the syntax to perform those tasks.

The music and led module take in the inputs and process based on a series of parameters. They
utilise the timer interrupts to play a tune or to turn/blink a multitude of LEDs for a duration
of time.

NOTE for Music module:
In order to stop the input of more messages while the tune plays, the flag “inPlay_tune”
is checked, and an error message is provided if input is given while the tune is playing.


##########################  Instructions For The User #########################

The user will need to use a set of specific syntax in order to trigger actions of different modes.
There are two main modes that can be triggered; these modes are given by a hyphen followed by either M (music) or L
L (LED); for the LED mode, another letter needs to be given to indicate whether the LED should be blinking or non-blinking. For example:

-M or -L or -S

The next arguments are parameters for the different modules explained below

LED MODE
In LED Mode the general syntax is the following
-L <blinking> <number of LEDs On> <Duration to leave on>

There are a few things to note about these parameters:
- <blinking> is either K for blinking of O for on
- <number of LEDs on> is a number between 
- <duration to leave on> is 5,6 or 7 seconds

MUSIC MODE
In music mode the general syntax is the following
-<module> <number of notes> <note1> <length of note1 in seconds> <note2> ...<note5> <length of note5 in seconds> 

There are a few things to note about these parameters:
- <number of notes> is a number between 1 and 5 (max 5 notes)
- <noteN> has a possibility to be A, B, C, D, E or F
- <length of note1 in seconds> is a number between 1 and 5 (max 5 seconds)


##########################  Details About Testing Procedures #########################

Exercise 1 (demonstrate reading and writing via interrupts):
- Click run
- And pause, see that the main function is just looping infinitely (showing it is an interrupt)
- Type hello (or random string) into serial port (less than 64 characters)
- See the syntax error message along with your message printed out
- Repeat in simulation and change to serial port 0 to show serial port 0 working the same

Discussion questions
1. Store data in a char buffer, change the buffer size to extend the length
2. Interrupt either reads or writes a character
	- Looked at assembly code, depends on if message/action is generated, or end character
	- Approx. 180 instructions
	- Multiply by 3, each instruction takes approx. 3 clock cycles
	- 180*3*41.6*10^(-9) = 22.5 us
	
Which gives 23 micro seconds (approx..)

Exercise 2:
- Click run
- type a defined message in the user instructions (i.e., LED)
- See message of processing 
- See action 
- Repeat to show different action
- Repeat and type 64 characters
- See buffer overflow message given

Discussion questions
- Extend it by adding new function for new modules and change the input filtering to 
accept those new messages
- parser would fail if syntax is wrong, and buffer overflow

Exercise 3:
- Click run
- type a message for Music Mode with multiple notes and length
- Listen to tune play
- Try to type more notes while music plays to show error 
- When it is done type a different tune to show no error

Discussion questions
1. Flag “inPlay_tune” is used to stop and give error message
2. Syntax error message given
