
## Beacon Watch

A workstation at Odapeeka State has been quietly checking in with a host outside the network, over and over, like clockwork. Nothing about any single request looks wrong.

Download the capture. Find the compromised account and how often it phones home.

Flag format: C6S{account_beacons_every_Ns}.

**Solution:**\
Compromised account is jchen as referenced by the beacon.php script. Beacon is sent every 30 seconds as observed with the first few calls and continuing on from there in the logging.

Filtered on "beacon"\
`$ tcpdump -r 11-beacon-watch.pcap | grep beacon`

Log illustration:\
**12:00:30.149376** IP 67.205.138.164.56370 > 104.248.231.191.http: Flags [P.], seq 1:144, ack 1, win 8192, length 143: HTTP: GET /c2/**beacon**.php?id=jchen&**beacon**=2&ts=1716825630 HTTP/1.1. \
**12:01:00.182628** [another beacon call]\
**12:01:30.169630** [another beacon call]

## What did they take

Same session, later. A file left the network.

Recover it. The flag is inside.

**Solution:**\
They took the file records_memo_draft.txt. when i opened the pcap in wireshark, I was able to examine  the raw content of the file and saw the flag.

## 3. Ring Ring

DNS is the most honest protocol on any network. It tells you where everything wanted to go, even when it didn't get there.

Here's a capture from the Odapeeka State office segment. Something in it asked a question it had no business asking.

Flag format: C6S{queried_domain_with_underscores}.

**Solution:**\
Filtered the list of domains and aggregated to only unique values: \
`$ tcpdump -r 20-ring-ring.pcap | grep -v 'domain >' | cut -d ' ' -f 8 | sort | uniq -c`

One of these is not like the others - sync-relay-cdn.net

## 4. Handshake problems

The connection is encrypted. You can't read the traffic.

You can read who they said they were.

Every TLS handshake exchanges a certificate in the clear, before anything is encrypted. Look at what this one actually presented versus what the client was trying to reach.

Flag format: C6S{certificate_cn_with_underscores}.

**Solution:**.\
Had to google what “cn” meant here - common name. Once I knew that, I could ask where to find that in Wireshark

Found the line in the handshake where the certs are passed (“Server hello”). Looked at Transport Security Layer for that entry.

Found common name under:

TLS
- TLSv1.2 Record Layer
- Handshake Protocol: Certificate
- Certificates
- signedCertificate
- issuer
- all values here have ==id-at-commonName=portal-relay-cdn.ru==

## 1. Slow Leak

No file left this network. No connection looks unusual. Nothing tripped.

Data still got out. Reassemble it.

DNS doesn't only carry answers back - sometimes the question itself is the payload. One domain suffix keeps repeating in this capture. Pull out every query against it, in the order they appear, and look at what's actually being asked.

Flag format: C6S{lowercase_with_underscores} - submit exactly what you decode.
  
**Solution:**\
file: 21-slow-leak.pcap

Filtered the list of entries that have odapeekastate:\
`$ tcpdump -r 21-slow-leak.pcap | grep odapeekastate` 

Subdomains of urls in log when aggregated into a string looks like a base64 encoded value. Decoding reveals flag.

Filtered only the sub-domains of the odapeekastate urls:\
`$ tcpdump -r 21-slow-leak.pcap | grep odapeekastate | cut -d ' ' -f 8 | cut -d '.' -f 1 > subdoms.txt`

Flattened the list of sub-domains into one string:\
`$ tr '\n' ' ' < subdoms.txt`

Entered the string into [https://www.dcode.fr/cipher-identifier](https://www.dcode.fr/cipher-identifier). It suggested that the value was ASCII

Flag found once this value was decoded here: [https://www.dcode.fr/ascii-code](https://www.dcode.fr/ascii-code) (Hex/2)