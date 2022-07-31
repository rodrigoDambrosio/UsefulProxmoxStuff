# UsefulPromoxStuff

# Check temps

1- Install sensors ``` apt-get install lm-sensors ```

2- Configure sensors ``` sensors-detect ```
This will let you select which sensors you can sense.

3- If everything is setup correctly now you can use  ``` sensors ``` to check the temps.

# Setup Mail Service Proxmox

## Google Account

1- Enable 2FA in Google account settings
2- Create an app password

You will use the app password in the mail settings later.

## Proxmox

### Install libsasl2-modules package

```
apt update
apt install -y libsasl2-modules
```

### Create sasl_passwd file

``` nano /etc/postfix/sasl_passwd ```

and inside put your mail details

```smtp.gmail.com:puerto youremail@gmail.com:yourpassword```

### Give file permisions to the sasl_passwd file

``` chmod 600 /etc/postfix/sasl_passwd ```

### Create /etc/postfix/sasl_passwd.db

``` postmap hash:/etc/postfix/sasl_passwd ```

### Edit main.cf file

First comment with # the relayhost line

Edit main.cf file with nano and and add the following lines

``` nano /etc/postfix/main.cf ```

``` inet_protocols = ipv4
relayhost = smtp.gmail.com:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/Entrust_Root_Certification_Authority.pem
smtp_tls_session_cache_database = btree:/var/lib/postfix/smtp_tls_session_cache
smtp_tls_session_cache_timeout = 3600s 

```

### Execute postfix reload

``` postfix reload ```

Test if it works

``` echo "Mail body" | mail -s "Subject" mail@mail.com ```

In case you have queued messages from before you can delete the queue

``` postsuper -d ALL ```

If something is not working check the logs here

```
/var/log/mail.warn

/var/log/mail.info
```

# Sources
https://sendgrid.com/blog/how-to-flush-the-mail-queue-in-postfix/

https://www.digimoot.com/proxmox-setup-email-service/
