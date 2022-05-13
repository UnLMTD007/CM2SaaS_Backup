
# Qlik Backup and Restore
![MicrosoftTeams-image](https://user-images.githubusercontent.com/28060254/168284699-3cd5dd19-9589-4ebf-b922-e0dcb4631409.png)

Migration process from Qlik Client-Managed to Qlik SaaS requires to move applications.
You can backup (export) applications from Qlik Client-Managed or Qlik SaaS and restore it into Qlik SaaS.

Qlik Client-Managed and Qlik SaaS have different User Models.
User Groups in Client-Managed vs Users in SaaS, it means SaaS is more secure focused and users more isolated.
**In SaaS Tenant Admin cannot change sheet ownership like in Client-Managed QMC.**

The process can be performed using Qlik-CLI as described [here in Christof Schwarz LinkedIn Article](https://www.linkedin.com/pulse/bulk-migrating-qlik-sense-apps-from-windows-saas-christof-schwarz/?trackingId=9/fD1KIVSUuDTxjiLD2dIw==).

Qlik Client-Managed to Qlik SaaS Process
-
1. **Store Original Sheet Ownership information into JSON**
	
2. **Change Ownership to Admin who perform Backup**
	
3. **Rename Sheets (add user ID and Publish Status)**
	
4. **Export Application**
	
5. **Import Application into SaaS private space**
	
6. **Publish Application to Managed Space**
	
7. **Publish app sheets**
	
8. **Users duplicate their own Sheets to make it Private**
	
9. **Admin cleans up all community sheets**
	 

In CM we can capture all objects in application by changing personal objects ownership to person who perform backup.
We can restore all backup objects into SaaS, but in admin ownership.
They can be shared by publishing obj and duplicated by users to have their personal copy.
Clean-up process after can reduce obj amount after.

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

`qlik app import --file "C:\tmp\MyNewFile.qvf" --mode autoreplace`
- We can Import and restore with data (if needed) --nodata false 
- With “Autoreplace” feature, which will keep published/private status from current app (only for the user running the CLI command)
- If not using “Autoreplace” new copy of app will be created (new ID), all sheets will become private for current user

Sheets that are Private can be promoted to Published status using
- 

qlik app object publish sheetID -a appID

    qlik app object publish 51099afe-2dab-4476-a8dd-9263682ad681 -a 71bb562d-468b-4bb3-892d-decdc0d6b13e

We have tested a lot of CLI functions to get sheet attributes, but could not find anything useful
- How to get attributes about sheets before export?
