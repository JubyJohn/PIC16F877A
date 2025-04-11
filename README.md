# PIC16F877A

* code - MPLABPX IDE
* simulation - proteus 8 professional

#### MPLAB Setting
 * Download and install
 * Step 1 : Open MPLAB -> File -> New Project ->(New Tab opens) Device : PIC16F877A -> TOOL : Simulator -> FINISH
 * Step 2 :(Side bar click on project name) -> source file(RIGHT CLICK) -> NEW -> main.c
 * Step 3 : (Menu bar) Production -> set configuration bits -> change FOSC's EXTRC to HS -> generate configuration bits (copy config definition to main.c file)
   
#### TIMERS & INTERRUPTS

|Timer	|Size	|Control Register	|Count Register|	Min Delay|	Max Delay|
|:---:|:--:|:---:|:--:|:--:|:---:|
|TIMER0|	8-bit|OPTION_REG|	TMR0	|0.2usec	|13.107ms|
|TIMER1|	16-bit|	T1CON|	TMR1H,TMR1L|	0.2usec	|104.857ms|
|TIMER2|	8-bit|	T2CON	|TMR2|	0.2usec	|819usec|

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

#### PULSE WIDTH MODULATION

|PWM Channel|	Port Pin|	Control Register|	Duty Cycle Register	|Period Register|
|:---:|:--:|:---:|:--:|:--:|
|PWM1|	PC.2|	CCP1CON|	CCPR1L|	PR2|
|PWM2|	PC.1|	CCP2CON|	CCPR2L|	PR2|
