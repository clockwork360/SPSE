Part 1: Fetching Web Pages 

Fetching Web Pages 

	• Most basic of functionality to fetch data 
	• Urllib, urllib2
	• Allows for argument encoding
	• Note: Please install apache2 on our Ubuntu Server 

	# vi 
	-------------
		# Demo statements
		httpResponse = urllib.urlopen()
		
		# read data
		httpResponse.read()
		
		# response code
		httpResponse.code
		
		# groups URL
		url = "http://www.securitytube.net/groups?operation=view&groupId=10"
		
		# encoding it 
		args = { ' operation' : ' view' , 'groupId' : 10 } 
		encoded_Args = urllib.urlencode(args)
		urllib.urlopen(base_url + '?' + encoded_args) 
	-------------
	
	# python
	>>> import urllib
	>>> httpResponse = urllib.urlopen("http://localhost")
	>>> httpResponse.code
	>>> httpResponse = urllib.urlopen("http://localhost/asasa")
	>>> httpResponse.code
	
	>>> print httpResponse.read()
	
	>>> dir(httpResponse)
	>>> httpResponse.url
	>>> dir(httpResponse.headers)
	>>> httpReponse.header.items()
	>>> for header, value in httpResponse,headers.items():
	…        print header + ' : ' + value 
	
	>>> httpResponse.headers.keys() 
	
	>>> url = "http://www.securitytube.net/groups?operation=view&groupId=10"
	>>> base_url = "http://www.securitytube.net/groups?operation=view&groupId=10"
	>>> args = { 'operation' : 'view', 'groupId' : 10 }
	>>> encode_args = urllib.urlencode(args)
	>>> encode_args 
	
	>>> args = { 'operation' : 'view ab', 'groupId' : 10 }
	>>> encode_args = urllib.urlencode(args)
	>>> encode_args 
	
	----> space will replaced with + 
	
	>>> args = { 'operation' : 'view', 'groupId' : 10 }
	>>> encode_args = urllib.urlencode(args)
	>>> encode_args 
	
	
	>>> fp2 = urllib.urlopen(base_url + '?' +encode_args)
	>>> fp2.code 
	>>> fp2.read() 
	
	If you want to use POST then you should do as a seperate argument. 
	
	>>> fp2 = urllib.urlopen(base_url, encode_args)
	>>> fp2.code 
	405 -> it is not allowed, because not all the URLs support Post method. 

Exercise #1

	• If you try and download a very large file, then how do you monitor the progress? 
	• Hint: Research on urllib.urlretireve() to solve this problem. 

Exercise #2

	• Urlencode() does a bad job in handling special characters in the URL 
	• Hint: Research on .quote() and .quote_plus() and illustrate how they can help 


Part 2: Parsing HTML 

Understanding Data on the Web

	• Web data is primarily:
		○ HTML
		○ XHTML 
		○ XML
		○ JSON
	• Need a mechanism to receive this data and parse
	• Need a mechanism to generate this data and send 

Parsing HTML 

	• Hierarchical Data
	• Multiple Parsers
		○ LXML
		○ BeautifulSoup
		○ HTMLParser
		○ …
	• Challenges in HTML Parsing
		○ Non adherence to standards
		○ Most websites have broken HTML documents 

BeautifulSoup 

	• Fantastically easy to use
	• Version 4 onwards allows use of lxml and html5lib - handles bad HTML better 
	• Till version 3 was not so great at handling bad HTML 
	• Handles encoding very very well! 

Parser Comparison 


http://www.crummy.com/software/BeautifulSoup/bs4/doc/ 

	# python 
	>>> from bs4 import BeautifulSoup 
	>>> import urllib
	>>> html = urllib.urlopen('http://www.securitytube.net/video/3000
	>>> html 
	>>> html.code 
	
	>>> bt = BeautifulSoup(html.read(), "lxml")
	>>> bt.title
	>>> bt.title.string
	>>> bt.title.name 
	
	>>> bt.meta
	>>> bt.meta.next
	>>> bt.meta.next
	
	>>> allMetaTags = bt.find_all('meta') 
	>>> allMetaTags
	>>> allMetaTags[0]
	>>> allMetaTags[1]
	
	>>> allMetaTags[0]['content']
	>>> allMetaTags[0]['http-equiv']
	
	>>> allLinks = bt.find_all('a')
	>>> len(allLinks)
	>>> allLinks[0]
	>>> allLinks[1]
	>>> allLinks[1].string
	>>> allLinks[1]['href']
	
	>>> for link in allLinks:
	…        print link['href']
	
	>>> bt.get_text() 

Exercise 

	• Read the documentation of BeautifulSoup 4 and find other ways to iterate through tags and get to the juicy information. 


Part 3: Coding a Screen Scraper

Screen Scaper

	• Very very dependent on the HTML structure 
	• Slight chance might break scraper depending on how you have coded it.

	# python 
	>>> import urllib
	>>> from bs4 import BeautifulSoup
	>>> httpResponse = urllib.urlopen('http://securitytube.net/video/3000')
	>>> http.Response.code
	>>> html = httpResponse.read() 
	
	>>> bs = BeautifulSoup(html, "lxml")
	>>> descr = bs.find('div', id='description')
	>>> descr 
	
	>>> allLinks = descr.find_all('a')
	>>> allLinks
	
	>>> descr.get_text()
	
	>>> videoLink = bs.find('iframe', { 'title' : 'Youtube video player' } 
	>>> videoLink
	>>> videoLink['src']
	
	>>> forms = bs.find_all('form')
	>>> forms 
	

Part 4: Form Parsing and Submission With Mechanize 

Mechanize 

	• Based on the Perl module www:mechnize
	• Allows for stateful programming and browser emulation 
	• Very powerful yet easy way to work through a website 

	# python 
	>>> import mechanize 
	>>> br = mechnanize.Browser()
	>>> br.open('http://www.securitytube.net/video/3000')
	
	>>>  for form in br.forms() : 
	…         print form
	
	>>> br.select_form(nr=0)
	>>> br.form['q'] = 'defcon'
	>>> br.submit()
	
	>>> for link in br.links() :
	…        print link 
	
	>>> for link in br.links() :
	…        print link.url + ' : ' + link.text 

Exercise #1 

	• In the example shown we did not try and modify the hidden fields. Try to see how you can do it and send arbitrary data. :) 

Exercise #2

	• Install a vulnerable web application such as DVWA, OWASP Web Goad or other
	• Use mechnanize to try SQL Injection on form fields and deduce which fields are vulnerable to SQL injection 


Part 5: Stateful Web Application Browsing with Mechanize 

The Stateful Web 

	• Mechanize handles cookies by itself :) 
	• We need to understand how to "browse" the application
	• "click links", "fill and submit forms", "maintain state" 

http://student.securitytube.net/login/index.php 

http://student.securitytube.net/login/change_password.php?id=1 

	# python 
	>>> import mechnanize 
	>>> br = mechnanize.Browser()
	>>> br.open('http://student.securitytube.net/login/index.php')
	
	>>> for form in br.forms() :
	…         print form
	
	>>> br.select_form(nr=0)
	>>> br.form['username'] = 'demo-user' 
	>>> br.form['password'] = 'demouser1]M' 
	>>> br.submit() 
	>>> print br.response().read()  
	
	>>> for link in br.links() :
	…        print link.url + ' : ' + link.text
	
	>>> new_link = br.click_link(text='moodle[IMG]Change password')
	>>> br.open(new_link)
	>>> print br.response().read()
	
	>>> for form in br.forms() :
	….        print form 
	
	>>> br.open('https://gmail.com') 
	>>> print br.response().read()
	>>> for forms in br.forms() :
	…        print form

Exercise CookieJAR

	• Explore the concept of mechanize.CookieJar
	• Why is it useful?
	• Sample code to illustrate its functionality 

Exercise 

	• Explore http://seleniumhq.org/support/
	• Can you automate the current example in it? 


Part 6: XML Parsing and Web Services 

XML Parsing 

	• Can use the lxml parser
	• Can use BeautifulSoup as well

	# ls defcon.rss
	# python 
	>>> from bs4 import BeautifulSoup
	>>> xml = open('defcon.rss', 'r').read() 
	
	>>> bs = BeautifulSoup(xml, "xml")
	
	>>> for item in bs.find_all('item') :
	…        print item.title.string
	…        print item.link.string 

Exercise 

	• Web Services are an important part of web communication now
	• Zolera Soap Infrastructure http://pywebsvcs.sourceforge.net/zsi.html
	• Attack on WebGoat http://yehg.net/lab/pr0js/training/webgoat.php#Web_Services 


Part 7: Exercise Series 1 

Proxy Support Exercise

	• Investigate on how you can use Proxy support with:
		○ BeautifulSoup
		○ Urllib
		○ Mechanize 

Web Spider Exercise 
	• 
Create a Multi-Threaded Web Spider which
	• Takes a website and depth of spidering as input 
	• Download the HTML files only 
	• Inserts the HTML into a MySQL Database
		○ Design the schema
	• It also parses the forms on each page
		○ Interts into DB with details of form fields


Part 8: OWASP Top 10 Attack Scripting Exercise 

OWASP Top 10

	• A1: Injection
	• A2: Cross-Site-Scripting (XSS)
	• A3: Broken Authentication and Session Management
	• A4: Insecure Direct Object References
	• A5: Cross-Site Request Forgery (CSRF)
	• A6: Security Misconfiguration 
	• A7: Insecure Cryptographic Storage
	• A8: Failure to Restrict URL Access
	• A9: Insufficient Transport Layer Protection 
	• A10: Unvalidated Redirects and Forwards 

https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project

Massive Exercise :) 

	• For each of the OWASP Top 10 create Python scripts which can automate the testing of the vulnerability 
	• Vulneravle software to use: Mutillidae

http://www.irongeek.com/i.php?page=mutillidae/mutillidae-deliberately-vulnerable-php-owasp-top-10 

Further Study

	Offensive Python for Web Hackers talk at Blackhat 2010 by Nathan Hamiel and Marcin Wielgoszewski
	Video: http://www.securitytube.net/video/1142 