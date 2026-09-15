
## Shorthand

IPv6 addresses can be written more than one way, and people get lazy.

Expand this to its full, uncompressed form. That's your flag, lowercase, colons replaced with underscores.

2001:db8::c6:0:0:6

**Solution:**
Expand the value based on the rules of proper IPv6 formatting.
1. Empty colons - each expands to 0000
2. Restore leading zeros to fill each group’s 4 char slot. db8 -> 0db8, c6 -> 00c6, 0 -> 0000 and 6 -> 0006 

## Nobody Turned it off

Odapeeka State runs an IPv4-only network. That's what the policy says.

Here's a capture from their office segment. The policy is wrong - prove it, and tell us the address that shouldn't be there. Submit it exactly as it appears in the capture, colons replaced with underscores.

**Solution:** 
file: 10-nobody-turned-it-off.pcap

Found ipv6 entries in log as policy as not working. Formatted flag with IPv6 address found

## Neighborly

On an IPv6 network, machines introduce themselves. Anyone can make an introduction - including someone claiming to be the router.

Who's lying in this capture? Not every Router Advertisement here comes from Odapeeka State's real gateway - and the impostor isn't exactly being subtle about it once you know what a router actually behaves like.

Flag format: C6S{mac_address_with_underscores} - the rogue router's source MAC.

**Solution:** 
file: 22-neighborly.pcap

Had to use Wireshark. The third advertisement in the log looked weird and was repeated several times. Inspected and found strange MAC address Src value.

## The Long Way Around

Scanning an IPv4 /24 takes seconds. Scanning an IPv6 /64 - 18 quintillion addresses - would take longer than the universe has existed at any realistic packet rate.

But every IPv6 host on a segment is required to listen on one specific, well-known multicast address whether it wants to or not - which is exactly how hosts actually get discovered on IPv6 networks, without ever brute-forcing the address space.

Name that address, written exactly the way it's normally written.

Flag format: colons -> underscores, so ff02::1 becomes C6S{ff02__1} (double underscore for the double colon).

**Solution:**
ff02::1 is the all nodes multicast address. Formatted flag with the address and completed this challenge.