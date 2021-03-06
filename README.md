# nagios-slack-notify

This program is intended to be used to send [Nagios][] notifications to a
[Slack][] channel.  It matches the author's aesthetic preferences better
than the script in [khera][]'s [slack-integrations][] repository.

  [Nagios]: https://www.nagios.org/
  [Slack]: https://slack.com/
  [khera]: https://github.com/khera
  [slack-integrations]: https://github.com/khera/slack-integrations

## Setup

You'll need host and service notification definitions like the following:

    define command {
        command_name host-notify-slack
        command_line /usr/local/bin/notify-slack \
            --webhook https://hooks.slack.com/services/FOO/BAR/baz \
            --notification-type '$NOTIFICATIONTYPE$' \
            --notification-comment '$NOTIFICATIONCOMMENT$' \
            --username '$HOSTNAME$' \
            --host-alias '$HOSTNAME$' \
            --state '$HOSTSTATE$' \
            --output '$HOSTOUTPUT$' \
            --long-output '$LONGHOSTOUTPUT$' \
            --ack-author '$HOSTACKAUTHOR$' \
            --ack-comment '$HOSTACKCOMMENT$' \
            --time '$TIMET$'
    }
    
    define command {
        command_name service-notify-slack
        command_line /usr/local/bin/notify-slack \
            --webhook https://hooks.slack.com/services/FOO/BAR/baz \
            --notification-type '$NOTIFICATIONTYPE$' \
            --notification-comment '$NOTIFICATIONCOMMENT$' \
            --username '$HOSTNAME$' \
            --host-alias '$HOSTNAME$' \
            --service-desc '$SERVICEDESC$' \
            --state '$SERVICESTATE$' \
            --output '$SERVICEOUTPUT$' \
            --long-output '$LONGSERVICEOUTPUT$' \
            --ack-author '$SERVICEACKAUTHOR$' \
            --ack-comment '$SERVICEACKCOMMENT$' \
            --time '$TIMET$'
    }

Note that the above definitions use the webhook's default channel, but use
the notification's hostname as the bot's username.  That's not necessary,
of course, but the author likes the way it looks.

Next, you need a contact definition.  The following will work:

    define contact {
        contact_name  slack
    
        host_notification_period       24x7
        service_notification_period    24x7
        host_notification_options      d,r,f,s
        service_notification_options   w,u,c,r,f,s
        host_notification_commands     host-notify-slack
        service_notification_commands  service-notify-slack
    }

Simply add that contact to all of your hosts.  (Hopefully, that just
requires adding it to a few contactgroups.)

## Icons/Emoji

There are a few options available for use of emoji.  By default, the
program will use a reasonably-appropriate emoji from the standard Slack
definitions for each notification.  The emoji will be placed inline with
the main message.

If you use the `--state-icon` parameter, the emoji will instead be used as
the icon for the bot's message.

If you use the `--custom-emoji` parameter, the program will use one of the
following emoji, which you will need to have defined beforehand:

 * `:nagios_up:`
 * `:nagios_down:`
 * `:nagios_unreachable:`
 * `:nagios_ok:`
 * `:nagios_warning:`
 * `:nagios_critical:`
 * `:nagios_unknown:`
 * `:nagios_problem:`
 * `:nagios_recovery:`
 * `:nagios_acknowledgement:`
 * `:nagios_flappingstart:`
 * `:nagios_flappingstop:`
 * `:nagios_flappingdisabled:`
 * `:nagios_downtimestart:`
 * `:nagios_downtimeend:`
 * `:nagios_downtimecancelled:`
 * `:nagios_custom:`
