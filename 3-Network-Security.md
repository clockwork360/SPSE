Part 1: Client - Server Programming Basics 

Network Programming 

	• Socket Programming 
		○ TCP and UDP sockets 
			§ Regular servers and clients
		○ Raw Sockets
			§ Sniffing and injection 

	Terminal #1:
	# vim Server.py 
	-----------
	#!/usr/bin/env python 
	
	import socket 
	
	tcpSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	
	# the line belows, allows the reuse the same socket if the connection fails on the server side. So you can keep the previous connection again. 
	tcpSocket.setSockopt(socket.SQL_SOCKET, socket.SO_REUSEADDR, 1)
	
	tcpSocket.bind(("0.0.0.0", 8000))
	tcpSocket.listen(2)
	
	print "Waiting for a client … "
	(client, ( ip, port)) = tcpSocket.accept()
	
	print "Received connection from : ", ip 
	
	print "Starting ECHO output …." 
	
	data = 'dummy' 
	
	while len(data) :
		data = client.recv(2048)
		print "Client sent: ", data
		client.send(data)
		
	print "Closing connection …"
	client.close()
	
	print "Shutting down server …"
	tcpSocket.close()
	-----------
	
	Terminal #2: 
	# python 
	>>> import socket 
	>>> tcpSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	>>> tcpSocket.bind(("0.0.0.0", 8000))
	>>> tcpSocket.listen(2)
	>>> (client, (ip, port)) = tcpSocket.accept()
	>>> client
	>>> ip
	>>> port 
	>>> client.send("Welcome to the SPSE course") 
	>>> data = client.recv(2048)
	>>> data 
	
	Terminal #3: 
	# ifconfig 
	--> inet addr: 192.168.56.102 
	
	# nc 192.168.56.102 8000 
	This is my first Python class! 
	
	Terminal #1:
	# chmod a+x Server.py
	# ./Server.py 
	
	Terminal #3: 
	# nc 192.168.56.102 8000 

Process Client Operations 

	• Process clients sequentially and one at a time 
	• Multi-threaded server 
	• Multi-process server 
	• Non-blocking sockets with select (multiplexing) 

Exercise 

	• Create a simple Echo Server to handle 1 client
	• Create a multi-threaded echo server 
	• Create a multi-process echo server
	• Create a non-blocking multiplexed echo server using select() 

Part 2: SocketServer Framework 

SocketServer 

	• Framework in Python to create TCP and UDP servers 
	• Does all the basic steps for you in the background
	• Comes in handy if you want to create a server to lure a client and analyze its behavior 

	Terminal #1: 
	# ls
	# cd Lab/ 
	# vim Server.py 
	# ./Server.py 
	
	Terminal #2: 
	# vim Client.py 
	----------
	#!/usr/bin/env python 
	
	import socket
	import sys 
	
	tcpSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM9
	
	tcpSocket.connect((sys.argv[1], 8000))
	
	while 1: 
		userInput = raw_input("Please enter a string: ")
		tcpSocket.send(userInput)
		Print tcpSocket.recv(2048)
	
	tcpSocket.close()
	----------
	
	# chmod a+x Client.py
	# ./Client.py 127.0.0.1
	Please enter a string: Hi 

Module Usage 

	• Has to be subclass of BaseRequestHandler
	• Override handle() to process request
	• Call handle_request ot serve_forever to process clients
	• For TCP Servers
		○ self.request is the client socket
		○ self.client_address is the client details 

	# vim TcpServer.py 
	----------
	#!/usr/bin/env python 
	
	import SocketServer
	
	class EchoHolder(SocketServer.BaseRequestHandler) :
	
		def handle(self):
			print "Got Connection from : ", self.client_address
			data = 'dummy'
			
			while len(data) :
				data = self.request.recv(1024)
				print "Client sent: " + data 
				self.request.send(data)
			print "Client left"
			
	serverAddr = ("0.0.0.0", 9000)
	server = SocketServer.TCPServer(serverAddr, EchoHandler)
	server.serve_forever()
	----------
	Terminal #1: 
	# python TcpServer.py 
	
	Terminal #2: 
	# nc localhost 9000 
	Hi 

Creating an ECHO server 

	• Code and Demo 

Exercise 

	• Is this server multi-threaded? 
	• Code up the multi-threaded version of the SocketServer. 


Part 3: Creating a Web Server 

How does a Web Application Server work? 

	• Listen on port 80 / 443 
	• Wait for client requests (GET, POST, HEAD …)
	• Process Request
		○ Serve files
		○ Execute CGI scripts 

Simple Web Application Server 

	• SimpleHTTPServer class
	• Implement do_GET()

	• Can be used to serve exploit code to a client
	• Can be used to penetration test client side code 

	Very basic one. 
	
	# vi BasicWebServer.py 
	-----------
	#!/usr/bin/python 
	
	"""
			Creating a Simple HTTP Server
	""" 
	
	import SocketServer
	import SimpleHTTPServer
	
	httpServer = SocketServer.TCPServer(('', 10001), SimpleHTTPServer.SimpleHTTPRequestHandle)
	httpServer.serve_forever()
	------------
	http://localhost:10001 
	
	
	Adding some features on it. If it get a GET request, there will be handler working. 
	
	# vi WebServer.py
	-----------
	#!/usr/bin/python 
	
	"""
			Creating a Simple HTTP Server
	""" 
	
	import SocketServer
	import SimpleHTTPServer
	
	class HttpRequestHandler (SimpleHTTPServer.SimpleHTTPRequestHandler) :
		def do_GET(self) :
			if self.path == '/admin' :
				self.wfile.write("This page is only for Admins!')
				self.wfile.write(self.headers)
			else :
				simpleHTTPServer.SimpleHTTPRequestHandler.do_GET(self)
	
	httpServer = SocketServer.TCPServer(('', 10000), HttpRequestHandler)
	httpServer.serve_forever()
	-----------
	
	# python WebServer.py 
	
	http://localhost:10000 
	http://localhost:10000/admin

Exercise 

	• Is there a module available to run CGI as well? 
	• Please write a PoC for the above. 


Part 4: Packet Sniffing with Raw Sockets 

Raw Socket Basics 

	Raw sockets provide a way to bypass the whole network stack traversal of a packet and deliver it directly to an application.
	Multiple ways to create raw sockets. We will concentrate on the PF_PACKET interface.


PF_PACKET 

	• It is a software interface to send/receive packets at layer 2 of the OSI i.e. device driver 
	• All packets received will be complete with all headers and data
	• All packets sent will be transmitted without modification by the kernel to the medium
	• Supports filtering using Berkley Packet Filtering 

Creating Raw Sockets 

	• Use the socket module
	• Read packets
	• Interpret and analyze them
	• Can send out responses as well 

Understanding Packet Headers



Extracting Binary Data into Variables 

	• Struck.unpack()
	• Returns tuple format
	• First character indicates Byte Ordering 
		○ Network Byte Order is Big-Endian 
		


	# python 
	>>> import struct 
	>>> struck.pack("B", 1)
	>>> struck.pack("H", 1)
	>>> struck.pack(">H", 1)
	>>> struck.pack("!H", 1)
	>>> struck.pack("!L", 1)
	>>> struck.unpack("!H", 1)
	
	# vim /usr/include/linux/if_ether.h
	--> define ETH_P_IP   0x0800 
	
	You have to have root privileges in order to use raw sockets. 
	
	# vim 
	--------
	#!/usr/bin/python 
	
	import socket
	import struct
	import binascii 
	
	rawSocket = socket.socket(socket.PF_PACKET, socket.SOCK_RAW, socket.htons(0x0800))
	
	pkt = rawSocket.recvfrom(2048)
	
	ethernetHeader = pkt[0][0:14]
	eth_hdr = struct.unpack("!6s6s2s", ethernetHeader)
	binascii.hexlify(eth_hdr[0])
	binascii.hexlify(eth_hdr[1])
	binascii.hexlify(eth_hdr[2])
	
	ipHeader = pkt[0][14:34]
	ip_hdr = struct.unpack("!12s4s4s", ipHeader)
	print "Source IP address: " + socket.inet_ntoa(ip_hdr[2])
	print "Destination IP address: " + socket.inet_ntoa(ip_hdr[2])
	
	# initial part of the TCP header
	tcpHeader = pkt[0][34:54]
	tcp_hdr = struct.unpack("!HH16s", tcpHeader)
	--------
	
Exercise 

	• Create a Packet Sniffer using Raw Sockets which can parse TCP packets 
		○ Parse invidivual fields

	• Create a sniffer which uses a filter to only print details of an HTTP packet (TCP, Port 80)
		○ Also dump the data

	Note: How to make your interface on promiscious mode - take this as an exercise. 
	
	# sudo bash 
	# ifconfig eth0 promisc up
	# ifconfig 
	-> you will see PROMISC flag is up 


Part 5: Packet Injection with Raw Sockets 

Packet Injection 

	• Ability to inject raw packets into the network
	• Powerful as we can stimulate responses from the network
	• Packet construction not scalable with raw sockets 

Inject Random Stuff :) 

	• If you can inject random data into the network 
		○ You know you can send anything them :) 

	# vi Injector.py
	----------------
	#!/usr/bin/python 
	
	import socket 
	import struct 
	
	rawSocket = socket.socket(socket.PF_PACKET, socket.SOCK_RAW, socket.htons(0x0800))
	rawSocket.bind(("eth0", socket.htons(0x0800))) 
	packet = struck.pack("!6s6s2s", '\xaa\xaa\xaa\xaa\xaa\xaa', '\xbb\xbb\xbb\xbb\xbb\xbb', '\x08\x00') 
	rawSocket.send(packet + "Hello there")  
	----------------
	
	Terminal #1: 
	# sudo python 
	>>> import socket 
	>>>  import struct 
	>>> rawSocket = socket.socket(socket.PF_PACKET, socket.SOCK_RAW, socket.htons(0x0800))
	>>> rawSocket.bind(("eth0", socket.htons(0x0800)))
	>>> packet = struck.pack("!6s6s2s", '\xaa\xaa\xaa\xaa\xaa\xaa', '\xbb\xbb\xbb\xbb\xbb\xbb', '\x08\x00')
	>>> packet
	>>> len(packet)
	>>> rawSocket.send(packet + "Hello there")  
	
	Terminal #2:
	# tcpdump -i eth0 --v -XX 
	
	Terminal #1: 
	>>> rawSocket.send(packet + "Hello there")  
	
	Terminal #2:
	# tcpdump -i eth0 --v -XX  "not port 22" 

Exercise 

	• Send an ARP Request Packet using Raw Sockets
	• Verify the same with tcpdump or wireshark 


Part 6: Packet Sniffing with Scapy 

3rd Party Libraries 

	• Raw sockets are painful to use and not too portable across Oss
	• Use of 3rd party libs: 
		○ Pylibpcap
		○ Pycapy
		○ Pypcap
		○ Lmpacket
		○ Scapy
	• We will use Scapy in this course, because it's the most powerful and flexible among all other options 

Scapy 

	• Interactive mode or use as library
	• Can be used for packet sniffing and forging
	• Tons of protocols already implemented 
	• Allows to build "reactive" tools 

http://www.secdev.org/projects/scapy/doc/usage.html 

	# sudo scapy 
	>>> ls() 
	>>> conf
	>>> lsc()

Protocol Layers Available 

	• ls() -> shows the supported protocols 
	lls(IP)
	• IP().show()
	• lsc() --> list commands 
	• conf

Sniffing with Scapy 



	# vim Scapy-Basics.py 
	-----------------
		# finding protocols and details 
		ls()
		ls(IP)
		IP().show()
		
		# Sniffing packets 
		pkts = sniff(iface="eth1", count=3)
		hexdump()
		
		# Simulating sniffing with a offline pcap capture 
		pkts = sniff(offline="offline.pcap")
		
		# Adding filters 
		pkts = sniff(iface="eth1", filter="arp", count=3)
		
		# Print packets live 
		pkts = sniff(iface="eth1", filter="icmp", count=20, prn=lambda x: x.summary())
		
		# Write packets to a pcap file 
		wrpcap("demo.cap", pkts)
		
		# read from pcap
		rdpcap("demo.cap")
		
		# export packet asBase64 encoded
		export_object(str(pkts[0])) 
		
		# import packet from Base64
		newPky = import_object()
		
		# export packets as string
		str(pkts[0])
		
		# import packet from string
		Firstheader e.g. Ether(pkt_string)
	-----------------
	
	# sudo scapy 
	>>> pkts = sniff(iface="eth1", count=3)
	>>> pkts 
	>>> pkts[0] 
	>>> pkts[1] 
	>>> pkts[2] 
	
	>>> pkts[0].show()
	----> shows the beautifully formatted output
	
	>>> hexdump(pkts[1])
	
	>>> wrpcap("demo.cap", pkts)
	>>> read_pkts = rdpcap("demo.pcap") 
	>>> read_pkts 
	>>> read.pkts[0]
	>>> read.pkts[1]
	>>> read.pkts[2]
	
	>>> pkts = sniff(iface="eth1", filter="icmp", count=3)
	
	Terminal #2: 
	# ping google.com 
	
	>>> pkts 
	>>> pkts[0] 
	>>> pkts[1] 
	
	>>> pkts = sniff(iface="eth1", filter="icmp", count=30, prn=lambda x: x.summary())
	-----> will show incoming data in summary realtime 
	
	Terminal #2: 
	# ping google.com 
	
	>>> pkts = sniff(iface="eth1", filter="icmp", count=30, prn=lambda x: x.show())
	-----> will show incoming data with a lot more details realtime 
	
	Terminal #2: 
	# ping google.com 
	
	>>> icmp_str = str(pkts[0])
	>>> icmp_str
	
	>>> recon = Ether(icmp_str)
	>>> recon 
	
	>>> icmp_str
	>>> export_object(icmp_str)
	
	>>> newPkt = import_object()
	-----> paste a data here 
	
	>>> newPkt 
	>>> Ether(newPkt) 

Exercise 

	• Create a Packet sniffer with Scapy for HTTP protocol and print out 
		○ the HTTP headers
		○ data in GET/POST
	• Create a Wi-fi Sniffer and print out the unique SSIDs of the Wi-fi networks in your vicinity 


Part 7: Packet Injection with Scapy 

Packet Forging with Scapy 



	# sudo scapy 
	>>> pkt = IP(dst="google.com")
	>>> pkt 
	>>> pkt.show()
	
	>>> pkt = IP(dst="google.com")/ICMP()/"Melih was here" 
	>>> pkt.show()

Send Packets 

	• Sendp - Send packets at Layer 2. Need to give right interface etc. 
	• Send - send packets at Layer 3. Routing decided based on local table 
		○ loop on the same packet
		○ inter : time interval in seconds 

	# vi Scapy-Inject.py
	----------
		# Scapy Injection and Forging 
		sendp(Ether()/IP(dst="google.com")/ICMP()/"XXX", iface="eth1", loop=", inter=")
		
		# Send and Receive
		srp1(Ether()/IP(dst="google.com", ttl=22)/ICMP()/"XXX")
		r1(IP(dst="google.com"), timeout=3=
		
		# routing
		conf.route.add(host="192.168.1.10", gw="192.168.1.22")
		conf.route.resync()
	----------
	Terminal #1: 
	# sudo tcpdump -i eth1 -XX -vvv icmp 
	
	Terminal #2: 
	>>> pkt
	>>> send(pkt)
	
	>>> sendp(Ether()/IP(dst="google.com")/ICMP()/"XXX", iface="eth1")
	-----> send only 1 packets 
	>>> sendp(Ether()/IP(dst="google.com")/ICMP()/"XXX", iface="eth1", loop=1)
	-----> keeps sending packets 
	>>> sendp(Ether()/IP(dst="google.com")/ICMP()/"XXX", iface="eth1", loop=1, inter=1)
	-----> keeps sending packets in every 1 second

Send and Recive at Layer 2 and 3 

	• Layer 3
		○ sr()
			§ Returns answers and unanswered packets 
		○ sr1()
			§ Returns only answer or sent packets
	• Layer 2
		○ srp()
		○ srp1() 

	>>> sr(IP(dst="google.com")/ICMP()/"XXX")
	>>> response, no_response = _ 
	>>> response 
	>>> no_response 
	>>> response[0]
	
	>>> sr(IP(dst="google.com") timeout=5)
	
	>>> sr1(IP(dst="google.com")/ICMP()/"XXX")
	----> it just waits for single answer. 
	
	>>> conf.route 
	>>> conf.route.add(host="192.168.1.10", gw="192.168.1.22")
	>>> conf.route 
	
	>>> conf.route.add(net="192.168.10.1/8", gw="192.168.1.23")
	>>> conf.route 
	
	>>> conf.route.resync()
	>>> conf.route 

Injected Packet Routing 

	• Uses a local routes by default
	• Can be overridden by Scapy 
	• Modified routes can be flushed
	• Does not affect system routes 


Part 8: Programming with Scapy 

Using Scapy as a Library 

	• From scapy.all import Ether, IP, TCP, sr1
	• Use as you please in your program :) 
	• Really, really, powerful! 

	# sudo python 
	>>> from scapy.all import *
	>>> ls(ARP)
	>>>  pkt = Ether(dst="ff:ff:ff:ff:ff:ff"(/ARP(pdst='192.168.1.1', hwdst="ff:ff:ff:ff:ff:ff") 
	>>>  pkt
	>>>  pkt.show()
	
	>>>  srp1(pkt)
	
	>>>  range(1, 256) 
	>>> for lsb in range(1,256) :
	…        ip = 192.168.1." + str(lsb)
	…        arpRequest = Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=ip, hwdst="ff:ff:ff:ff:ff:ff")
	…        arpResponse = srp1(arpRequest, timeout=1, verbose=0)
	…        if arpResponse :
	…             print "IP: " + arpResponse.psrc + " MAC: " + arpResponse.hwsrc
	
	# vi ArpScanner.py
	------------
	#!/usr/bin/python 
	
	from scapy.all import *
	
	# Subnet Scanner 
	
	for lsb in range(1,50)
		ip = "192.168.1." + str(lsb)
		arpRequest = Ether(dst="ff:ff:ff:ff:ff:ff")/ARP(pdst=ip, hwdst="ff:ff:ff:ff:ff:ff") 
		arpRequest = srp"(arpRequest, timeout=1, verbose=0)
		if arpResponse :
			print "IP: " + arpResponse.psrc + "MAC: " + arpResponse.hwsrc
	------------
	# python ArpScanner.py 

ARP Scanner 

	• Create ARP Request packets for the local subnet
	• Send and receive responses 
	• Get results and publish
	• Find out how to get the local subnet automatically 

Exercise #1 

	• Create a DNS poisoning tool similar to DNSspoof using Scapy 
	• Create a ARP MITM tool using Scapy 
	• Create a TCP SYN Scanner using Scapy 

Exercise #2

	Explore how to create a Fuzzer with Scapy 
	Create a DNS Fuzzer with Scapy and try it against DNSspoof 
