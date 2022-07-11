---
title: Fix Web Permissions
---
<script type="text/javascript">(function(w,s){var e=document.createElement("script");e.type="text/javascript";e.async=true;e.src="https://cdn.pagesense.io/js/webally/f2527eebee974243853bcd47b32631f4.js";var x=document.getElementsByTagName("script")[0];x.parentNode.insertBefore(e,x);})(window,"script");</script>


## This script sets the folders to 755 and the files to 644

```sh
if [[ -z $1 ]]                      # Check if parth is set
then
    FIXPATH="-h"
    echo "fwp [path] [user:group]"  # Set Path and group
else
    FIXPATH="$1"

    echo "setting folder permissions to 755 recursive from $FIXPATH"
    find "$FIXPATH" -type d -exec chmod 755 {} \;


    echo "setting file permissions to 644 recursive from $FIXPATH"
    find "$FIXPATH" -type f -exec chmod 644 {} \;


    if [[ -z $2 ]]
    GROUPTOSET="$2"
    then
        echo "Setting folder $FIXPATH to be owned by $GROUPTOSET"
        find "$FIXPATH" -exec chown "$GROUPTOSET" {} \;
    fi
    echo "restarting Apache"
    echo systemctl restart httpd
fi
```
