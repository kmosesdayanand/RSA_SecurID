# Integration Type1
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

### Sample Logs

```
imsAdminAudit.log
 
2022-11-08 14:51:32,042, 5b75a1364b14000a4d8f9b1311c0b4a0,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,DELETE_PRINCIPAL,10054,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,391d64d14b14000a66dc881c2bd53b20,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,,
2022-11-08 14:53:14,301, 2a8b497b4b14000a70451e25d9970480,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,CREATE_PRINCIPAL,10051,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,,
2022-11-08 16:00:26,041, e0fdf5de4b14000a224a6cb1105635d9,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UNLINK_PRINCIPAL_ADMIN_ROLE,10025,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,ADMIN_ROLE,000000000000000000001000e0031000,,,SuperAdminRole,PRINCIPAL,afe940c34b14000a665dc1cbc07b46d2,000000000000000000001000d0011000,000000000000000000001000e0011000,123,
2022-11-08 16:27:43,265, 13290bbb4b14000a47516bdb876996a1,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UPDATE_PRINCIPAL,10055,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,,
2022-11-08 16:27:43,297, 1509120d4b14000a1d64bb39143dedf7,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AM_LINK_TOKEN_PRINCIPAL,20045,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxxxx,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,Sheetal
2022-11-08 16:29:22,587, 3336c2c94b14000a621d1f4261d6aac6,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_TOKENPROFILES_ADD,20248,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SW_TOKEN_PROFILES,330bfd644b14000a3785cddb59a80409,,,sith,,,,,,Sheetal
2022-11-08 16:29:22,592, 338151804b14000a26488f150e6514e0,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_SELFSERVICETOKEN_MANAGEMENT,20125,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SELF_SERVICE_TOKEN,,,,SELF_SERVICE_TOKEN,,,,,,Sheetal
2022-11-08 16:30:26,628, 1c57ab094b14000a671201ae7ae408ba,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_SELFSERVICETOKEN_MANAGEMENT,20125,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SELF_SERVICE_TOKEN,3342ff544b14000a11f474eebdf65c64,,,SELF_SERVICE_TOKEN,,,,,,Sheetal
2022-11-08 16:30:26,629, 1c6d96a34b14000a6b532d2917c287dd,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_SELFSERVICETOKEN_MANAGEMENT,20125,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SELF_SERVICE_TOKEN,3342ff544b14000a11f474eebdf65c64,,,SELF_SERVICE_TOKEN,,,,,,Sheetal
2022-11-08 16:30:26,630, 1c74970d4b14000a2253f856bf705cfb,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_TOKENPROFILES_UPDATE,20249,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SW_TOKEN_PROFILES,330bfd644b14000a3785cddb59a80409,,,sith,,,,,,Sheetal
2022-11-08 16:32:34,375, daa723584b14000a7abeb84628a26596,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_TOKENPROFILES_DELETE,20250,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SW_TOKEN_PROFILES,330bfd644b14000a3785cddb59a80409,,,sith,,,,,,Sheetal
2022-11-08 16:38:16,385, 7c052c164b14000a0af893efb129cb6d,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_TOKENPROFILES_ADD,20248,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SW_TOKEN_PROFILES,7bb6cf7c4b14000a76b43b33296b3bbd,,,sithiiii,,,,,,Sheetal
2022-11-08 16:38:16,389, 7c4e56494b14000a5e0f9f7e9afcaf0f,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHMGR_SELFSERVICETOKEN_MANAGEMENT,20125,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_SELF_SERVICE_TOKEN,,,,SELF_SERVICE_TOKEN,,,,,,Sheetal
2022-11-08 16:39:14,022, e77b0dfb4b14000a01a9fbf109b3a176,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UPDATE_AM_TOKEN,20027,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxxxx,,,,,,Sheetal
2022-11-08 16:39:14,100, ec2470494b14000a4a8b97fc2e9fe99e,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,EXPORT_SOFT_TOKEN,20089,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxxxx,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,
 
 
 
imsRuntimeAudit.log



2022-11-08 15:41:35,116, 909a83074b14000a56b008ceb86cae63,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,AUTHN_LOGOUT_EVENT,13001,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,,,,,,,,,,,,,,,
2022-11-08 15:58:40,524, 4fa925e54b14000a18c87885fb909ea4,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_METHOD_FAILED,ae5645014b14000a24b68e80c00d6146-ASRsznHgyEPQ,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-08 15:58:57,359, 3b1f16584b14000a69dcf463d386c8a6,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_PRINCIPAL_NOT_FOUND,,,,,unknown user,,,,,,,,,,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-08 15:59:32,119, 52ff86ed4b14000a3bc4aa64ca244d31,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_METHOD_FAILED,7da778154b14000a164e597313b19817-OGQz4OTwLEC9,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-08 15:59:57,452, 38f5c76c4b14000a2c2b9b3f12651e94,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,SUCCESS,AUTHN_METHOD_SUCCESS,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-08 17:23:35,116, 7d0d45ee4b14000a64b22e2c22ec1f20,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,AUTHN_LOGOUT_EVENT,13001,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,,,,,,,,,,,,,,,
2022-11-09 14:50:16,453, 541183a44b14000a2ece6288290fceb2,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_METHOD_FAILED,f47d2e0f4b14000a7c659ebc9af0bcaa-IPVDHXtfowUL,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-09 14:50:26,522, ac421c424b14000a1995c607de8a06e2,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_PRINCIPAL_NOT_FOUND,,,,,unknown user,,,,,,,,,,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-09 14:50:46,483, 5202209b4b14000a3d2e65219a4c14e1,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,SUCCESS,AUTHN_METHOD_SUCCESS,7d0bc6cb4b14000a04f40bc3b20e7cdc-uGERKGU/apAU,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
2022-11-11 10:34:40,804, 85b9fd7c4b14000a28f38c6f9f80b1b6,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,SUCCESS,AUTHN_METHOD_SUCCESS,85809ab74b14000a50502e71073bc7f6-d35TPYeudYym,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,
 
 
 
imsSystem.log



2022-10-12 15:38:57,379, 5f77f1264b14000a62d4a0490fe50afd,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,ADJUDICATOR_CLOCK_SETBACK,26041,WARN,CLOCK_SET_BACK_DETECTED,,,,,,,,2022-10-12 10:38 UTC,2022-10-12 10:39 UTC,,,,,
2022-10-12 15:38:58,219, 919563ca4b14000a0d5b9d14c28f7577,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,UDP_SERVER_SHUTDOWN,26071,WARN,UDP_SERVER_STOPPED,,,,,,,,Authentication service,5500,,,,,
2022-10-12 15:38:58,240, 92ccc9e44b14000a53491438bd1293e8,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,EVENT_DISPATCH,16360,FAIL,UNEXPECTED_EXCEPTION,,,,,,,,com.rsa.ims.components.spring.IMSContainerStoppingEvent[source=IMS],,,,,,
com.rsa.common.AuditLogFailureException: Failed to write to audit log.
    at com.rsa.ims.logging.log4j.Log4jAuditLog.doLog(Log4jAuditLog.java:145)
    at com.rsa.ims.logging.auditsys.AbstractAuditLogger.log(AbstractAuditLogger.java:238)
    at com.rsa.ims.logging.auditsys.AbstractAuditLogger.logSystemEvent(AbstractAuditLogger.java:132)
    at com.rsa.authmgr.internal.common.server.UDPServer.stopRunning(UDPServer.java:505)
    at com.rsa.authmgr.internal.common.server.ServerRunnableController.stopServerRunnables(ServerRunnableController.java:212)
    at com.rsa.authmgr.internal.common.server.ServerRunnableController.onApplicationEvent(ServerRunnableController.java:176)
    at com.rsa.ims.components.spring.ListenerEventData.dispatchEvent(IMSEventDispatcher.java:208)
    at com.rsa.ims.components.spring.IMSEventDispatcher.dispatchEvent(IMSEventDispatcher.java:87)
    at com.rsa.ims.components.spring.IMSEventMulticaster$1$1.run(IMSEventMulticaster.java:119)
    at com.rsa.ims.security.spi.SimpleSecurityContextImpl.doAs(SimpleSecurityContextImpl.java:80)
    at com.rsa.security.SecurityContext.doAs(SecurityContext.java:412)
    at com.rsa.security.SecurityContext.doAsSystem(SecurityContext.java:454)
    at com.rsa.ims.components.spring.IMSEventMulticaster$1.run(IMSEventMulticaster.java:117)
    at org.springframework.core.task.SyncTaskExecutor.execute(SyncTaskExecutor.java:49)
    at com.rsa.ims.components.spring.IMSEventMulticaster.multicastEvent(IMSEventMulticaster.java:115)
    at org.springframework.context.support.AbstractApplicationContext.publishEvent(AbstractApplicationContext.java:335)
    at com.rsa.ims.components.spring.IMSContainerStatePublisher.publishEvent(IMSContainerStatePublisher.java:149)
    at com.rsa.ims.components.spring.IMSContainerStatePublisher.onServerStopping(IMSContainerStatePublisher.java:185)
    at com.rsa.ims.common.jmx.AbstractServerStateListener.handleNotification(AbstractServerStateListener.java:181)
    at com.rsa.ims.common.jmx.JMXNotificationServiceImpl$DelegatingNotificationListener.handleNotification(JMXNotificationServiceImpl.java:227)
    at weblogic.management.mbeanservers.internal.PartitionJMXInterceptor$IntermediateNotificationListener.fireEvent(PartitionJMXInterceptor.java:1490)
    at weblogic.management.mbeanservers.internal.PartitionJMXInterceptor$IntermediateNotificationListener.handleAttributeChangeNotification(PartitionJMXInterceptor.java:1641)
    at weblogic.management.mbeanservers.internal.PartitionJMXInterceptor$IntermediateNotificationListener.handleNotification(PartitionJMXInterceptor.java:1707)
    at com.sun.jmx.interceptor.DefaultMBeanServerInterceptor$ListenerWrapper.handleNotification(DefaultMBeanServerInterceptor.java:1754)
    at javax.management.NotificationBroadcasterSupport.handleNotification(NotificationBroadcasterSupport.java:275)
    at javax.management.NotificationBroadcasterSupport$SendNotifJob.run(NotificationBroadcasterSupport.java:352)
    at javax.management.NotificationBroadcasterSupport$1.execute(NotificationBroadcasterSupport.java:337)
    at javax.management.NotificationBroadcasterSupport.sendNotification(NotificationBroadcasterSupport.java:248)
    at weblogic.management.jmx.modelmbean.WLSModelMBean.sendNotification(WLSModelMBean.java:923)
    at weblogic.management.jmx.modelmbean.PropertyChangeNotificationTranslator.generateNotification(PropertyChangeNotificationTranslator.java:169)
    at weblogic.management.jmx.modelmbean.PropertyChangeNotificationTranslator$1.propertyChange(PropertyChangeNotificationTranslator.java:151)
    at java.beans.PropertyChangeSupport.fire(PropertyChangeSupport.java:335)
    at java.beans.PropertyChangeSupport.firePropertyChange(PropertyChangeSupport.java:327)
    at java.beans.PropertyChangeSupport.firePropertyChange(PropertyChangeSupport.java:263)
    at weblogic.management.runtime.RuntimeMBeanDelegate._postSet(RuntimeMBeanDelegate.java:421)
    at weblogic.t3.srvr.ServerRuntime.updateRunState(ServerRuntime.java:487)
    at weblogic.t3.srvr.T3Srvr.setState(T3Srvr.java:294)
    at weblogic.t3.srvr.T3Srvr.suspend(T3Srvr.java:1434)
    at weblogic.t3.srvr.T3Srvr.shutdown(T3Srvr.java:1372)
    at weblogic.t3.srvr.T3Srvr.shutdown(T3Srvr.java:1340)
    at weblogic.t3.srvr.T3Srvr.forceShutdown(T3Srvr.java:1229)
    at weblogic.t3.srvr.T3Srvr$3.run(T3Srvr.java:1248)
```
 
### Log transmission settings
1. Install Splunk Universal Forwarder on the RSA Authentication Manager in /opt directory.
2. Navigate to **/opt/splunk/bin and add monitor**
     <ul>
		<li><b>./splunk add monitor /opt/rsa/am/server/logs/imsAdminAudit.log</b></li>
		<li><b>./splunk add monitor /opt/rsa/am/server/logs/imsRuntimeAudit.log</b></li>
                <li><b>./splunk add monitor /opt/rsa/am/server/logs/imsSystem.log</b></li>
     </ul>
3. After you execute each command you will be able to see a acknowledgement message saying the monitor has been added.
4. Now add a Forward server as
		<b>./splunk add forward-server <ip_of_Splunk_Enterprise:port></b>
5. Now you will be able to see all the logs of RSA Authentication Manager.
 
	
## References
<ul>
<li>Audit Log Configuration:  https://community.rsa.com/t5/securid-knowledge-base/how-to-configure-rsa-authentication-manager-to-send-log-messages/ta-p/6449 </li>
<li>List of Actions that can be performed by user: https://community.rsa.com/t5/securid-authentication-manager/rsa-authentication-manager-log-messages/ta-p/630160![image](https://user-images.githubusercontent.com/80450749/216566418-3903fdf3-7289-4c2d-acb9-1058519071d7.png)</li>
</ul>
