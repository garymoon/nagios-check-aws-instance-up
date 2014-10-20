nagios-check-aws-instance-up
============================

A Nagios check for monitoring the status of machines in an AutoScaling group

Usage
-----
    Usage: check_aws_instance_up [options]
            --groups group[,group]       which ASG do you wish to report on?
            --stack stack                which stack do you wish to report on?
            --string string              specify a string to look for
            --port port                  specify the port the instance is listening on
            --host host                  specify the host header to use
            --tags tag,[tag]             which tag(s) do you wish to report on?
        -k, --key key                    specify your AWS key ID
        -s, --secret secret              specify your AWS secret
            --debug                      enable debug mode
            --ssl                        enable SSL
            --user username              Basic auth username
            --pass password              Basic auth password
            --insecure                   Disable SSL peer verification
            --spoof-https                Spoof HTTPS from a proxy with X-Forwarded-Proto (for ELBs with SSL termination enabled)
            --region region              which region do you wish to report on?
        -h, --help                       help

Configuration
-------------

    define command{
      command_name  check_aws_instance_up
      command_line  $USER1$/check_aws_instance_up.rb --stack '$ARG1$' --tags '$ARG2$' --key '$ARG3$' --secret '$ARG4$' --region '$ARG5$' --host '$ARG6$' --user '$ARG7$' --pass '$ARG8$' --spoof-https --string '$ARG9$'
      }
    
    define service{
      use                             generic-service
      host_name                       aws
      service_description             Instance Up
      check_command                   check_aws_instance_up!<%= @aws_cfn_stack %>!WWWFleet!<%= @aws_nagios_key %>!<%= @aws_nagios_secret %>!<%= @aws_region_code %>!<%= @primary_domain %>!user!pass!string!
      check_interval                  5
    }


Notes:
* For our purposes, it supports only a tagged cfn stack. I can add other options if there's interest.
* It's quite use case-specific, you will want to fully understand its purpose before deploying it. We used it because ocasionally our monitor url would return 200 but the box wasn't responding properly.
* The default region is us-west-2 (Oregon)