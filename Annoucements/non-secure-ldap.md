---
layout: default
title: Non-secure LDAP Issue
parent: Announcements
nav_order: 2
---

## What
During our regular security audit for CES, we noticed that we have Cisco Secure Email Cloud Gateway customers that are using port 3268 or 389 for LDAP sessions. By default, there is no security provision for these ports, making you vulnerable to man-in-the-middle attacks.

> <details>
>   <summary> What is a Man in the Middle Attack? </summary>  
>
> Man-In-The-Middle (MiTM) is an Attacker or an Observer which is between the Protocol Client and the Protocol Server.
> 
> A Man-In-The-Middle Attacker is typically trying for impersonation of the Protocol Client or the Protocol Server.
> 
> Secure connections are methods to attempt to prevent Man-In-The-Middle
> </details>

We request you to change your LDAP ports to 3269/636 ASAP!
[block:callout]
{
  "type": "info",
  "body": "Most Active Directory servers use the following ports:\n* port 3268\n* port 3269 (SSL)\n\nMost OpenLDAP servers use the following ports:\n* port 389\n* port 636 (SSL)",
  "title": "Examples of Active Directory/LDAP ports"
}
[/block]
## When
[block:callout]
{
  "type": "warning",
  "body": "We suggest you take this task as a high priority for better security!\n\nCisco Secure Email Cloud Gateway Ops will be closing ports 3268 and 389 for LDAP on July 29, 2022.",
  "title": "NON-SECURE PORT CLOSURE DATE IS JULY 29, 2022"
}
[/block]
## Customer Action
### Configuring your Email Gateway with Secure LDAP Ports
1. From your ESA UI, click on **System Administration > LDAP**
2. In the LDAP Server Profiles section, click on your configured Server Profile
3. In Server Attributes, review the configuration and the *Port* in use
> :pencil2:  If you are currently using port 3268 or port 389, please work with your LDAP administrator and assure port 3269 or port 636 are opened and available for your LDAP host.
4. Replace the port you have configured with the updated port: 3268 > 3269 or 389 > 636
5. Click the "Use SSL" option under Connection Protocol
6. Click the **Test Server(s)** button in the Server Attributes section
7. If your test is successful, scroll to the bottom of the page and click **Submit** 
> :heavy-check-mark:  You may also wish to scroll through your LDAP configuration and run **Test Query** for enabled queries.
8. From the upper-right corner of the UI, click **Commit Changes** and complete the configuration change at this point on the Gateway

## More Resources + Information
* [User Guide for AsyncOS 14.0: LDAP Queries](https://www.cisco.com/c/en/us/td/docs/security/ces/user_guide/esa_user_guide_14-0/b_ESA_Admin_Guide_ces_14-0/b_ESA_Admin_Guide_12_1_chapter_011011.html?bookSearch=true#:~:text=GD%20(-,General,-Deployment)%20-%20LDAP%20Queries) 
* [Configuring Azure AD DS](https://docs.ces.cisco.com/docs/using-azure-ad-ds-with-ces)

If you have any urgent questions about this maintenance or the possible impact, please contact [Cisco TAC](http://www.cisco.com/c/en/us/support/web/tsd-cisco-worldwide-contacts.html).
