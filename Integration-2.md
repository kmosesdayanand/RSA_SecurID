## Integration Type-2
### 1 Introduction
 
 
**Purpose:** 

The purpose of this document is to introduce auditing for RSA Authentication Manager, followed by a generalized guide, including commands and scripts (if any), on how to implement auditing functionality.
    
**Scope:**

RSA Authentication Manager does come with logs that can be used for auditing; however, the agency will need to perform certain specific configuration steps to enable the auditing and/or to pipe the data. The guidelines within this document provide a basic and pre-configured script for RSA Authentication Manager.
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
 <ol>
  <li><b>Overview</b></li>
  <ul>
The following audit categories are being covered for this data source.

<li>User (Create, Modify & Delete).</li>
<li>Groups (Create, Modify & Delete).</li>
<li>Tokens (Assign, Unassign).</li>
<li>Batch Jobs.</li>
<li>Authentication Logs.</li>
<li>Security Domains (Create, Modify & delete).</li>
<li>Token policies (Create, Modify & delete).</li>
<li>Lockout policies (Create, Modify & delete).</li>
<li>Offline authentication policies (Create, Modify & delete).</li>
<li>Password policies (Create, Modify & delete).</li>
<li>Self-service troubleshooting policies (Create, Modify & delete).</li>
<li>Risk-based authentication policies (Create, Modify & delete).</li>
 </ul>
  <li><b>Audit events</b></li>
  The following table provides an overview of the types of audit log events that are available in the logs for RSA Authentication Manager with location.
  
|        Log Files(consists of all the actions performed by administrator) | var/log/message* (log rotation happens)  |
| ------------------------------------------------------------------------ |:----------------------------------------:|
  
  <li><b>Configuration and setting changes</b></li>
In RSA Authentication Manager the log data is usually stored in the internal database. The data can be viewed in different ways. Since, we are using the addon to parse the data in Splunk we should use Rsyslog to transmit the data to Rsyslog Server. 
First we will configure RSA to send data to Rsyslog Server as follows
  
1. Go to Operations Console and navigate to Administration > Operating System Access and enable SSH.
2. Navigate to <b>Setup -> logging -> select your instance</b> Select Save to internal database and local operating system syslog. Now move to SSH and execute the following command 
3. Change the directory to <b>/opt/rsa/am/server.</b>
4. Run <b>./rsaserv restart all.</b>
  
![image](https://user-images.githubusercontent.com/80450749/216576022-d1d3173f-5393-45e9-a1b2-355c2633595b.png)
![image](https://user-images.githubusercontent.com/80450749/216576183-20b02a11-d57d-43af-90dd-67ede846654d.png)
![image](https://user-images.githubusercontent.com/80450749/216576244-8d951dd9-2ff6-4875-9915-e40312f45a03.png)


5. Navigate to /etc/rsyslog.d/remote.conf.
6. Edit the following stanza as follows.
```
  ########## Sending Messages to Remote Hosts ##########
 
# Remote Logging using TCP for reliable delivery
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @@remote-host
 
# Remote Logging using UDP
# remote host is: name/ip:port, e.g. 192.168.0.1:514, port optional
#*.* @remote-host
        *.*@<YourIP>:514

########## Receiving Messages from Local AM Server ##########
$ModLoad imudp.so
$UDPServerAddress 127.0.0.1
$UDPServerRun 514
```
  
7. Since RSA add-on for Splunk only supports UDP use only UDP module to send data. After you make changes on RSA.
8. Now restart your rsyslog using <b>systemctl restart rsyslog</b>.

```
########################################Admin Logs#################################
 
#### Delete User
  
2022-11-08 14:51:32,042, 5b75a1364b14000a4d8f9b1311c0b4a0,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,DELETE_PRINCIPAL,10054,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,391d64d14b14000a66dc881c2bd53b20,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,,
 
#### Create User
  
2022-11-08 14:53:14,301, 2a8b497b4b14000a70451e25d9970480,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,CREATE_PRINCIPAL,10051,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,

#### Promoting user as Admin
2022-11-08 16:00:26,041, e0fdf5de4b14000a224a6cb1105635d9,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UNLINK_PRINCIPAL_ADMIN_ROLE,10025,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,ADMIN_ROLE,000000000000000000001000e0031000,,,SuperAdminRole,PRINCIPAL,afe940c34b14000a665dc1cbc07b46d2,000000000000000000001000d0011000,000000000000000000001000e0011000,123,

####Updating User
2022-11-08 16:27:43,265, 13290bbb4b14000a47516bdb876996a1,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UPDATE_PRINCIPAL,10055,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,,,,,,

####Assigning Token to User
2022-11-08 16:27:43,297, 1509120d4b14000a1d64bb39143dedf7,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AM_LINK_TOKEN_PRINCIPAL,20045,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxxxx,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,Sheetal

####Updating Token 
2022-11-08 16:39:14,022, e77b0dfb4b14000a01a9fbf109b3a176,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UPDATE_AM_TOKEN,20027,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxx,,,,,,Sheetal

####Exporting Token
2022-11-08 16:39:14,100, ec2470494b14000a4a8b97fc2e9fe99e,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,EXPORT_SOFT_TOKEN,20089,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,AM_TOKEN,5ce625014b14000a16faf12f5a1a8c2a,,000000000000000000001000e0011000,xxxxxxxxxxxx,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,

####Link User to a group
2022-11-08 16:44:53,805, 041af0a44b14000a5ac911ec8aabbe79,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,LINK_GROUP_PRINCIPAL,10047,SUCCESS,,ea0065cd4b14000a4ee17012a16175b0-8aMhvKXyxxkS,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,GROUP,b34dd3ae4b14000a6215238cea95f842,000000000000000000001000d0011000,000000000000000000001000e0011000,hello,PRINCIPAL,2a6d649b4b14000a7f3135d77032aea7,000000000000000000001000d0011000,000000000000000000001000e0011000,15,

####Add Batch Job
2022-11-09 16:00:27,322, bf7234ca4b14000a003601aabbf3d1f8,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,ADD_BATCH_JOB,10212,SUCCESS,,7d0bc6cb4b14000a04f40bc3b20e7cdc-uGERKGU/apAU,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,BATCH_JOB,bf1ff7514b14000a396bc4ec29844a5f,,,mmmmmmmmmm_Sheetal_110922_1600PM_IST,,,,,,report

####Delete Batch Job
2022-11-09 16:10:31,921, 84607ef94b14000a2de66b30d5bc76a8,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,DELETE_BATCH_JOB,10209,SUCCESS,,,83c4b2654b14000a7d5d2389171b8238,000000000000000000001000d0021000,,000000000000000000001000e0011000,Sheetal,,,BATCH_JOB,bf1ff7514b14000a396bc4ec29844a5f,,,mmmmmmmmmmm_Sheetal_110922_1600PM_IST,,,,,,report

####Unklink user from group
2022-11-11 11:05:11,742, d21f61774b14000a6f0f9edbe3b1e72e,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,UNLINK_GROUP_PRINCIPAL,10048,SUCCESS,,85809ab74b14000a50502e71073bc7f6-d35TPYeudYym,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,GROUP,b34dd3ae4b14000a6215238cea95f842,000000000000000000001000d0011000,000000000000000000001000e0011000,hello,PRINCIPAL,959717074b14000a19954f8cc2ed616e,000000000000000000001000d0011000,000000000000000000001000e0011000,1235,

################################################imsRuntime.log########################################################################
  
####Authentication Success
2022-11-08 15:41:35,116, 909a83074b14000a56b008ceb86cae63,6973b47e4b14000a63cd604c54a5c1f5,,10.0.20.75,AUTHN_LOGOUT_EVENT,13001,SUCCESS,,814d2d234b14000a16da17eb9d416c93-vXIQRMWqNYNg,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,,,,,,,,,,,,,,,

####Authentication Failed
2022-11-08 15:58:40,524, 4fa925e54b14000a18c87885fb909ea4,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,AUTHN_LOGIN_EVENT,13002,FAIL,AUTHN_METHOD_FAILED,ae5645014b14000a24b68e80c00d6146-ASRsznHgyEPQ,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,Sheetal,Admin,Admin,,,,,,000000000000000000001000f0022000,RSA_Password,000000000000000000001000f0023001,RSA_Password/LDAP_Password,,,,,,,,,,,

####Authentication Success  for Operations Console
2022-11-19 09:38:00,032, 73b16efd4b14000a7a281ed8b9018c19,6973b47e4b14000a63cd604c54a5c1f5,14.97.230.214,10.0.20.75,OC_ADMIN_LOGIN_EVENT,13006,SUCCESS,,,,,,moses,,,,,,,0,,,,,,,,,,,,,,

####Security Domain
Dec 15 11:55:42 ip-10-0-20-76 2022-12-15 11:55:42,947, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.admin.impl.SecurityDomainAdministrationImpl, INFO, 77da16994c14000a3cd4a8687d4e55fa,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,CREATE_SECURITY_DOMAIN,10005,SUCCESS,,d7a626434c14000a20e4bdea6659a3f0-G8sIhKsmYghf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SECURITY_DOMAIN,778fe2a74c14000a0de8cc0249490e4f,,778fe2a74c14000a0de8cc0249490e4f,Dumbledore Army,,,,,,SystemDomain.Dumbledore Army
Dec 15 11:59:12 ip-10-0-20-76 2022-12-15 11:59:12,127, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.admin.impl.SecurityDomainAdministrationImpl, INFO, 2bff7f4e4c14000a6e2764c591db92c9,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,DELETE_SECURITY_DOMAIN,10006,SUCCESS,,d7a626434c14000a20e4bdea6659a3f0-G8sIhKsmYghf,0b8c156c4c14000a4dc267438c7a4e3d,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SECURITY_DOMAIN,778fe2a74c14000a0de8cc0249490e4f,,778fe2a74c14000a0de8cc0249490e4f,Dumbledore Army,,,,,,SystemDomain.Dumbledore Army
Dec 22 12:20:17 ip-10-0-20-76 2022-12-22 12:20:17,084, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.admin.impl.SecurityDomainAdministrationImpl, INFO, dae318704c14000a663e61e6e687e726,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_SECURITY_DOMAIN,10007,SUCCESS,,b7b79c904c14000a486c414e4e7834b4-XxcywTUyNCAf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SECURITY_DOMAIN,fcc4554d4c14000a4ff63681fbe38f9a,,fcc4554d4c14000a4ff63681fbe38f9a,Dumbledore Army,,,,,,SystemDomain.Wizarding World.Dumbledore Army

####Token policies
Dec 16 09:21:25 ip-10-0-20-76 2022-12-16 09:21:25,433, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.authmgr.internal.admin.policymgt.impl.TokenPolicyAdministrationImpl, INFO, 028556ab4c14000a77dd85689ebdfbce,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,AUTHMGR_TOKEN_POLICY_CREATE,20052,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,TOKEN_POLICY,,,000000000000000000001000e0011000,Wizarding world,,,,,,
Dec 22 10:51:33 ip-10-0-20-76 2022-12-22 10:51:33,641, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.authmgr.internal.admin.policymgt.impl.TokenPolicyAdministrationImpl, INFO, 64f57eac4c14000a1a235e6e9b077baa,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,AUTHMGR_TOKEN_POLICY_UPDATE,20053,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,TOKEN_POLICY,022b3d5a4c14000a6754776b859d487c,,000000000000000000001000e0011000,Wizarding world,,,,,,
Dec 22 10:51:40 ip-10-0-20-76 2022-12-22 10:51:40,576, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.authmgr.internal.admin.policymgt.impl.TokenPolicyAdministrationImpl, INFO, 024c5e2a4c14000a544625ace04c7866,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,AUTHMGR_TOKEN_POLICY_DELETE,20054,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,TOKEN_POLICY,000000000000000000002000f0026001,,000000000000000000001000e0011000,Initial Token Policy,,,,,,

############policies
Dec 16 09:22:09 ip-10-0-20-76 2022-12-16 09:22:09,711, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 51b44be14c14000a506af53236a0275d,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,CREATE_LOCKOUT_POLICY,10034,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,LOCKOUT_POLICY,4fa2421c4c14000a5e923fef8c44db19,,000000000000000000001000e0011000,goctechLP,,,,,,
Dec 16 09:22:09 ip-10-0-20-76 2022-12-16 09:22:09,675, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 4f8663974c14000a337f8a3d74c51584,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_LOCKOUT_POLICY,10036,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,LOCKOUT_POLICY,000000000000000000001000e0023000,,000000000000000000001000e0011000,Initial Lockout Policy,,,,,,
Dec 22 10:49:52 ip-10-0-20-76 2022-12-22 10:49:52,386, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, d1aaa7e44c14000a0f54fbe3f62bd00f,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_LOCKOUT_POLICY,10036,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,LOCKOUT_POLICY,000000000000000000001000e0023000,,000000000000000000001000e0011000,Initial Lockout Policy,,,,,,
Dec 22 10:49:57 ip-10-0-20-76 2022-12-22 10:49:57,431, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, fe57c2124c14000a25d5ae5590d5d9ec,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,DELETE_LOCKOUT_POLICY,10035,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,LOCKOUT_POLICY,000000000000000000001000e0023000,,000000000000000000001000e0011000,Initial Lockout Policy,,,,,,

####Offline authentication policies
Dec 16 09:24:20 ip-10-0-20-762022-12-16 09:23:39,992, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.authmgr.internal.admin.policymgt.impl.OfflineAuthnPolicyAdministrationImpl, INFO, 56d729f74c14000a54f93c929e4820f7,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,AUTHMGR_OFFLINE_AUTHN_POLICY_CREATE,20048,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,AUTHN_POLICY,,,000000000000000000001000e0011000,govtechOAP,,,,,,
Dec 22 12:15:33 ip-10-0-20-76 2022-12-22 12:15:33,769, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, e3f8a99c4c14000a5cde0c29b8a0dc3f,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_PWD_POLICY,10032,SUCCESS,,b7b79c904c14000a486c414e4e7834b4-XxcywTUyNCAf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,PASSWORD_POLICY,c752f20e4c14000a57280b4abbd5aaf2,,000000000000000000001000e0011000,govtechpp,,,,,,
Dec 22 10:49:36 ip-10-0-20-76 2022-12-22 10:49:36,148, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 09cdad014c14000a5ccc7da29fe6a327,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,DELETE_PWD_POLICY,10031,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,PASSWORD_POLICY,000000000000000000001000e0022000,,000000000000000000001000e0011000,Initial RSA Password Policy,,,,,,

####Password policies
2022-12-16T15:22:49+05:30 ip-10-0-20-76 2022-12-16 15:22:49,728, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, c73574d24c14000a5a22117d89495012,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_PWD_POLICY,10032,SUCCESS,,3913dd1d4c14000a2647987766f35827-qoaaAo4XBuzr,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,PASSWORD_POLICY,000000000000000000001000e0022000,,000000000000000000001000e0011000,Initial RSA Password Policy,,,,,,
2022-12-16T15:22:49+05:30 ip-10-0-20-76 2022-12-16 15:22:49,731, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, c76358ba4c14000a2d29dbdde1a83209,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,CREATE_PWD_POLICY,10030,SUCCESS,,3913dd1d4c14000a2647987766f35827-qoaaAo4XBuzr,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,PASSWORD_POLICY,c752f20e4c14000a57280b4abbd5aaf2,,000000000000000000001000e0011000,govtechpp,,,,,,

####Self-service troubleshooting policies
Dec 16 09:24:20 ip-10-0-20-76 2022-12-16 09:24:20,715, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, d21d0b934c14000a6e9e44240429c4e7,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_SELFSERVICE_POLICY,10083,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SELFSERVICE_POLICY,000000000000000000001000e0024000,,000000000000000000001000e0011000,Initial Self-Service Troubleshooting Policy,,,,,,
Dec 16 09:24:20 ip-10-0-20-76 2022-12-16 09:24:20,718, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, d2489b5d4c14000a17e6d709b73b2a00,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,CREATE_SELFSERVICE_POLICY,10081,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SELFSERVICE_POLICY,d23779494c14000a6cadf7dd571100d1,,000000000000000000001000e0011000,govtechSSTP,,,,,,
Dec 22 10:50:14 ip-10-0-20-76 2022-12-22 10:50:14,799, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 0996de994c14000a38c4aae285a6a95a,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_SELFSERVICE_POLICY,10083,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SELFSERVICE_POLICY,000000000000000000001000e0024000,,000000000000000000001000e0011000,Initial Self-Service Troubleshooting Policy,,,,,,
Dec 22 10:50:19 ip-10-0-20-76 2022-12-22 10:50:19,743, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 30437b384c14000a565e66cc804d5370,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,DELETE_SELFSERVICE_POLICY,10082,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,SELFSERVICE_POLICY,000000000000000000001000e0024000,,000000000000000000001000e0011000,Initial Self-Service Troubleshooting Policy,,,,,,

####Risk-based authenticaton policies
Dec 16 09:25:41 ip-10-0-20-76 2022-12-16 09:25:41,301, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 9570de964c14000a324ad18f337e5483,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_RBA_POLICY,10264,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,RBA_POLICY,000000000000000000001000e0028000,,000000000000000000001000e0011000,Initial Risk Based Authentication Policy,,,,,,
Dec 16 09:25:41 ip-10-0-20-76 2022-12-16 09:25:41,307, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 95c6bed84c14000a0d7d50d5e0d57376,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,CREATE_RBA_POLICY,10262,SUCCESS,,70f82c7b4c14000a01bc0496fc34a032-SzfWBcRD+w3O,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,RBA_POLICY,958ad59f4c14000a1533ad7006de27f9,,000000000000000000001000e0011000,govtechRBA,,,,,,
Dec 22 12:16:47 ip-10-0-20-76 2022-12-22 12:16:47,137, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, f90dda014c14000a619eb7d377a12291,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,UPDATE_RBA_POLICY,10264,SUCCESS,,b7b79c904c14000a486c414e4e7834b4-XxcywTUyNCAf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,RBA_POLICY,311726744c14000a70ad008d76ffc1d0,,000000000000000000001000e0011000,hovtechscRBB,,,,,,
Dec 22 12:16:53 ip-10-0-20-76 2022-12-22 12:16:53,331, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ims.authn.impl.PolicyAdministrationBase, INFO, 6a37196c4c14000a6d65947b10e87464,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,DELETE_RBA_POLICY,10263,SUCCESS,,b7b79c904c14000a486c414e4e7834b4-XxcywTUyNCAf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,RBA_POLICY,311726744c14000a70ad008d76ffc1d0,,000000000000000000001000e0011000,hovtechscRBB,,,,,,

####RBA Message policy
Dec 22 10:55:40 ip-10-0-20-76 2022-12-22 10:55:40,727, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ucm.internal.admin.impl.RBANotificationPolicyAdministrationImpl, INFO, ec694b224c14000a6525d5395d6b256d,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,RBA_NOTIFICATION_POLICY_CREATE,30058,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,,,,,,,,,,,
Dec 22 10:56:53 ip-10-0-20-76 2022-12-22 10:56:53,495, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ucm.internal.admin.impl.RBANotificationPolicyAdministrationImpl, INFO, ddb99aaa4c14000a484b5e105f56f1d6,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,RBA_NOTIFICATION_POLICY_DELETE,30060,SUCCESS,,ad39df554c14000a157eabeb835ce273-TMmpJUlxc2c4,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,,,,,,,,,,,
Dec 22 12:44:05 ip-10-0-20-76 2022-12-22 12:44:05,338, ip-10-0-20-76.ap-south-1.compute.internal, audit.admin.com.rsa.ucm.internal.admin.impl.RBANotificationPolicyAdministrationImpl, INFO, 6571093a4c14000a7852407929386b5d,ffe0d3574c14000a63cd604c9abbb64a,14.97.230.214,10.0.20.76,RBA_NOTIFICATION_POLICY_UPDATE,30059,SUCCESS,,b7b79c904c14000a486c414e4e7834b4-XxcywTUyNCAf,,000000000000000000001000d0021000,000000000000000000001000d0011000,000000000000000000001000e0011000,govtechsc,Admin,Admin,,,,,,,,,,,
 
```
  
  <li><b>Log Transmission</b></li>
	
1. Install Splunk Enterprise Edition on the Linux machine which is acting as Rsyslog Server.
2. Install Splunk addon for RSA SecurID on the Splunk Instance.
3. Enable UDP port on the Splunk.
4. In the backend navigate to <b>/opt/splunk/etc/apps/Splunk_TA_rsa-securid/local.</b> 
5. Create an inputs.conf file and add the following lines.
	
 ```
  [udp://514]
connection_host = ip 
index = <index name>
sourcetype = rsa:securid:syslog  

 ```
	
6. Save the inputs.conf
7. Once you logon to splunk after restart you should be able to find all the data which is fully parsed related to RSA Authentication Manager.
8. You will also be seeing the internal logs of the Linux machine as well as RSA machine.
9. In the same location create props.conf file and add the following line
  ```
  [rsa:securid:syslog]
TRANSFORMS-RSA-nullqueue=syslog-nullqueue, syslog-indexqueue

  ```
10. Save the file and exit .
11. Now create transforms.conf file and add the following line
 ```
  [syslog-nullqueue]
REGEX = .
DEST_KEY = queue
FORMAT = nullQueue
 
[syslog-indexqueue]
REGEX = audit.admin.com.rsa
DEST_KEY = queue
FORMAT = indexQueue


 ```
12. Now save and exit the file.
13. Create outputs.conf in <b>/opt/splunk/etc/system/local/</b> location and add the following lines:
 ```
  	 
	[indexAndForward]
	index=false
	 
	[tcpout]
	defaultGroup = default-autolb-group
	 
	[tcpout:default-autolb-group]
	server =  <any splunk instance ip:port>
	 
[tcpout-server://<any splunk instance ip:port>] 
  
```
14. You need to enable the same port in the splunk instance as you mention in the above outputs.conf file.
15. Save and exit the file and restart Splunk using <b>/opt/splunk/bin/splunk restart.</b>





  <li><b>Reference</b></li>
  <ul>
<li>Audit Log Configuration:  https://community.rsa.com/t5/securid-authentication-manager/configure-logging/ta-p/629844</li>
<li>List of Actions that can be performed by user: https://community.rsa.com/t5/securid-authentication-manager/rsa-authentication-manager-log-messages/ta-p/630160</li>
<li>Splunk Add-on for RSA SecurID: https://docs.splunk.com/Documentation/AddOns/released/RSASecurID/About</li>
  </ul>
  
  </ol>


