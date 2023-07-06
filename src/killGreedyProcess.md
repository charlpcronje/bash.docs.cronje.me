## Kill Greedy Procss

This script continuously monitors processes and kills those that have been
running for at least 3 consecutive minutes with 100% CPU usage, excluding
critical system processes. It operates in an infinite loop, periodically
checking the CPU usage of processes and terminating the ones that meet the
specified criteria.

```sh

#!/bin/bash
################################################################################
# Script: saveCpu.sh
#
# Description:
# This script continuously monitors processes and kills those that have been
# running for at least 3 consecutive minutes with 100% CPU usage, excluding
# critical system processes. It operates in an infinite loop, periodically
# checking the CPU usage of processes and terminating the ones that meet the
# specified criteria.
#
# Process selection criteria:
# - Processes with CPU usage of 100% are considered.
# - Only processes with CPU time in the format "HH:MM:SS" are considered.
#
# Process termination conditions:
# - Processes that have been running for at least 3 consecutive minutes with
#   100% CPU usage are terminated.
# - Critical system processes and the script itself are excluded from termination.
#
# Usage:
# - Make sure the script is executable (chmod +x cpu_monitor.sh).
# - Run the script: ./cpu_monitor.sh
################################################################################

while true; do
    ps -eo pid,%cpu,time,cmd --sort=-%cpu |
    awk '
        $2 == 100 && $3 ~ /:/ {
            split($3, time, ":")
            if ((time[1] * 60 + time[2]) >= 3) {
                consecutive_minutes[$1]++
            } else {
                delete consecutive_minutes[$1]
            }
        }
        END {
            output = ""
            for (pid in consecutive_minutes) {
                if (consecutive_minutes[pid] >= 3) {
                    output = output " " pid
                }
            }
            if (output != "") {
                printf "%s", output | "xargs -r kill -9"
                fflush()
            }
        }
    '
    sleep 100
done
```

