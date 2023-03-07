![plot](./alx-system-engineering-devops/0x19-postmortem/33i9zl.jpg)

<h1 align="left"> Postmoterm: </h1>

On November 8th at around 10 a.m., the server access went down resulting in 504 error for anyone trying to access a website. Background on the server being based on a LAMP stack.

<h2 align ="left"> Timeline: </h2>

    • 10:00 EAT - 500 error for anyone trying to access the website 
    • 10:05 EAT - Ensuring Apache and MySQL are up and running. 
    • 10:10 EAT - The website was not loading properly which on background check revealed that the server was working properly as well as the database. 
    • 10:15 EAT - After quick restart to Apache server returned a status of 200 and OK while trying to curl the website. 
    • 10:22 EAT - Reviewing error logs to check where the error might be coming from. 
    • 10:28 EAT - Check /var/log to see that the Apache server was being prematurely shut down. The error log for PHP were nowhere to be found. 
    • 10:33 EAT - Checking php.ini settings revealed all error logging had been turned off. Turning the error logging on. 
    • 10:37 EAT - Restarting apache server and going to the error logs to check what is being logged into the php error logs. 
    • 10:41 EAT - Reviewing error logs for php revealed a mistyped file name which was resulting in incorrect loading and premature closing of apache. 
    • 10:46 EAT - Fixing file name and restarting Apache server. 
    • 10:50 EAT - Server is now running normally and the website is loading properly. 

<h2 align="left">Root Cause and Resolution: </h2>

The issue was connected with a wrong file name being referred to in the wp-settings.php file. The error was raised when trying to curl the server, wherein the server responded with 500 error. 
Checking the error logs revealed that the no error log file was being created for the php errors and reading the default error log for apache did not result in much information regarding the premature closing of the server. 
The realization that the errors for php logs were not being directed anywhere prompted the reviewing of the error log setting for the php in the php.ini file which revealed that all error logging was turned off. 
Once turned on, the error logging the apache server was restarted to check if any errors were being registered in the log. As initially suspected, it was found out that the php log showed that a file with a .phpp extension was not found in the wp-settings.php file. Notably, this was clearly a misspelled error that resulted in the error to site access. Essentially, since this was one server that the error was found in, it was clear that this error might have been replicated in other servers as well. Ideally, an easy fix by changing the file extension by puppet would result in the fix being made to other servers as well. 
Eventually, a quick deployment of the puppet code replaced all misspelled file extensions with the right one and restarting of the server resulted in properly loading of the site and server.

<h2 align="left">Corrective and Preventive Measures </h2>

    • Testing is imperative in order to prevent such incidents and more importantly diminish errors that require unnecessary work such as a typographical error. Nevertheless reviewing code and monitoring servers for a time period are also good ways to correct and prevent these types of occurrences and at the same time focus on more hard to detect situations. 
    • All servers and sites should have error logging turned on to easily identify errors if anything goes wrong. 
    • All servers and sites should be tested locally before deploying on a multi-server setup this will result in correcting errors before going live resulting in less fixing time if site goes down. 
