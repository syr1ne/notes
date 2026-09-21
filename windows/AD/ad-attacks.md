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
- NTLM relay attack is different from pass-the-hash attack. PtH reuses a stolen NT hash, while relay attack forwards live authentication exchange to another service.
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

## Pass-the-Hash attack

**How NTLM works:**
```
Client: Password → MD4 hash → Encrypt challenge → Server verifies
```

**How PtH works:**
```
Attacker: Stolen MD4 hash → Encrypt challenge → Server verifies
         (No password needed!)
```
**Key Insight:** Server doesn't verify how you got the hash, only that challenge-response is correct.

