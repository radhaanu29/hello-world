# Table of contents

1. [Introduction](#introduction)
    1. [Document purpose](#doc)
    2. [References](#References)

2. [Prerequisites](#PreRequisites)
    1. [ServiceNow prerequistes](##ServiceNowprerequistes)
    2. [JIT/FTP Prerequisites](#JIT/FTPPrerequisites)
    3. [User Role Prerequisites](#UserRolePrerequisites)

3. [Implementation Instructions](#ImplementationInstructions)
    1. [Installation](#Installation)
        1. [Retrieving the Update Sets](#RetrievingtheUpdateSets)
        2. [Previewing and Committing Update Set](#PreviewingandCommittingUpdateSets)
    2. [Origin Energy Data](#OriginEnergyData)
        1. [Integration File Type](#IntegrationFileType)
        2. [Integration Account](#IntegrationAccount)
        3. [MID File Transfer](#MIDFileTransfer)
        4. [MID Transfer Fields](#MIDTransferFields)
    3. [System properties](#Systemproperties)
        1. [Set Default Location](#SetDefaultLocation)
        1. [Set Default email](#SetDefaultemail)
        1. [Set Default name](#SetDefaultname)
     4. [Business Service Configuration](#[BusinessServiceConfiguration)
        1. [Approval Group Configuration](#ApprovalGroupConfiguration)
        2. [Support Group Configuration](#SupportGroupConfiguration)
        3. [Owned by and Assigned to Configuration](#OwnedbyandAssignedtoConfiguration)

	5. [Create Run as User](#CreateRunasUser)
    6. [Create Incident Template](#CreateIncidentTemplate)
    7. [ServiceNow / MID Server Package Implementation](#ServiceNowMIDServerPackageImplementation)
4. [Back Out Instructions](#BackOutInstructions)
    1. [Backing Out Update Set](#BackingOutUpdateSet)
5. [Limitations and Known Issues](#LimitationsandKnownIssues)
6. [Appendix](#Appendix)
    1. [Install MID Server](#InstallMIDServer)
    2. [Batch Files-Sample Snapshot for Reference](#BatchFiles-SampleSnapshotforReference)



# Introduction

<a name = "doc">

## Document purpose

</a>

The purpose of this document is to provide installation and configuration instructions for the ‘Origin Energy Users to ServiceNow Users’ integration. The main components are:

* “MID File Transfer” configurations for configuring transfers and transferred fields.
* The “MID File Transfer” mechanism includes an optional set of script includes for integration with the “Business Services” form.
* Script Includes, Script Actions, UI Policies and Business Rules on ServiceNow related to the functionality of this integration.

These required components are installed on ServiceNow.

The intended audiences for this document are:
* Service Management Development Studio
* Global Delivery Network (Service Management)

## References

|No | Author | Document Title | Filename / Repository
-|:-:|:-:|-
| 1|	Basu, Debarata |	ICD |https://dxcportal.sharepoint.com/sites/SMDevStudio/Shared%20Documents/Forms/AllItems.aspx?FolderCTID=0x012000D112D155D5D1C245869705B81202D754&id=%2Fsites%2FSMDevStudio%2FShared%20Documents%2FIntegrations%2FProducts%2FOrigin%20Energy%2FOriginEnergy%20People%20Data%20Integration
|2	| Basu, Debarata 	|DMW      |  https://dxcportal.sharepoint.com/sites/SMDevStudio/Shared%20Documents/Forms/AllItems.aspx?FolderCTID=0x012000D112D155D5D1C245869705B81202D754&id=%2Fsites%2FSMDevStudio%2FShared%20Documents%2FIntegrations%2FProducts%2FOrigin%20Energy%2FOriginEnergy%20People%20Data%20Integration	
|3	|RAGHU, NALINAKSHI 	|ADD 	| GD-PDXC-SN-INT-ARCH-ADD-Origin Energy to ServiceNow v1.0.docx



# Prerequisites

<a name = PR>

## ServiceNow prerequistes

</a>

The supplied components were developed on the Istanbul release of the ServiceNow platform.  It is expected that the target system implementing the ServiceNow components will have the latest blueprint updates in place, as well as any associated roles and related data that comprise the Blueprint system configuration.

## JIT/FTP Prerequisites

This integration requires SFTP access to a nominated JIT server. 

SNOW Mid File Transfer batch process setup/configure (Inbound transaction): The JIT server should be configured to move the file(s) from the “Outgoing” folder on the JIT/SFTP server to the desired target folders for access by the external system. 

The user that would typically be used to log in via SFTP to the JIT box should already have or get a private key generated that can be used by this integration. The key file should be available on a specific folder of Origin on the real MID Server.

<u> JIT SETUP : </u>

**User account for Origin Energy:**

Username: oetojit4

File Location: File to be placed under folder /Incoming   

File Name: DXC_SNOW_OG_PEOPLE_.20140123_143400.CSV

JIT Server Hostname: jitdcs4.csc.com

**User account from Origin Energy to Servicenow Origin Energy:**

Username: jit42oes  

File Location: File to be placed under folder /Outgoing   

File Name: Origin_Energy20140123_143400.CSV 

JIT Server Hostname: jitdcs4.csc.com  

**Midserver:**

<u> Pre-Prod: </u>

Name: cscmidtmdcpp01-preprod-sccmi

Hostname: cscmidtmdcpp01

IP Address: 20.138.56.125

<u> Prod: </u>

Host-name: cscmidtmdcpr02

IP address: 20.138.53.168 

## User Role Prerequisites

User with appropriate role/permission that should be created to access to the Users and Business Services tables on ServiceNow

# Implementation Instructions

## Installation

Installation of the “Origin Energy Users to ServiceNow Users” components will consist of:

* Retrieving the Update Sets
* Previewing and Committing Update Sets

**Note: You should first verify if the update sets as part of this release are already committed to the specific target environment you are intending to work from.  Only apply the update sets that have not yet been applied.**

The completed update sets for Origin Energy Users to ServiceNow Users are:

| Update Set | Has Configuration Records | Release 
-|:-: |-
| ORIGIN_People_Import_RITM0752463_v1.0 | Yes | 1.0.0
| ORIGIN_People_Import_Data_RITM0752463_v1.0 | No(data) | 1.0.0
| ORIGIN_People_Import_RITM0752463_v1.1 | Yes | 1.0.0
| ORIGIN_People_Import_Data_RITM0752463_v1.1 | No(data) | 1.0.0


### Retrieving the Update Sets

To retrieve completed update sets from another instance:

* If IP address access control is enabled on the source instance, set up the target instance as an exception.
* On the target instance, navigate to System Update Sets > Update Source and click New.
* Define the connection settings.

![remote instance](remote_instance1.png)

In this example, the name is ‘Dev – Parent’, and the URL is pointing to a demo instance. Here please use the URL https://cscdev.service-now.com to retrieve.

Supply the administrative user account name and password for credentials, and supply a description in the appropriate field.
* Click Test Connection	
    * If the connection is successful, a confirmation message appears.
    * If the connection fails, a warning message identifies the reason why connection failed. 
* If the connection fails, modify the settings to establish connectivity.
    * You must establish connectivity before you can save the connection settings 
    * You may want to modify the source instance (for example, change the password). 
* Right-click the form header and select Save.
* Under Related Links, click Retrieve Completed Update Sets.
    * Any update sets marked as Completed are transferred from the source instance to the target instance. Update sets that already exist on the target instance are skipped.
    * The confirmation page provides detailed messages about how many update sets were transferred and how many were skipped (starting with the Dublin release).
    * To view retrieved update sets, navigate to System Update Sets > Retrieved Update Sets

### Previewing and Committing Update Sets

Previewing compares an update sets retrieved from a remote instance to updates on the local instance and detects potential problems. You must preview an update set and address all problems before it can be committed.

NOTE:  The scope of this document is to provide basic instruction on how to retrieve, install and commit the release.  The specific techniques and methodologies for addressing problems or issues during update set validation are not covered in the supplied documentation and should be addressed by a qualified ServiceNow administrator.

To preview, install and commit the retrieved update sets:

1. Navigate to System Update Sets > Retrieved Update Sets.
2. Locate the first non-applied update set (update sets list from the above section 'Installation')
3. Open the update set.
4. Click Preview Update Set.

The preview completion page indicates when problems are detected.

![preview page](preview_page.png)

5. If no problems are detected, click Commit Update Set to commit all changes on the instance without reviewing the preview results.
6. If problems are detected, click Show Preview Problems.
7. Scroll down to the Update Set Preview Problems related list and address each problem.
8. When finished, navigate to System Update Sets > Retrieved Update Sets.
9. Locate the next update set
10. Repeat steps 3 thru 7 for all the update sets listed in section 'Installation'

At this point, the installation of the update sets is completed.

## Origin Energy Data

These are data configuration. They are included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0 and ORIGIN_People_Import_Data_RITM0752463_v1.1**

### Integration Master

The purpose of this table (u_integration_master) is to record the name of the integration on ServiceNow as well as to carry integration specific settings.

The data configuration of Integration Master table is included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0**

|Field name| Value | 
-|-|
| Integration name | Origin people import | 
| Billing service| Uncheck | 

### Integration file type

The purpose of this table (u_integration_filetype) is to carry the file types defined for this integration.

The data configuration of Integration File Type table is included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0**

|Field name| Value | 
-|-|
| Integration name | Origin people import | 
| Short description| Origin_People_User_Import| 
| MID File transfer | Origin people integration |
| Context | 

**Note:** The deployment engineer needs to update the Origin MID File Transfer record name into “MID File Transfer” field. They will update after completing “Copy Transfer” of **Origin People Integration**

### Integration account

The purpose of this table (u_integration_account) is to carry the settings of account (company) for this integration.

The data configuration of Integration Account table is included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0**

| Field name | Value 
-|-
| Number | Number is generated automatically 
| Integration ID | Origin people import 
| Company | Origin energy 
| Account code | OGES 
| Source of truth | 3rd party 
| Incident template | Origin MID Incident Template
| Active | Check 

### MID file transfer

This form will be used to configure for Users and Business Service transfer. The data configuration of MID File Transfer is included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0**

Navigate to the Integration File Transfers\ MID File Transfer \ MID File Transfers \

1. Find and open MID File Transfer record name is Origin People Integration 

2. Choose “Copy Transfer”, the copied MID File Transfer is populated with the named “Origin People Integration - [(Copied)]”. 

3. The name of MID File Transfer can be changed. After Copy Transfer, the deployment team or User must edit/input the name of MID File Transfer in Integration File Type table (section 'Integration file type') with the new named Origin People Integration that has copied.

These are configuration instructions on MID File Transfer form. The deployment team or User will follow this configuration guide to fill in the value for Origin MID File Transfer record. Then, deployment engineer needs to update this Origin MID File Transfer record name into Integration File Type table (section 'Integration file type') 

**Origin Energy Specific Configuration**

| Field name | Description / Values 
-|-
| Name | Origin People Integration
| Company | Origin energy |
| Direction	| Inbound
| Active | Checked
| Run By Other Transfer | Not Checked 
| Run | The type of schedule to run the transfer. Choices are:
| Run as | The Origin MID File Transfer will be run by “Run as” user. The value of “Run as” user that has appropriate role/permission to User and Business Service tables on ServiceNow. The value “Run as” of Origin MID File Transfer should be a user ID created for Origin Energy company. The “Run as” user is member of SEC: ServiceNow Integrations group (See section 'Business service configuration' for details) <br> - daily <br> - weekly <br> - Monthly <br> - Periodically<br> - Once<br>- On Demand <br> The value of this field and its associated fields (Day (of week/month), Repeat interval, Time) should be updated for a specific running. The value “Run” of Origin MID File Transfer is Periodically.
| Conditional | Not checked
| MID server | Real MID Server Name is used for getting Origin Energy data files from JIT server. The deployment team will decide to install specific MID Server Name for Origin Energy or use common MID Server Name with another integration. <br> **Note:** The value “MID Server” of Origin MID File Transfer can be MID Server Name Local. Users can install local MID Server on DEV TEST environment for testing. (Refer to Appendix 7.1 Install MID Server to install local MID Server)
| Directory on MID | The real folder of MID Server machine will get Origin data files from JIT server.<br><br>**The example value of “Directory on MID” field for Origin MID File Transfer using real MID Server :**<br>  X:\CSC\ORIGIN\in <br><br> **The example value of “Directory on MID” field for Origin MID File Transfer using local MID Server:**<br>\Local Folder\in
| Filename | The name of data file. The value “Filename” of Origin MID File Transfer is %FILENAMENOEXT%
| File extension | The extension of data file. The value “File Extension” of Origin MID File Transfer is %FILEEXT%
| Include Header | Checked
| Delimiter	| The value “Delimiter” of Origin MID File Transfer is comma (,). 
| Import Table | Origin People Import
| Command | The DXC integration have to the specific folder “Directory on MID” on real MID Server machine (\CSC\OGES)<br>The command is used to transfer Origin data files between “Directory on MID” and a FTP folder on JIT Server.<br>The structure path command:<br><br>\<folder>SFTPTransferIn.bat "%FILENAME%" "<private_key>.ppk" "\<user_name>@\<JIT server>" "\<Folder on JIT server>" "\<host key>".<br><br>\<folder>: specific folder of Origin on real MID Server that contains SFTPTransfer.bat code.<br><br>“%FILENAME%: can be used inside of the “Command” field and will be replaced with the contents of the “Filename” field concatenated with the optional date-time suffix.<br><br><private_key>: The key file to access to the JIT server.<br><br><user_name>:  User account of JIT server.<br><br>\<JIT server>:    JIT server.<br><br>\<Folder on JIT server>: Folder on JIT server where put Origin Energy data files.<br><br>\<host key>: The identifier identifying the JIT/SFTP server’s host key.<br><br>The example value of “Command” field for Origin MID File Transfer using real MID Server: (Developers tested successfully with this command)<br><br>D:\Originpeopleint\MidServer\SFTPTransferIn.bat "%FILENAME%" "Ottlabprikey.ppk" "Originjit4@20.137.0.49" "Outgoing" "9e:ac:f6:65:22:49:44:02:62:08:c9:6e:27:1b:b2:c4" "%CCODE%" "NOZIP" 60<br><br>_***After processing/importing the Origin Energy data file successfully, the Origin Energy data file will be moved to “archive” folder automatically._<br><br>The example value of “Command” field for Origin MID File Transfer using local MID Server:<br>\Local Folder\FileTransferIn.bat "%FILENAME%"
|Raise Incident on Failure | Checked
| Incident Template	| Origin MID Incident Template Note: The incident template is Origin MID Incident Template that can put here or can put in Integration Account table (section 'Integration account').

Run this script :

 ``` var gt = new GlideRecord('u_integration_filetype');
gt.addQuery('u_transfer', current.sys_id);
gt.query();
while (gt.next()) {
	var gr = new GlideRecord('cmdb_ci_service');
	gr.addEncodedQuery('u_file_type=' + gt.sys_id.toString() + '^u_integration_name='+gt.u_integration.toString() + '^service_classification=Integration Service');
	gr.query();
	while (gr.next()) {
		gs.log('Start processing file for Business Service: ['+gr.name+'] - [sys_id='+gr.sys_id.toString()+']', 'MIDTransferRun_BS');
		var context = (gt.u_context)?JSON.parse(gt.u_context):{};
		context.vars = {};
		context.vars['BILLING_ID'] = gr.sys_id.toString();
        
		var companyName=gs.getProperty("origin.people.int.company.name");
		var grCompany = new GlideRecord("core_company");
		grCompany.addQuery('name',companyName); //Delete the “encoded” word from the line to have grCompany.addQuery('name',companyName);
		grCompany.addActiveQuery();
		grCompany.query();
		var objCompanies = {};
		while(grCompany.next())
			objCompanies[grCompany.name] = grCompany.sys_id.toString();
		context.vars['Company'] = objCompanies;
		
		(new MIDTransferRun_BS(null, '' + gt.u_transfer.sys_id.toString(), null, context)).start(); 
	}
}
```
### MID Transfer Fields

This form will be used to configure attributes of field(s) that will be transferred: header’s name, field from source table, field to target table, order of field…

The data configuration of MID Transfer Fields is included in Update Set Data: **ORIGIN_People_Import_Data_RITM0752463_v1.0**

The order of fields must be same as the order of column field names in data file input. Need to modify the same order between “Column Field Name” in data file input and “Order” in MID Transfer Fields before press “Execute Now” on MID File Transfer form.

Screen-shot is Column Name and Order of MID Transfer Fields in Origin Integration:

**Origin MID Transfer Fields**

![MID Transfer Fields](MID_transfer_fields.png)

## System properties

**Preview the below system properties have been moved successfully into the pre-prod or prod system:**

<u>origin.people.int.company.name:</u> Name of the Company “Origin Energy”<br>
<u>origin.people.int.default.email:</u> Default email id is stored in this property(no-email@originenergy.com.au)<br>
<u>origin.people.int.default.location:</u> Default location has to be stored in the value field of this property.

Please confirm the default location loaded by Origin and copy the name of the location in the value field in this property.

![location name](location_name.png)
  
### Set Default Location 

1. Navigate to cmn_location table, search for the default location loaded by Origin Energy and copy the location name:

![default location1](default_location1.png)

2.  In Navigator type “sys_properties.LIST”  and search the property “origin.people.int.default.location” and  paste the name copied earlier in the below highlighted field: 

![default location2](default_location2.png)

### Set Default Email 

1.	In Navigator type “sys_properties.LIST”  and search the property “origin.people.int.default.email” and  paste the default email provided by Origin in the value field.

![default email](default_email.png)

### Set Default name 

1.	In Navigator type “sys_properties.LIST”  and search the property “origin.people.int.default.company” and  paste the default company Name “Origin Energy” here in the value field.

![default name](default_name.png)

## Business Service Configuration

Configurations have been moved from ‘MID File Transfer’ form to ‘Business Service’ form, setup ‘Business Service’ record is required for the following Origin_People_User_Import transaction (file type):

Follow the steps below to setup ‘Business Service’ records:
* Navigate to Configuration -> Business Services
* Click New button
* Enter appropriate values for the following fields:

| Field name | Description
|-|-|
| Name | Origin-People-Import-Integration
| Company | Origin Energy
| Approval group | Value of this field will be used as ‘Assignment group’ of the incident created in case of data issue.
| Owned by | Value of this field will be used as ‘Assigned to’ of the incident created in case of data issue.
|Support group | Value of this field will be used as ‘Assignment group’ of the incident created in case of connectivity issue.
| Assigned to | Value of this field will be used as ‘Assigned to’ of the incident created in case of connectivity issue.
| Service classification | Integration Service

* Click on ‘Integration Service’ tab, and input values for the fields below:

| Field name | Description
-|-
| Integration Name|Origin People Import
| File type | Origin_People_User_Import.
| File name	| DXC_SNOW_OG_PEOPLE*.csv
| Calendar type | Refer to “User_Import” transaction below for more detail.
| Run time zone | Refer to “User_Import” transaction below for more detail.
| Run hours | Refer to “User_Import” transaction below for more detail.
| Run time zone | Refer to “User_Import” transaction below for more detail.
| Run hours | Refer to “User_Import” transaction below for more detail.

* Click on Submit button.

The following values are used to setup Business Service records for the transaction ‘Origin-People-Import-Integration’:

| Field name | Value | Note 
-|:-:|-
| Name | Origin-People-Import-Integration | 	
| Company | Origin Energy |
| Approval group | TBD |	
| Owned by | TBD |
| Support group | TBD |
| Assigned to | TBD |
| Service classification | Integration Service |

<br>

| Field name | Value
-|-
| Integration name | Origin People Import
| File type | Origin_People_User_Import
|File name | DXC_SNOW_OG_PEOPLE*.CSV
| Calendar type | D(can be configured accordingly)
| Run time zone | 5(can be configured accordingly)
| Run hours	| 23(can be configured accordingly)

### Approval Group Configuration

This Group is configured on “Business Service” form. The value of this group will be used as ‘Assignment group’ of the incident created in case of data issue.

Follow the steps below to configure this group: 

* Navigate to System Security -> Users and Groups -> Groups.
* Find name = “User Integration - Data Issue – Origin” is existed yet. If not, create this group.
* Click New button to create “User Integration - Data Issue - Origin “group like picture below:

TBD

### Support Group Configuration

Support Group will be described latter. Because Support Group Name will be provided later.<br><br>
/*
This Group is configured on “Business Service” form. The value of this group will be used as ‘Assignment group’ of the incident created in case of connectivity issue.

Follow the steps below to configure this group:

* Navigate to System Security -> Users and Groups -> Groups.
* Find name = “will be described” is existed yet. It not, create this group.
* Click New button to create “will be described“group like picture below:

*/

### Owned by and Assigned to Configuration
These users are configured on “Business Service” form. Neither they can be used as the same one user or they can be different users.

The value of “Owned by” will be used as ‘Assigned to’ of the incident created in case of data issue. And, “Owned by” user is member of “Approval group” group.

The value of “Assigned to” will be used as ‘Assigned to’ of the incident created in case of connectivity issue. And, “Assigned to” user is member of “Support group” group.
Follow the steps below to configure this user:

* Navigate to System Security -> Users and Groups -> Users (see below snapshot where you can configure originintegrationrunuser– MID batch process run user)
* Find name = “Origin Customer” is existed yet. It not, create this user.
* Click New button to create “Origin Customer “user like picture below:

TBD

The configuration for “Origin Energy Customer” user is member of “User Integration - Data Issue – Origin” and “will be described” groups.

## Create Run as User

Follow the steps below to create the user which will be used as the Run As user in the MID File Transfer
* Navigate to User Administration -> Users
* Click on the New button
* Use the values in the following table against the new user fields

| Field name | Value
-|-
| User Id | originintegrationrunuser
| First Name | Origin
| Middle Name | RUN User
| Last Name | People Integration
| Company | Origin Energy
| Title | DO NOT DELETE
| Email | originintegrationrunuser@nomail.com
| Active | check

## Create Incident Template

Follow the steps below to configure incident template:

* Navigate to System Definition -> Templates
* New  record and create Incident Template.
* The following values are used for INCIDENT Template (MID Origin Incident). 

| Field name | Value
-|-
| Name | Origin MID Incident Template
| Table | Incident
| Application | Global
| Group | SEC: ServiceNow Integrations
| Short description | MID File Transfer Incident
| Company | Origin Energy
| Ca|ller | Origin People Integration
| Domain | TOP
| Global | uncheck
| Active | check

**Note:** Ensure that the Origin MID File Transfer’s 'Run As' user is a member of **SEC: ServiceNow Integrations group** that specified in the incident template. Otherwise, the configured 'Run As' user is not able to see the INC template. 

## ServiceNow / MID Server Package Implementation

<span style="color:blue">ORIGIN_People_Import_RITM0752463_v1.0 update set holds MidServer.zip file and it should be implemented on ServiceNow/Mid Server by MID Server Administrator.</span> Please follow below steps to implement MIdServer.zip file on DXC Leveraged Mid Server.

1. Download MidServer.zip file from ORIGIN_People_Import_RITM0752463_v1.0update set and copy into DXC Leveraged Mid Server (specific drive and folder)
2. Unzip MidServer.zip file
3. In folder path: please make a note of ‘in’ folder path so that you should be configure MID File Transfer batch job

See below sample snapshot

![sample snapshot](sample_snapshot.png)

At this point, the MID Server package (MidServer.zip) has been implemented on the target ServiceNow/MID Server system.

# Back Out Instructions

## Backing Out Update Set

Follow the steps below to back out the update set:

4. As an administrative user, navigate to System Update Sets > Local Update Sets.
5. Open the update set record for the last (most recent) update set in section 'Installation'.
6. To avoid causing issues, carefully review the contents of the update set and consider the impact of backing it out (see the table of expected results).

7. Click Back Out.

    1. The Progress popup displays actions, progress, and problems (changes in more recent update sets that affect the update set that is being backed out).
    2. All changes are reversed. The current update set tracks all of the new changes that occur.
    3. The update set and all associated update records are deleted. If needed, you can still navigate to the retrieved update set, preview it, and commit it again.
8.	When completed, navigate to System Update Sets > Local Update Sets.
9.	Repeat steps 3 and 4 for all update sets listed in section 'Installation' in reverse order.

At this point, the back out update set has been removed from the target system.

# Limitations and Known Issues

No known defects exist for this release.

# Appendix

## Install MID Server

Login to ServiceNow instance: DEVTEST, QA or Production to install MID Server:

https://csctest.service-now.com

https://cscqa.service-now.com

https://csc.service-now.com

This installation is captured on DEV ServiceNow instance for reference:
1. Login to: https://cscdev.service-now.com/ <br>Username:<br>Password:
<br>
This is username to install MID Server in step 4. And, this user has “mid_server” role.

2. Left Navigator to MID Server<br>Click on: Downloads
<br>Download MID Server: Choose Windows operating system (32 bit or 64 bit).

![installation steps 1](installation_steps(1).png)


3. Create directory and extract file download
- Create folder on local machine:<br>\MIDServer\dev_csc_servicenow (or devtest_csc_servicenow or qa_csc_servicenow or pro_csc_servicenow).
- Extract file download:
<br>Copy “agent” folder into \MIDServer\dev_csc_servicenow (or devtest_csc_servicenow or qa_csc_servicenow or pro_csc_servicenow).

4. Installing

- Go to \MIDServer\dev_csc_servicenow\agent

- Double click: installer.bat

![installation steps 2](installation_steps(2).png)

-	Put information: Service Now Dev Instance, username/password login.

-	Press: Test your connection.

![installation steps 3](installation_steps(3).png)

-	Press: Next

![installation steps 4](installation_steps(4).png)

-	Type MID Server name: CLOUD_MID_SERVER_DEV_ttu2, that is the name and service of MID Server in Development environment.

-	Press: Next

![installation steps 5](installation_steps(5).png)

-	Press: Next

![installation steps 6](installation_steps(6).png)

-	Click “Start MID Server” then click “MID Servers List Page”.

5.	Validate

-	Dev CSC ServiceNow Screen display, find the MID Server record name. In Development environment, it is “CLOUD_MID_SERVER_DEV_ttu2”

-	Click “Validate” under Related Links.

-	The status of record “CLOUD_MID_SERVER_DEV_ttu2”: Up

-	The validated of record “CLOUD_MID_SERVER_DEV_ttu2”: Yes

##	Batch Files-Sample Snapshot

The Origin Energy integration has a specific folder on real MID Server machine (\CSC\Origin Energy). This folder will contain all Batch Files to run Origin MID File Transfer.

The real MID Server machine and structure path for each integration would appear like the below screenshot:

![installation steps 7](installation_steps(7).png)

