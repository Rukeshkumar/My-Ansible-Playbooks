**Deployment of Automated Health Check for Backup Exec**
========================================================

**Purpose:** The supplied script is designed to run a series of Backup
Exec Daily health checks, generate output, and send the health check
results via SMTP to [Operations
Center](https://oc.onerun.hpecorp.net/ui5/index.html#home) for
presentation within []{#_Toc465775986 .anchor}the DORM report. The
script sends the same health check results to the account's distribution
List along with the log file for debugging.

**Supported Products & Versions:** Backup Exec & Media Server with
versions of BE 13.X, 14.X, 15.X, 16.X for Windows and Unix/Linux Media
OS.

**Health Checks for Data Protector:**

-   Service Availability

-   Backup Devices Availability

-   Backup Database Backed up

-   Server Capacity

-   Capacity Storage

-   Success Rate

-   Consecutive Failure

-   Long Running Backups for 12hr

-   Long Running Backups for 24hr

**Technical Implementation Overview: **

![](media/image1.png){width="5.695430883639545in"
height="3.1388156167979004in"}

**Deployment Package:** This deployment package contains the following
files.

  **File**                                **Description**
  --------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------
  DORM\_HC\_BackupExec\_Deployment.docx   It contains the deployment instructions.
  DORM\_HC\_BackupExec\_Master.ps1        The PowerShell script is responsible for retrieving the required data and sending the results via SMTP.
  DORM\_HC\_BackupExec\_Master.cfg        The configuration file is to be deployed along with the script for setup and customization purposes.
  DORM\_HC\_BackupExec\_Master.ps1        This PowerShell script is responsible for capturing the windows Cell manager command output in a centralized shared directory.
  DORM\_HC\_BackupExec\_Master.cfg        This configuration file is to be deployed along with the DORM\_HC\_BackupExec\_Master.ps1 for setup and customization purposes.
  SSH-session.zip                         This SSH module for PowerShell should be imported to allow SSH connectivity from Windows PowerShell to the destination hosts.

**Deployment Steps**
====================

### Step \#1 -- CONFIGURATION CUSTOMIZATION 

Copy the configuration file "DORM\_HC\_BackupExec\_Master.cfg" to a
location such as C:/Monitoring/BackupExec and customize the same with
account specific details. NOTE: *It is important that each and every
parameter in the configuration file is configured correctly. The
configuration file contains the required description about each
parameter*.

### Step \#2 -- DEPLOYMENT 

The client script has been developed to generate output for various
commands, and then dump the output to a Shared Directory which is
accessible from a centralized server such as a Jump Server or a Master
server.

The Master script has been developed for Windows and Unix/Linux Media
servers environment. In case of BackupExec running on Windows platform,
the Master script will read the output from each of the files generated
by the client script. In case BackupExec media servers is hosted on
Unix/Linux platform, it will connect through SSH module to run the cell
manager commands and it will generate a health check report, and email
it to the required recipients.

Pre-requisite (*applicable only for Backup Exec Media servers on Unix/Linux)*
-----------------------------------------------------------------------------

In order to allow SSH connectivity from Windows to the Unix/Linux
BackupExec Media servers, we're using a PowerShell Module. Additional
details can be found in this below URL (and the SSH module for
PowerShell can be downloaded from the same. It is also supplied along
with the deployment package):

<http://www.powershelladmin.com/wiki/SSH_from_PowerShell_using_the_SSH.NET_library#Downloads>

Steps to deploy the SSH-Sessions module:

-   Copy the attached SSH-Session zip file, unblock the zip file (in the
    file properties)

-   Then unzip and place the unzipped folder in one of your PowerShell
    modules folders which is usually
    "C:\\windows\\system32\\WindowsPowerShell\\v1.0\\Modules"

> Note:

-   SSH Modules require PowerShell version 2 or later. The DLL file
    requires .NET 3.5 or later.

-   The PowerShell module folders should be listed in the
    variable \$env:PSModulePath.

-   This is from our test environment (for e.g., PATH:
    C:\\windows\\system32\\WindowsPowerShell\\v1.0\\Modules)

The configuration file for the **Client and Master Script** should be
customized for each BackupExec Servers, for each account. The script and
the customized configuration file should be copied to either
'C:\\Monitoring\\BackupExec \\' or any other appropriate location. A
directory should be created in one of the centralized servers such as
the Jump Server or a Master Server in the environment, and shared with
all the BackupExec windows and Linux/Unix Media Servers.

### Step \#3 -- SCHEDULING 

The DORM\_HC\_BackupExec\_Master.ps1 script should be scheduled and
executed from each of the BackupExec windows and Linux/Unix Media
Servers.

+-----------------------------------+-----------------------------------+
| **Scheduler**                     | Windows Task Scheduler            |
+===================================+===================================+
| **Schedule**                      | Every day at 06:00 AM             |
+-----------------------------------+-----------------------------------+
| **Script Usage**                  | DORM\_HC\_BackupExec\_Client.ps1  |
|                                   | -FileName                         |
|                                   | DORM\_HC\_BackupExec\_Client.cfg  |
|                                   | or                                |
|                                   |                                   |
|                                   | Run.bat                           |
+-----------------------------------+-----------------------------------+
| **Execution Interface**           | Windows PowerShell                |
+-----------------------------------+-----------------------------------+

The DORM\_HC\_BackupExec\_Master.ps1 script should be scheduled and
executed from one of the centralized servers such as a Jump Server or a
BackupExec Master server that has access to the Shared Directory.

+-----------------------------------+-----------------------------------+
| **Scheduler**                     | Windows Task Scheduler            |
+===================================+===================================+
| **Schedule**                      | Every day at 06:15 AM             |
+-----------------------------------+-----------------------------------+
| **Script Usage**                  | DORM\_HC\_BackupExec\_Master.ps1  |
|                                   | -FileName                         |
|                                   | DORM\_HC\_BackupExec\_Master.cfg  |
|                                   |                                   |
|                                   | Or Run.bat                        |
+-----------------------------------+-----------------------------------+
| **Execution Interface**           | Windows PowerShell                |
+-----------------------------------+-----------------------------------+
| **Firewall Port(s)**              | SMTP Protocol over port 25        |
+-----------------------------------+-----------------------------------+

### Step \#4 -- CONFIRMATION 

An output log will be generated in the same folder as the script, named
DORM\_HC\_BackupExec\_Master.log. This log can be used to analyze output
and do error checking, and will be overwritten each time the script is
executed. The retention of the log can be modified using the Log
retention parameter in the configuration file.

A successful deployment will lead to a health check result being
populated in the Operation Center DORM report shortly after the script
is executed. Note that while 'Test\_Mode' is enabled in the
configuration file, no results will show up in Operation Center.

For support with this process, or confirming the success of the script,
respond to the email in which this package was supplied, or
alternatively send an email to:
[dormcheck\_deployment@hpe.com](mailto:dormcheck_deployment@hpe.com?subject=Exchange%202010%20automation%20script)
