# Configure Postfix on Ubuntu 14.04 ec2 instance
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Configure Postfix on Ubuntu 14.04 ec2 instance](#configure-postfix-on-ubuntu-1404-ec2-instance)
    - [Configure Godaddy settings](#configure-godaddy-settings)
    - [Configure Ubuntu 14.04 server](#configure-ubuntu-1404-server)
    - [Configure Postfix](#configure-postfix)
    - [Troubleshooting](#troubleshooting)
    - [Resources used](#resources-used)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

Prerequisites:
  - A Fully Qualified Domain Name (FQDN) which I got from GoDaddy
  - An ubuntu server 14.04 which I got from amazon web services

### Configure Godaddy settings
My domain is **agoodcompany.co** and the hostname of my ec2 instance is **koba**. To see the hostname of your ec2 instance just type `hostname`. To change the hostname edit the `/etc/hostname` file. Below are my DNS settings in the Godaddy dashboard:

    A Records
    Host | Points to
    @ | 52.26.86.55
    koba.agoodcompany.co | 52.26.86.55

    CNAME Records
    Host | Points to
    mail | koba.agoodcompany.co

    MX Records
    Priority | Host | Goes to
    0 | @ | mail.agoodcompany.co

where 52.26.86.55 is the elastic IP of my instance.

### Configure Ubuntu 14.04 server 
For Postfix to be able to recieve external email the server needs to listen on **port 25**. For the amazon web services this change is done in the **Security Groups** tab. Choose the security group your instance is using and add an inbound rule. It is going to be a Custom TCP rule. In the port section enter 25 and you are good.

In the `/etc/hosts` file add the following line:
    
    XX.XX.XX.XX your_hostname.your_domain.TLD your_hostname
    
where **XX.XX.XX.XX** is the elastic IP. In my case it looks like this:

    52.26.86.55 koba.agoodcompany.co koba

### Configure Postfix
Install Postfix:

    sudo apt-get install mailutils
  - Press ok and choose **Internet Site** option
  - Make sure that the system mail name is your_domain.TLD, e.g. agoodcompany.co. If it is your_hostname.your_domain.TLD change it to your_domain.TLD, e.g. koba.agoodcompany.co changes to agoodcompany.co.

Configure postfix:

    sudo nano /etc/postfix/main.cf
    
Below is my postfix configuration file `/etc/postfix/main.cf`. Make sure the General part in your file looks similar. I had to add `myhostname`,`relays_domains`,`mydomain`, and `inet_interfaces = all`.
    # See /usr/share/postfix/main.cf.dist for a commented, more complete version


    # Debian specific:  Specifying a file name will cause the first
    # line of that file to be used as the name.  The Debian default
    # is /etc/mailname.
    #myorigin = /etc/mailname
    
    smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)
    biff = no
    
    # appending .domain is the MUA's job.
    append_dot_mydomain = no
    
    # Uncomment the next line to generate "delayed mail" warnings
    #delay_warning_time = 4h
    
    readme_directory = no
    
    # TLS parameters
    smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
    smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
    smtpd_use_tls=yes
    smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
    smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
    
    # See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
    # information on enabling SSL in the smtp client.
    
    smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
    
    # General part
    myhostname = mail.agoodcompany.co
    relay_domains = agoodcompany.co
    alias_maps = hash:/etc/aliases
    alias_database = hash:/etc/aliases
    mydomain = agoodcompany.co
    myorigin = /etc/mailname
    mydestination = agoodcompany.co, koba.agoodcompany.co, localhost.agoodcompany.co, localhost
    relayhost =
    mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
    mailbox_command = procmail -a "$EXTENSION"
    mailbox_size_limit = 0
    recipient_delimiter = +
    inet_interfaces = all
    inet_protocols = all

Now, do `sudo service postfix restart`. At this point you should be able to send and recieve emails using your server. To check whether your server sends emails execute replacing user@example.com with the email you are sending to:

    echo "This is the body of the email" | mail -s "This is the subject line" user@example.com
    
To check whether your server can recieve external emails send an email from gmail or any other service to **username@your_doamin.TLD**. For me it is `ubuntu@agoodcompany.co`. Then in your ec2 instance type `mail` and you should see a new email in your inbox.

### Troubleshooting
- In case you don't see new emails after typing `mail` check whether port 25 is open byt typing `telnet your_domain.TLD 25` if the output has the folowing line, then the problem is something else:

        220 mail.your_domain.TLD ESMTP Postfix (Ubuntu)
    
### Resources used
- [Postfix: Ubuntu Configuration
](http://ubuntuforums.org/showthread.php?t=1371763)
- [PostfixBasicSetupHowto](https://help.ubuntu.com/community/PostfixBasicSetupHowto)
- [Postfix Dovecot, No External Emails Coming In](http://ubuntuforums.org/showthread.php?t=1281971)
 



    
    