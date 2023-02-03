# Integration-1
### 1 Introduction
 
 
**Purpose:** The purpose of this document is to introduce auditing for RSA Authentication Manager, followed by a generalized guide, including commands and scripts (if any), on how to implement auditing functionality.
    
**Scope:** RSA Authentication Manager does come with logs that can be used for auditing; however, the agency will need to perform certain specific configuration steps to enable the auditing and/or to pipe the data. The guidelines within this document provide a basic and pre-configured script for RSA Authentication Manager.
 Also, please note that any screenshots provided are only for reference purposes and should be used only for the same.  The output of commands may vary in format for different environments or platforms, and as such, it is recommended that the user of this guide refer to the platform specific documentation for exact outputs.
 
 
**What is Auditing?**
 
RSA Authentication Manager auditing is a thorough inspection of actions performed by RSA  Authentication Manager’s admin, users and other privileged users . It is done to assure that security is highly implemented on the data and the utilised systems. Auditing can be used to scrutinize individual actions, such as user deletion and modification of configuration; it could even provide a detailed report based on combinations of data which includes username, application, or time stamp, for example. As such, proper auditing of RSA Authentication Manager will enable strong security being in place and a control to monitor for possible security breaches. 
 
**Why Audit?**
Auditing is generally performed to ensure any of the following:
<ul>
	<li>Enable accountability for actions.</li>
	<li>Deter users from inappropriate actions.</li>
	<li>Investigate suspicious activity.</li>
	<li>Notify an auditor of actions by an unauthorized user.</li>
	<li> Detect problems with an authorization or access control implementation.</li>
	<li> Address auditing requirements for compliance.</li>
	<li>Monitor and gather data about specific activities.</li>
 </ul>
 
 
 **Configure auditing for RSA Authentication Manager**
 
	
  <li><b>Overview</b></li>
The following audit categories are being covered for this data source.
· Administrator Actions like Create, Delete, modify etc., apart from these there are approximately 1000+ that can be performed by Administrator. 
· Authentication Logs of the Administrator, users, and other privileged users.
· System logs like uptime, Session information and other server related data.
  <li><b>Specifications</b></li>
	Hosting type	On-premises 
	Software name	RSA Authentication Manager 
	Software versions which have been tested	8.6
	OS versions which have been tested	 
  
  <li><b>Audit events</b></li>
  
The following table provides an overview of the types of audit log events that are available in the logs for RSA Authentication Manager.
  <ul>
<li><b>Admin Log File</b> (consists of all the actions performed by administrator) <b>RSA_AM_HOME/server/logs/imsAdminAudit.log</b></li>
<li><b>Runtime Log file</b> (consists of all authentication logs) <b>RSA_AM_HOME/server/logs/imsRuntimeAudit.log</b></li>
<li><b>System Log File</b> (consists of all system related logs) <b>RSA_AM_HOME/server/logs/imsSystem.log</b></li>
  </ul>  
  
## Configuration and setting changes


In RSA Authentication Manager the log data is usually stored in the internal database. This data cannot be accessed using other third-party tools. This is data is either accessed by using the GUI of Security Console by the Admin or by integrating with Net witness software from RSA manufacturers. 
In order to access the data we need to hardcode the system to store logs into files and not in the internal database. Follow the following steps to configure
 
1. Go to Operations Console and navigate to Administration > Operating System Access and enable SSH.
2. Now logon to the RSA using the Public IP in any SSH client using rsaadmin credentials that you have created during setup.
3.  Now navigate to /opt/rsa/am/utils/ 
4. Execute the following commands 
		 <b>./rsautil store -a config_all ims.logging.audit.admin.datastore database,file.</b>
5. You will be prompted for Operations Console username and password after you enter you should see a success message config_all
6.   <b>./rsautil store -a config_all ims.logging.audit.runtime.datastore database,file.</b>
7.   <b>./rsautil store -a config_all ims.logging.system.datastore database,file.</b>
8. Change the directory to /opt/rsa/am/server
9. Run <b>./rsaserv restart all.</b>
10. Run the Above two commands in the same manner.
11. The above commands overwrite the default settings and make the system to write logs into files ignoring the internal database.
12. Now you have Completely configured your log settings. You can perform any action in the Security console and you can see that the logs have been written in the locations mentioned in section 3.6.


          
**Log file location**
<ul>
<li><b>/opt/rsa/am/server/logs/imsAdminAudit.log</b></li>
<li><b>/opt/rsa/am/server/logs/imsRuntimeAudit.log</b></li>
<li><b>/opt/rsa/am/server/logs/imsSystem.log</b></li>
</ul>
