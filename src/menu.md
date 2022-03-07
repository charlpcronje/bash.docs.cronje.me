# Bash - Create a Menu

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
