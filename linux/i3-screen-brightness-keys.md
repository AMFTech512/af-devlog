---
title: Configuring the screen brightness keys in i3wm
date: 2022-10-26T00:00:40Z
---

First, install `brightnessctl`

You have to run this because brightnessctl requires root privilges
```shell
sudo chmod +s /usr/bin/brightnessctl
```

Then add the following lines to your `~/.config/i3/config` file
```
# adjust screen brightness
bindsym XF86MonBrightnessUp exec --no-startup-id brightnessctl set +10%
bindsym XF86MonBrightnessDown exec --no-startup-id brightnessctl set 10%-
```

Reload i3 and you should be good to go.