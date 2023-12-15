# How to Create a Timer in *systemd*

This note describes how to create a timer unit that triggers system suspend.

## Create Timer Unit Configuration File

Create new file named after target that is going to be triggered under `/etc/systemd/system` directory:

    sudo touch /etc/systemd/system/suspend.timer

> Timer units should be named after the service or target they triggers. Example: the timer for target `suspend` should be configured in a unit file named `suspend.timer`.

Add content to file `/etc/systemd/system/suspend.timer`:

    [Unit]
    Description=System Suspend Timer
    
    [Timer]
    OnCalendar=*-*-* 10:00:00
    Unit=suspend.target

## Apply New Configuration File

Reload all `systemd` configuration files:

    $ sudo systemctl daemon-reload

Check status of a timer unit:

    $ sudo systemctl status suspend.timer

The output will look like this:

    [SYSTEMCTRL OUTPUT]

[TBD]

    $ sudo systemctl list-timers --all

The output will look like this:

    [SYSTEMCTRL OUTPUT]

## Start a Timer

Start system suspend timer:

    $ sudo systemctl start suspend.timer

[TBD]

    $ sudo systemctl list-timers suspend

The output will look like this:

    [SYSTEMCTRL OUTPUT]

[TBD]
