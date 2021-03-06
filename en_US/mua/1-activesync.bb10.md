# Exchange ActiveSync: Setup BlackBerry 10 devices

!!! note "Important Notes"

    * SOGo provides __EAS (Exchange ActiveSync)__ support, but not __EWS
      (Exchange Web Service)__.
    * __Outlook__ 2013, 2016 for Windows works well with EAS.
    * Mainstream __mobile devices__ (iOS, Android, BlackBerry 10) work well with
      EAS, they can sync mails, calendars, contacts, tasks.
    * Apple Mail.app, and Outlook for Mac support EWS. But not EAS.
    * Outlook 2010 for Windows supports MAPI.

    iRedMail doesn't integrate [OpenChange](http://www.openchange.org) and
    [Samba4](http://www.samba.org) for native MAPI support.

## Requirements

* iRedMail-0.9.0 or later releases is required.
* You must choose to install SOGo groupware during iRedMail installation.

## Step-by-step configuration

1: Open application `Settings`:

![](./images/sogo/bb10.settings.png)

2: Click `Accounts`:

![](./images/sogo/bb10.settings.accounts.png)

3: Click `Add Account` at the bottom:

![](./images/sogo/bb10.settings.accounts.list.png)

4: Click `Advanced` at the bottom:

![](./images/sogo/bb10.settings.add.account.png)

5: Choose `Microsoft Exchange ActiveSync`.

![](./images/sogo/bb10.add.exchange.png)

6: Fill up the form with your server address and email account credential

* Description: `you can type anything here`
* Domain: `you can type anything here`
* Username: `your full email address`
* Email Address: `your full email address`
* Password: `password of your email account`
* Server Address: `your server name or IP address`
* Port: `443`
* Use SSL: checked

![](./images/sogo/bb10.exchange.1.png)
![](./images/sogo/bb10.exchange.2.png)

That's all.
