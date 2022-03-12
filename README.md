# pedestrain-light-MCU8051
Code and [report](cso_report_pedestrian_light.pdf) for course WIX1003 Computer System Organization (2020/2021 semester 1), which simulates the countdown and pedestrain lights for crossroad. 

# Code

```assembly
;ped lights	
		ORG		00h
		AJMP		MAIN

MAIN:		MOV		A,#00h		;D-GREEN B-RED E-YELLOW
		MOV		P0,A		;set P0 as output for North (p0.4-p0.7) and West (p0.0-p0.3)
		MOV		P1,A		;set p1 as output for South (p1.4-p1.7) and East (p1.0-p1.3)
		MOV		P2,A		;set p2 as output for segment countdown
		MOV		DPTR,#SEG	;move table address to Data Pointer

START:		MOV		A,#0FFh		;D-GREEN B-RED E-YELLOW
		MOV		P0,A
		MOV		P1,A
		MOV		A,#00h
		MOV		P2,A
		AJMP		STATE1

;State 1 - State when North is not red
STATE1:		MOV		A,#0DBh
		MOV		P0,A		;North->Green	West->Red
		MOV		A,#0BBh
		MOV		P1,A		;South->Red	East->Red
		MOV		B,#009h		;set time for countdown
		ACALL		COUNT		;call a 9-second countdown for North (Green)
		;
		MOV		A,#0EBh
		MOV		P0,A		;North->Yellow	West->Red
		MOV		B,#003h		;set time for countdown
		ACALL		COUNT		;call a 3-second countdown for North (Yellow)
		SJMP		STATE2		;jump to next state STATE2

;State 2 - State when West is not red
STATE2:		MOV		A,#0BDh
			MOV		P0,A		;North->Red	West->Green
			MOV		A,#0BBh
			MOV		P1,A		;South->Red	East->Red
			MOV		B,#009h		;set time for countdown
			ACALL	COUNT		;call a 9-second countdown for West (Green)

			MOV		A,#0BEh
			MOV		P0,A		;North->Red	West->Yellow
			MOV		B,#003h		;set time for countdown
			ACALL	COUNT		;call a 3-second countdown for West (Yellow)
			SJMP	STATE3		;jump to next state STATE3

;State 3 - State when South is not red
STATE3:		MOV		A,#0BBh
			MOV		P0,A		;North->Red	West->Red
			MOV		A,#0DBh
			MOV		P1,A		;South->Green	East->Red
			MOV		B,#009h		;set time for countdown
			ACALL	COUNT		;call a 9-second countdown for South (Green)

			MOV		A,#0EBh
			MOV		P1,A		;South->Yellow	East->Red
			MOV		B,#003h		;set time for countdown
			ACALL	COUNT		;call a 3-second countdown for South (Yellow)
			SJMP	STATE4	;	jump to next state STATE4

;State 4 - State when East is not red
STATE4:		MOV		A,#0BBh
			MOV		P0,A		;North->Red	West->Red
			MOV		A,#0BDh
			MOV		P1,A		;South->Red	East->Green
			MOV		B,#009h		;set time for countdown
			ACALL	COUNT		;call a 9-second countdown for South (Green)
		;
			MOV		A,#0BEh
			MOV		P1,A		;South->Red	East->Yellow
			MOV		B,#003h		;set time for countdown
			ACALL	COUNT		;call a 3-second countdown for East (Yellow)
			SJMP	STATE1		;jump to next state STATE1

;subroutine to count down
COUNT:		MOV		A,B			;move timeset from B to A
			JZ		RETURN		;A not 0, continue; else return
			MOVC 	A,@A+DPTR	;load value from table
			MOV 	P2,A		;to display the segment number
			ACALL 	DELAY  		;call a one-second delay
			DEC		B			;decline B by 1
			SJMP 	COUNT		;jump to count to display the next number

RETURN:		RET

;subroutine to delay
DELAY:		MOV 	R1,#001h
DELAY1:		MOV		R2,#0Fh
DELAY2:		DJNZ	R2,DELAY2
			DJNZ	R1,DELAY1
			RET

;lookup table for 7-segment display pattern from 0-9
SEG:		DB		3Fh,06h,5Bh,4Fh,66h,6Dh,7Dh,07h,7Fh,6Fh

END
```
