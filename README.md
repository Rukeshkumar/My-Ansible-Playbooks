**DataProtector automation to generate consolidated Backup Report**
===================================================================

Purpose of the script:
======================

-   The script will be scheduled to run every day on all the
    Dataprotector servers to collect the status of the backup servers
    and generates a 2 day backup report and stores in a network share.

-   The monthly report generation script is intended to read the 2 day
    backup report which is stored in the network share and generates a
    single consolidation report which contains the backup report of last
    30 days.

Business Justification:
=======================

**Dataprotector Dashboard**: This report provides backup status of all
the Dataprotector Backup servers in the environment. Hence reducing time
and human effort.

> **Dataset Collected:** The script collects the status of all the
> Dataprotector Backup servers in the environment.

Pre-Requisites:
===============

-   PowerShell version 3 or above

-   Common share folder to store the 2 day report which is the result of
    the Client Script ( We need to create a new folder ) 

-   Destination Path where the consolidated report is to be stored

-   Service Account to schedule the script in Task Scheduler

Deployment Package:
===================

This deployment package contains the following files.

File | Description |
-----|-------------|
Dataprotector\_Unified\_Reporting\_Deployment.docx | This document |
DataProtector-Report-ClientScript.ps1 | PowerShell script to be scheduled daily on each Dataprotector Master servers to generate 20 day backup report. |
DataProtector-Report-ClientScript.cfg | Configuration script for DataProtector-Report-ClientScript.ps1 to provide input. |
DataProtector-Report-Masterscript.ps1 | PowerShell script for consolidating the multiple 7 day backup reports of all the DataProtector servers |
DataProtector-Report-Masterscript.Cfg | Configuration script for DataProtector-Report-Masterscript.Cfg to provide input. |

Installation Steps 
===================

DataProtector-Report-ClientScript.ps1
-------------------------------------

### Step \#1 -- DataProtector-Report-ClientScript -- CONFIGURATION

-   Open the configuration file named
    DataProtector-Report-ClientScript.cfg

-   Set the configuration parameters shown in the below image as per the
    corresponding instructions. These values typically should not be
    changed following the initial setup.

![](../images/DORM_HC_BackupExec.PNG)

**\[GENERAL\]**

**Version** -- This field is only to be modified by the script
developers. In the event of a change, the configuration file will be
resent.

**Account --** This field should be set to the name of the account as
per standards. This value is provided along with the deployment package
communication.

**Technology** -- This field will be set to the correct value by the
developer, and hence will not need to be amended.

**NetworkShare --** This field needs to be filled with the network share
path where the 20 day reports are stored.

**Windows\_Servers --** This field should contain the list of Windows
servers where the client script is to be run.

**Unix\_Servers --** This field should contain the list of Unix / Linux
servers where the client script is to be run.

**\[OMNIRPT\_LOCATION\]**

**Unix\_Omnirpt\_dir --** This filed is used to set the Omnirpt
installed location and this needs to change only when the installed
location gets changed.

### Step \#2 -- DataProtector-Report-ClientScript - DEPLOYMENT 

The script and the configuration file should be copied to a proper
location in each and every Dataprotector Master server from where the
NetworkShare is accessible.

### Step \#3 -- DataProtector-Report-ClientScript - SCHEDULING 

The script should be scheduled and executed from the designated
deployment server.

Scheduler | Windows Task Scheduler |
----------|------------------------|
Schedule | Every day at 07:00 AM (recommended) |
Script Usage | DataProtector-Report-ClientScript.ps1 |
Execution Interface | Windows PowerShell |
Firewall Port(s) | SMTP Protocol over port 25 |

### 

### Step \#4 -- DataProtector-Report-ClientScript - CONFIRMATION 

A report gets generated as a spreadsheet and stored in the Network Share
which is configured in Client script configuration file.

DataProtector-Report-Masterscript.ps1
-------------------------------------

### Step \#1 -- DataProtector-Report-Masterscript - CONFIGURATION

-   Open the configuration file named
    Dataprotector\_Backup\_Report\_MasterScript.cfg

-   Set the configuration parameters shown in the below image as per the
    corresponding instructions. These values typically should not be
    changed following the initial setup.

![](../images/DORM_HC_BackupExec.PNG)

**\[GENERAL\]**

**Version** -- This field is only to be modified by the script
developers. In the event of a change, the configuration file will be
resent.

**Account --** This field should be set to the name of the account as
per standards. This value is provided along with the deployment package
communication.

**Technology** -- This field will be set to the correct value by the
developer, and hence will not need to be amended.

**NetworkShare --** This field needs to be filled with the network share
path where the 20 day reports are stored.

**OutputPath --** This field should be set to the path where the
consolidated report to be stored.

### Step \#2 -- DataProtector-Report-Masterscript - DEPLOYMENT 

The script and the modified configuration file should be copied to a
centralized location from where the NetworkShare is accessible.

### Step \#3 -- DataProtector-Report-Masterscript - SCHEDULING 

The script should be scheduled and executed from the designated
deployment server.

Scheduler | Windows Task Scheduler |
----------|------------------------|
Schedule | Every day at 10:00 AM (recommended) |
Script Usage | DataProtector-Report-Masterscript.ps1 |
Execution Interface | Windows PowerShell |
Firewall Port(s) | SMTP Protocol over port 25 |

### Step \#4 -- DataProtector-Report-Masterscript - CONFIRMATION 

An output log will be generated in the same folder as the script, named
DataProtector-Report-Masterscript.log. This log can be used to analyze
output and do error checking, and will be overwritten each time the
script is executed.

A consolidated report is generated and stored in the given output path.

**Sample output -**

a.  **DataProtector-Report-ClientScript.ps1 :**

![](../images/DORM_HC_BackupExec.PNG)

a.  **DataProtector-Report-Masterscript.ps1:**

![](../images/DORM_HC_BackupExec.PNG)

Technical Implementation Details:
=================================

![](../images/DORM_HC_BackupExec.PNG)

|Script Repository | DataProtector-Report-ClientScript.ps1 is scheduled locally on each Master Dataprotector Server DataProtector-Report-Masterscript.ps1 is scheduled on only 1 server |
| Schedule                      | Daily (Between 5 AM -- 6 AM)  |
| Username Requirements        | Any service account  |
