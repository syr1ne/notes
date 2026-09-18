## key takeaways:
- Domain controllers have a local SAM database with users and groups, but unlike domain member computers, the "Local Users and Groups" management interface is not typically accessed on them. However, DCs do have a built-in local Administrator group that if any non "Domain Admins" group user is a member of, they can still use ADUC (Active Directory Users and Computers) with full rights.

> "Computer Management" -> "System tools" -> "Local Users and Groups"

- Domain Users cannot by-default login to Domain Controller, unless explicitly set.
- Active Directory uses Kerberos (primary) and NTLM (legacy) for authentication.

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

## LLMNR poisoning
#### what is LLMNR?
Link-Local Multicast Name Resolution (LLMNR) is used to identify and resolve hosts and hostnames when DNS fails to do so.
![](../../attachments/Pasted%20image%2020260907171119.png)
- previously, **NBT-NS (NetBIOS over TCP/IP Name Service)** is being used.
- key flaw is that the services utilise user's username and NTLMv2 password hash when appropriately responded to.
- to capture username and NTLMv2 hash, we will use "Responder" tool. this is the first thing that should be running in the background in the pentest engagement.
#### mitigations
- Disable LLMNR, NBT-NS
- if cannot disable LLMNR / NBT-NS, then
	- require Network Access Control
	- require strong user passwords.

![](../../attachments/Pasted%20image%2020260907171152.png)

## NTLM relay attack
- NTLM relay attack involves capturing live authentication exchange and forwarding it to the victim to authenticate as that user without cracking the password-derived hash.
- NTLM relay attack is different from pass-the-hash attack. pth reuses a stolen NT hash, while relay attack forwards live authentication exchange to another service.
- `impacket-ntlmrelayx` tool automates NTLM relay.
	- it listens for inbound NTLM authentication on protocols like SMB, HTTP, LDAP etc.
	- forwards the captured exchange to SMB, LDAP supported services.
- there are SMB, LDAP, HTTP, MSSQL relay attacks as well but they are all sub-part of NTLM relay attack where the NTLM authenticates to the destination services like SMB, LDAP, HTTP, MSSQL respectively.

**Requirements**
- victim must authenticate to attacker-controlled listener.
- the target must accept NTLM.
- the target service must lack effective relay protections.

**Defenses**
- Prefer kerberos and reduce or disable NTLM.
- Require SMB and LDAP signing.


> To get the NTLMv1 or NTLMv2 hash, we perform LLMNR poisoning and NTLM relay attack. the NTLMv1 or NTLMv2 hash we get are the challenge-response hash and we cannot use for pass-the-hash attack.
> **Then how can we get NTLM (or NT) hash?**
> As we already know, NTLM hash are MD4 hash that are used to generate NTLMv1 and NTLMv2 challenge-response, so the NTLM hash never gets transferred over any protocol through the internet. So, we cannot capture it on network. to get the NT hash, we need to extract it from the memory or registry of a domain-joined computer using: Mimikatz on LSASS process, SAM registry hive dump. Result: Raw MD4 hash → usable for PtH


