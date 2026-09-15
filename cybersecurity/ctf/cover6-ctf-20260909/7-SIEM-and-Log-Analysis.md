
## First Watch

You've just taken the overnight shift. Here's the auth log for the last 24 hours at Odapeeka State. One account logged in from somewhere it never has before. Find the username.

Flag format: C6S{username}.

**Solution:**. 
lfile: 13-auth.log

==jchen== is not listed as existing staff. We see he tried many times and finally got in from a public ip address that is different from what appears to be internal ip addresses at 10.20.30.*
  
## Patient Zero

Same night, wider view. The anomalous login wasn't the beginning. Work backwards. What did the attacker do _first_ - before they ever had a valid credential? Name the technique.

Flag format: C6S{technique_name}.

**Solution:**. 
file: 14-connections.log

The log shows he was looking at a lot of different ports on the machine first (ie. DPT=22) . This is called a ==port scan==.

## Needle, Haystack

Thousands of failed logins against Odapeeka State's VPN gateway over six hours. Rotating source addresses, one or two guesses per account - somebody built this to stay under any per-account lockout threshold.

Then one combination worked.

Which account, and which address let them in?

Flag format: C6S{username_source_ip_with_underscores}.

**Solution:**. 
Filtered out the "Failed" entries in the log to narrow the list:  
`$ cat 23-vpn-auth.log | grep -v Failed`

Filtered log shows svalenti was able to log in.

## Living off the land

Nothing malicious ran on this box. Every binary in this execution log ships with the operating system, signed, built-in, boring.

Something bad still happened. One of these ordinary tools got used for a job it was never meant for - twice, back to back - and then whatever it produced got run.

Which binary was abused?

Flag format: C6S{binary_name_lowercase} (include the extension, dot -> underscore).

**Solution:**. 
file: 29-endpoint-exec.log

Looks like he ran certutil and added his IP address. 

You can see the entry here:  
`$ cat 29-endpoint-exec.log | grep certutil`