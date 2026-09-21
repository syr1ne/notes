## What is ActiveDirectory?:
Active directory is a directory service for windows network environments. it allows for centralised management of an organisation's resources (basically objects) like users, computers, network devices, file shares, and including authentication and authorisation functions within the windows domain environment.
- Active Directory uses Kerberos (primary) and NTLM (legacy) for authentication.
- Active Directory is a read-only directory service which means a domain user with no privileges can still enumerate lot of stuff on AD network.
- Domain Users cannot by-default login to Domain Controller, unless explicitly set.
- Having local administrator access on a Domain Controller (DC) means you have full access to Active Directory Users and Computers (ADUC).

#### Key Terminology:
- **Objects** are ANY resource presents within an AD environment such as OUs, printers, users, computers, etc.
- **Attributes** are the properties of an object—for example, a user's username, email address, or group memberships.
- **Domains** are the logical group of objects. Domains can operate entirely independently of one another or be connected via trust relationships.
- **Forest** is a collection of Active Directory domains. it is the topmost container. Each forest operates independently but may have various trust relationships with other forests.
- **Tree** is a collection of AD domains that begins at a single root domain. A forest is a collection of AD trees. A parent-child trust relationship is formed when a domain is added under another domain in a tree. All domains in a tree share a standard Global catalog which contains all information about objects that belong to the tree.
- **Containers** are objects that can hold other objects and have a defined place in the directory subtree hierarchy.
- **Organisational Units (OUs)** are special container objects primarily used for organising objects and applying Group Policy or delegated administration.
- **Leaf** Lead objects do not contain other objects and are found at the end of the subtree hierarchy.
- **Global Unique Identifier (GUID)** is a unique 128-bit value assigned to every single object created within Active Directory. The GUID is stored in `ObjectGUID` attribute. When querying and searching for objects in AD, we can user name, GUID, SID or SAM account name. However, using GUID will provide most accurate results as it is always unique and never changes as long as that object exists in the domain.
- **Security principals** in AD, are domain objects that can manage access to other resources within the domain. These are not managed by AD but rather by the ***Security Accounts Manager (SAM)***
- **Security Identifier (SID)** is used as unique identifier for a security principal or security group. A SID can only be used once. Even if the security principal is deleted, it can never be used again in that environment to identify another user or group.
- **Distinguished Name (DN)** describes the full path to an object in AD (such as `cn=bjones, ou=IT, ou=Employees, dc=inlanefreight, dc=local`). In this example, the user `bjones` works in the IT department of the company Inlanefreight, and his account is created in an Organizational Unit (OU) that holds accounts for company employees. The Common Name (CN) `bjones` is just one way the user object could be searched for or accessed within the domain.
- **Relative Distinguished Name (RDN)** is a single component of the Distinguished Name that identifies the object as unique from other objects at the current level in the naming hierarchy. In our example, `bjones` is the Relative Distinguished Name of the object. AD does not allow two objects with the same name under the same parent container, but there can be two objects with the same RDNs that are still unique in the domain because they have different DNs. For example, the object `cn=bjones,dc=dev,dc=inlanefreight,dc=local` would be recognized as different from `cn=bjones,dc=inlanefreight,dc=local`.
![](../../attachments/Pasted%20image%2020260921120300.png)
- **sAMAccountName** is the user's logon name. Here it would just be `bjones`. It must be a unique value and 20 or fewer characters.
- **userPrincipalName** attribute is another way to identify users in AD. This attribute consists of a prefix (the user account name) and a suffix (the domain name) in the format of `bjones@inlanefreight.local`. This attribute is not mandatory.
- **Flexible Single Master Operation (FSMO) Roles**
- **Global Catalog**
- **Read-Only Domain Controller (RODC)**
- **Replication**
- **Service Principal Name (SPN)**
- **Group Policy Object (GPO)**
- **Access Control List (ACL)**
- **Access Control Entries (ACEs)**
- **Discretionary Access Control List (DACL)**
- **System Access Control Lists (SACL)**
- **Fully Qualified Domain Name (FQDN)**
- **Tombstone**
- **AD Recycle Bin**
- **SYSVOL**
- **AdminSDHolder**
- **dsHeuristics**
- **adminCount**
- **Active Directory Users and Computers (ADUC)**
- **ADSI Edit**
- **sIDHistory**
- **NTDS.DIT**
- **MSBROWSE**

#### Main Active Directory components
- **AD DS — Active Directory Domain Services**
    - The core directory service.
    - Manages **users, computers, groups, domains, OUs**, authentication, authorisation etc.
    - This is what people usually mean when they casually say "Active Directory."
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
- **AD UC — Active Directory Users and Computers**
	- 


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
