Part 1: Introduction to Python and Setting up an Environment 

Python - a short history 

	• Created in 1989 by Guido Van Rossum (works for Google)
	• Python 2.x in 2000
	• Python 3.x in 2008
	• Not backward compatible
	• 2.x is the status quo
	• 3.x future 

Why Python? 

	• Open source
	• Multi platform
	• Rich set of libraries
	• Large number of open source tools
	• HLL used for Rapid Prototyping

Multiple OS Support 

	• Unix/Linux 
	• Mac OSX
	• Windows
	• Mobile Platforms - Android, iOS
	• Embedded Systems 

Implementations

	• Cpython - reference implementation "Python" 
	• Jython Pythonin Java
	• Iron Python Pythonin  C# 

More: http://wiki.python.org/moin/PythonImplementations?action=show&redirect=implementation

Why Python in InfoSec

	• Rapid prototyping - POC
	• Extensive library support
	• Ton of tools already written 

Python on different OSs 

	• Linux - preloaded
	• Windows - activestate 
	• Mac - preloaded

Python 2.7 or 3.x? 

	• Emphasis on 2.7
	• Most tools/libraries still do not support 3.x 
	• Eventually everything will support 3.x 
		○ Will take a couple of years 

Platform of Choice

	• Ubuntu Server 11.10 64 Bit 
	http://www.ubuntu.com/download/server/download
	• Will be running inside Virtualbox
	• Connect with Putty 

Demo: 

	# ssh melih@192.168.0.193
	# mkdir Python
	# mkdir Lesson-1
	# cd Lesson-1
	
	# python 
	>>> print "Hello World" 
	>>> print "Welcome to the SPSE Course!"
	>>> quit
	
	# vim HelloWorld.py
	------------
	Print "Hello World"
	------------
	# python HelloWorld.py
	
	# vim HelloWorld.py
	------------
	#!/usr/bin/python  -> tell the shell which command interpreter should be used 
	
	Print "Hello World"
	------------
	# chmod a+x HelloWorld.py
	# ./HelloWorld.py

Customary "Hello World" 

	• Interactive Mode
	• Script 

Module 1: Exercise 1 

	• Install Python 3.x in Ubuntu 11.10
	• How can you switch between different versions of Python? 
		○ Console
		○ Script 
	• Explore Virtualenv in Python 


Part 2: Variables and Data Types

Variables, Objects and References 

Variable:
	
	>>> name = "melih" 
	>>> name 
	'melih' 

Object: 
	
	>>> id(name)
	23177904
	>>> 
	>>> name.__repr__
	<method-wrapper '__repr__' of str object at 0x161aab0>
	>>> 
	>>> hex(id(name))
	'0x161aab0'

	# mkdir Lesson-2
	# cd Lesson-2
	# python 
	>>> name = "melih"
	>>> name
	'melih'
	>>> name = 10
	>>> name 
	10 
	
	>>> id(name)
	231312313 -> memory location where the object is stored
	>>>
	>>> name.__repr__
	<method-wrapper '__repr__' of str object at 0x161aab0>
	>>>
	>>> hex(in(name))
	'0x161aab0'
	>>>

Data Types

	• Strings
	• Numbers
	• Lists 
	• Dictionaries
	• Tuples
	• Boolean
	• ..

Strings 

	• Definition
		○ name = "melih"
		○ name = 'melih'
		○ name = 'melih's1
		○ name = "melih\ntas1
		○ name = r'melih\ntas' (raw string turns off escaping)
		○ name = """
				Melih
				Tas
			"""

Unicode String

	• Used for Internationalization
	• "wide characters" are they are called 
	• Name = u'melih'
	• Unicode to regular string conversion
		○ str(name)
	• Regular string to unicode conversion
		○ unicode(name) 

	>>> name = u'melih'
	>>> name 
	u'melih'
	>>> str(name)
	>>> 'melih' 
	>>> unicode("melih")
	u'melih

String Operations 

	• Strings are immutable objects in Python
	• Concatenating strings 
		○ s1 + s2
	• Repeated sequence in string
		○ buffer = "A"*20
	• Slicing - breaking up the string
		○ string[start:end:steps]
	• Int to String
		○ str(42)

String Methods

	• String.find()
	• String.replace()
	• String.split()
	• …

String Formatting

	• "Hack this IP: %s" %ip 
	• "Hack %s with IP %s" (domain, ip)
	• "Hack %(domain)s with IP %(ip)s" %{"domain" :"securitytube.net", "ip" : "192.168.1.10"}

Numbers

	• Integers, floats etc. can be represented.
	• Operators
		○ + - * / 
		○ x**y (x to the power y)
		○ (> = <  >=  <=  ==)
		○ x|y, x^y, x&y (bitwise operators)
		○ x and y, x or y, not x (logical operators)

Lists

	• Collection of objects which can be heterogeneous
	• myList = [1,2,3,4]
	• myList = [1, 'melih', 'SPSE', 2.5]
	• myList = [1, [3,4, 'hello'], [3,4], 2, 3]
	• len(myList)
	• len(myList[1])

List Operations

	• Concatenate [1,2] + [3,4] = [1 2,3,4]
	• Append -- list.append()
	• Extend -- list.extend([])
	• Reverse -- list.reverse()
	• Pop -- list.pop()
	• Insert -- list.insert(indexi item)
	• Delete -- del list[index] 


Part 3: Data Types: Tuple, Sets, Dictionaries 

Tuple

	• Similar to list but immutable 
	• Can conver from list to tuple and vice versa
		○ Tuple(list)
		○ List(tuple)
	• Video = ("Hello World", 5, 10, 0)
	• Sequence unpacking
		○ videoName, time, upvotes, downvotes = video 

Sets 

	• Unordered collection of unique objects 
	• List to set: b = set(a)
	• Set to list: a = list(b)
	• Set operations
		○ Union: a|b
		○ Intersection: a&b
		○ Difference: a-b
		○ … 

Dictionaries 

	• Unordered key-value pairs
	• Keys are unique and immutable objects 
	• Value can change 
	• dict = {}, dict['name'] = 'melih'
	• dict(name='melih', age='31')
	• dict = {'name' : 'melih', 'age' : 31 }
	• Check if a given key is present
		○ dict.has_key(key)
		○ key in dict 

Dictionary Operations

	• Get tuple of items: dict.items()
	• Get list of keys: dict.keys()
	• Get list of values: dict.values()
	• Get a particular item: dict.get(key)
	• Item deletion
		○ All items: dict.clear()
		○ One item: del dict[key]
 
Getting Help on Methods etc. 

	• dir() - lists  all attributes 
	• help(string.replace) - list method help

	dir(myBio) 
	help(myBio.has_key)
	help(myBio.update)


Part 4: Conditional Statements

If Statement

	if test_condition1:
		do stuff
		do stuf
	elif test_condition2:
		do stuff
		do stuff
	elif test_condition3:
		do stuff
		do stuff
	else:
		do stuff

While Loops

	while statement_is_true:
		do stuff
		do stuff
	
	-break: get out of inntermost loop
	-continue: start the next pass of the innermost loop
	-pass: do nothing, placeholder 

Exercise

	• While loops can also have a "else" in Python 
	• Explore this functionality and write a simple program to illustrate

For loops

	for item in object:
		do stuff
		do stuff
	
	for item in [1,2,3]f
	for item in ['a', 2, '3']
	for (x,y) in [("melih", 31), ("ali", 25)]

Exercise

	• For loops can have a "else" statement as well
	• Write a simple program to illustrate this functionality 

Emulating C stype for loops

	C style loops: for (i=1; i<10; i++)
	
	Use range in python:
	
	Range(lower, upper, step) creates a list for use 
	range(n) - [0, ….., n-1]


Part 5: Functions

Functions

	• Functions allow sections of code to be grouped better as per functionality 
	• def function(arg1, arg2=default, ..):
		○ …
		○ …
		○ Return value 


Part 6: Classes and Objects 

Class

Class Calculator:

	def __init__(self, inp1, inp2):
		self.a = inp1
		self.b = inp3
	
	def sum(self):
		return self.a+self.b
	
	def product(self):
		return self.a*self.b

Inheritance 

Class ScientificCalculator (Calculator):

	def power(self):
		return pow(self.a, self.b) 

Exercise

	• What are Global, Class and Instance variables? 
	• How can we override a method in parent class? 


Part 7: Creating Modules

Modules

	• Better way of organizing code
	• Can define classes, functions and variable
	• import MODULE_NAME
	• from MODULE_NAME import … 


Part 8: Creating Packages

Packages

	• Hierarchical file directory structure to organize code 
	• Consists of modules and sub-packages 


Part 9: Exception Handling

Exceptions

	• Simply put exceptions are error conditions which disrupt the normal flow of the program
	• Python allows for a simple and elegant way to handle exceptions 

Exercise 

	• Python allows for user defined exceptions
	• Code up a demo which has a user defined exception and an example use case 


Part 10: Python on Other Devices

Python on the iPhone (iOS)

	• On a Jailbroken iPhone using Cydia
		○ Install Python scripting support 
	• Original Blog post by Sauirik: http://www.saurik.com/id/5
	• Can do a ton of stuff! Lets read the SMS DB
	• With restrictions - Python for iOS
	http://itunes.apple.com/us/app/python-for-ios/id485729872?mt=8&ign-mpt=uo%3D4 

Python on Android 

	• Scripting Layer for Android: http:///code.google.com/p/android-scripting/
	
Exercise: Install the scripting layer on your Android Phone and try the previous demo 

Python in your Wi-Fi Router 

	• Open Source firmwares such as DD-WRT support running Python on them: http://www.dd-wrt.com/site/index
	
Exercise: Purchase a DD-WRT compatable router (DLINK DIR-615 E4) and run python on it 