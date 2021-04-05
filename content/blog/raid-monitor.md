---
title: "RAID Monitor for LSI/3Ware MegaRAID controllers"
date: 2021-04-05T22:17:41+02:00
tags: ["devops", "python", "sysadmin"]
draft: false
---

I still manage a few old servers with 3Ware/LSI MegaRAID controllers (e.g. the 9650SE)
and wrote
the following Python3 script which is sitting in `/etc/cron.daily` and sends
and email if there is any problem with the battery, disks or units.
Keep in mind that scripts inside the `/etc/cron.*` folders are not allowed
to have a dot in the filename, otherwise those will be ignored.

If you don't have a working mail configuration, I recommend `exim4` which is
easily set up in a few minutes with standard settings.

Here is the script, make sure to change the `TW_CLI_CMD`, `CONTROLLER` and
`ALERT_MAIL` and of course feel free to [copy transform and combine](https://www.youtube.com/watch?v=nJPERZDfyWc).

```python
#!/usr/bin/env python3
"""
Monitoring and mail alert for LSI/3Ware RAID systems, like the 9650SE.

Author: Tamas Gal
Email: tamas.gal@fau.de
Date: 2021-03-25

"""
import re
import smtplib
import socket
import subprocess

TW_CLI_CMD = "/usr/sbin/tw_cli"
CONTROLLER = "c0"
ALERT_MAIL = "..."


def check_units(stdout):
    """Check all RAID units and return True if they are OK"""
    units_status = re.search(r'\nu\d+\s+[^ ]+\s+([^ ]+)', stdout).groups()
    return all(s == "OK" for s in units_status)


def check_disks(stdout):
    """Check all disks and return True if they are OK"""
    disks_status = re.search(r'\np\d+\s+([^ ]+)', stdout).groups()
    return all(s == "OK" for s in disks_status)


def check_battery(stdout):
    """Check the battery and return True if it's OK"""
    battery_status = re.search(r'\nbbu\s+([^\s]+)\s+([^\s]+)\s+([^\s]+)\s+([^\s]+)\s+([^\s]+)', stdout).groups()
    return battery_status == ('On', 'Yes', 'OK', 'OK', 'OK')


if __name__ == "__main__":

    process = subprocess.Popen(
        [TW_CLI_CMD, f"/{CONTROLLER}", "show"],
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE)

    stdout, stderr = [s.decode() for s in process.communicate()]

    alert_messages = []

    if not check_units(stdout):
        alert_messages.append("Not all units are OK")

    if not check_disks(stdout):
        alert_messages.append("Not all disks are OK")

    if not check_battery(stdout):
        alert_messages.append("Battery is not OK")

    if stderr:
        alert_messages.append(f"STDERR:\n{stderr}")

    if alert_messages:
        hostname = socket.gethostbyaddr(socket.gethostname())[0]
        sender = "root@" + hostname
        subject = 'RAID alert!'
        message = f"From: <{sender}>\nTo: {ALERT_MAIL}\nSubject: {subject}\n"
        message += "\n\n".join(alert_messages)
        message += f"\n\nRAID status:\n{stdout}"

        smtplib.SMTP('localhost').sendmail(sender, ALERT_MAIL, message)

        exit(1)
```
