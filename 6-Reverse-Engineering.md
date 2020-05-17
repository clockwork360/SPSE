Part 1: Portable Executable Basics 

PE File Format 

	• PE is the native file format for Windows executable 
	• Portable across different version of Windows
	• Can be an EXE, DLL etc. 

Understanding the PE File Format

	• Any executable would have to have a mix of executable code and data it uses
	• Hence, PE should have minimum 1 code and 1 data section
	• Windows NT defined 9 different section possible in a PE 
	• Definitive guide to the PE 
	http://msdn.microsoft.com/en-us/magazine/cc301805.aspx

How Does a PE look like in File vs Memory? 



Sections in a PE Fle

	• Code Sections
		○ .text - executable instructions
	• Data Sections
		○ .bss - uninitialized data e.g. static variables 
		○ .rdata - read only data e.g. constants 
		○ .data - all other variables
	• Export / Import Data
		○ .edata - name+address of export functions
		○ .idata - contains Import Address Table and others
	• Debug Section
		○ .debug - debug info for the file
	• Resource Section
		○ .rsrc - contains resource information
	• Relocation Section
		○ .reloc - relocatin information
	• Thread Local Storage template
		○ .tls - template section for thread data storage 

Note: Check the Resource Hacker tool. 

Analyzing a PE File 

	• Use a software to understand the headers and format
		○ PEBrowse
		○ PEview 
	• Search, Download and Install 

	Demo purpose we will use Windows 7 Professional
	Open PEview 
	Open notepad.exe 
	
	DOS HEADER signature- see MZ 
	IMAGE_FILE_HEADER signature - see PE 


Part 2: PE Analysis with pefile 

DIY Installation (Do It Yourself) 

	• Install Immunity Debugger + Python 
	• Install pefile 

Ripping a PE file in Python 

	• Pefile is GREAT! :) 
	• Easy and simple to use 
	• Comprehensive in coverage

	Open Python Shell
	>>> import pefile
	>>> pe = pefile.PE('C:\\windows\notepad.exe')
	>>> pe
	>>> dir(pe)
	
	>>> import pprint 
	>>> pprint.pprint(dir(pe))
	>>> pe.DOS_HEADER
	
	>>> pprint.pprint(dir(pe.DOS_HEADER))
	>>> pe.DOS_HEADER.e_magic
	>>> hex(pe.DOS_HEADER.e.magic)
	>>> hex(pe.DOS_HEADER.e_lfanew) 
	
	>>> pprint.pprint(dir(pe.FILE_HEADER)
	>>> pe.FILE_HEADER.NumberOfSections
	
	>>> pe.sections 
	>>> pprint.pprint(dir(pe.sections))
	
	>>> for section in pe.sections : 
	…          print section.Name
	…          print section.SizeOfRawData
	…          print '\n'
	
	Windows Command Prompt
	>upx -9 -o notepad-upx.exe c:\windows\notepad.exe 
	
	Open notepad-upx.exe in PEBrowse at first 
	
	Return back to Python Shell
	>>> pe2 = pefile.PE('C:\\Users\\securitytube\\Desktop\\upx308w\\upx308w\\notepad-upx.exe') 
	>>> for section in pe2.sections : 
	…          print section.Name
	…          print section.SizeOfRawData
	…          print '\n'
	
	>>> pe.dump_info() 
	
Exercise 

	• Take a DLL name as input and check if a given PE imports it and print the list of imports 


Part 3: Disassembling Code with Pydasm

Installation pf Pydasm

	• It's a painful challenge :) 
	• Install Pydams (libdasm) on Windows 7 to use Python 2.7

Pydasm

	• Uses libdasm
	• Can disassemble instructions
	• Can be easily incorporated into your programs 

	Open Immunity Debugger 
	And Open Notepad in ID
	
	Open Python Shell
	>>> import pydasm 
	>>> instruction = pydasm.get_instruction('\x50', pydams.MODE_32) 
	>>> instruction 
	>>> 
	>>> pydasm.get_instruction_string(instruction, pydasm.FORMAT_INTEL, 0)
	'push eax' 
	
	>>> instruction = pydasm.get_instruction('\x85x\C0', pydams.MODE_32) 
	>>> pydasm.get_instruction_string(instruction, pydasm.FORMAT_INTEL, 0)
	'test eaxi eax' 
	
	>>> pydasm.get_instruction_string(instruction, pydasm.FORMAT_ATT, 0)
	'test %eax, %eax'
	
	>>> instruction = pydasm.get_instruction('\xC2\x04\x00', pydams.MODE_32) 
	>>> pydasm.get_instruction_string(instruction, pydasm.FORMAT_INTEL, 0)
	'retn 0x4' 
	
	>>> import pprint
	>>> pprint.pprint(dir(pydasm))
	
	>>> pprint.pprint(dir(instruction))

Exercise 

	• Create a simple program which can disassemble the first 200 bytes of executable code
	• Create simple shellcode for a windows bind shell and then use pydasm to disassemble it 


Part 4: PyDbg Basics 

Exercise 

	• Install PyDbg to run on Python 2.7.x
	• Spend time researching online :) 

What is Debugging? 

	• "De"  "Bug" (reduce number of bugs) :) 
	• Systematic process of analyzing a program to find and fix bugs. 
	• Debugger aids in this analysis by allowing programmer to inspect a running process 

Breakpoints 

	• Breakpoints allow you to freeze a program in execution and allows you to inspect things such as the 
		○ Variables 
		○ Memory dumps
		○ Stack 
		○ Processor registers
		○ Etc. 

Type of Breakpoints 

	• Software Breakpoints 
		○ When a specific instruction is to be executed 
		○ Any number can be applied
		○ Can not be used to check for memory access
	• Hardware Breakpoints
		○ Applied for instruction execution
		○ Memory access (read/write)
		○ Maximum number depends on Architecture
		○ Uses debug registers to create breakpoints 

First and Second Chance Exceptions

	• In a program running in a debugger, when an exception occurs, the debugger gets notified immediately.
		○ First chance exception
		○ Exception handling code is not invoked yet 
	• If program does not handle exception, then debugger gets notified again 
		○ Second chance exception
	• In most cases, we are interested in only Second Chance Exceptions 

PyDbg

	• Python based, Open Source User Mode debugger
	• Allows us to script and automate debugging tasks when reversing or doing exploit research 

Analyzing Crashes using Pydbg 

	• Will use a binary which is vulnerable to buffer overflow and try and catch exception 

	Open PyDbg
	Save as crash-demo.py 
	------
	#!/usr/bin/env python 
	
	from debug import *
	from pytdbg.defines import *
	import sys
	
	def detect_overflow(dbg) :
		if dbg.dbg.u.Exception.dvFirstChance:
			return DBG_EXCEPTION_NOT_HANDLED 
	
	print "Access Violation Happened!"
	print "EIP: %0X"% dbg.context.Eip
	return DBG_EXCEPTION_NOT_HANDLED 
	
	dbg = pydbg()
	dbg.attach(int(sys.argv[1]))
	
	dbg.set_callback(EXCEPTION_ACCESS_VIOLATION, detect_overflow)
	dbg.run() 
	------
	
	Start the Task Manager and  check the PID of Server-Strcpy.exe - 1548 
	
	Run the vulnerable program
	>cd c:\codesamples
	>python crash-demo.py 1548 
	
	# vim demo.py 
	--------
	#!/usr/bin/env python 
	
	import socket
	import sys 
	
	sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM, 0)
	sock.connect((sys.argv[1], 10000))
	buffer = "A"*500
	sock.send(buffer)
	sock.close() 
	--------
	
	# ./demo.py 192.168.1.10 

Exercise #1

	• Modify the code to take a file location as input and then automatically runs to file 

Exercise #2 

	• Modify the program to include full crash dump details. Explore how you can get all the info using the utils module 


Part 5: Monitoring API Calls 

Malware Analysis and Reversing 

	• One of the most important tasks is to monitor API calls
	• e.g. What does the malware send / recv over the network?
	• Which registry keys is it setting? 
	• Which files is it opening? 

PyDbg to the Rescue! 

	• Simple Steps for API Monitoring: 
		○ Find Address of Function you are insterested in monitoring 
		○ Set a breakpoint
		○ Dump context and inspect when breakpoint is hit 

	Open Python Shell
	>>> import pydbg 
	>>> dbg = pydbg.pydbg()
	>>> dbg.enumerate_processes() 
	
	Create a new file and save as monitor-api.py
	----------
	#!/usr/bin/env python 
	
	from pydbg import *
	from pydbg.defines import * 
	
	def send_bp(dpg): 
		print "Send() called!"
		print dbg.dump_context(dbg.context)
		
		return DBG_CONTINUE
		
	dbg = pydbg()
	
	dor pid, name in dbg.enumerate_processes() :
		if name == 'Server-Strcpy.exe' :
			dbg.attach(pid)
			
	send_api_addr = dbg.func_resolve("ws2_32", "send")
	dpg.bp_set(send_api_addr, description='Send Bp', handler=send_bp)
	dbg.run()
	----------
	
	Open Command Prompt as Administrator 
	>cd C:\codesamples
	>python monitor-api.py 
	
	# nc 192.168.1.10 1000 
	Hi 
	
	----------
	#!/usr/bin/env python 
	
	from pydbg import *
	from pydbg.defines import * 
	
	def send_bp(dpg): 
		print "Send() called!"
		print dbg.dump_context(dbg.context)
		
		return DBG_CONTINUE
		
	dbg = pydbg()
	
	dor pid, name in dbg.enumerate_processes() :
		if name == 'Server-Strcpy.exe' :
			dbg.attach(pid)
			
	send_api_addr = dbg.func_resolve("ws2_32", "recv")
	dpg.bp_set(send_api_addr, description='Send Bp', handler=send_bp)
	dbg.run()
	----------
	
	Open Command Prompt as Administrator 
	>cd C:\codesamples
	>python monitor-api.py 
	
	# nc 192.168.1.10 1000 
	Hi 

Exercise 

	• For both send/recv calls read the arguments from the stack when the breakpoint is hit and print the contents out in an intelligible way coherent with the API documentation. 

Analyze Malware 

	• Create API monitors for the following:
		○ Registry writes to "run on login/boot"
		○ Opening / Writing of files
		○ Send/recv on network 
	• Once you create the above framework, get a malware or program sample and test against it 


Part 6: Malware Analysis with SandBox

Sandbox 

	• Mechanism to observe the behavior of a program without allowing it to cause damage 
	• Monitoring of 
		○ API calls
		○ Data sent/received over the network
		○ Files, registry and other modifications
	• Used in the development of AV signatures 

Cuckoo Sandbox 

	• http://www.cuckoobox.org/
	• Allows you to submit malware and look at results 
	• Infrastructure can be set using Virtualbox 

Exercise 

	• Setup Cuckoo Box
	• Analyze a program with it and log
		○ API calls
		○ Files it reads/writes
		○ Host it communicates with 
