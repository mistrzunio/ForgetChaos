
restart wifi ater wake up from sleep:

```bash
#manually: 
systemctl restart iwd

#or:
sudo nano /usr/lib/systemd/system-sleep/restart-iwd

#!/bin/bash

if [ "$1" = "post" ]; then
    /usr/bin/systemctl restart iwd
fi


```

## Links: 

[https://www.ssp.sh/blog/macbook-to-arch-linux-omarchy/]
