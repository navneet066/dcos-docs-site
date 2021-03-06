---
layout: layout.pug
navigationTitle: Limitations 
excerpt: Limitations o
title: Limitations of Confluent Kafka 
menuWeight: 120
model: /services/confluent-kafka/data.yml
render: mustache
---

#include /services/include/limitations.tmpl
#include /services/include/limitations-zones.tmpl
#include /services/include/limitations-regions.tmpl

## Log Retention Bytes

The "disk" configuration value is denominated in MB. We recommend you set the configuration value `log_retention_bytes` to a value smaller than the indicated "disk" configuration. See the Configuration section for instructions for customizing these values.

## Security

### Kafka CLI

When any security functions are enabled, the Kafka service CLI sub-command `topic` will not function. While the service CLI convenience functions will not work, the tooling bundled with [Apache Kafka](https://cwiki.apache.org/confluence/display/KAFKA/System+Tools) and other tools that support the enabled security modes will of course work.


### Kerberos

When Kerberos is enabled, the broker VIP is disabled as Kerberized clients will not be able to use it. This is because each Kafka broker uses a specific Kerberos principal and cannot accept connections from a single unified principal which the VIP would require.

### Toggling Kerberos

Kerberos authentication can be toggled (enabled / disabled), but this triggers a rolling restart of the cluster. Clients configured with the old security settings will lose connectivity during and after this process. It is recommended that backups are made and downtime is scheduled.

### Toggling Transport Encryption

Transport encryption using TLS can be toggled (enabled / disabled), but will trigger a rolling restart of the cluster. As each broker restarts, a client may lose connectivity based on its security settings and the value of the `service.security.transport_encryption.allow_plaintext` configuration option. It is recommended that backups are made and downtime is scheduled.

In order to enable TLS, a service account and corresponding secret is required. Since it is not possible to change the service account used by a service, it is recommended that the service is deployed with an explicit service account to allow for TLS to be enabled at a later stage.
