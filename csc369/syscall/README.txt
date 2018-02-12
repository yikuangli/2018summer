The system call path:

At user level:
	- load the system call number into register v0
	- issue syscall instruction to cause interrupt

At kernel level:
	- processor begins executing interrupt handling code which starts at 
	exception (line 64 in exception.S)
	- read the comments in the assembly code to get a sense of what gets
	saved and what gets setup in the switch to kernel mode
	- call "common_exception"
		- creates the trap frame to save registers
		- call "mips_trap" (trap.c)
			- determines the cause of the exception
			- if it was caused by a system call then call "mips_syscall"
				- "mips_syscall" (syscall.c)
					This function is primarily a bit switch statement
					on the system call number. 
					Based on the system call number, the appropriate 
					function is called that carries out the operation of the
					system call.  Note that arguments from the trap frame are
					passed to these funcitons.

				- on return from the function that carries out the opreation,
				either the return value or the error code is stored in the v0
				field of the trap frame, and the a3 field is set to indicate
				success or failure.
				- epc is advanced to the next instruction
		- on return from "mips_trap", registers are restored from the trap frame
		- process is now ready to return to user-mode
