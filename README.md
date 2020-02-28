# ENGR-478-Lab1

;A program to compute the sum, difference, 
;and absolute difference of two signed 
;32-bit numbers.

;------Assembler Directives----------------
        THUMB						; uses Thumb instructions
		; Data Variables
		AREA    DATA, ALIGN=2 		; places objects in data memory (RAM)
		EXPORT  SUM	[DATA,SIZE=4]	; export public varialbe "SUM" for use elsewhere
		EXPORT  DIFF [DATA,SIZE=4]	; export public varialbe "DIFF" for use elsewhere
		EXPORT  ABS [DATA,SIZE=4]	; export public varialbe "ABS" for use elsewhere
		EXPORT  LARGER [DATA,SIZE=4]; export public varialbe "LARGER" for use elsewhere
SUM     SPACE	4   				; allocates 4 uninitialized bytes in RAM for SUM
DIFF	SPACE	4					; allocates 4 uninitialized bytes in RAM for DIFF
ABS		SPACE	4					; allocates 4 uninitialized bytes in RAM for ABS
LARGER  SPACE	4					; allocates 4 uninitialized bytes in RAM for LARGER

		; Code
		AREA    |.text|, CODE, READONLY, ALIGN=2	; code in flash ROM
		EXPORT  Start				; export public function "start" for use elsewhere
NUM1   	DCD   	3						; 32-bit constant data NUM1 = 5
NUM2	DCD		4294967295					; 32-bit constant data NUM2 = 3
;-------End of Assembler Directives----------


GET_SUM								; subroutine GET_SUM
		ADD R0, R1, R2				; R0=R1+R2
		LDR R3, =SUM				; R3=&SUM, R3 points to SUM (address)
		STR R0, [R3]				; store the sum of NUM1 and NUM2 to SUM
		BX	LR						; subroutine return
GET_DIFF							; subroutine GET_DIFF
		SUBS R0, R1, R2				; R0=R1-R2
		LDR R3, =DIFF				; R3=&DIFF, R3 points to DIFF
		STR R0, [R3]				; store the different of NUM1 and NUM2 to DIFF
		BMI	GET_ABS					; check condition code, if N=1 (i.e. the difference is negative), 
									; branch to GET_ABS to calculate the absolute difference
STR_ABS								; label STR_ABS, store the absolute difference
		LDR R3, =ABS				; R3=&ABS, R3 points to ABS
		STR R0, [R3]				; store the absolute difference to ABS
		BX	LR						; subroutine return
GET_ABS								; label GET_ABS, calculate the absolute difference if the difference is negative
		RSB	R0, R0, #0				; R0=0-R0 Reverse subtract carry {R0}, R0, #0
		B	STR_ABS					; branch to STR_ABS to store the result
		
GET_LARGER
		CMP R1, R2					; compare R1-R2 and update condition
		LDR R3, =LARGER				; R3 points to LARGER
		STR R1, [R3]				; store R1 into LARGER if R1>R2
		BLT NUM2_LARGER				; branch to NUM2_LARGER if R1<R2, N!=V (signed overflow)
		BEQ NO_LARGER				; branch to NO_LARGER if R1=R2, Z=1
		BX LR						; branch exchange subroutine return
;		LDR R3, =LARGER				; R3=&LARGER, R3 points to LARGER
;		STR R1, [R3]				; store larger value into LARGER
;		BMI STR_LARGER				; branch to STR_LARGER if R2>R1, check if N=1
;		BEQ NO_LARGER				; branch to NO_LARGER if no larger value, check if Z=1
;		BX LR		
NUM2_LARGER							; label NUM2_LARGER, store larger value, which is NUM2
		LDR R3, =LARGER				; R3 points to LARGER
		STR R2, [R3]				; store R2 into LARGER
		BX LR						; return to main routine
NO_LARGER							; label NO_LARGER, there's no larger value ;MOV R0, #0					; move 0 into R0
		LDR R3, =LARGER				; R3 points to LARGER
		STR R2, [R3]				; store R0 into LARGER
		BX LR						; subroutine return
		
Start	LDR R1, NUM1				; R1=NUM1 load NUM1 memory into register R1
		LDR R2,	NUM2				; R2=NUM2 load NUM2 memory into register R2
		BL	GET_SUM					; branch to GET_SUM 
		BL	GET_DIFF				; branch to Get_DIFF
		BL 	GET_LARGER				; branch to GET_LARGER

		ALIGN                       ; make sure the end of this section is aligned
		END                         ; end of file
