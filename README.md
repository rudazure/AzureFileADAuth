# AzureFileADAuth

## This below is the logical sequence to implement Azure Files being authenticated through AD (kerberos) Domain Controller.

how it works:
Overview - Azure Files identity-based authorization | Microsoft Docs

Requirements:

	- onprems AD need to be synced to Azure AD
	- only supports with clients newer than Win7 or Win Serv 2008 R2

Prerequisites:

	- ADDS synced to Azure AD
	- a domain-joined VM (on-prems or Azure)
	- azure storage account

NOTE

	- you control share level access with identities synced to Azure AD
	- while managing file/share level access with on-prems ADDS

Step by step:

	1. create storage account for Az File Share
	
	2. enable ADDS authentication for your Azure File share
		a. download AzFilesHybrid module to use Powershell
		b. run Join-AzStorageAccountForAuth command
			i. there is caveats if using OU. read web page
			ii. the command will create a computer acc or service logon acc on AD for Storage Acc
		c. enable the feature on your storage account
		d. Confirm the feature is enabled

	3. assign share-level permissions to an identity
		a. configure share-level permission through Azure RBAC (portal or PS)
		
	4. configure directory and file level permission over SMB
		a. configure proper Windows ACL at root, directory or file level
		
		NOTE:
			§ RBAC works as high-level gatekeeper for the entire share.
			§ Windows ACL operates at granular level to determine what operations (read, write, etc) will be granted.
			§ Az Files supports the full set of basic and advanced Windows ACLs.
		
		b. To configure ACLs with superuser permissions, you must mount the share by using your storage account key from your domain-joined VM.
			i. mount a file share from a ps command
			ii. configure Windows ACLs with Windows File Explorer
			
	5. mount a file share from a domain-joined VM (to test it)

	NOTE:
	• Share-level Azure role assignment can take some time to take effect.
	
		a. mount the file share in a VM that is domain-joined to AD
	
	NOTE / Prerequisites:
		○ If you are mounting the file share from a client that has previously mounted the file share using your storage account key, make sure that you have disconnected the share, removed the persistent credentials of the storage account key, and are currently using AD DS credentials for authentication.
		○ Your client must have line of sight to your AD DS. If your machine or VM is out of the network managed by your AD DS, you will need to enable VPN to reach AD DS for authentication.
	
	
	6. Update the password of your Storage account identity in ADDS

		a. you may want to trigger password rotation for the account that represents your Storage Account at AD
		b. pay attention if you register this account in a OU or domain that enforces password expiration time
