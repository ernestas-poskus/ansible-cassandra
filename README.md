ansible-cassandra
=========

[![Build Status](https://travis-ci.org/ernestas-poskus/ansible-cassandra.svg?branch=master)](https://travis-ci.org/ernestas-poskus/ansible-cassandra)

Ansible playbook install and manages Cassandra database as well as cluster.

Installation
------------

ansible-galaxy install ernestas-poskus.ansible-cassandra

Requirements
------------

Java.

Example Playbook
----------------

```yaml
- name: Cassandra install
  hosts: cassandra
  sudo: yes
  roles:
    - role: ernestas-poskus.ansible-cassandra
```

For cluster configuration extra:

```yaml
cassandra_endpoint_snitch: PropertyFileSnitch
cassandra_propertyfilesnitch_ip_dc_rack:
  - ip: "192.168.250.1"
    dc: 'DC1'
    rack: 'RAC1'
  - ip: "192.168.250.2"
    dc: 'DC1'
    rack: 'RAC1'
  - ip: "192.168.250.3"
    dc: 'DC1'
    rack: 'RAC1'
```

Role Variables
--------------

```yaml
---
# defaults file for ansible-cassandra
cassandra_ver : '3.9'
cassandra_mirror : 'http://www-eu.apache.org/dist/cassandra' # EU
# cassandra_mirror : 'http://www-us.apache.org/dist/cassandra' # US

cassandra_owner: 'cassandra'
cassandra_group: 'cassandra'

# Either specify interface or network address
cassandra_listen_network_address: ''
cassandra_listen_network_interface: "{{ ansible_default_ipv4.interface }}"

# Used for listing seeders
cassandra_cluster_group_name: 'cassandra'

cassandra_cluster_name : 'Test Cluster'

cassandra_dir: '/opt'
cassandra_install_dir: "{{ cassandra_dir }}/{{ cassandra_name }}"

cassandra_lib_dir: '/var/lib/cassandra'
cassandra_data_dir: "{{ cassandra_lib_dir }}/data"
cassnadra_commitlog_dir: "{{ cassandra_lib_dir }}/commitlog"
cassandra_cdc_raw_dir: "{{ cassandra_lib_dir }}/cdc_raw"
cassandra_saved_caches_dir: "{{ cassandra_lib_dir }}/saved_caches"
cassandra_log_dir: '/var/log/cassandra'

# If you already have a cluster with 1 token per node, and wish to migrate to 
# multiple tokens per node, see http://wiki.apache.org/cassandra/Operations
cassandra_num_tokens : 256

# cassandra_allocate_tokens_for_keyspace: KEYSPACE

# initial_token allows you to specify tokens manually.  While you can use it with
# vnodes (num_tokens > 1, above) -- in which case you should provide a 
# comma-separated list -- it's primarily used when adding nodes to legacy clusters 
# that do not have vnodes enabled.
#cassandra_initial_token : '4f442b82-d39a-11e5-a81d-78843cb2df2b'

# See http://wiki.apache.org/cassandra/HintedHandoff
# May either be "true" or "false" to enable globally
cassandra_hinted_handoff_enabled: true

# When hinted_handoff_enabled is true, a black list of data centers that will not
# perform hinted handoff
cassandra_hinted_handoff_disabled_datacenters: []

# this defines the maximum amount of time a dead host will have hints
# generated.  After it has been dead this long, new hints for it will not be
# created until it has been seen alive and gone down again.
cassandra_max_hint_window_in_ms: 10800000

# Maximum throttle in KBs per second, per delivery thread.  This will be
# reduced proportionally to the number of nodes in the cluster.  (If there
# are two nodes in the cluster, each delivery thread will use the maximum
# rate; if there are three, each will throttle to half of the maximum,
# since we expect two nodes to be delivering hints simultaneously.)
cassandra_hinted_handoff_throttle_in_kb: 1024

# Number of threads with which to deliver hints;
# Consider increasing this number when you have multi-dc deployments, since
# cross-dc handoff tends to be slower
cassandra_max_hints_delivery_threads: 2

# Directory where Cassandra should store hints.
# If not set, the default directory is $CASSANDRA_HOME/data/hints.
cassandra_hints_dir : /var/lib/cassandra/hints

# How often hints should be flushed from the internal buffers to disk.
# Will *not* trigger fsync.
cassandra_hints_flush_period_in_ms : 10000

# Maximum size for a single hints file, in megabytes.
cassandra_hints_max_file_size_mb : 128

# Compression to apply to the hint files. If omitted, hints files
# will be written uncompressed. LZ4, Snappy, and Deflate compressors
# are supported.
cassandra_hints_compression: {}

# Maximum throttle in KBs per second, total. This will be
# reduced proportionally to the number of nodes in the cluster.
cassandra_batchlog_replay_throttle_in_kb : 1024

cassandra_authenticator : AllowAllAuthenticator
cassandra_authorizer : AllowAllAuthorizer
cassandra_role_manager : CassandraRoleManager

# Validity period for roles cache (fetching granted roles can be an expensive
# operation depending on the role manager, CassandraRoleManager is one example)
# Granted roles are cached for authenticated sessions in AuthenticatedUser and
# after the period specified here, become eligible for (async) reload.
# Defaults to 2000, set to 0 to disable caching entirely.
# Will be disabled automatically for AllowAllAuthenticator.
cassandra_roles_validity_in_ms : 2000

# Refresh interval for roles cache (if enabled).
# After this interval, cache entries become eligible for refresh. Upon next
# access, an async reload is scheduled and the old value returned until it
# completes. If roles_validity_in_ms is non-zero, then this must be
# also.
# Defaults to the same value as roles_validity_in_ms.
cassandra_roles_update_interval_in_ms: 2000

# Validity period for permissions cache (fetching permissions can be an
# expensive operation depending on the authorizer, CassandraAuthorizer is
# one example). Defaults to 2000, set to 0 to disable.
# Will be disabled automatically for AllowAllAuthorizer.
cassandra_permissions_validity_in_ms : 2000

# Refresh interval for permissions cache (if enabled).
# After this interval, cache entries become eligible for refresh. Upon next
# access, an async reload is scheduled and the old value returned until it
# completes. If permissions_validity_in_ms is non-zero, then this must be
# also.
# Defaults to the same value as permissions_validity_in_ms.
cassandra_permissions_update_interval_in_ms: 2000

# Validity period for credentials cache. This cache is tightly coupled to
# the provided PasswordAuthenticator implementation of IAuthenticator. If
# another IAuthenticator implementation is configured, this cache will not
# be automatically used and so the following settings will have no effect.
# Please note, credentials are cached in their encrypted form, so while
# activating this cache may reduce the number of queries made to the
# underlying table, it may not  bring a significant reduction in the
# latency of individual authentication attempts.
# Defaults to 2000, set to 0 to disable credentials caching.
cassandra_credentials_validity_in_ms: 2000

# Refresh interval for credentials cache (if enabled).
# After this interval, cache entries become eligible for refresh. Upon next
# access, an async reload is scheduled and the old value returned until it
# completes. If credentials_validity_in_ms is non-zero, then this must be
# also.
# Defaults to the same value as credentials_validity_in_ms.
cassandra_credentials_update_interval_in_ms: 2000

cassandra_partitioner : 'org.apache.cassandra.dht.Murmur3Partitioner'

cassandra_data_file_directories:
 - "{{ cassandra_data_dir }}"

# Enable / disable CDC functionality on a per-node basis. This modifies the logic used
# for write path allocation rejection (standard: never reject. cdc: reject Mutation
# containing a CDC-enabled table if at space limit in cdc_raw_directory).
cassandra_cdc_enabled: false

cassandra_disk_failure_policy : stop
cassandra_commit_failure_policy : stop

# Default value ("auto") is 1/256th of the heap or 10MB, whichever is greater
cassandra_prepared_statements_cache_size_mb:

# Default value ("auto") is 1/256th of the heap or 10MB, whichever is greater
cassandra_thrift_prepared_statements_cache_size_mb:

# Default value is empty to make it "auto" (min(5% of Heap (in MB), 100MB)). Set to 0 to disable key cache.
cassandra_key_cache_size_in_mb:

# Default is 14400 or 4 hours.
cassandra_key_cache_save_period: 14400

# Number of keys from the key cache to save
# Disabled by default, meaning all keys are going to be saved
# cassandra_key_cache_keys_to_save: 100

cassandra_row_cache_size_in_mb: 0
cassandra_row_cache_save_period: 0

# Number of keys from the row cache to save.
# Specify 0 (which is the default), meaning all keys are going to be saved
# cassandra_row_cache_keys_to_save : 100

# Default value is empty to make it "auto" (min(2.5% of Heap (in MB), 50MB)). Set to 0 to disable counter cache.
# NOTE: if you perform counter deletes and rely on low gcgs, you should disable the counter cache.
cassandra_counter_cache_size_in_mb:

cassandra_counter_cache_save_period: 7200
# cassandra_counter_cache_keys_to_save: 100

cassandra_commitlog_sync: periodic
cassandra_commitlog_sync_period_in_ms: 10000
cassandra_commitlog_segment_size_in_mb: 32

# Compression to apply to the commit log. If omitted, the commit log
# will be written uncompressed.  LZ4, Snappy, and Deflate compressors
# are supported.
#   - class_name: LZ4Compressor
#     parameters:
#         -
cassandra_commitlog_compression: {}

cassandra_concurrent_reads: 32
cassandra_concurrent_writes: 32
cassandra_concurrent_counter_writes: 32
cassandra_concurrent_materialized_view_writes: 32

# Maximum memory to use for sstable chunk cache and buffer pooling.
# 32MB of this are reserved for pooling buffers, the rest is used as an
# cache that holds uncompressed sstable chunks.
# Defaults to the smaller of 1/4 of heap or 512MB. This pool is allocated off-heap,
# so is in addition to the memory allocated for heap. The cache also has on-heap
# overhead which is roughly 128 bytes per chunk (i.e. 0.2% of the reserved size
# if the default 64k chunk size is used).
# Memory is only allocated when needed.
# cassandra_file_cache_size_in_mb: 512

# Flag indicating whether to allocate on or off heap when the sstable buffer
# pool is exhausted, that is when it has exceeded the maximum memory
# file_cache_size_in_mb, beyond which it will not cache buffers but allocate on request.
cassandra_buffer_pool_use_heap_if_exhausted: true

# The strategy for optimizing disk read
# Possible values are:
# ssd (for solid state disks, the default)
# spinning (for spinning disks)
cassandra_disk_optimization_strategy: spinning

# Total permitted memory to use for memtables. Cassandra will stop
# accepting writes when the limit is exceeded until a flush completes,
# and will trigger a flush based on memtable_cleanup_threshold
# If omitted, Cassandra will set both to 1/4 the size of the heap.
cassandra_memtable_heap_space_in_mb: 2048
cassandra_memtable_offheap_space_in_mb: 2048

# Ratio of occupied non-flushing memtable size to total permitted size
# that will trigger a flush of the largest memtable. Larger mct will
# mean larger flushes and hence less compaction, but also less concurrent
# flush activity which can make it difficult to keep your disks fed
# under heavy write load.
# cassandra_memtable_cleanup_threshold defaults to 1 / (memtable_flush_writers + 1)
# cassandra_memtable_cleanup_threshold: 0.11

# Specify the way Cassandra allocates and manages memtable memory.
cassandra_memtable_allocation_type: heap_buffers

# Total space to use for commit logs on disk.
# cassandra_commitlog_total_space_in_mb: 8192

# If your data directories are backed by SSD, you can increase this, but
# avoid having memtable_flush_writers * data_file_directories > number of cores
cassandra_memtable_flush_writers: 1

# Total space to use for change-data-capture logs on disk.
# cassandra_cdc_total_space_in_mb: 4096

# When we hit our cdc_raw limit and the CDCCompactor is either running behind
# or experiencing backpressure, we check at the following interval to see if any
# new space for cdc-tracked tables has been made available. Default to 250ms
# cassandra_cdc_free_space_check_interval_ms: 250

# A fixed memory pool size in MB for for SSTable index summaries. If left
# empty, this will default to 5% of the heap size. If the memory usage of
# all index summaries exceeds this limit, SSTables with low read rates will
# shrink their index summaries in order to meet this limit.  However, this
# is a best-effort process. In extreme conditions Cassandra may need to use
# more than this amount of memory.
cassandra_index_summary_capacity_in_mb:

# How frequently index summaries should be resampled.  This is done
# periodically to redistribute memory from the fixed-size pool to sstables
# proportional their recent read rates.  Setting to -1 will disable this
# process, leaving existing index summaries at their current sampling level.
cassandra_index_summary_resize_interval_in_minutes: 60

cassandra_trickle_fsync: false
cassandra_trickle_fsync_interval_in_kb: 10240

cassandra_storage_port: 7000
cassandra_ssl_storage_port: 7001

cassandra_listen_interface_prefer_ipv6: false

# Address to broadcast to other Cassandra nodes
# Leaving this blank will set it to the same value as listen_address
# cassandra_broadcast_address: 1.2.3.4

# When using multiple physical network interfaces, set this
# to true to listen on broadcast_address in addition to
# the listen_address, allowing nodes to communicate in both
# interfaces.
# Ignore this property if the network configuration automatically
# routes  between the public and private networks such as EC2.
cassandra_listen_on_broadcast_address: false

# Internode authentication backend, implementing IInternodeAuthenticator;
# used to allow/disallow connections from peer nodes.
# cassandra_internode_authenticator: org.apache.cassandra.auth.AllowAllInternodeAuthenticator

# Whether to start the native transport server.
# Please note that the address on which the native transport is bound is the
# same as the rpc_address. The port however is different and specified below.
cassandra_start_native_transport: true

# port for the CQL native transport to listen for clients on
# For security reasons, you should not expose this port to the internet.  Firewall it if needed.
cassandra_native_transport_port: 9042

# Enabling client encryption and keeping native_transport_port_ssl disabled will use encryption
# for native_transport_port. Setting native_transport_port_ssl to a different value
# from native_transport_port will use encryption for native_transport_port_ssl while
# keeping native_transport_port unencrypted.
# cassandra_native_transport_port_ssl: 9142

# The maximum threads for handling requests when the native transport is used.
# This is similar to rpc_max_threads though the default differs slightly (and
# there is no native_transport_min_threads, idle threads will always be stopped
# after 30 seconds).
cassandra_native_transport_max_threads: 128

# The maximum size of allowed frame. Frame (requests) larger than this will
# be rejected as invalid. The default is 256MB. If you're changing this parameter,
# you may want to adjust max_value_size_in_mb accordingly.
cassandra_native_transport_max_frame_size_in_mb: 256

# The maximum number of concurrent client connections.
# The default is -1, which means unlimited.
cassandra_native_transport_max_concurrent_connections: -1

# The maximum number of concurrent client connections per source ip.
# The default is -1, which means unlimited.
cassandra_native_transport_max_concurrent_connections_per_ip: -1

# Whether to start the thrift rpc server.
cassandra_start_rpc: 'false'
cassandra_rpc_address: ''
cassandra_rpc_network_interface: '{{ ansible_default_ipv4.interface }}'
cassandra_rpc_interface_prefer_ipv6: false
cassandra_rpc_port: 9160
# cassandra_broadcast_rpc_address: 1.2.3.4
cassandra_rpc_keepalive: 'true'
cassandra_rpc_server_type: sync
cassandra_rpc_min_threads: 16
cassandra_rpc_max_threads: 2048

# Uncomment to set socket buffer size for internode communication
# Note that when setting this, the buffer size is limited by net.core.wmem_max
# and when not setting it it is defined by net.ipv4.tcp_wmem
# See also:
# /proc/sys/net/core/wmem_max
# /proc/sys/net/core/rmem_max
# /proc/sys/net/ipv4/tcp_wmem
# /proc/sys/net/ipv4/tcp_wmem
# and 'man tcp'
# cassandra_internode_send_buff_size_in_bytes:

# Uncomment to set socket buffer size for internode communication
# Note that when setting this, the buffer size is limited by net.core.wmem_max
# and when not setting it it is defined by net.ipv4.tcp_wmem
# cassandra_internode_recv_buff_size_in_bytes:

# Frame size for thrift (maximum message length).
cassandra_thrift_framed_transport_size_in_mb: 15

cassandra_incremental_backups: false
cassandra_snapshot_before_compaction: false
cassandra_auto_snapshot: true

# Granularity of the collation index of rows within a partition.
# Increase if your rows are large, or if you have a very large
# number of rows per partition.  The competing goals are these:
#
# - a smaller granularity means more index entries are generated
#   and looking up rows withing the partition by collation column
#   is faster
# - but, Cassandra will keep the collation index in memory for hot
#   rows (as part of the key cache), so a larger granularity means
#   you can cache more hot rows
cassandra_column_index_size_in_kb: 64

# Per sstable indexed key cache entries (the collation index in memory
# mentioned above) exceeding this size will not be held on heap.
# This means that only partition information is held on heap and the
# index entries are read from disk.
#
# Note that this size refers to the size of the
# serialized index information and not the size of the partition.
cassandra_column_index_cache_size_in_kb: 2

# Number of simultaneous compactions to allow, NOT including
# validation "compactions" for anti-entropy repair.  Simultaneous
# compactions can help preserve read performance in a mixed read/write
# workload, by mitigating the tendency of small sstables to accumulate
# during a single long running compactions. The default is usually
# fine and if you experience problems with compaction running too
# slowly or too fast, you should look at
# compaction_throughput_mb_per_sec first.
#
# concurrent_compactors defaults to the smaller of (number of disks,
# number of cores), with a minimum of 2 and a maximum of 8.
#
# If your data directories are backed by SSD, you should increase this
# to the number of cores.
cassandra_concurrent_compactors: 1

# Throttles compaction to the given total throughput across the entire
# system. The faster you insert data, the faster you need to compact in
# order to keep the sstable count down, but in general, setting this to
# 16 to 32 times the rate you are inserting data is more than sufficient.
# Setting this to 0 disables throttling. Note that this account for all types
# of compaction, including validation compaction.
cassandra_compaction_throughput_mb_per_sec: 16

# When compacting, the replacement sstable(s) can be opened before they
# are completely written, and used in place of the prior sstables for
# any range that has been written. This helps to smoothly transfer reads
# between the sstables, reducing page cache churn and keeping hot rows hot
cassandra_sstable_preemptive_open_interval_in_mb: 50

# Throttles all outbound streaming file transfers on this node to the
# given total throughput in Mbps. This is necessary because Cassandra does
# mostly sequential IO when streaming data during bootstrap or repair, which
# can lead to saturating the network connection and degrading rpc performance.
# When unset, the default is 200 Mbps or 25 MB/s.
cassandra_stream_throughput_outbound_megabits_per_sec: 200

# Throttles all streaming file transfer between the datacenters,
# this setting allows users to throttle inter dc stream throughput in addition
# to throttling all network stream traffic as configured with
# stream_throughput_outbound_megabits_per_sec
# When unset, the default is 200 Mbps or 25 MB/s
cassandra_inter_dc_stream_throughput_outbound_megabits_per_sec: 200


cassandra_read_request_timeout_in_ms: 5000
cassandra_range_request_timeout_in_ms: 10000
cassandra_write_request_timeout_in_ms: 2000
cassandra_counter_write_request_timeout_in_ms: 5000
cassandra_cas_contention_timeout_in_ms: 1000
cassandra_truncate_request_timeout_in_ms: 60000
cassandra_request_timeout_in_ms: 10000

# Enable operation timeout information exchange between nodes to accurately
# measure request timeouts.  If disabled, replicas will assume that requests
# were forwarded to them instantly by the coordinator, which means that
# under overload conditions we will waste that much extra time processing
# already-timed-out requests.
#
# Warning: before enabling this property make sure to ntp is installed
# and the times are synchronized between the nodes.
cassandra_cross_node_timeout: false

# Set socket timeout for streaming operation.
# The stream session is failed if no data/ack is received by any of the participants
# within that period, which means this should also be sufficient to stream a large
# sstable or rebuild table indexes.
# Default value is 86400000ms, which means stale streams timeout after 24 hours.
# A value of zero means stream sockets should never time out.
cassandra_streaming_socket_timeout_in_ms: 86400000

# phi value that must be reached for a host to be marked down.
# most users should never need to adjust this.
cassandra_phi_convict_threshold: 8

# You can use a custom Snitch by setting this to the full class name
# of the snitch, which will be assumed to be on your classpath.
cassandra_endpoint_snitch: 'SimpleSnitch'

# controls how often to perform the more expensive part of host score
# calculation
cassandr_dynamic_snitch_update_interval_in_ms: 100
# controls how often to reset all host scores, allowing a bad host to
# possibly recover
cassandra_dynamic_snitch_reset_interval_in_ms: 600000
# if set greater than zero and read_repair_chance is < 1.0, this will allow
# 'pinning' of replicas to hosts in order to increase cache capacity.
# The badness threshold will control how much worse the pinned host has to be
# before the dynamic snitch will prefer other replicas over it.  This is
# expressed as a double which represents a percentage.  Thus, a value of
# 0.2 means Cassandra would continue to prefer the static snitch values
# until the pinned host was 20% worse than the fastest.
cassandra_dynamic_snitch_badness_threshold: 0.1

# request_scheduler -- Set this to a class that implements
# RequestScheduler, which will schedule incoming client requests
# according to the specific policy. This is useful for multi-tenancy
# with a single Cassandra cluster.
# NOTE: This is specifically for requests from the client and does
# not affect inter node communication.
# org.apache.cassandra.scheduler.NoScheduler - No scheduling takes place
# org.apache.cassandra.scheduler.RoundRobinScheduler - Round robin of
# client requests to a node with a separate queue for each
# request_scheduler_id. The scheduler is further customized by
# request_scheduler_options as described below.
cassandra_request_scheduler: org.apache.cassandra.scheduler.NoScheduler

cassandra_request_scheduler_options: {}

# request_scheduler_id -- An identifier based on which to perform
# the request scheduling. Currently the only valid option is keyspace.
cassndra_request_scheduler_id: keyspace

# Enable or disable inter-node encryption
# JVM defaults for supported SSL socket protocols and cipher suites can
# be replaced using custom encryption options. This is not recommended
# unless you have policies in place that dictate certain settings, or
# need to disable vulnerable ciphers or protocols in case the JVM cannot
# be updated.
# FIPS compliant settings can be configured at JVM level and should not
# involve changing encryption settings here:
# https://docs.oracle.com/javase/8/docs/technotes/guides/security/jsse/FIPS.html
# *NOTE* No custom encryption options are enabled at the moment
# The available internode options are : all, none, dc, rack
#
# If set to dc cassandra will encrypt the traffic between the DCs
# If set to rack cassandra will encrypt the traffic between the racks
#
# The passwords used in these options must match the passwords used when generating
# the keystore and truststore.  For instructions on generating these files, see:
# http://download.oracle.com/javase/6/docs/technotes/guides/security/jsse/JSSERefGuide.html#CreateKeystore
cassandra_server_encryption_options:
    internode_encryption: none
    keystore: conf/.keystore
    keystore_password: cassandra
    truststore: conf/.truststore
    truststore_password: cassandra
    # More advanced defaults below:
    # protocol: TLS
    # algorithm: SunX509
    # store_type: JKS
    # cipher_suites: [TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA]
    # require_client_auth: false
    # require_endpoint_verification: false

# enable or disable client/server encryption.
cassandra_client_encryption_options:
    enabled: false
    # If enabled and optional is set to true encrypted and unencrypted connections are handled.
    optional: false
    keystore: conf/.keystore
    keystore_password: cassandra
    # require_client_auth: false
    # Set trustore and truststore_password if require_client_auth is true
    # truststore: conf/.truststore
    # truststore_password: cassandra
    # More advanced defaults below:
    # protocol: TLS
    # algorithm: SunX509
    # store_type: JKS
    # cipher_suites: [TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_DHE_RSA_WITH_AES_128_CBC_SHA,TLS_DHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA]

# internode_compression controls whether traffic between nodes is
# compressed.
# Can be:
#
# all
#   all traffic is compressed
#
# dc
#   traffic between different datacenters is compressed
#
# none
#   nothing is compressed.
cassandra_internode_compression: dc

# Enable or disable tcp_nodelay for inter-dc communication.
# Disabling it will result in larger (but fewer) network packets being sent,
# reducing overhead from the TCP protocol itself, at the cost of increasing
# latency if you block for cross-datacenter responses.
cassandra_inter_dc_tcp_nodelay: false

# TTL for different trace types used during logging of the repair process.
cassandra_tracetype_query_ttl: 86400
cassandra_tracetype_repair_ttl: 604800

# By default, Cassandra logs GC Pauses greater than 200 ms at INFO level
# This threshold can be adjusted to minimize logging if necessary
cassandra_gc_log_threshold_in_ms: 200

# If unset, all GC Pauses greater than gc_log_threshold_in_ms will log at
# INFO level
# UDFs (user defined functions) are disabled by default.
# As of Cassandra 3.0 there is a sandbox in place that should prevent execution of evil code.
cassandra_enable_user_defined_functions: false

# Enables scripted UDFs (JavaScript UDFs).
# Java UDFs are always enabled, if enable_user_defined_functions is true.
# Enable this option to be able to use UDFs with "language javascript" or any custom JSR-223 provider.
# This option has no effect, if enable_user_defined_functions is false.
cassandra_enable_scripted_user_defined_functions: false

# The default Windows kernel timer and scheduling resolution is 15.6ms for power conservation.
# Lowering this value on Windows can provide much tighter latency and better throughput, however
# some virtualized environments may see a negative performance impact from changing this setting
# below their system default. The sysinternals 'clockres' tool can confirm your system's default
# setting.
cassandra_windows_timer_interval: 1

# Enables encrypting data at-rest (on disk). Different key providers can be plugged in, but the default reads from
# a JCE-style keystore. A single keystore can hold multiple keys, but the one referenced by
# the "key_alias" is the only key that will be used for encrypt opertaions; previously used keys
# can still (and should!) be in the keystore and will be used on decrypt operations
# (to handle the case of key rotation).
#
# It is strongly recommended to download and install Java Cryptography Extension (JCE)
# Unlimited Strength Jurisdiction Policy Files for your version of the JDK.
# (current link: http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)
#
# Currently, only the following file types are supported for transparent data encryption, although
# more are coming in future cassandra releases: commitlog, hints
cassandra_transparent_data_encryption_options:
    enabled: false
    chunk_length_kb: 64
    cipher: AES/CBC/PKCS5Padding
    key_alias: testing:1
    # CBC IV length for AES needs to be 16 bytes (which is also the default size)
    # iv_length: 16
    key_provider:
      - class_name: org.apache.cassandra.security.JKSKeyProvider
        parameters:
          - keystore: conf/.keystore
            keystore_password: cassandra
            store_type: JCEKS
            key_password: cassandra


#####################
# SAFETY THRESHOLDS #
#####################

# When executing a scan, within or across a partition, we need to keep the
# tombstones seen in memory so we can return them to the coordinator, which
# will use them to make sure other replicas also know about the deleted rows.
# With workloads that generate a lot of tombstones, this can cause performance
# problems and even exaust the server heap.
# (http://www.datastax.com/dev/blog/cassandra-anti-patterns-queues-and-queue-like-datasets)
# Adjust the thresholds here if you understand the dangers and want to
# scan more tombstones anyway.  These thresholds may also be adjusted at runtime
# using the StorageService mbean.
cassandra_tombstone_warn_threshold: 1000
cassandra_tombstone_failure_threshold: 100000

# Log WARN on any batch size exceeding this value. 5kb per batch by default.
# Caution should be taken on increasing the size of this threshold as it can lead to node instability.
cassandra_batch_size_warn_threshold_in_kb: 5

# Fail any batch exceeding this value. 50kb (10x warn threshold) by default.
cassandra_batch_size_fail_threshold_in_kb: 50

# Log WARN on any batches not of type LOGGED than span across more partitions than this limit
cassandra_unlogged_batch_across_partitions_warn_threshold: 10

# Log a warning when compacting partitions larger than this value
cassandra_compaction_large_partition_warning_threshold_mb: 100

# GC Pauses greater than gc_warn_threshold_in_ms will be logged at WARN level
# Adjust the threshold based on your application throughput requirement
# By default, Cassandra logs GC Pauses greater than 200 ms at INFO level
cassandra_gc_warn_threshold_in_ms: 1000

# Maximum size of any value in SSTables. Safety measure to detect SSTable corruption
# early. Any value size larger than this threshold will result into marking an SSTable
# as corrupted.
cassandra_max_value_size_in_mb: 256

#####################
# SNITCH CONFIG #
#####################

# GossipingPropertyFileSnitch
# http://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archsnitchGossipPF.html
cassandra_gossipingpropertyfilesnitch_dc: 'dc1'
cassandra_gossipingpropertyfilesnitch_rack: 'rack1'
# cassandra_gossipingpropertyfilesnitch_dc_suffix:
# cassandra_gossipingpropertyfilesnitch_dc_prefer_local: true

# PropertyFileSnitch
# http://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchPFSnitch.html
# Native IPv6 is supported, however you must escape the colon in the IPv6 Address
# Also be sure to comment out JVM_OPTS="$JVM_OPTS -Djava.net.preferIPv4Stack=true"
# in cassandra-env.sh
# fe80\:0\:0\:0\:202\:b3ff\:fe1e\:8329=DC1:RAC3

cassandra_propertyfilesnitch_default: 'DC1:r1' # default for unknown nodes
cassandra_propertyfilesnitch_ip_dc_rack:
  - ip: 192.168.1.100
    dc: 'DC1'
    rack: 'RAC1'
  - ip: 192.168.2.200
    dc: 'DC2'
    rack: 'RAC2'
```

Dependencies
------------

None.

License
-------

BSD

Author Information
------------------

Twitter: @ernestas_poskus
