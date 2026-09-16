
## View Source

The Odapeeka State Department of Records just launched a new site. They were in a hurry.

Target: http://target.cover6solutions.com

Before you touch a tool, look at what the page is already telling you.

**Solution:**\
Title of challenge is a clue. Let's view the source of the homepage:\
`$ curl https://target.cover6solutions.com`

Found flag in head element of html

## Ask Politely

Most sites keep a file telling search engines where _not_ to look. It's a suggestion, not a lock - and it's a map of everything they'd rather you didn't find.

**Solution:**\
Search engines use the robots.txt file located at the root of a site to determine what can be indexed. I looked at the contents of that file:\ 
`$ curl https://target.cover6solutions.com/robots.txt`

Found flag at the bottom of the text in the file.

## Read the Envelope

A page is more than what you can see. Every response carries headers - the envelope it arrived in. Somebody at Odapeeka State left something in theirs.

**Solution:**\
We need to inspect the headers of the page(s). I started with the homepage:\
`$ curl -I [https://target.cover6solutions.com]`

Found flag in x-secret-token header

## Nobody Cleans Up

Robots.txt told you where they didn't want you looking. Go look.

**Solution:**\
One of the paths blocked for crawlers is /admin-backup. I looked into that:\
`$ curl -L https://target.cover6solutions.com/admin-backup/`

Reveals public directory listing. Clicked on file readme.txt. Flag found inside at bottom of file.

## The Front Desk

There's a staff portal. It's password protected, which would matter more if anyone had changed the password.

**Solution:**\
readme.txt file from “Nobody Cleans Up” told us the password. The link to the staff portal is on the home page. Going to it in browser triggers password challenge. At first, I tried all of the listed employee names, ie. mreyes, panand with the password. When all failed, I tried the user that many use as default - “admin.” That worked. Flag is revealed on this page.

## Cookie Jar

You're logged into the staff portal as a clerk. The site decides what you're allowed to see by asking your browser who you are.

That's the mistake. Exploit it.

**Solution:**\
Looked at page and saw “if you need admin” link and clicked it. in the Source of that page, in the javascript, a condition of “if role == admin, go to admin_vault file”. I loaded that url in the browser. Flag was there. But this is Cover6 level and not intended to be used for the hunt! The instructions say exploit the cookie. Took a look in Inspect tools under Chrome: Application > Storage > Cookies and found the value for “role” appears to be base64 encoded. Got the value for admin and manually replaced it in Inspect. The flag showed up in the newly revealed contents of the page.

This is how we got the value for base64 of "admin". 
$ echo -n admin | base64

## Parameter Tampering

The records portal shows you your own file. It decides which file that is by asking you. Target: https://target.cover6solutions.com/records/ IDOR. Iterate ?id= to find the flagged record.

**Solution:**\
The title of challenge invites us to tamper with the url parameter. Incremented id in url until page revealed flag. 

## Handle Hunt

Odapeeka State publishes a staff directory. Four employees, four profile pages, four sets of details that mostly agree.

Target: https://target.cover6solutions.com/directory/

One of them is lying about something. Find the inconsistency.

**Solution:**\
Visited all pages under "directory/" path with browser until flag revealed on Devon Cole page




