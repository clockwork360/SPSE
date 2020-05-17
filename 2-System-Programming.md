System Programming

Part 1: File Handling

File I/O in Python 

	• open(file_name, access_mode, buffering)
	• read(byte_count)
	• write(data)
	• close()
	• os.rename(), os.delete() 

	# python 
	>>> fdesc = open("spse.txt", "w")
	>>> fdesc 
	>>> for count in range(0,100) :
	…        fdesc.write(str(count) + "\n")
	>>> fdesc.close() 
	# cat spse.txt 

	# python 
	>>> fdesc = open("spse.txt", "a")
	>>> fdesc 
	>>> for count in range(100,200) :
	…        fdesc.write(str(count) + "\n")
	>>> fdesc.close() 
	# cat spse.txt 

	>>> fdesc = open("spse.txt", "r")
	>>> fdesc 
	>>> for line in fdesc.readlines() :
	…        print line.strip()
	>>> fdesc.close() 
	
	>>> help 
	
	>>> import os
	>>> os.rename("spse.txt", "spse2.txt")
	# ls 
	
	Note: os.delete() is leaved for you as an exercise. 

Exercise

	• Read /var/log/messages
	• Find all the logs in it which pertain to USB and print them out selectively. 

Part 2: Directory Navigation 

Directroy Details 

	• Methods for traversing directories
	• Listing files and their information
	• Creating and deleting directories + files 
	• Test to check if something is a file or directory 

	# python 
	>>> import os
	>>> os.getcwd() 
	
	>>> os.mkdir("NewDirectory")
	# ls
	
	# python 
	>>> import os
	>>> os.listdir(".")
	
	>>> os.listdir("/")
	
	>>> for item in os.listdir(".") :
	…       if os.path.isfile(item) :
	…          print item + " is a file"
	…       elif os.path.isdir(item) :
	…          print item + " is a directory)
	…       else :
	…          print "unknown!"
	
	
	>>> import globe
	>>> for item glob.glob(os.path.join(".", "*")) :
	…       print item 
	
	>>> import globe
	>>> for item glob.glob(os.path.join(".", "*.py")) :
	…       print item 
	
	>>> import globe
	>>> for item glob.glob(os.path.join(".", "*.txt")) :
	…       print item 
	
	Note: When you have a very large file listing, probably globe is not a good option. There is  another options but I leave it to you as an exercise. Go to glob documentation in Python and you will have the answer. 

Exercise 

	• Create a program which can recursively traverse directories and print the file listing in a hierarchical way. 

	A
	---a.txt
	----b.txt
	----B
	---------c.out 

	• For any given filename list out all the stats related to the file such as size, creation time, path etc. 

Part 3: Process Creation 

Forking 

	• Cloning of a process
	• Forking creates an identical process as the parent 
	• The thread of execution is duplicated exactly at the point of call to fork()
		○ Returns 0 in the child 
		○ Returns PID of child in the parent
	• PID is different for parent/child 
	
	You want to do multiple tasks and probable distribute that to different worker processes. That's the ability to create new processes within this course is in valuable. There are multiple ways to create processes . We will take up 2 of them right now and we will look at couple more when we look at IPC. 
	
	The first one I want to discuss is fork, also forking. Forking is the process of cloning a process. Forking basically creates an identical process as the parent. The thread of execution is duplicated exactly at the point of call to fork(). Exactly everything is the same. The only differences when you call the API for the os.fork this returns 0 in the child process, while returns the process ID of the child in the parent process. Using this inside call we can figure out if it's in the child or in the parent and decide to do absolutely different tasks. 
	
	Inside the parent process it returns the id of the child, inside the child process it just returns the value 0. 
	
	# vim fork-demo.py 
	-------------------
	#!/usr/bin/env python 
	
	import os 
	
	def child_process () :
		print "I am the child process and my PID is : %d"%os.getpid()
		
		print "The child is exiting"
		
	def parent_process() :
		print "I am the parent process with PID : %d"%os.getpid()
		
		childpid = os.fork() 
		
		if childpid == 0 :
			# we are inside the child 
			child_process()
		else :
			# we are inside the parent process
			print "We are inside the parent process"
			print "Our child has the PID : %d"%childpid 
		while True :
			pass
	
	parent_process()
	-------------------
	
	# chmod a+x fork-demo.py 
	# ./fork-demo.py 

Use of fork() 

	• Dedicate child to a task given by the parent
	• Parent and child can communicate if required using IPC
	• Parent/child binary remains the same 

Spawning New Process

	• os.exec* functions
		○ os.execl
		○ os.execle
		○ …
	• Overlays parent process with the child 
	
	Overlay: üzerine bindirmek
	
	What if we want to create processes which basically overlay the parent process which an absolutely binary which I am running. This is basically where the os.exec family of functions come in. There are a bunch of them which you can look up later for the specific tasks we will look at execvp. 
	
	# python 
	>>> import os 
	>>> os.execvp("ping", ["ping", "127.0.0.1"])
	
	If we kill the ping program, if you notice we back to our command prompt rather than ping back in the python interpreter. 
	The child overlays the parent. The key difference, the original process is overlay.


Part 4: Python Threads 

Global Interpreter Lock

	Apart from I/O Tasks such as Network reads, Writing disk etc. Python Threads are not too useful. 
	
	CPython implementation detail: Due to the Global Interpreter Lock, in Cpython only one thread can execute Python code at once (even though certain performance-oriented libraries might overcome this limitation). If you want your application to make better of use of the computational resources of multi-core machines, you are advised to use multiprocessing. However, threading is still an appropriate model if you want to run multiple I/O-bound tasks simulatenously. 

Threads in Python 

	• Simple threads using the thread module
	• More complicated ones using the threading module 
	
	# vim thread.py 
	
	--------------
	#!/usr/bin/env python 
	
	import thread
	import time
	
	def worker_thread(id) :
		
		print "Thread ID % now alive!"%id
		
		count = 1
		while True :
			print "Thread with ID %d has counter value %d"% (id, count) 
			time.sleep(2)
			count += 1
			
	for i in range(5) :
		thread.start_new_thread(worker_thread, (i,))
	
	print "Main thread going for a infinitive wait loop"
	
	while True:
		pass
	--------------
	
	# python thread.py 

Exercise

	• Based on the knowledge you have gained in the network programming module, create a multi-threaded port scanner in Python which uses SYN Scanning. 


Part 5: Threading and Queues

Threading and Queues

	• Create tasks queues
	• Threads receive tasks
	• Threads complete tasks and inform the queue
	• All threads exit once queue is empty
	
	# vim q.py 
	
	-----------------
	#!/usr/bin/env python 
	
	import threading 
	import Queue
	import time 
	
	class workerThread(threading.Thread) :
		
		def __init__(self, queue) :
			threading.Thread.__init__(self)
			self.queue = queue
			
		def run(self) :
			print "In WorkerThread"
			while True :
				counter = self.queue.get()
				print "Ordered to sleep for %d seconds!"%counter
				time.sleep(counter)
				print "Finished sleeping for %d seconds!"%counter
				self.queue.task_done()
				
	queue = Queue.Queue()
	
	for i in range(10) :
		print "Creating WorkerThread : %d"%i
		worker = WorkerThread(queue)
		worker.setDaemon(True)
		worker.start()
		print "WorkerThread %d Created!"%i
	
	for j in range (10) :
		queue.put(j)
	
	queue.join()
	
	print "All tasks over!" 
	
	-----------------
	You can do whatever you want inside that run method, you could have distributed port scanner, password cracking tool. 
	
	# chmod a+x q.py 
	# python q.py  

Exercise -1

	• Create a list of FTP sites
	• Create a WorkerThread and Queue which can login to thoese sites and list the root directory and exit. 
	• Use 5 threads for this job and 10 FTP sites. 

Exercise -2: Threads and Locks

	• There is a locking mechanism avalabile in the Thread class which you can use to lock resources for dedicated use
	• Created a sample code to illustrate this concept

Execrise -3: Multiprocessing 

	• Explore the multiprocessing module in Python 
	• How does it leverage multic-ore setups? 
	• Program the TCP SYN scanner using multiprocessing 


Part 6: Signals and IPC

Signals 

	• Allows handling of Asynchronous events
	• SIGKILL is what gets sent when you use "kill -9"
	• Programming with Signals is easy 
	
	# man 7 signal
	# man signal
	
	# vim signal.py 
	
	-----------------
	#!/usr/bin/env python 
	
	import signal
	
	def ctrlc_handler(signum, frm) :
		print "Haha! You cannot kill me!"
	print "Installing signal handler …."
	
	signal.signal(signal.SIGINT, ctrlc_handler)
	print "Done!"
	
	while True:
		pass
	-----------------
	
	# python signal.py 

Exercise 

	• Create a TCP server which listens to a port.
	• Implement signals to ensure it automatically shuts down after a pre-configured duration, which is given via command line.
	• e.g. Tcp-server -s 100
	
	Shutdown after listening to port for 100 seconds. 
	
	You can use sigalarm to solve this problem. 


Part 7: Subprocess

Subprocess

	• Subprocess.call( ['ps', 'aux']) 
	• Subprocess.check_output(['ls', '-al'])
	
	Shell = False 
	Why is shell = True a security issue? 
	
	Basically it helps to invoke other process and interact with them.
	
	# python 
	>>> import subprocess
	>>> subprocess.call(['ps', 'aux'])
	
	>>> subprocess.call(['ls', '-al'])
	
	>>> subprocess.check_output(['ls', '-al'])
	>>> lines = subprocess.check_output(['ls', '-al'])
	>>> lines[0]
	>>> lines[1] 
	
	>>> lines = subprocess.check_output(['ls'])
	>>> lines[0]
	>>> lines 
	>>> line_list = lines.split('\n')
	>>> line_list 
	
	>>> handle = subprocess.Popen("ls", stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE, close_ds=True)
	>>> handle.stdout.read() 

Advanced Usage

	• Mapping of 
		○ STDIN
		○ STDOUT
		○ STDERR
	• Subprocess.Popen(…)
	
	We will use subprocess in the later module when we look at automation. 
