# OWASPjuiceshop
## Compromising Accounts
- Using Burp Suite on Kali Linux, I opened the proxy browser and proceeded to navigate to “192.168.141.128:3000”  where  the  website  in question  is  currently  being  hosted.  Once  the page was loaded, I navigated to the “login” or “account” page. This is when I inserted dummy information into the form and submitted it. I then went back into the burp suite where I moved forward (continued the requests to move the action forward) until the data for “email” and “password” appeared.
- Then I highlighted the entire code, right clicked for options, and sent it to “intruder”. Switching to      the      intruder      tab,      I      loaded      a      payload      from      Kali      Linux      under “/usr/share/wordlists/wfuzz/SQL.txt”.  Unfortunately,  no  results  from  these  SQL  Injections succeeded.
- That’s when I started researching the best SQL Injections that every attacker should use on a vulnerable  system-  When  I  came  across  the  SQL  query  “  ‘  or  1=1--  “,  which  proceeded  to succeed upon entry.


- Once I had access to the administrator account, I went searching for the admin page that was mentioned on the scoreboard.
- So using the address bar, I added “administration” to the end of the URL, and was able to successfully find the administration page.
- From this point, it was as simple as copying each account email for login, and then using the
SQL query “ ‘-- “ at the end of the string to bypass the password requirement of the login process.

- Starting with Bender, I inserted the following into the login form “bender@juice-sh.op’--” and then inserted “test” as the password (could have used any input).
- Repeating  this  same  process  for  Jim,  I  used  the  same  input  “jim@juice-sh.op’--”  and successfully logged in.
- Next, I used the same input on “uvogin@juice-sh.op’-- “ to compromise this account too.
- The final user account I decided to compromise was “amy@juice-sh.op. Using the same SQL hijacking adding “ ‘-- ” to the end of the user login email.

## OWASP 10 Findings
1. XXS. Using the search bar on the website, I was able to input JavaScript html code to activate an   alert   on   the   web   page   with   whatever   I   wanted.   Using   the   code   “<iframe" "src=""javascript:alert('Testing')"">” I was able to set off an alert with the text “Testing” on the webpage.
2. SQL Injection to access the main admin account, along with the SQL injection vulnerability that exposed any and every other account on the website. The process to do so was already documented under the “compromising accounts” section above.
3. Access  to  sensitive  data  without  authorization  was  another  issue  uncovered  with  some investigation. Extending the side menu and viewing the “About Us” page reveals something
rather strange with the text being displayed.

> - There is green text in hyperlink form that sticks out clearly different from the other text on the page.
> - Following  the  link  reveals  that  there  is  a  “  /ftp  “  directory  on  the  server  that  is unprotected from unauthorized web viewing.
> - After viewing most of the files in this unprotected directory, the file that was the most sensitive and devastating as a leak is the “acquisitions.md” file.

> - This file (though unprotected) is stated to be confidential and to not distribute it to anyone.

4. Repetitive registration exists on the registration page. To access this page, simply make sure you’re signed out of any account, then click “login” and at the bottom of the login form click “not a customer yet?”
> - Filling the form out with dummy data reveals that the password checking has an issue where if you write  the desired password, then confirm the same password below, after the two match- You can then change the first password and bypass the “same password” error check.
> - Clearly  seen above, the  top  password prompt  is  doable the length of the  “repeat password” entry, yet no error is being thrown. And, unfortunately, the form allows the submission of the different password lengths, but only keeps the first password with
the ability to log in. 

5. One challenge listed on the “score-board” portion of the website is to find a redirect that is no longer  promoted.  This  “Outdated  Allowlist”  vulnerability  can  be  found  anywhere  on  the website, as long as you start Google chrome’s “inspect” developer tool."

> - The “main.js” JavaScript file contains one long line of outdated code (listed as 2018). Doing a simple lookup (ctrl+f) of the work “redirect” will locate the link of a blockchain address used to send bitcoin.

6. A password strength issue can be uncovered with the admin account- Mainly leading towards a weak password policy in general. I was able to SQL inject into accessing the admin account, but we can use a similar attack setup with a different payload in BURP to obtain the password
through brute force.

- With “intercept on” selected within BURP (while also using the BURP Proxy), I was able to capture the POST request sent by my submission of dummy login data.
> - Highlighting this POST request, I then sent the entire thing to “Intruder” to start up an attack. The attack I decided to run on the account is a bruteforce-wordlist approach,
using a wordlist dedicated to default admin passwords on Kali Linux."

> - I selected only the password portion of the code for the payload marker, as I knew that the admin account is the account I wanted to try and crack.
> - Once the payload was selected , I started the attack."

> - The brute force attack was successful (indicated by the server returning a status of “200” with the password “admin123”."

7. Using another similar tactic as one of the previous exploits, the website has broken access control  as  an  attacker  can  delete  all  the  five-star  reviews  without  needing  to  confirm authentication beforehand. Using SQL injection (or brute forcing the admin password), the admin account has access to the backend “administration page”. This administration page not only displays all users on the website, but also displays their reviews and gives the option to delete them.

8. Another form of broken access control exists through the hijacking of an existing account and using that access  (through  SQL injection)  to  forge  reviews in their name  by  writing a  new review and then editing that review through BURP.
> - Using the SQL injection technique to bypass login credentials, we can use “jim@juice- sh.op'--” and input anything as a password to login as jim.
> - Then going to one of the products, such as the apple juice, simply write a review in Jim’s name."

> - Using BURP I was able to intercept the PUT request of the review submission. There, the  “message”  and  “author”  could  be  clearly  viewed  without  any  protection  or safeguards in place from repeating the PUT request. Therefore, I sent the code to
repeater for modification."

> - The “message” and “author” portions of the code can be freely edited with whatever user or review the attacker would want to say in their place. I decided just to modify Jim’s original negative review into a positive one- SUCCESS.

9. Sensitive data exposure due to the famous rapper “Mc. Safesearch” exposing his username and password through his hit song “Protect Ya Passwordz”. In the song lyrics, he reveals that his dog’s name “Mr. N00dles” with two 0’s instead of “o”’s is his password. He also reveals
that his username is his rapper’s name Mc. Safesearch.

> - I could have just filled out the form on the website straight up, but I decide to use BURP’s  repeater  functionality  to  repeat  my  password/username  guesses.  The
username ended up being “mc.safesearch@juice-sh.op” with the password of “Mr. N00dles”.

10.  The final finding stems from the vulnerability of being able to bypass the CAPTCHA security measure because of broken anti-automation. Starting at the “Customer Feedback” page, we see that there is a form of anti-automation presented in the form of a CAPTCHA math problem. Filling out the data in the forms correctly will return a status of 200 in BURP.

- Sending  the  code  into  “Repeater”  and  with  further  digging  there  is  something interesting discovered.

- There is a “captchaID” field along with the other original data we filled out in the form. This captchaID is associated with the answer given, so, if we repeat an attack with the same  captchaID  and  captcha  answer  for  each  request,  we  can  send  a  series  of requests  within  seconds  of  each  other  completely  bypassing  the  anti-automation
protection.

- Sending the code to “Intruder” allowed me to clear all the previous payload markers, because  all  we  want  to  do  is  send  the  same  request  15  times  without  delay  and bypass the captcha.
- I then changed the payload type into null payloads, and set the generation of those null payloads to a total of 15.
- Running this attack completed the challenge, allowing me to completely bypass the captcha field."
