#Notes
##Commands
### Terminal
+ `chmod +x FILE` - Make a file executable (needed for cron to be able to execute scripts)
+ `readlink -f FILE` - Get full path of file
+ `ps -ef` -  Show running processes
+ `grep CRON /var/log/syslog` - Show the cron jobs log
+ `nohup COMMAND` - Execute a command that wont stop after logging out the EC2. Stores output in`~/nohup.out` by default. To redirect output to a file
do `nohup COMMAND >> ~/FILEPATH`.  Ex.g., `nohup python ~/Shippy/myscript.py >> ~/myscript.out`. Redirecting output is optional. 

### Crontab
+ `crontab -l` - Show the cron jobs file
+ `crontab -e` - Edit the cron jobs file

## Instructions
### Schedule python scripts with cron
To schedule a python script cron job, in the beggining of the python script put shabang `#! /usr/bin/env python`. Then, make the python script file 
executable like so `chmod +x myscript.py`. Then, edit the crontab file using command `crontab -e`. To run it every 5 minutes write the following line
```
*/5 * * * * /usr/bin/python ~FILEPATH/myscript.py >> ~/myscript.out
```
It will run the python script every five minutes and redirect the output to `~/myscript.out`.
Read the article on [cron scheduling](http://www.thegeekstuff.com/2011/07/cron-every-5-minutes/) for further info. 
