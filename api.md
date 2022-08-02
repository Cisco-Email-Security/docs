---
title: "AsyncOS API"
slug: "api"
excerpt: "Cisco Secure Email Gateway & Cloud Gateway"
hidden: false
createdAt: "2019-05-13T12:43:14.646Z"
updatedAt: "2022-07-29T16:59:10.155Z"
---
About
-----

Cisco Secure Email supports Application Programming Interface (API) with:

- AsyncOS 13.x for Cisco Secure Email Gateway and newer
  - [Secure Email Gateway Programming Guides](https://www.cisco.com/c/en/us/support/security/email-security-appliance//products-programming-reference-guides-list.html)
- AsyncOS 12.x for Cisco Secure Email and Web Manager and newer
  - [Secure Email and Web Manager Programming Guides](https://www.cisco.com/c/en/us/support/security/content-security-management-appliance/products-programming-reference-guides-list.html)

The AsyncOS API is a representational state transfer API (<<glossary:RESTful API>>) based set of operations that provide secure and authenticated access to the email gateway reports, report counters, and tracking. You can retrieve the Gateway reporting, tracking, and quarantine data using the API.

> 🚧 API Supportability Note
> 
> For current AsyncOS releases, you can query for reporting, tracking, and quarantine data.  
> **Viewing or posting configuration changes are not supported at this time.**

For more information, please refer to the Swagger API help. To view the API help, access the new web interface of the appliance, click the help icon on the top right corner of the page and select API Help: Swagger.

> <details>
>   <summary>Swagger? </summary> 
>
> As part of the ng-login to the Gateway (formerly ESA) or Email and Web Manager (formerly SMA), the Swagger UI is built-in and available from the following URL: https\://\<hostname\>/swagger
>
> To fully utilze Swagger, please **Authorize** your user.  This will ensure that if you choose to **Try it out** in the UI, you will see results and you will also be presented cURL and Request URL command output that can be used outside of Cisco Secure Email.  (See _Authenticaion and Authorization_ in the next section of this guide.)
>
> </details>

This guide will introduce you to using API on the Cisco Secure Email Email and Web Manager. The guide assumes user accounts are created, users have Administrator or Cloud Administrator access, and the user has administrative knowledge with the Gateway or Email and Web Manager.

Prerequisites for Using AsyncOS API
-----------------------------------

To use AsyncOS API, you need:

- Gateway running AsyncOS 13.x or newer, or Email and Web Manager running AsyncOS 12.x or newer
- AsyncOS API is enabled using the ​web interface or CLI. [See Enabling AsyncOS API](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_01.html#task_1092221).
- Knowledge of:
  - HTTP is the protocol used for API transactions. Secure communication over TLS.
  - JavaScript Object Notation (JSON), which the API uses to construct resource representations.
  - JSON Web Token (JWT)
- A client or programming library that initiates requests and receives responses from the AsyncOS API using HTTP or HTTPS, for example, <<glossary:cURL>>. The client or programming library must support JSON to interpret the response from the API.
- Authorization to access the AsyncOS API. [See Authorization](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_01.html#con_1092269).

> 📘 Authentication and Authorization
> 
> When using the Swagger UI, user authentication and authorization is required to access and "try it out" for the API queries available. [See Authorization](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_01.html#con_1092269).
> 
> In the Swagger UI, before you use any query, please be sure that you authenticate with your username and password or by constructing a jwtToken for your username and password.
> 
> ### BasicAuth (http, Basic)
> 
> Basic authorization is the username and password used to log in.
> 
> ### UserSecurity (apiKey)
> 
> An apiKey value may be generated and used. This is the username and password in the Base64-encoded format or with a JSON Web Token (<<glossary:jwtToken>>).
> 
> >  <details>
> >
> > <summary> How to get jwtToken for Swagger UI use </summary>
> >
> > 1. Open the ng-login page (https\://\<hostname\>/ng-login)
> > 2. In your browser, open the **Developer Tools**
> > 3. Next, from the Developer Tool, go to the **Network** tab
> > 4. Select the option for **Preserve log**
> > 5. Enter your credentials, **Username** and **Passphrase**, and click **Log in**
> > 6. Upon successful log in, select any API call in the log and copy the jwtToken for use with Swagger UI authorization; the example here shows Microsoft Edge w/ Developer Tools open:
> >
> > ![](https://files.readme.io/d115ba8-jwtToken_developer_tools_1.jpg)
> >
> >
> >
> > Note: Use the same browser for the Swagger UI that you are using for ng-login.  Otherwise the jwtToken is invalidated.  The jwtToken is only authenitcated via the platform (browser) that the fetch and request headers are sent from.
> >
> > </details>
> 
>   
> 
> > <details>
> >   <summary>How to construct jwtToken for use in Postman </summary>  
> >
> > Note: Before you begin, you will need to have your **username** and **password** in [Base64](https://base64.guru/converter). 
> >
> > Next, the jwtToken can be constructed using a third party application or website. This example will use [Postman](https://www.getpostman.com/):
> >
> > 1. In Postman, open a new tab for a new request
> > 2. Change the request from GET to POST
> > 3. For the request URL, enter in **https\://\<hostname\>/sma/api/v2.0/login**
> > 4. In the tool bar options below the request URL line, select **Body** > **raw**
> > 5. Copy and paste in the following code:
> >
> > ```
> > {
> >     "data": {
> >         "userName": "<your Base64 userName string>",
> >         "passphrase": "<your Base64 passphrase string>"
> >     }
> > }
> > ```
> >
> > 6. Enter your Base64 strings for **userName** and **passphrase**
> > 7. Click **Send**
> > 8. The request should authorize and complete similar to:
> >
> > ![](https://files.readme.io/874b0f5-jwtToken_construct.jpg)
> >
> > 9. Now you are ready to configure Postman to use your jwtToken authorization.  Copy the jwtToken string and configure the **Authorization** tab for API:
> >
> > ![](https://files.readme.io/5e2b4ab-jwtToken_example.jpg)
> >
> > Note: The jwtToken constructed is only authenticated and valid for Postman.  The jwtToken is only authenitcated via the platform (application) that the fetch and request headers are sent from. 
> >
> > </details>

> 📘 Time/Date Strings
> 
> The startDate and endDate strings for API are as follows:
> 
> | Usage       | String Format                            | Example                      |
> | :---------- | :--------------------------------------- | :--------------------------- |
> | Swagger API | **YYYY-MM-DD**T**hh:mm**:00.000Z         | 2022-07-25T19:06:00.000Z     |
> | cURL        | **YYYY-MM-DD**T**hh**%3A**mm**%3A00.000Z | 2022-07-25T19%3A06%3A00.000Z |
> 
> (For cURL, %3A is encode for ":")
> 
> **Note: **The duration attribute supports only 00 as value in the minutes (mm) and seconds (ss) parameters.
> 
> If you enter in seconds, you will see this error:
> 
> ```Text Details
> {
>   "error": {
>     "message": "startDate and endDate attribute supports only 00 seconds and 000 microseconds.",
>     "code": "404",
>     "explanation": "404 = Nothing matches the given URI."
>   }
> }
> ```

Setup
-----

API Access for SMA
------------------

1. Locate the URL for admin access. It should look like: <https://dhxxxx-sma1.iphmx.com/>  
   (Where xxxx = your unique customer account number.) 

> 📘 Note
> 
> Please assure the version number upon entry and ensure its 12.x or above. If not, please contact your account team.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/00f3edb-2019-05-13_09-04-24.png",
        "2019-05-13_09-04-24.png",
        750
      ],
      "caption": "UI log-in for SMA"
    }
  ]
}
[/block]

2. Once logged in, ensure the API is enabled.  Navigate to **Management Appliance > Network > IP Interfaces**. Click on the interface listed; typically, this is the Management interface.  View the AsyncOS API section:

> 📘 Note
> 
> This step for CES administrators will require validation from a Cisco account team member or [Cisco TAC](https://mycase.cloudapps.cisco.com/case).  CES customers only have a ​Cloud Administrator account, which does not permit administrative access to view Network configuration.

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/284cd24-2019-05-13_09-12-28.png",
        "2019-05-13_09-12-28.png",
        1379
      ],
      "caption": "API configuration on the SMA's IP Interface"
    }
  ]
}
[/block]

3. Enable the API ports if they are not already enabled.  The associate ports for HTTP/HTTPS traffic will need to be allowed on the local network and firewall if not already configured.

Configure Your Preferred API Development Environment
----------------------------------------------------

You may download/utilize your own API environment or 3rd party software.  For the purpose of this guide, we will reference using [Postman](https://www.getpostman.com/).

1. Open Postman and create a new GET request:

![](https://files.readme.io/704746c-2019-05-13_09-28-36.png "2019-05-13_09-28-36.png")

2. Save your request using a name/description and file location:

![](https://files.readme.io/c77042c-2019-05-13_09-32-19.png "2019-05-13_09-32-19.png")

3. In your newly opened request window, click the **Auth** (Authorization) tab
4. Select _TYPE=Basic Auth_
5. Enter the same **username** and **password** that you log in to Cisco Secure Email
6. Click **Save**:

![](https://files.readme.io/f813fb7-2019-05-13_09-37-09.png "2019-05-13_09-37-09.png")

7. Input a simple API query in the requestor to test connectivity and click **Send**:  
   `https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/`  

If authorization is successful, you should be presented with: 

```
{
    "error": {
        "message": "Not Found.",
        "code": "404",
        "explanation": "404 = Nothing matches the given URI."
    }
} 
```

If not, the query would return:  

```
{
    "error": {
        "message": "Unauthorized.",
        "code": "401",
        "explanation": "401 = No permission -- see authorization schemes."
    }
} 
```

8. A second simple API query that should return results:  
   `https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/config/system_time?device_type=sma`

```
{
    "data": {
        "timezone": "America/Los_Angeles",
        "continent": [
            "America",
            "United States",
            "Los_Angeles"
        ],
        "time": "Mon May 13 07:02:36 2019 PDT"
    }
}
```

For complete details on Requests and Responses: [AsyncOS API Requests and Responses](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_01.html#con_1092312)

Using the API
-------------

Let's build out an API string.  First, you will need the default API URL:  
`https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/`

Second, we'll need a "resource" to focus on.  

Resources are the following:

- Calls related to **reporting**:  
  `  https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/reporting/`
- Calls related to **message tracking**:  
  `  https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/message-tracking/`
- Calls related to **quarantines**:  
  `  https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/quarantine/`

Within each of the "resources", there is a multitude of counter groups that you can retrieve:  
[Reporting](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_010.html#concept_an1_jlh_mdb)  
[Tracking](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_010.html#id_66316)  
[Quarantines](https://www.cisco.com/c/en/us/td/docs/security/security_management/sma/sma12-0/api/b_SMA_API_12/test_chapter_010.html#id_91759)

Finally, you may need other notable variables:

- Device type: **device_type=esa**  
- Device name: **device_name=esa1.hcxxx-xx.iphmx.com**  
  (there are # of ESA devices powering the CES account: esa1, esa2, esa3, etc)  
- Group name: **device_group_name=Hosted_Cluster**
- Date/Time format: **startDate=YYYY-MM-DDT00:00:00.000Z&endDate=YYYY-MM-DDT00:00:00.000Z**

Scenario #1: Incoming Mail Summary
----------------------------------

Counter query adding totals across all ESAs grouped in Hosted_Cluster – Incoming Mail Summary Stats by category

Query:

```
https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/reporting/mail_incoming_traffic_summary?startDate=2019-05-01T19:00:00.000Z&endDate=2019-05-12T23:00:00.000Z&device_type=esa&device_group_name=Hosted_Cluster
```

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/69b473f-2019-05-13_14-11-07.png",
        "2019-05-13_14-11-07.png",
        854
      ],
      "caption": "Sample Output (truncated screenshot, using the \"Pretty\" output option in Postman)"
    }
  ]
}
[/block]

Scenario #2: AMP
----------------

Counter query adding totals AMP hits for all ESAs in Hosted_Cluster

Query:

```
https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/reporting/mail_incoming_traffic_summary/detected_amp?startDate=2019-05-01T19:00:00.000Z&endDate=2019-05-12T23:00:00.000Z&device_type=esa&device_group_name=Hosted_Cluster
```

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7accc3e-2019-05-13_14-13-16.png",
        "2019-05-13_14-13-16.png",
        856
      ],
      "caption": "Sample Output (truncated screenshot, using the \"Raw\" output option in Postman)"
    }
  ]
}
[/block]

Scenario #3: Output query from Message Tracking
-----------------------------------------------

Query:

```
https://dhxxxx-sma1.iphmx.com/sma/api/v2.0/message-tracking/messages?startDate=2019-04-01T01:00:00.000Z&endDate=2019-05-02T09:36:00.000Z&ciscoHost=All_Hosts&searchOption=messages&offset=0&limit=20
```

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/cfe1840-2019-05-13_14-16-36.png",
        "2019-05-13_14-16-36.png",
        855
      ],
      "caption": "Sample Output (truncated screenshot, using the \"Pretty\" output option in Postman)"
    }
  ]
}
[/block]

Troubleshooting
---------------

> 📘 Note
> 
> For CLI access, please see: [CLI access > connect2ces.sh](https://docs.ces.cisco.com/docs/connect2ces)

Create and view an API log from the CLI:

```
sma1.hcxxx-yy.iphmx.com> logconfig

Choose the operation you want to perform:
- NEW - Create a new log.
- EDIT - Modify a log subscription.
- DELETE - Remove a log subscription.
- SETUP - General settings.
- LOGHEADERS - Configure headers to log.
- HOSTKEYCONFIG - Configure SSH host keys.
[]> new

Choose the log file type for this subscription:
1. Cisco Text Mail Logs
2. qmail Format Mail Logs
3. Delivery Logs
4. Bounce Logs
5. Status Logs
6. Smartlicense Logs
7. Domain Debug Logs
8. Injection Debug Logs
9. SMTP Conversation Logs
10. System Logs
11. CLI Audit Logs
12. FTP Server Logs
13. HTTP Logs
14. NTP logs
15. LDAP Debug Logs
16. Scanning Logs
17. Spam Quarantine Logs
18. Spam Quarantine GUI Logs
19. Reporting Logs
20. Reporting Query Logs
21. Updater Logs
22. SNMP Logs
23. Tracking Logs
24. SMA Logs
25. Safe/Block Lists Logs
26. Authentication Logs
27. Upgrade Logs
28. Haystack Logs
29. Backup Logs
30. API Logs
31. CTR Logs
[1]> 30

Please enter the name for the log:
[]> mysma_api

Log level:
1. Critical
2. Warning
3. Information
4. Debug
5. Trace
[3]>

Choose the method to retrieve the logs.
1. FTP Poll
2. FTP Push
3. SCP Push
[1]>

Filename to use for log files:
[api]>

Would you like to append system based unique identifiers like $hostname, $serialnumber to the log filename? [N]>

Do you want to configure time-based log files rollover? [N]>

Please enter the maximum file size. You can specify suffixes: "m" for megabytes, "k" for kilobytes. Suffixes are case-insensitive:
[10485760]>

Please enter the maximum number of files:
[10]> 
```

Once you have committed the changes post-log creation, you can then run **tail** to monitor the log:

```
sma1.hcxxxx-yy.iphmx.com> tail mysma_api

Press Ctrl-C to stop.
Mon May 13 07:25:10 2019 Info: Begin Logfile
Mon May 13 07:25:10 2019 Info: Version: 12.0.0-478 SN: 420D4B6FA42Exxxxyyyy-CE7Dxxxxyyyy
Mon May 13 07:25:10 2019 Info: Time offset from UTC: -25200 seconds
Mon May 13 07:25:22 2019 Info: 68.123.123.123 - - 13/May/2019 07:25:22 -0700 GET /sma/api/v2.0/config/system_time?device_type=sma HTTP/1.0 200 -
Mon May 13 07:25:36 2019 Info: User joe_user not authorized
Mon May 13 07:25:36 2019 Info: 68.123.123.123 - - 13/May/2019 07:25:36 -0700 GET /sma/api/v2.0/config/appliances?device_type=sma HTTP/1.0 401 -
Mon May 13 07:25:47 2019 Info: 68.123.123.123 - - 13/May/2019 07:25:47 -0700 GET /sma/api/v2.0/config/centralized_services?device_type=sma HTTP/1.0 200 -
```

The following are some of the events that are logged in the API logs:

- API has started or stopped
- Connection to the API failed or closed (after providing a response)
- Authentication succeeded or failed
- Request contains errors
- Error while communicating network configuration changes with AsyncOS API 

Additional References
---------------------

Programming Guides:

- [Secure Email](https://www.cisco.com/c/en/us/support/security/email-security-appliance//products-programming-reference-guides-list.html)
- [Secure Manager](https://www.cisco.com/c/en/us/support/security/content-security-management-appliance/products-programming-reference-guides-list.html)

User Guides:

- [Secure Email](https://www.cisco.com/c/en/us/support/security/email-security-appliance/products-user-guide-list.html)
- [Secure Manager](https://www.cisco.com/c/en/us/support/security/content-security-management-appliance/products-user-guide-list.html)