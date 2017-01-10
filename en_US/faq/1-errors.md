# Errors you may see while maintaining iRedMail server

[TOC]

## Postfix

### Intended policy rejection, please try again later

Sample error message in Postfix log file:

> Jul 24 06:43:08 mx0 postfix/smtpd[12719]: NOQUEUE: reject: RCPT from sender.com[xx.xx.xx.xx]: 451 4.7.1 <recipient@my-domain.com>: Recipient address rejected: __Intentional policy rejection, please try again later__{: .red }; from=<sender@sender-domain.com> to=<recipient@my-domain.com> proto=SMTP helo=<mx2.sender.com>

This error is caused by greylisting service, sender server will retry to
deliver the same email, and your server will accept it after few retries.

* For more technical details about Greylisting, please visit:
    * [Homepage: What is greylisting?](http://greylisting.org)
    * [Articles about greylisting](http://greylisting.org/articles/)
* To manage greylisting service, please read iRedAPD tutorial: [Manage iRedAPD: Greylisting](./manage.iredapd.html#greylisting)

### Sender address rejected: not logged in

Sample error message in Postfix log file:

> Jun 24 11:57:13 mx1 postfix/smtpd[2667]: NOQUEUE: reject: RCPT from
> mail.mydomain.com[1.2.3.4]: 553 5.7.1 <sombody@my-domain.com\>: __Sender address
> rejected: not logged in__{: .red }; from=<sombody@my-domain.com\>
> to=<receipent@receipentdomain.com\> proto=ESMTP helo=<client_helo.com\>

This error is caused by incorrectly configured mail client application, not a
server issue.

All mail users are forced to perform SMTP auth before sending email, so you
must configure your mail client applications (Outlook, Thunderbird, ...) to
enable SMTP authentication.

### Sender address rejected: not owned by user user@domain.ltd

This error is caused by restriction rule `reject_sender_login_mismatch` in
Postfix parameter `smtpd_recipient_restrictions`, in file `/etc/postfix/main.cf`:

```
smtpd_recipient_restrictions =
    ...
    reject_sender_login_mismatch,
    ...
```

It will reject the request when $smtpd_sender_login_maps specifies an owner
for the MAIL FROM address, but the client is not (SASL) logged in as that MAIL
FROM address owner; or when the client is (SASL) logged in, but the client
login name doesn't own the MAIL FROM address according to $smtpd_sender_login_maps.
Check [manual page of Postfix configuration file](http://www.postfix.org/postconf.5.html#reject_sender_login_mismatch) for more details.

Removing `reject_sender_login_mismatch` and restarting Postfix service fixes
this issue.

!!! note

    If you want to allow some users to send as other users, or allow all users
    to send as their alias addresses, or allow member of mail list/alias to send
    as mail list/alias, you should try iRedAPD plugin `reject_sender_login_mismatch`
    instead (requires iRedAPD-1.4.4 or later releases).

    Read comments in file `/opt/iredapd/plugins/reject_sender_login_mismatch.py`,
    then enable it in iRedAPD config file `/opt/iredapd/settings.py` (`plugins = `),
    restart iRedAPD service. That's all.

### Recipient address rejected: Sender is not same as SMTP authenticate username

If the smtp authenticate username is different than the address in mail header
`From:` field, you will get this rejection (by iRedAPD).

Solutions:

* If you don't need to send as different sender, please update your mail
  composer (like Outlook, Thunderbird, webmail, your own script used to send
  email, etc) to use same address as smtp authenticate username and sender
  address in `From:`.
* If you do need to send as different sender address (`From:`), please add one
  setting in iRedAPD config file `/opt/iredapd/settings.py`:

```
ALLOWED_LOGIN_MISMATCH_SENDERS = ['user@mydomain.com']
```

Notes: `user@mydomain.com` is the email address you used for smtp authentication.

### unreasonable virtual_alias_maps map expansion size for user@domain.com

Sample error message in Postfix log file:

> Feb 11 19:59:06 mail postfix/cleanup[30575]: warning: 23C334232FB3:
> __unreasonable virtual_alias_maps map expansion size__{: .red } for
> user@domain.com -- deferring delivery

It means the maximal number of addresses that virtual alias expansion produces
from each original recipient exceeds hard limit, please either increase the
hard limit (default is 1000), or reduce alias members.

To increase the limit to, for example, 1500, please add below setting in
Postfix config file `/etc/postfix/main.cf`:

```
virtual_alias_expansion_limit = 1500
```

Reference: [Postfix Configuration Parameters](http://www.postfix.org/postconf.5.html#virtual_alias_expansion_limit)

### Helo command rejected: ACCESS DENIED. Your email was rejected because the sending mail server does not identify itself correctly (.local)

It means sender mail server uses a FQDN hostname which ends with `.local` as
HELO identity. `.local` is not a valid top level domain name, and all mail
servers should use a valid domain name which is resolvable from DNS query.

Two solutions:

1. Temporarily remove this HELO check rule on YOUR server, in file
   `/etc/postfix/helo_access.pcre` (Linux/OpenBSD) or
   `/usr/local/etc/postfix/helo_access.pcre` (FreeBSD), then reload Postfix
   service.
1. Ask sender server system administrator to correct their HELO identity, they
   will experience same issue while sending email to others.

## Amavisd

### connect to 127.0.0.1[127.0.0.1]:10024: Connection refused

This error means Amavisd service is not running, please try to start it first.

* RHEL/CentOS/FreeBSD: ```# service amavisd restart```
* Debian/Ubuntu: ```# service amavis restart```
* OpenBSD: `# /etc/rc.d/amavisd restart` or `# rcctl restart amavisd`

After restarted amavisd service, please check its
[log file](./file.locations.html#amavisd) to make sure it's running.

Notes:

* At least 2GB memory is required for a low traffic mail server. If your
  server doesn't have enough memory, Amavisd and ClamAV may be not able to
  start, or stop running automatically after running for a while. If it's just
  a testing server, you can follow
  [our tutorial](./completely.disable.amavisd.clamav.spamassassin.html) to
  disable some features of Amavisd to keep it running, or disable it completely.
