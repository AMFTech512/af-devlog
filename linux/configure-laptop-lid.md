---
title: Configuring sleep/lock on laptop lid close
date: 2022-10-25T18:56:09Z
---

I used the info from this reddit post: https://www.reddit.com/r/i3wm/comments/5g86f1/suspend_on_lid_close/

When writing the systemd service, you have to put the config file in `/etc/systemd/system/suspend.service`

I had to change the `ExecStart` line to be `/usr/bin/i3lock` (since I was using i3wm).

Also, according to https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/assembly_working-with-systemd-unit-files_configuring-basic-system-settings#important-unit-specifiers_assembly_working-with-systemd-unit-files, the `%I` (specified for the user in the reddit post) actually just represents the instance name for the service when it runs. Maybe the original poster named the service something else when he configured it so that `%I` would be the correct user when it runs? Who knows. The only way I was able to get it to work was by setting `User` to my username, which in this case was `austin`.