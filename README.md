# PIC16F877A
## GENERAL
### 1) TO FAMILIARIZE PIC16F877A

* code - MPLABPX IDE
* simulation - proteus 8 professional
* Datasheet - https://ww1.microchip.com/downloads/en/devicedoc/39582b.pdf

### 2) MPLAB Setting
 * Download and install
 * Step 1 : Open MPLAB -> File -> New Project ->(New Tab opens) Device : PIC16F877A -> TOOL : Simulator -> FINISH
 * Step 2 :(Side bar click on project name) -> source file(RIGHT CLICK) -> NEW -> main.c
 * Step 3 : (Menu bar) Production -> set configuration bits -> change FOSC's EXTRC to HS -> generate configuration bits (copy config definition to main.c file) external high-speed crystal oscillator (typically >4 MHz, often up to 20 MHz or more)
 * Step 4 : add "#define _XTAL_FREQ 16000000" after headerfile

### 3) Proteus Setting
 * Run Setup and install
 * Step 1 : Open Proteus ->  New Project ->File Path setting -> (next) -> FINISH
 * Step 2 : Click on P icon -> No library found
 * Step 3 : This PC -> OS -> Program Files(x86) -> Labcenter Electronics -> Proteus 8 Professional -> DATA -> LIBRARY( check for library folder ,if not add from installation folder)
 * Step 4 : Right click on Proteus Icon -> Properties ->Compatibility -> Run this program as an administrator(tick mark)
 * Step 5 : Close Proteus, then refresh and open again

### 3) #pragma 
* is a compiler directive in C and C-like languages (including MPLAB XC8 for PIC microcontrollers).
* Special instruction sent to the compiler, not part of the C language itself. It's used to control compiler behavior — things like memory configuration, interrupt vector definitions, or special optimizations.


## TOPICS

### 1) SWITCH

* In Proteus, type button -> SPST Push Button
* For circuit,
   * Button pressed → RB0 is HIGH (connected to Vcc).
   * Button not pressed → RB0 is LOW (via pull-down).

<br>GND
<br>|
<br>[10kΩ]       ←  Pull-down resistor
<br>|
<br>+-------> RB0 (PORTB.0)
<br>|
<br>(Button)
<br>|
<br>+5V



#### Code Explanation
* PORTC |= 0x01;(consider this as x)
     * This line sets bit 0 (RC0) of PORTC to HIGH while leaving the other bits unchanged.

|Before PORTC |After x |
|:---:|:--:|
|00000000 |00000001 |
|00000100 |00000101 |
|11111110 |11111111 |

* PORTB &= 0x01;
    * You’re attempting to clear RB1 to RB7 — this writes zero to those output pins




#### MOTOR DIRECTION

|DIRECTION	|IN1	|IN2	|IN3|	IN4|	MOVEMENT|
|:---:|:--:|:---:|:--:|:--:|:---:|
|STOP|	0|0|	0	|0	|Both Stops|
|FORWARD|	1|	0|	1|	0	|Both Forward Moves|
|BACKWARD|	0|	1|	0|	1	|Both Backward Moves|
|LEFT|	0|	1	|1|	0	|Left motor backward, Right motor forward|
|RIGHT|	1|	0	|0|	1	|Right motor backward, Left motor forward|

Refer: https://randomnerdtutorials.com/esp32-dc-motor-l298n-motor-driver-control-speed-direction/




### 2) 7 SEGMENT DISPLAY

* To display all numbers and to properly see them all
     * Add a 220 - 470 ohm resistor between each PORT pin and its respective segment pin. Like this,

<img width="684" height="519" alt="Screenshot 2025-07-23 105246" src="https://github.com/user-attachments/assets/1d5c83c3-1756-4e32-9892-9ccf736d1837" />




   
#### TIMERS & INTERRUPTS

|Timer	|Size	|Control Register	|Count Register|	Min Delay|	Max Delay|
|:---:|:--:|:---:|:--:|:--:|:---:|
|TIMER0|	8-bit|OPTION_REG|	TMR0	|0.2usec	|13.107ms|
|TIMER1|	16-bit|	T1CON|	TMR1H,TMR1L|	0.2usec	|104.857ms|
|TIMER2|	8-bit|	T2CON	|TMR2|	0.2usec	|819usec|



##### TIMER 0 

Time to increment the Timer count by one(timer tick):
<br> tick = (Prescalar/(Fosc/4)  Fosc=16MHz
<br> Here, Timer0 is 8 bit hence time period to complete 0 to 255 is consider as delay 
<br> i.e, delay = Count * tick
<br> Count = (Delay/tick)
<br> RegValue = TimerMax- Count
<br> RegValue = TimerMax-(Delay/tick) = TimerMax - (Delay/((Prescalar *4)/Fosc))




|Timer|	Size|	Formula for delay calculation|
|:---:|:--:|:---:|
|TIMER0	|8-bit|	RegValue = 256-((Delay * Fosc)/(Prescalar*4))|
TIMER1 |16-bit|	RegValue = 65536-((Delay * Fosc)/(Prescalar*4))|
TIMER2	|8-bit	|RegValue = 256-((Delay * Fosc)/(Prescalar*4))|

* Calculate the Timer Count for the required delay.
* Set the Presaclar bits in OPTION_REG as per the delay calculations.
* Clear the PSAbit for using the prescalar.
* Select the Clock Source Internal/External using TOCS bit.
* Load the timer value into TMRO register.
* Enable the Timer0 Interrupt by setting TMR0IE bit
* Enable the Global and Peripheral interrupts by setting GIE and PIE bits
* Refer to https://exploreembedded.com/wiki/PIC16f877a_Timer


##### TIMER 1

 |CCP Mode | Timer Resource| 
 |:--:|:---:|
|Capture|	Timer1|
|Compare	|Timer1|
|PWM	|Timer2|

#### PULSE WIDTH MODULATION

|PWM Channel|	Port Pin|	Control Register|	Duty Cycle Register	|Period Register|
|:---:|:--:|:---:|:--:|:--:|
|PWM1|	PC.2|	CCP1CON|	CCPR1L|	PR2|
|PWM2|	PC.1|	CCP2CON|	CCPR2L|	PR2|

-> for setting PWM signal at 2Khz
* PWMperiod = 1/2kHz
* PWMperiod = (PR2 + 1) x 4 x Tosc x TMR2PrescalerValue
* Tosc = 1/Fosc:: Fosc = 16Mhz 


##### INTERRUPTS

 REFER :https://deepbluembedded.com/irq-interrupt-requests/
 
* An external interrupt is a signal generated by a hardware device outside the processor, like a switch, sensor, or another device, that triggers a system to respond immediately.
* IRQ stands for interrupt requests.
* Two types for IRQ pins.
        (1) RB0 INT (edge-triggered)
        (2) PORTB IOC (interrupt-on-change)
* The registers which control the interrupt circuitry are the following 5-Registers

|INTCON|	PIE1|	PIE2|	PIR1|	PIR2|
|:---:|:---:|:--:|:--:|:---:|

#### SPI - Serial Peripheral Interface

<br> Synchronous serial communication protocol used for high-speed data transfer.

Key Features:
* Master-slave architecture.
* Uses four lines:
  <br> MISO - Master input slave output (Dout From Slave)
  <br> MOSI - Master output slave input (Dout From Master)
  <br> SCK - Serial Clock, generated by the master and goes to the slave
  <br> SS - Slave Select. Generated by the master to control which slave to talk to.
  <br> &ensp;&ensp;It’s usually an active-low signal
  <br> &ensp;&ensp;The master device can select which slave to talk to by setting the SS (slave select) pin to logic low.
* Full-duplex communication.
* Speed Up to tens of Mbps.

<br> Applications: Flash memory, sensors, SD cards, displays.



