## What is ActiveDirectory?:
Active directory is a directory service for windows network environments. it allows for centralised management of an organisation's resources (basically objects) like users, computers, network devices, file shares, and including authentication and authorisation functions within the windows domain environment.
- Active Directory uses Kerberos (primary) and NTLM (legacy) for authentication.
- Active Directory is a read-only directory service which means a domain user with no privileges can still enumerate lot of stuff on AD network.
- Domain Users cannot by-default login to Domain Controller, unless explicitly set.
- Having local administrator access on a Domain Controller (DC) means you have full access to Active Directory Users and Computers (ADUC).

#### Key Terminology:
- **Objects** are ANY resource presents within an AD environment such as OUs, printers, users, computers, etc.
- **Attributes** are the properties of an object—for example, a user's username, email address, or group memberships.
- **Schema** is the blueprint of any enterprise environment. It defines what types of objects can exist in the AD database and their associated attributes. It lists definitions corresponding to AD objects and holds information about each object. When an object is created from a class, this is called instantiation, and an object created from a specific class is called an instance of that class. For example, if we take the computer RDS01. This computer object is an instance of the "computer" class in Active Directory.
- **Domains** are the logical group of objects. Domains can operate entirely independently of one another or be connected via trust relationships.
- **Forest** is a collection of Active Directory domains. it is the topmost container. Each forest operates independently but may have various trust relationships with other forests.
- **Tree** is a collection of AD domains that begins at a single root domain. A forest is a collection of AD trees. A parent-child trust relationship is formed when a domain is added under another domain in a tree. All domains in a tree share a standard Global catalog which contains all information about objects that belong to the tree.
- **Containers** are objects that can hold other objects and have a defined place in the directory subtree hierarchy.
- **Organisational Units (OUs)** are special container objects primarily used for organising objects and applying Group Policy or delegated administration.
- **Leaf** objects do not contain other objects and are found at the end of the subtree hierarchy. For Example: Users, Contacts, Printers, and Computers.
- **Global Unique Identifier (GUID)** is a unique 128-bit value assigned to every single object created within Active Directory. The GUID is stored in `ObjectGUID` attribute. When querying and searching for objects in AD, we can query user name, GUID, SID or SAM account name. However, using GUID will provide most accurate results as it is always unique and never changes as long as that object exists in the domain.
- **Security principals** in AD are entities that can manage access to other resources within the domain. These are not managed by AD but rather by the ***Security Accounts Manager (SAM)***. There are 3 main types of security principals: Users, Groups, Computers.
- **Security Identifier (SID)** is used as unique identifier for a security principal or security group. A SID can only be used once. Even if the security principal is deleted, it can never be used again in that environment to identify another user or group. SID looks like this: `S-1-5-21-3623811015-3361044348-30300510-1013`
- **Distinguished Name (DN)** describes the full path to an object in AD (such as `cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local`). In this example, the user `bjones` works in the IT department of the company Inlanefreight, and his account is created in an Organizational Unit (OU) that holds accounts for company employees. The Common Name (CN) `bjones` is just one way the user object could be searched for or accessed within the domain.
- **Relative Distinguished Name (RDN)** is a single component of the Distinguished Name. AD does not allow two objects with the same name under the same parent container, but there can be two objects with the same RDNs that are still unique in the domain because they have different DNs. For example, the object `cn=bjones,dc=dev,dc=inlanefreight,dc=local` would be recognized as different from `cn=bjones,dc=inlanefreight,dc=local`.
![](../../attachments/Pasted%20image%2020260921120300.png)
- **sAMAccountName** is an Active Directory attribute that stores user's logon name. Here it would just be `bjones`. It must be a unique value and 20 or fewer characters.
	- when logging into a Windows domain, you can use the sAMAccountName instead of the full DN name:
		- **sAMAccountName:** `INLANEFREIGHT.LOCAL\bjones`
		- **Instead of:** `CN=bjones,OU=Users,DC=inlanefreight,DC=local`
- **userPrincipalName** attribute is another way to identify users in AD. This attribute consists of a prefix (the user account name) and a suffix (the domain name) in the format of `bjones@inlanefreight.local`. This attribute is not mandatory.
- **Flexible Single Master Operation (FSMO) Roles** are specialised Active Directory functions held by a single domain controller to prevent conflicts. The five roles include Schema Master (schema changes), Domain Naming Master (domain creation), RID Master (security identifiers), PDC Emulator (password changes and time sync), and Infrastructure Master (cross-domain references). These single-master roles ensure consistency since certain critical operations cannot use multi-master replication without causing conflicts.
- **Global Catalog (GC)** is a domain controller that stores a full copy of all objects in the current domain and a partial copy of objects that belong to other domains in the forest. The GC allows both users and applications to find information about any objects in ANY domain in the forest.
- **Read-Only Domain Controller (RODC)** is a domain controller with a read-only copy of Active Directory that cannot make changes or replicate back to writable DCs. It's used in branch offices or untrusted locations to reduce security risk. If compromised, no AD data can be modified. RODCs also reduce replication traffic and support offline authentication.
- **Replication** happens in AD when AD objects are updated and transferred from one Domain Controller to another. Whenever a DC is added, connection objects are created to manage replication between them. These connections are made by the Knowledge Consistency Checker (KCC) service, which is present on all DCs. Replication ensures that changes are synchronised with all other DCs in a forest, helping to create a backup in case one domain controller fails.
- **Service Principal Name (SPN)** is a unique identifier that associates a service with a service account in Active Directory, used for Kerberos authentication. It's formatted as `service/hostname:port` (e.g., `HTTP/webserver.domain.com` or `MSSQLSvc/sqlserver.domain.com:1433`). When a client needs to authenticate to a service, it uses the SPN to request a service ticket from the Kerberos ticket-granting service. SPNs must be registered in AD on the service account and must be unique within the domain. Without a properly configured SPN, Kerberos authentication fails and the system falls back to NTLM.
- **Group Policy Objects (GPOs)** are collections of policy settings. Each GPO has a unique GUID. A GPO can contain local file system settings or Active Directory settings. They can be applied to all users and computers within the domain or defined more granularly at the OU level.
- **Access Control List (ACL)** is the ordered collection of Access Control Entries (ACEs) that apply to an object.
- **Access Control Entries (ACEs)** each ACEs in an ACL identifies a trustee (user account, group account, or logon session) and lists the access rights that are allowed, denied, or audited for the given trustee.
- **Discretionary Access Control List (DACL)** define which security principals are granted or denied access to an object; it contains a list of ACEs. When a process tries to access a securable object, the system checks the ACEs in the object's DACL to determine whether or not to grant access. If an object does NOT have a DACL, then the system will grant full access to everyone, but if the DACL has no ACE entries, the system will deny all access attempts. ACEs in the DACL are checked in sequence until a match is found that allows the requested rights or until access is denied.
- **System Access Control Lists (SACL)** allows for administrators to log access attempts that are made to secured objects. ACEs specify the types of access attempts that cause the system to generate a record in the security event log.
- **Fully Qualified Domain Name (FQDN)** is the complete name for a specific computer or host, written with the hostname and domain name in the format `hostname.domain_name.tld`. This is used to specify an object's location in the tree hierarchy of DNS. The FQDN can be used to locate hosts in an Active Directory without knowing the IP address. An example would be the host `DC01` in the domain `INLANEFREIGHT.LOCAL`. The FQDN here would be `DC01.INLANEFREIGHT.LOCAL`.
- **Tombstone** is a container object in AD that holds deleted AD objects. When an object is deleted from AD, the object remains for a set period of time known as the `Tombstone Lifetime,` and the `isDeleted` attribute is set to `TRUE`. Once an object exceeds the `Tombstone Lifetime`, it will be entirely removed. Microsoft recommends a tombstone lifetime of 180 days to increase the usefulness of backups, but this value may differ across environments. Depending on the DC operating system version, this value will default to 60 or 180 days. If an object is deleted in a domain that does not have an AD Recycle Bin, it will become a tombstone object. When this happens, the object is stripped of most of its attributes and placed in the `Deleted Objects` container for the duration of the `tombstoneLifetime`. It can be recovered, but any attributes that were lost can no longer be recovered.
- **AD Recycle Bin** was first introduced in Windows Server 2008 R2 to facilitate the recovery of deleted AD objects. This made it easier for sysadmins to restore objects, avoiding the need to restore from backups, restarting Active Directory Domain Services (AD DS), or rebooting a Domain Controller. When the AD Recycle Bin is enabled, any deleted objects are preserved for a period of time, facilitating restoration if needed. Sysadmins can set how long an object remains in a deleted, recoverable state. If this is not specified, the object will be restorable for a default value of 60 days. The biggest advantage of using the AD Recycle Bin is that most of a deleted object's attributes are preserved, which makes it far easier to fully restore a deleted object to its previous state.
- **SYSVOL** folder, or share, stores copies of public files in the domain such as system policies, Group Policy settings, logon/logoff scripts, and often contains other types of scripts that are executed to perform various tasks in the AD environment. The contents of the SYSVOL folder are replicated to all DCs within the environment using File Replication Services (FRS). You can read more about the SYSVOL structure [here](https://networkencyclopedia.com/sysvol-share/#Components-and-Structure).
- **AdminSDHolder** object contains an Access Control List (ACL) that is automatically applied to protected groups in Active Directory. A background process called ***SDProp (SD Propagator)*** uses this template to enforce consistent security permissions on critical groups to prevent privilege escalation or unauthorized access from accidental or malicious modifications.
- **dsHeuristics** is a multi-valued string attribute on the Directory Service object that controls various behavioral settings and feature toggles throughout Active Directory. It acts as a configuration mechanism for AD-wide policies and features. One of these settings is to exclude built-in groups from the Protected Groups list. Groups in this list are protected from modification via the `AdminSDHolder` object. If a group is excluded via the `dsHeuristics` attribute, then any changes that affect it will not be reverted when the SDProp process runs.
- **adminCount**is a binary attribute on user and group objects that indicates whether an account is a member of a protected group or has been delegated administrative privileges. If the value is set to `0` or not specified, the user is not protected. If the attribute value is set to `1`, the user is protected. Critically, adminCount remains set to `1` even after an account is removed from privileged groups, making it a high-value target for attackers during internal reconnaissance. These accounts often retain elevated access rights and can serve as a stepping stone to further compromise or full domain takeover.
- **ADSI Edit** is a GUI tool that provides direct access to Active Directory objects and attributes through the ADSI (Active Directory Service Interface) API, displaying the directory structure in a tree view. It exposes far more functionality than standard tools like ADUC, allowing administrators to set or delete any attribute on an object, as well as add, remove, and move objects throughout the directory. While invaluable for troubleshooting and making advanced changes, ADSI Edit should be used with extreme caution. Modifications here can cause major problems in Active Directory and potentially compromise directory integrity.
- **sIDHistory** is a multi-valued AD attribute that stores previous Security Identifiers (SIDs) assigned to user and group objects. It is primarily used during domain migrations to allow accounts to maintain their existing access rights to resources when moved to a new domain or forest. However, sIDHistory poses a critical security risk if not properly managed, as attackers can abuse this attribute to escalate privileges, impersonate accounts with elevated access, or gain unauthorised resource access; this threat is mitigated by enabling SID Filtering, which removes SIDs from untrusted domains from a user's access token and should be verified after any migration.
- **NTDS.DIT** file can be considered the heart of Active Directory. It is stored on a Domain Controller at `C:\Windows\NTDS\` and is a database that stores AD data such as information about user and group objects, group membership, and, most important to attackers and penetration testers, the password hashes for all users in the domain. Once full domain compromise is reached, an attacker can retrieve this file, extract the hashes, and either use them to perform a pass-the-hash attack or crack them offline using a tool such as Hashcat to access additional resources in the domain. If the setting Store password with reversible encryption is enabled, then the NTDS.DIT will also store the cleartext passwords for all users created or who changed their password after this policy was set. While rare, some organisations may enable this setting if they use applications or protocols that need to use a user's existing password (and not Kerberos) for authentication.
- **MSBROWSE** is a Microsoft networking protocol that was used used by the Windows Computer Browser service for Master Browser election and network browsing. It allows Windows computers to discover and browse shared resources on a LAN, with the Master Browser collecting and distributing lists of available computers and shares to other machines on the network. It's primarily a legacy Windows networking protocol; modern networks typically rely on SMB, DNS and Active Directory instead.
	- In older version of Windows we could use `nbtstat -A ip-address` to search for the Master Browser. If we see *MSBROWSE* it means that's the Master Browser. Additionally, we could use `nltest` utility to query a Windows Master Browser for the names of the Domain Controllers.
	- Today, MSBROWSE is largely obsolete and is no longer in widespread use. Modern Windows-based LANs use the Server Message Block (SMB) protocol for file and printer sharing, and the Common Internet File System (CIFS) protocol for browsing services.
- **Sites** a set of computers across one or more subnets connected using high-speed links. They are used to make replication across domain controllers run efficiently.
- **Built-in** is a container that holds default groups in an AD domain. They are predefined when an AD domain is created.
- **Foreign Security Principals (FSP)** is an object created in AD to represent a security principal that belongs to a trusted external forest. They are created when an object such as a user, group, or computer from an external (outside of the current) forest is added to a group in the current domain. They are created automatically after adding a security principal to a group. Every foreign security principal is a placeholder object that holds the SID of the foreign object (an object that belongs to another forest.) Windows uses this SID to resolve the object's name via the trust relationship. FSPs are created in a specific container named ForeignSecurityPrincipals with a distinguished name like `cn=ForeignSecurityPrincipals,dc=inlanefreight,dc=local`.
#### Main Active Directory components
- **AD DS — Active Directory Domain Services**
    - The core directory service.
    - Manages **users, computers, groups, domains, OUs**, authentication, authorisation etc.
    - This is what people usually mean when they casually say "Active Directory."
- **AD UC — Active Directory Users and Computers**
	- is a GUI console commonly used for managing users, groups, computers, and contacts in AD. Changes made in ADUC can be done via PowerShell as well.
- **AD CS — Active Directory Certificate Services**
    - Provides **PKI (Public Key Infrastructure)**.
    - Issues and manages digital certificates.
    - Used for things like TLS certificates, smart cards, Wi-Fi/VPN authentication, and certificate-based authentication.
- **AD FS — Active Directory Federation Services**
    - Provides **federation and SSO**.
    - Allows users authenticated by one organisation/domain to access applications or services that trust that identity provider.
- **AD LDS — Active Directory Lightweight Directory Services**
    - A lightweight directory service based on LDAP.
    - Useful when an application needs a directory but **doesn't require a full AD DS domain**.
- **AD RMS — Active Directory Rights Management Services**
    - Provides **information protection/rights management**.
    - Can control how protected documents and other content are accessed and used.


### Kerberos Authentication Flow
When you log into a domain computer, this happens:
1. User submits credentials to the Kerberos **Key Distribution Center (KDC)** running on the Domain Controller
2. KDC checks if the user exists and verifies the password hash matches
3. User receives a Ticket Granting Ticket (TGT) — a token valid for ~10 hours that says "this user is authenticated"
4. When accessing a resource (like a file server), the user presents the TGT
5. KDC issues a Service Ticket — a time-limited token proving the user can access that specific service
6. User sends the Service Ticket to the resource, and access is granted

The key insight: All credentials are hashes, not plaintext. Passwords are stored as NTLM hashes (MD4 hash of the password).

### NTLM Authentication Flow
NTLM (NT LAN Manager) is the fallback authentication protocol when Kerberos fails. It's older, weaker, and more exploitable—which is why it's central to many AD attacks.

It uses a **challenge–response process**:
1. The server sends a random challenge.
2. The client combines it with a password-derived hash.
3. The client sends back the response.
4. The server verifies it without receiving the plaintext password.

The NTLM challenge-response process involves:
- NT hash: `MD4(UTF-16LE(password))`. This is the password-derived value stored or obtained by the authentication system.
- NTLMv1 response: Uses the NT hash as a DES key source to encrypt the server’s 8-byte challenge, producing a 24-byte response.
- NTLMv2 response: Uses the NT hash as the key for an HMAC-MD5 operation involving the username, domain, server challenge, client challenge, timestamp, and other data.
```
password
   ↓ MD4
NT hash
   ↓
NTLMv1 or NTLMv2 challenge-response calculation
   ↓
response sent to the server
```

## Why NTLM Is Weak

- Hash = Password: Knowing the hash is like knowing the password. Used in Pass-the-Hash attacks.
- No mutual authentication: Client doesn't verify server. Attackers relay auth between two parties without knowing credentials.
- Weak encryption: Uses DES (56-bit), not modern encryption.
- Static secret: Password hash never changes, unlike Kerberos session keys.
