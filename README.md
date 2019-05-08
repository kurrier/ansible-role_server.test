Ansible Role: server.test [![CircleCI](https://circleci.com/gh/kurrier/ansible-role_server.test.svg?style=svg)](https://circleci.com/gh/kurrier/ansible-role_server.test)
=========

RHEL Server Test

Requirements
------------

N/A

Role Variables
--------------
## Run ##
* common_function_setup: [true/false] - general job setup for alerts role
* common_function_net: [true/false] -  network test
* common_function_service: [true/false] - services test
* common_function_app: [true/false] - application test
* common_function_health: [true/false] - hardware stats
* common_function_report: [true/false] - create report for alerts role
* common_function_slack: [true/false] - will enable slack alerts through alerts role
* common_function_email: [true/flase] - will enable email alerts through alerts role

## Misc Settings ##

* app_test_url:
    - https://example.com/health - API healthcheck url to test
* common_app_name: -  Service list to test
    - service1
    - service2

Dependecies
-----------

kurrier.alerts - if want to use alerts

Example Playbook
----------------
        - hosts: localhost
          become: true
          gather_facts: false
          vars:
            satellite_url: https://my.satelllite.io
            sat_promote: true
            satellite_auth_api: "123API"
            satellite_cv_name: "My APP"
            satellite_env: "QA"
        - role: kurrier.satellite

        - hosts: localhost
          become: no
          gather_facts: false
          vars:
            commmon_app_name:
              - docker
            app_test_url:
              - "https://{{ ansible_hostname }}/api/health"
            common_function_setup: true
            common_function_net: true
            common_function_service: true
            common_function_app: true
            common_function_health: true
            common_function_report: true
            common_function_slack: true

            #kurrier.alerts vars
            msg_function_alert: true
            msg_slack_crit: true
            msg_slack_warn: true
            msg_slack_notice: true
            msg_slack_info: true
          pre_tasks:
            - setup:
                filter: ansible_hostname
            - setup:
                filter: ansible_fqdn
            - setup:
                filter: ansible_distribution
            - setup:
                filter: ansible_distribution_major_version
          roles:
            - ../..
            - { role: kurrier.alerts, when: msg_alert_txt is defined or msg_alert_body is defined }

Test
----------------

ansible-playbook tests/test.yml -i tests/inventory

License
-------

Licensed under GPLv3 License. See LICENSE for details.

Author Information
------------------

Nick Lalumiere
