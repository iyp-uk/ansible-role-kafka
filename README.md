Role Name
=========

This role provides kafka as a cluster.
It does not provide Zookeeper.
It can run as standalone, but won't launch the built-in Zookeeper.

Requirements
------------

When setting up your ensemble, you will want to have several zookeeper instances running.

Role Variables
--------------

See [defaults/main.yml](defaults/main.yml).

Dependencies
------------

There's a dependency on Java being installed.

> Refer to [geerlingguy/ansible-role-java](https://github.com/geerlingguy/ansible-role-java) for an Ansible Java role.

Example Playbook
----------------

The example below shows how to include the role in your playbook, overriding many of the variables exposed.

Notice `kafka_config` is a dictionary, and we only override a few keys.
For this to work you'll need to have the following in your `ansible.cfg`: 

```ini
[defaults]
hash_behaviour=merge
```

> If you prefer not to do it, you can still copy the whole `kafka_config` dict and override just where you want.
You'd then have to keep track of changes when this role updates.

```yaml
- name: Installing Kakfa
  hosts: kafka
  become: yes
  roles:
    - role: kafka
      kafka_hosts: "{{groups['kafka']}}"
      kafka_zookeeper_hosts: "{{groups['zookeeper']}}"
      kafka_config:
        server:
          num_partitions: 3
          offsets_topic_replication_factor: 3
        log4j:
          log4j_logger_kafka_controller: "INFO, controllerAppender"
          custom_settings:
            # kafka.server.ClientQuotaManager
            'log4j.appender.clientQuotaManagerAppender': "{{ log4j_default_file_appender_class }}"
            'log4j.appender.clientQuotaManagerAppender.DatePattern': "{{ log4j_default_file_appender_date_pattern }}"
            'log4j.appender.clientQuotaManagerAppender.File': "${kafka.logs.dir}/client-quota-manager.log"
            'log4j.appender.clientQuotaManagerAppender.layout': "{{ log4j_default_pattern_layout_class }}"
            'log4j.appender.clientQuotaManagerAppender.layout.ConversionPattern': "{{ log4j_default_conversion_pattern }}"
            'log4j.logger.kafka.server.ClientQuotaManager': "INFO, clientQuotaManagerAppender"
            'log4j.additivity.kafka.server.ClientQuotaManager': "false"
```

License
-------

MIT

Author Information
------------------

[@miaoulafrite](https://github.com/miaoulafrite)
