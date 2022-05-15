
# Qlik Backup and Restore
![MicrosoftTeams-image](https://user-images.githubusercontent.com/28060254/168284699-3cd5dd19-9589-4ebf-b922-e0dcb4631409.png)

Migration process from Qlik Client-Managed to Qlik SaaS requires to move applications.
You can backup (export) applications from Qlik Client-Managed or Qlik SaaS and restore it into Qlik SaaS.

Qlik Client-Managed and Qlik SaaS have different User Models.
User Groups in Client-Managed vs Users in SaaS, it means SaaS is more secure focused and users more isolated.
**In SaaS Tenant Admin cannot change sheet ownership like in Client-Managed QMC.**

The process can be performed using Qlik-CLI as described [here in Christof Schwarz LinkedIn Article](https://www.linkedin.com/pulse/bulk-migrating-qlik-sense-apps-from-windows-saas-christof-schwarz/?trackingId=9/fD1KIVSUuDTxjiLD2dIw==).

    qlik app unbuild --app APP-ID

Qlik Client-Managed to Qlik SaaS Process
-
**1. Store Original Sheet Ownership information into JSON**

 - Why: To be able to restore and check App State before and after backup.
 - How To: Execute Qlik-CLI command:

![image](https://user-images.githubusercontent.com/28060254/168292995-4f55e07a-622b-4abd-9e79-09da177fea84.png)

    qlik app unbuild --app APP-ID

https://www.qlik.dev/libraries-and-tools/qlik-cli/app-unbuild

**2. Change Ownership of App User Objects to Admin who perform the Backup**

 - Why: To be able to capture Private App Ojects created by Other Users
 - How To:
   - In Client-Managed QMC filter App Objects by Application you need
   - Select all by Ctrl+LMB click
   - Edit all by button "Edit (#)"
![image](https://user-images.githubusercontent.com/28060254/168471459-1b6d0232-31cd-4f4b-b8f2-b7ea328ed3a3.png)
   - Change Owner to Current User who perform the Backup
![image](https://user-images.githubusercontent.com/28060254/168471522-088d2a9d-d8c2-419d-9362-91eb258ba5bb.png)
	
**3. (Optional) Rename Sheets (add user ID and Privacy Status)**

 - Why: To have clear vision to whom this objects belongs and published/private status
 - How To: Using Step#1 Info we loop thru App Objects using Qlik-CLI
	
**4. Export Application**

 - Why: Export QVF file into Local Storage to be able to Restore it into SaaS Tenant
 - How To: Using Qlik-CLI Export-QlikApp command

https://github.com/ahaydon/Qlik-Cli-Windows/
	
**5. Import Application into SaaS Tenant Admin private space**

 - Why: Admin Private Space used as Drop-Off location for all App Objects
 - How To: Can be performed by Qlik-CLI and Manually thru GUI

   - Qlik-CLI
   `qlik app import --file "C:\tmp\MyNewFile.qvf" --mode autoreplace`
     - We can Import and restore with data (if needed) --nodata false 
     - With “Autoreplace” feature, which will keep published/private status from current app (only for the user running the CLI command)
     - If not using “Autoreplace” new copy of app will be created (new ID), all sheets will become private for current user

   - Manually thru GUI

![image](https://user-images.githubusercontent.com/28060254/168472475-0105ba1a-c9a1-4a76-972e-d1926459ad1c.png)
	
**6. Publish Application to Managed Space**

 - Why: Now we share an Application with authorised Users
 - How To: 
   - Thru GUI

![image](https://user-images.githubusercontent.com/28060254/168472839-a0ac2d9c-4e58-4cf9-8932-8a27103c5b80.png) ![image](https://user-images.githubusercontent.com/28060254/168472871-0618c352-5c2d-4f9e-ad9e-f0c41bec7e41.png)

   - Using Qlik-CLI https://qlik.dev/libraries-and-tools/qlik-cli/app-publish

`qlik app publish [flags]`
	
**7. Publish app sheets**

 - Why: Sheets that are Private need to be promoted to Published to be availiable for another users
 - How To: 
   - Thru GUI

![image](https://user-images.githubusercontent.com/28060254/168473210-8873561a-4724-4cd6-a729-610770f7fa34.png)

   - Using Qlik-CLI https://qlik.dev/libraries-and-tools/qlik-cli/app-object-publish

`qlik app object publish sheetID -a appID`

**8. Users duplicate their own Sheets to make it Private**

 - Why: 
 - How To: 
![image](https://user-images.githubusercontent.com/28060254/168473305-8623233d-f805-4e63-84ed-04ec9b0c10c7.png)
	
**9. Admin cleans up all community sheets**

 - Why: 
 - How To: 
	 
In CM we can capture all objects in application by changing personal objects ownership to person who perform backup.
We can restore all backup objects into SaaS, but in admin ownership.
They can be shared by publishing obj and duplicated by users to have their personal copy.
Clean-up process after can reduce obj amount after.

**10. Tenant Admin can enable externa IdP to have the same UserIDs**

![image](https://user-images.githubusercontent.com/28060254/168296062-f0ff2ec7-bfcd-4a88-b9e2-4431aafd705a.png)

By default, no user will be the same on Qlik Cloud. It depends on how you configure your Identity Provider (IDP). The "UserID" in Qlik SaaS returns the "sub" (subject) field of the IDP, which can be some meaning-less surrogate key (for example Qlik's default IDP until you configure another, will return values like "auth0|a08D00001RNqXIAX") and this will break Section Access functionality or the use of OSUser(). But you can change that (not with Qlik's default IDP, but when you configure your own). I mananged that the IDP sends the user-prefix of the email as sub ... christof.schwarz

The "UserDirectory" field is unknown to Qlik SaaS, unless you use the "Realm" field of an IdP with a static text value. In my case above, I set it to the same text, that under Windows was my Active Directory name "GITDIR". And then only, I was still the same as before, and Section Access and OSUser() in apps will still work without change.

Qlik SaaS to Qlik SaaS Process (same or another tenant)
-
- QAA is good point to start from and to try it
- qlik-cli is better, you have more control over the export process
- qlik-cli supports switching user context (loop thu users shared their API keys)
- QAA vs qlik-cli conclusion





ATM we have no full backup options, other user's private content cannot be reached.
-

We can recommend the use of qlik-cli and QAA to backup current user and published objects.
-



Windows or other tools scheduling with qlik-cli
-

We can put some info about backup from CM and restore into SaaS
-







Backup/restore apps in the cloud QAA
How to: Qlik Application Automation for backing up and versioning Qlik Cloud apps on GitHub?
https://community.qlik.com/t5/Knowledge/How-to-Qlik-Application-Automation-for-backing-up-and-versioning/ta-p/1835917
	- Need to be updated to use the below templates
	- GitHub templates uses Base64 encoding, which does NOT include any app data!
	- Other user’s (not running the Automation) private objects are completely HIDDEN from tenant Admin at this time.
	- To be able to catch all user’s private objects, they need to be impersonated -> each user needs an API key.
	- QAA can not loop through multiple API keys to impersonate.

![image](https://user-images.githubusercontent.com/28060254/168032934-9bd96927-edfc-4243-813d-9113031d8025.png)


Qlik CLI commands (more detailed control)
-
Export/Import through Qlik CLI
`qlik app export 368afaef-9b49-4140-b7fd-6b98873332cd --output-file "C:\tmp\MyNewFile.qvf“`
- Export process does NOT include any Published/Private status about sheets




- 

qlik app object publish sheetID -a appID

    qlik app object publish 51099afe-2dab-4476-a8dd-9263682ad681 -a 71bb562d-468b-4bb3-892d-decdc0d6b13e

We have tested a lot of CLI functions to get sheet attributes, but could not find anything useful
- How to get attributes about sheets before export?
