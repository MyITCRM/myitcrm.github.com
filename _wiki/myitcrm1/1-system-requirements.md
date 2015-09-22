---
layout: wiki
title:  1.System Requirements
author: Glen Vanderhel
tags: [myitcrm1]
updated_by:
updated_at:
---

## System Requirements.


In order for MyITCRM1 to be installed and work correctly, your web host environment must meet these minimum requirements. If you don't have a web host, check out our list of [recommended web host](2-recommended-web-hosts.html). 

```
Min. 8MB of Disk Space Apache - 2.2.14
MySQL - 5.0.91
PHP - 5.3.2 or later
```
Additional PHP Requirements Ensure that your server's php.ini has the following settings values. Find out more on how to modify these at http://www.php.net/manual/en/configuration.file.php.

```
register_globals=off
safe_mode=off
memory_limit=32M (This is the minimum value, the higher the better. Normally 128M)
```

