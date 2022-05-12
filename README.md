# CM2SaaS_Backup
 1. ATM we have no full backup options, other user's private content cannot be reached.
 2. We can recommend the use of qlik-cli and QAA to backup current user and published objects.
	 - QAA is good point to start from and to try it
	 - qlik-cli is better, you have more control over the export process
	 - qlik-cli supports switching user context (loop thu users shared their API keys)
	 - QAA vs qlik-cli conclusion
 3. Windows or other tools sceduling with qlik-cli
 4. We can put some info about backup from CM and restore into SaaS

Backup/restore apps in the cloud QAA

How to: Qlik Application Automation for backing up and versioning Qlik Cloud apps on Github
https://community.qlik.com/t5/Knowledge/How-to-Qlik-Application-Automation-for-backing-up-and-versioning/ta-p/1835917
	- Need to be updated to use the below templates
	- GitHub templates uses Base64 encoding, which does NOT include any app data!
	- Other user’s (not running the Automation) private objects are completely HIDDEN from tenant Admin at this time.
	- To be able to catch all user’s private objects, they need to be impersonated -> each user needs an API key.
	- QAA can not loop through multiple API keys to impersonate.

![image](https://user-images.githubusercontent.com/28060254/168032934-9bd96927-edfc-4243-813d-9113031d8025.png)


Qlik CLI commands (more detailed control)
Export/Import through Qlik CLI
- qlik app export 368afaef-9b49-4140-b7fd-6b98873332cd --output-file "C:\tmp\MyNewFile.qvf“
- Export process does NOT include any Published/Private status about sheets

- qlik app import --file "C:\tmp\MyNewFile.qvf" --mode autoreplace
- We can Import and restore with data (if needed) --nodata false 
- With “Autoreplace” feature, which will keep published/private status from current app (only for the user running the CLI command)
- If not using “Autoreplace” new copy of app will be created (new ID), all sheets will become private for current user

Sheets that are Private can be promoted to Published status using
- qlik app object publish <sheetID> -a <appID>
- qlik app object publish 51099afe-2dab-4476-a8dd-9263682ad681 -a 71bb562d-468b-4bb3-892d-decdc0d6b13e

We have tested a lot of CLI functions to get sheet attributes, but could not find anything useful
- How to get attributes about sheets before export?
