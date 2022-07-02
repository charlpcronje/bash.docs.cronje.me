---
title: Bash Scripting - Create a Menu
---
<script type="text/javascript">(function(w,s){var e=document.createElement("script");e.type="text/javascript";e.async=true;e.src="https://cdn.pagesense.io/js/webally/f2527eebee974243853bcd47b32631f4.js";var x=document.getElementsByTagName("script")[0];x.parentNode.insertBefore(e,x);})(window,"script");</script>

I'm going to create a menu to start / Stop and get the status for the services / Containers available on the server

Create a new file `menu.sh`

```bash
#!/bin/bash
echo "Start / Stop Services

services="Apache MariaDB Portainer Code-Server Heimdal-Dashboard Cockpit"

select option in #services; do
    echo "start / stop $option: $REPLY?"
done
```
