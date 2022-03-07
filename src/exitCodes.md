# Bash Scripting - Exit Codes

## What is an exit code?

Every Linux or Unix command executed by the shell script or user has an exit status. Exit status is an integer number. 0 exit status means the command was successful without any errors. A non-zero (1-255 values) exit status means command was a failure.

## Getting an exit code

You need to use a particular shell variable: `$?` to get the `exit status` of the previously executed command. To print `$?` variable use the `echo` command or `printf` command:

```bash
date
echo $?
date-or-no-date
printf '%d\n' $?
```

## Using exit codes

Assign `$?` to a shell variable

```bash
command
status=$?
## run date command ##
cmd="date"
$cmd
## get status ##
status=$?
## take some decision ## 
[ $status -eq 0 ] && echo "$cmd command was successful" || echo "$cmd failed"
```

## Set an exit code for my own shell scripts?

The exit command cause normal shell script termination. It exits the shell with a status of N. The syntax is:

```bash
exit N
exit 1
exit 999
```

Example:

```bash
#!/bin/bash
/path/to/some/command
[ $? -eq 0 ]  || exit 1
```

## Script to get the exit code of a command

```bash
#!/bin/bash
#
# Sample shell script to demo exit code usage #
#
set -e
 
## find ip in the file ##
grep -q 192.168.2.254 /etc/resolv.conf
 
## Did we found IP address? Use exit status of the grep command ##
if [ $? -eq 0 ]
then
  echo "Success: I found IP address in file."
  exit 0
else
  echo "Failure: I did not found IP address in file. Script failed" >&2
  exit 1
fi
```

## Exit Codes With Special Meanings

| Exit status | Description                                                |
|-------------|------------------------------------------------------------|
|1            | Catchall for general errors                                |
|2            | Misuse of shell builtins (according to Bash documentation) |
|126          | Command invoked cannot execute                             |
|127          | Command not found                                          |
|128          | Invalid argument to exit command                           |
|128+n        | Fatal error signal “n”                                     |
|130          | Bash script terminated by Control-C                        |
|255*         | Exit status out of range                                   |

## Exit codes of all piped commands

So to get the exit codes for something like `command1 | command2 | commandN`

or `command1 | filter_data_command > output`

or `get_data_command | verify_data_command | process_data_command | format_data_command > output.data.file`

## Pipes to connect programs

Use the vertical bar `|` between two commands. In this example, send netstat command output to grep command i.e. find out if nginx process exits or not in the system:

```shell
netstat -tulpn | grep nginx
```

## How to get exit status of process that’s piped to another

```bash
command1 | command2 
echo "${PIPESTATUS[@]}"
```

or

```bash
command1 | command2 
echo "${PIPESTATUS[0]} ${PIPESTATUS[1]}"
```

`PIPESTATUS` is an array variable containing a list of exit status values from the processes in the most-recently-executed foreground pipeline. Try the following commands:

```shell
netstat -tulpn | grep nginx
echo "${PIPESTATUS[@]}"
 
true | true
echo "The exit status of first command ${PIPESTATUS[0]}, and the second command ${PIPESTATUS[1]}"
 
true | false
echo "The exit status of first command ${PIPESTATUS[0]}, and the second command ${PIPESTATUS[1]}"
 
false | false | true 
echo "The exit status of first command ${PIPESTATUS[0]}, second command ${PIPESTATUS[1]}, and third command ${PIPESTATUS[2]}"
```

## Putting it all together

Here is a sample script that use `${PIPESTATUS[0]}` to find out the exit status of `mysqldump` command in order to notify user on screen about database backup status:

```bash
#!/bin/bash
### Purpose: mysql.backup.sh : Backup database ###
### Author: Vivek Gite <https://www.cyberciti.biz>, under GPL v2.x+ or above. ###
### Change as per your needs ###
set -eo pipefail
MUSER='USERNAME-here'
MPASS='PASSWORD-here'
MHOST='10.0.3.100'  
DEST="/nfs42/backups/mysql"
NOWFORMAT="%m_%d_%Y_%H_%M_%S%P"
MYSQL="/usr/bin/mysql"
MYSQLDUMP="/usr/bin/mysqldump"
MKDIR="/bin/mkdir"
RM="/bin/rm"
GZIP="/bin/gzip"
DATE="/bin/date"
SED="/bin/sed"

# Failsafe? Create dir #
[  ! -d "$DEST" ] && $MKDIR -p "$DEST"
 
# Filter db names
DBS="$($MYSQL -u $MUSER -h $MHOST -p$MPASS -Bse 'show databases')"
DBS="$($SED -e 's/performance_schema//' -e 's/information_schema//' <<<$DBS)"
 
# Okay, let us go
for db in $DBS
do
    tTime=$(date +"${NOWFORMAT}")
    FILE="$DEST/${db}.${tTime}.gz"
    $MYSQLDUMP -u $MUSER -h $MHOST -p$MPASS $db | $GZIP -9 > $FILE
    if [ ${PIPESTATUS[0]} -ne "0" ];
    then
        echo "The command $MYSQLDUMP failed with error code ${PIPESTATUS[0]}."
        exit 1
    else
        echo "Database $db dump successfully."    
    fi
done
```

## Use the array called pipestatus as follows

```bash
true | true
echo "${pipestatus[1]} ${pipestatus[2]}"

## Outputs:
## 0 0
```
