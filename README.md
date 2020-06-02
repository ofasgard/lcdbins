# lcdbins

An **lcdbin** is a lowest-common denominator binary; a platform-agnostic oneliner that should work on almost any UNIX environment.

Parse listening ports on /proc/net/tcp:

```shell
for i in $(awk -F "[ :]+" '{print $4}' /proc/net/tcp); do bc <<< "obase=10; ibase=16; $i"; done | sort -un
```

Get all IP addresses in /etc/:

```shell
grep -Eo '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' /etc/* 2>/dev/null
```
