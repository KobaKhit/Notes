#Notes
##Commands
### Terminal
#### Basic
+ `mv FILE DESTINATION` - Moves a file to the DESTINATION folder. Can also be used to rename files like so `mv FILE NEWFILENAME`. Ex.g., `mv oldname.py newname.py`. [Hint](http://askubuntu.com/questions/214560/how-to-move-multiple-files-at-once-to-a-specific-destination-directory): to move multiple files do `mv -t DESTINATION file1 file2 file3`.
+ `chmod +x FILE` - Make a file executable (needed for cron to be able to execute scripts)
+ `readlink -f FILE` - Get full path of file
+ `ps -ef` -  Show running processes
+ `grep CRON /var/log/syslog` - Show the cron jobs log
+ `nohup COMMAND` - Execute a command that wont stop after logging out the EC2. Stores output in`~/nohup.out` by default. To redirect output to a file
do `nohup COMMAND >> ~/FILEPATH`.  Ex.g., `nohup python ~/Shippy/myscript.py >> ~/myscript.out`. Redirecting output is optional. 
+ `crontab -l` - Show the cron jobs file
+ `crontab -e` - Edit the cron jobs file
+ `scp -r [FILE or FOLDER] DESTINATION` - upload files to EC2 or download files from EC2. Ex.g., to upload a folder called Shippy to my EC2 instance execute `scp -r ~/PATH/Shippy myec2:~/Shippy` on your local machine. To download a folder from EC2 to my local machine execute `scp -r myec2:~/Shippy ~/Desktop/Shippy` on your local machine.

## Instructions
### Schedule python scripts with cron
To schedule a python script cron job, in the beggining of the python script put shabang `#! /usr/bin/env python`. Then, make the python script file 
executable like so `chmod +x myscript.py`. Then, edit the crontab file using command `crontab -e`. To run it every 5 minutes write the following line
```
*/5 * * * * /usr/bin/python ~FILEPATH/myscript.py >> ~/myscript.out
```
It will run the python script every five minutes and redirect the output to `~/myscript.out`.
Read the article on [cron scheduling](http://www.thegeekstuff.com/2011/07/cron-every-5-minutes/) for further info. 
