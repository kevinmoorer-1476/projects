
## Hash Value

Here's a file hash pulled from an EDR alert. You don't have the file and you don't need it - the internet already knows what this is.

SHA256: 24d004a104d4d54034dbcffc2a4b19a11f39008a575aa614ea04703480b1022

Look it up (VirusTotal, MalwareBazaar, or any reputable hash-intel source). What malware family does it belong to?

Flag format: C6S{family_name_lowercase}.

**Solution:**. 
Tried viruswatch and malwareBazaar - no match. Tried google Ai Mode and it suggested the family it belonged to: ==WannaCry==. Learned that attackers sometime manipulate sha to make identifying more difficult. Go figure.

## Know thy Enemy

An intrusion report on a separate Odapeeka State incident, written in plain language, no jargon:

"The attacker already had a valid, low-privileged account. Rather than install anything new, they registered a Windows Task Scheduler job that silently relaunched their backdoor process every time the machine rebooted, so the compromise would survive a restart without a new service ever appearing."

Give us the MITRE ATT&CK **technique ID** that names this behavior.

Flag format: C6S{t1234_001} (or C6S{t1234} if there's no sub-technique) - lowercase.

**Solution:**. 
Went to site [https://attack.mitre.org/tactics/TA0004/](https://attack.mitre.org/tactics/TA0004/)

Searched for “Windows Task Scheduler” and found attack closest to the one described. 

## Attribution is Hard

Six indicators of compromise, pulled from three separate-looking incidents at Odapeeka State over eight months.

**Incident A - Mar 2026, records portal defacement attempt**

- A1: source IP 104.248.231.191
- A2: user-agent Mozilla/4.0 (compatible; Synapse/1.2)

**Incident B - Jul 2026, phishing against three staff mailboxes**

- B1: sender domain secure-odapeeka-verify.com - passive DNS shows this domain resolved to 104.248.231.191 for two weeks in June, before the registrant moved it elsewhere
- B2: attached macro hash SHA256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08

**Incident C - Sep 2026, the workstation beacon (see: Beacon Watch, What Did They Take)**

- C1: C2 address 104.248.231.191
- C2: beacon interval 30 seconds

Three of these six belong to the same actor - not necessarily all from the same incident letter. Which indicator links them?

Flag format: C6S{the_shared_indicator_dots_as_underscores}.

**Solution:**. 
IP address is the same in all attacks.
