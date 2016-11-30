# nginx-wordpress



Perform the following system tuning before:

  - Mount /var/lib/nginx in tmpfs 
  - Perform kernel tuning in /etc/sysctl.conf
  - Change the limits of the maximum number of open files (RLIMIT_NOFILE) for worker processes in /etc/security/limits.conf



### Mount /var/lib/nginx in tmpfs 

Modify the `/etc/fstab` file to add the following lines:
```
# /var/lib/nginx/
tmpfs   /var/lib/nginx/                 tmpfs   rw,size=1G,uid=994,gid=991,mode=1770    0 0
```
Where uid 994 is the uid of the `www-data` user and the gid 991 the the gid of the `www-data` group.


### Perform kernel tuning in /etc/sysctl.conf

Modify the `/etc/sysctl.conf` file to add the following lines:


```sh
# Max listen queue backlog
# make sure to increase nginx backlog as well if changed (listen directive: backlog=16384)
###net.core.somaxconn = 16384
net.core.somaxconn = 65535

# Max number of packets that can be queued on interface input
# If kernel is receiving packets faster than can be processed
# this queue increases
####net.core.netdev_max_backlog = 16384
net.core.netdev_max_backlog = 65536


# Max receive buffer size (8 Mb)
net.core.rmem_max=8388608

# The net.ipv4.tcp_max_syn_backlog determines a number of packets to keep in the backlog before the kernel starts dropping them.
# A sane value is net.ipv4.tcp_max_syn_backlog = 3240000.
net.ipv4.tcp_max_syn_backlog=3240000


# Number of times SYNACKs for passive TCP connection.
net.ipv4.tcp_synack_retries = 2
#
# # Allowed local port range
net.ipv4.ip_local_port_range = 1025 65535
#
# Protect Against TCP Time-Wait
net.ipv4.tcp_rfc1337 = 1
#
# Decrease the time default value for tcp_fin_timeout connection
net.ipv4.tcp_fin_timeout = 15
#
# Decrease the time default value for connections to keep alive
net.ipv4.tcp_keepalive_time = 300
net.ipv4.tcp_keepalive_probes = 5
net.ipv4.tcp_keepalive_intvl = 15
#
# ### TUNING NETWORK PERFORMANCE ###
#
# Default Socket Receive Buffer
net.core.rmem_default = 31457280
#
# Maximum Socket Receive Buffer
net.core.rmem_max = 12582912
#
# Default Socket Send Buffer
net.core.wmem_default = 31457280
#
# Maximum Socket Send Buffer
net.core.wmem_max = 12582912
#
# Increase number of incoming connections
# net.core.somaxconn = 4096
#
# Increase number of incoming connections backlog
# net.core.netdev_max_backlog = 65536
#
# Increase the maximum amount of option memory buffers
net.core.optmem_max = 25165824
#
# Increase the maximum total buffer-space allocatable
# This is measured in units of pages (4096 bytes)
net.ipv4.tcp_mem = 65536 131072 262144
net.ipv4.udp_mem = 65536 131072 262144
#
# Increase the read-buffer space allocatable
net.ipv4.tcp_rmem = 8192 87380 16777216
net.ipv4.udp_rmem_min = 16384
#
# Increase the write-buffer-space allocatable
net.ipv4.tcp_wmem = 8192 65536 16777216
net.ipv4.udp_wmem_min = 16384
#
# Increase the tcp-time-wait buckets pool size to prevent simple DOS attacks
net.ipv4.tcp_max_tw_buckets = 1440000
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_tw_reuse = 1
```

### Change the limits of the maximum number of open files (RLIMIT_NOFILE) for worker processes in /etc/security/limits.conf

Modify the `/etc/security/limits.conf` file to add the following lines:

```
# <domain>      <type>  <item>         <value>
www-data soft nofile 15000
www-data hard nofile 30000
```

