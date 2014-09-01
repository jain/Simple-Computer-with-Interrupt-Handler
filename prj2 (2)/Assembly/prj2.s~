!************************************************************************!
!									 !
! general calling convention:						 !
!									 !
! (1) Register usage is as implied in the assembler names		 !
!									 !
! (2) Stack convention							 !
!									 !
!	  The stack grows towards higher addresses.  The stack pointer	 !
!	  ($sp) points to the next available (empty) location.		 !
!									 !
! (3) Mechanics								 !
!									 !
!	  (3a) Caller at call time:					 !
!	       o  Write any caller-saved stuff not saved at entry to	 !
!		  space on the stack that was reserved at entry time.	 !
!	       o  Do a JALR leaving the return address in $ra		 !
!									 !
!	  (3b) Callee at entry time:					 !
!	       o  Reserve all stack space that the subroutine will need	 !
!		  by adding that number of words to the stack pointer,	 !
!		  $sp.							 !
!	       o  Write any callee-saved stuff ($ra) to reserved space	 !
!		  on the stack.						 !
!	       o  Write any caller-saved stuff if it makes sense to	 !
!		  do so now.						 !
!									 !
!	  (3c) Callee at exit time:					 !
!	       o  Read back any callee-saved stuff from the stack ($ra)	 !
!	       o  Deallocate stack space by subtract the number of words !
!		  used from the stack pointer, $sp			 !
!	       o  return by executing $jalr $ra, $zero.			 !
!									 !
!	  (3d) Caller after return:					 !
!	       o  Read back any caller-saved stuff needed.		 !
!									 !
!************************************************************************!
!vector table
 vector0: .fill 0x00000000 !0
 .fill 0x00000000 !1
 .fill 0x00000000 !2
 .fill 0x00000000
 .fill 0x00000000 !4
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000 !8
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000
 .fill 0x00000000 !15
!end vector table


main:
	addi $sp, $zero, initsp ! initialize the stack pointer
	lw $sp, 0($sp)		! initialize stack
	! Install timer interrupt handler into the vector table
	la $s0, ti_inthandler	! get address of ti_handler
	la $a1, vector0		! get address of vector
	sw $s0, 1($a1)		! save ti_handler on vector
	!FIX ME
	ei 			!Don't forget to enable interrupts...

		
	addi $a0, $zero, 2	!load base for pow
	addi $a1, $zero, 1	!load power for pow
	addi $at, $zero, POW	!load address of pow
	jalr $at, $ra		!run pow
		
	halt	
		
		

POW: 
  addi $sp, $sp, 2   ! push 2 slots onto the stack
  sw $ra, -1($sp)   ! save RA to stack
  sw $a0, -2($sp)   ! save arg 0 to stack
  beq $zero, $a1, RET1 ! if the power is 0 return 1
  beq $zero, $a0, RET0 ! if the base is 0 return 0
  addi $a1, $a1, -1  ! decrement the power
  la $at, POW	! load the address of POW
  jalr $at, $ra   ! recursively call POW
  add $a1, $v0, $zero  ! store return value in arg 1
  lw $a0, -2($sp)   ! load the base into arg 0
  la $at, MULT		! load the address of MULT
  jalr $at, $ra   ! multiply arg 0 (base) and arg 1 (running product)
  lw $ra, -1($sp)   ! load RA from the stack
  addi $sp, $sp, -2  ! pop the RA and arg 0 off the stack
  jalr $ra, $zero   ! return
RET1: addi $v0, $zero, 1  ! return a value of 1
  addi $sp, $sp, -2
  jalr $ra, $zero
RET0: add $v0, $zero, $zero ! return a value of 0
  addi $sp, $sp, -2
  jalr $ra, $zero		
	
MULT: add $v0, $zero, $zero ! zero out return value
AGAIN: add $v0,$v0, $a0  ! multiply loop
  addi $a1, $a1, -1
  beq $a1, $zero, DONE ! finished multiplying
  beq $zero, $zero, AGAIN ! loop again
DONE: jalr $ra, $zero	
		
		
ti_inthandler:
	sw $k0, 0($sp);		! save $k0
	ei			! enable interrupts
	addi $sp, $sp, 13	! increase stack to save registers
	sw $at, 0($sp)		! save register $at
	sw $v0, -1($sp)		! save register $v0
	sw $a0, -2($sp)		! save register $a0
	sw $a1, -3($sp)		! save register $a1
	sw $a2, -4($sp)		! save register $a2
	sw $a3, -5($sp)		! save register $a3
	sw $a4, -6($sp)		! save register $a4
	sw $s0, -7($sp)		! save register $s0
	sw $s1, -8($sp)		! save register $s1
	sw $s2, -9($sp)		! save register $s2
	sw $s3, -10($sp)	! save register $s3
	sw $fp, -11($sp)	! save register $fp
	sw $ra, -12($sp)	! save register $ra
	la $a0, sec		! get location of sec in program
	lw $a0, 0($a0)		! get memory location of that place
	lw $a1, 0($a0)		! get value of seconds
	addi $a1, $a1, 1	! increment	
	addi $a2, $zero, 60	! input value of 60 for check
	beq $a1, $a2, second	! if it equals 60, branch
return:	sw $a1, 0($a0)		! save seconds at location
	lw $at, 0($sp)		! restore register $at
	lw $v0, -1($sp)		! restore register $v0
	lw $a0, -2($sp)		! restore register $a0
	lw $a1, -3($sp)		! restore register $a1
	lw $a2, -4($sp)		! restore register $a2
	lw $a3, -5($sp)		! restore register $a3
	lw $a4, -6($sp)		! restore register $a4
	lw $s0, -7($sp)		! restore register $s0
	lw $s1, -8($sp)		! restore register $s1
	lw $s2, -9($sp)		! restore register $s2
	lw $s3, -10($sp)	! restore register $s3
	lw $fp, -11($sp)	! restore register $fp
	lw $ra, -12($sp)	! restore register $ra
	addi $sp, $sp, -13	! restore value of $sp
	di			! disable interrupts
	lw $k0, 0($sp)		! restore $k0
	reti			! return from interrupt
second:	la $a1, minu		! get location of minutes
	lw $a3, 0($a1)		! get mem location of minutes
	lw $a1, 0($a3)		! get value of minutes
	addi $a1, $a1, 1	! increment minutes
	beq $a1, $a2, hr	! if it equals 60, change hour
second2:sw $a1, 0($a3)		! save value of minutes
	addi $a1, $zero, 0	! ensure seconds = 0
	beq $zero, $zero, return! return to location
hr:	la $a4, hour		! get location of hours
	lw $s0, 0($a4)		! get mem location of hours
	lw $a4, 0($s0)		! get value of hours
	addi $a4, $a4, 1	! increment it
	addi $a1, $zero, 1	! set minutes to 0
	sw $a4, 0($s0)		! save hours
	beq $zero, $zero, second2! return to finish handling minutes
sec: .fill 0xFFFFC
minu:.fill 0xFFFFD
hour:.fill 0xFFFFE
initsp: .fill 0xA00000
