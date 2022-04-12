# MTRX2700-CLAB G4

###################### Acknowledgement ############################# 
 
Some parts of the code were developed based on the 2022 Tuesday 
MTRX2700 weekly lecture example code demonstration. 

##########################  Group member: ########################## 
Name:		 SID:

Hugh Henry       490410688
Teddy Zuo	 TODO 
Peter Le	 TODO

##########################  Detail about the project #########################

This project primarily utilises the serial ports on the 68HCS12 dragonboard to
perform actions. Taking in messages from the serial ports and storing them, this is
done using interrupts. Then based on those messages are parsed into a series of 
possible actions.

The message which is recieved requires a specific syntax (defined below in instructions
for the user), this tells the dragonboard what actions and specific parameters for those actions.
There is also outputs of two types of errors, firstly error in syntax (incorrect message 
sent, based on definitions) and secondly, errors of buffer overflow (where too many characters
are typed such that they cannot be stored). There are three action modules.

The first module is the LED module
TODO

The second module is the Seven Segment module
TODO

Finally, the most extensive moudle in the program is the music module, whereby a tune 
is played, this module can play a combination of 5 notes to form a tune (from 7 different
notes options, this being ABCDEF) and for between 1-5 seconds each. This information
is based off the message the user inputs.

##########################  High Level Information About The Code #########################

The code was broken down into three main tasks, correspoing to the excerises, each with 
multiple functions.

INTERUPTS AND SERIAL PORT
The first task is utilising interrupts via the serials port to read and write data. 
This uses the init_serial function to set all the required bits in the control register
along with baud rate. Then interrupts are enabled and the use of isr_vectors to trigger 
serial port interrutps in the banked model. When the interrupt is triggered, program enteres
the function called serial_isr (serial interrupt service routine). This uses serial_read and
serial_write to read and write. These two functions work based off similar principels, these are
the following.

SERIAL READ/WRITE
Take in a pointer to a character buffer of length 64, a serial port along with external pointers to  
where to store the data. Each time the interrupt is triggered, the flag of reciving or transmitting
is checked. Then the data is stored or transmitted and the external pointer is incremented, untill 
it reaches the ascii value for the enter key. In which case it reverses the transmission/recieve flag
to know it is at the end of the input. This is the basic functionaly as to the use of the serial port.

GENERATION OF OUTPUT
The second task is processing those messages. This is done using the message_generator function,
this checks for errors in the messages along with the syntax to peform those tasks.

TODO ### MUSIC LED AND SEVEN SEG MODULE HIGH LEVEL DESCRIPTION


In order to stop the input of more messages while the tune plays the use of a flage called inPlay_tune,
is checked and an error message is provided if input is given while the tune is playing.


##########################  Instructions For The User #########################

The user needs to understand the syntax which is possible to achieve the different modes.
There are three modes, these modes are given by a hiphen followed by either M (music),
L (LED) or S (Seven Segment). For example:

-M or -L or -S

The next arguments are parameters for the different modules explained below

LED MODE
TODO

SEVEN SEGMENT MODE
TODO

MUSIC MODE
In music mode the general syntax is the following
-<module> <number of notes> <note1> <length of note1 in seconds> <note2> ...<note5> <length of note5 in seconds> 

There are a few things to note about these parameters:
- <number of notes> is a number between 1 and 5 (max 5 notes)
- <noteN> has a possiblity to be A, B, C, D, E or F
- <length of note1 in seconds> is a number between 1 and 5 (max 5 seconds)


##########################  Detials About Testing Procedures #########################

Excersise 1 (demonstrate reading and writing via interrupts):
- Click run
- And pause, see that the main function is just looping infinitley (showing it is an interrupt)
- Type hello (or random string) into serial port (less than 64 characters)
- See the syntax error message along with your message printed out
- Repeat in simulation and change to serial port 0 to show serial port 0 working the same

Discussion quesitions
1. Store data in a char buffer, change the buffer size to extend the length
2. Interrupt either reads or writes a character
	- Looked at assembly code, depends on if message/action is generated, or end character
	- Approx. 180 intstrucions
	- Mult by 3, each instruction takes approx 3 clock cycles
	- 180*3*41.6*10^(-9) = 22.5 us
	
So 23 mikro seconds (approx)

Excersise 2:
- Click run
- type a defined message in the user instructions (i.e LED)
- See message of processing 
- See action 
- Repeat to show different action
- Repeat and type 64 characters
- See buffer overflow message given

Discussion quesitions
- Extend it by adding new function for new modules and change the input filtering to 
accept those new messages
- parser would fail if syntax is wrong, and buffer overflow

Excersise 3:
- Click run
- type a message for Music Mode with multiple notes and length
- Listen to tune play
- Try to type more notes while music plays to show error 
- When it is done type a different tune to show no error

Discussion quesitions
1. Flag called inPlay_tune is used to stop and give error message
2. Syntax error message given
