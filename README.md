# scriptWorking
Working of the automatic pentesting tool  
# Automatic Pentesting  
The pentesting of a web application can be automated with a website in order to conserve time and cover a wider scope comapared to manual pentest . This tool uses scans the wesite i.e starts visiting all the pages on the website one at a time and stores the address of all the unique url it encounter on every page and stores it in a deque to visit for next time . The tool is made to find the following vulnerabilites  
1. **XSS**  
2. **SQL Injection**  
3. **XXE**  
4. **SSRF**  
5. **Subdomain Takeover**  
6. **Missing security Headers**  
7. **Host Header Injection**  
8. **CORS**  
9. **Sensitive Data Leak**  

## Step 1  
**Crawling** :- is the first step and it is the most important step as soon as the script starts , the crawler function in **scraping.py** is called it takes two arguments the website name and the file in which the urls which are crawled have to be saved . There are two crawling function based on the authentication   
1. Crawling the website as **unauthenticated user** (Takes two arguments website name and the filename to save processed urls)  
2. Crawling the website as **authenticated user** (Takes three arguments website name , filename to sava processed urls and the cookie for the authenticated user)  
The crawler starts visting the website from the index page and finds all the **anchor** and **HTML tags with src attribute** and the **links in javascript** it does this by using the python requests library and beautifulSoup library used to scrape data from the website .  
After visiting all the links it saves it in the **demofile.txt** internally and it also takes care for authenticated user that it does not loose the sesssion so , it avoids visiting the urls which contain **logout** or **signout** keywords in them .  
To make sure it visits all the url the it uses a script **waybackurls.py** it searches the web archives for the urls which belong to the target website which are mainly from the starting i.e when the website was created till now . It is a very rich source for urls which usually a manual pentest might miss it can contain the function that was used by the website in the starting and they forgot to remove it from the website and it vulnerable to some attack .  

## Step 2  
**Menu** :- it is the interface for the user , it helps the user with what is the tool does and makes the **navigation for the user very easy** it helps the user choose which bug they want to test in the website . There are many tools which do not provide the menu and take very long time to produce the result unlike them here you can check only particular bug which is very time saving and helps you conserve time and gives you a list to checklist of bugs . A manual tester not having a checklist might miss to test some bug . It uses **figlet** a linux utility to make the animated text in the linux terminal to give it an animated look . 

## Step 3  
**Testing for specific bugs** :-
### XSS  
So first option to choose from the menu is the xss it stands for **Cross-Site Scripting** it is the most common bug in the mordern web application , very large enterprises are also vulnerable to this bug type . In XSS an attacker is able to execute the javascript on the victims browser . The script on entering the option 1 intiates the xss function in **xxs_check.py** file it takes two argument the website name and the cookie value if provided and then uses the **form_input.py** file to get all the urls with form tag in them and their input types names . After storing all the urls and input types in the list it uses the loop to get a single url and its and data and test if the url is vulnerable to xss or not . It cheks a single url is vulnerable by sending a unique such as **batman** in the input and checks if the response contains the same string in the response or not , if the string is present then it sends **<>/()** in the input and see if they are encoded or not . If not it has a list of **xss polyglots** which it submits in the request and  checks if the string is reflected in the response if present in the string
it print on the terminal the url is vulnerable to xss with the payload used in the attack .  

### SQL Injection  
It is the second option in the menu it the very old bug on found on the website and it is very less common now days because website using web frameworks now days and CMS such as Wordpress . It is very severe bug if a attacker is able to exploit this he can access data of any user and even change the data . In the script on choosing 2 option sqli function is called present in **check_sql.py** it takes two arguments the website name and the cookie if provided and gets input and urls as xss . After getting the links and the take for it sets up a difference between the normal request and the malicious request by ignoring the response if it contains terms such as **error** , **exception** , **SELECT** and many more and then uses the payload such as **'** . **"** , **\*** and many more to check if the what the response is if the response status code is like **500** **502** etc then it flags the url and print on the terminal as sql injection found with the payload .  

### XXE Injection  
It is the third option in the menu it is a very new bug in the modern web found on the website which contain the xml file to fulfill store the data of the user or to use xml to respond to the user with the requested data  . So to find XXE using the script we use call xxe function in **check_xxe.py** file it takes two arguments the website name and the cookie if provided . In this it changes **content type** of the request to **application/xml** for each request and check the difference between the length and status code of the response of the request with normal data and the request with the xxe payload and if the difference shows the possibility of the xxe attack it flags it as the xxe vulnerablility found the specific url with the specific payload . It uses **xxe polyglots** as the payloads .   

## SSRF  
It is the fouth option in the menu it is also a very new bug types in this the attacker if able to exploit it can get access to the private file on the server and port scan the network on which the server is present . To find this bug in the script we call the function ssrf in the **check_ssrf.py** file it takes two argument as website and the cookie if provided . In this we develop the differnce between the normal request and malicious payload . If there is the differnce between the status code and response length then we flag it as the ssrf bug found the specific payload with specific payload . We uss **ssrf polyglots** as the payload .  

## CORS  
It is fifth option in the menu it is also a fairly new bug type in this if the website allows different origin other than the domain it is having to send it a request and send response to that malicious origin than an attacker would change the origin header for the victim request and receive the response on his malicious server and make changes in the javascipt and compromise the user . To find this bug we change the **Origin** header of each request and and check if **Allow-Access-Control** header is **\*** or **null** or the name of the website in origin and flag it and print on the terminal as CORS found the url .  

## Missing Security Headers  
It is the sixth option in the menu it check if all the security headers which a website response should have to make it secure against the attacks are present or not . To find this the script call the check_security_header function in **security_header.py** file . It check the **X-Frame-Options** ,**X-XSS-Protection** and many more header if present or not if not present flags it as vulnerable and prints on the terminal as the missing particular security header .  

## Subdomain takeover  
It is the seventh option in the menu in this a attacker an attacker can claim a subdomain . To find this by the function check_subdomain is called in the **subdomain_takeover.py** file . The script uses the **https://crt.sh/?q=%25** to search the subdomains of the target domain and then request all found subdomain and if the respose status code is **404** or the string **Not Found** are present then the script flags it as subdomain takeover found and prints it on the terminal .  

## Sensitive Data Leak  
It is the eighth option in the menu in the attacker could get access to the sensitive file on the server which the developers forgot to remove or is by default present on the server . To find this bug we use a list of file which are by default present on the server and contain sensitive file . As we request for the specific file and its response status is **200** we request it as sensitive data leak on specific url and print it on the terminal .  

## Host Header Injection  
It is the ninth option in the menu in this an attacker can manually divert the code to produce the hacker's desired output, simply by editing the host header. Most probably web servers are configured to pass the host header to the first virtual host in the list without proper reorganisation. So It is possible to send the HTTP requests with arbitrary host headers to the first virtual host. In that case, if we specify an invalid Host means that the web server process it and pass the invalid host header to the first virtual host in the list. To find this bug we use function check_host in the **check_host_header.py** file it requests the url with **Host** and **X-Forwarded-Host** header in the request and checks the response if the malicious host is found it flags it as the host header injection found with the url .





