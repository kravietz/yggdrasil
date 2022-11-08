ansible-yggdrasil
=================

Installs and configures an [Yggdrasil](https://yggdrasil-network.github.io/) node. Yggdrasil is an overlay mesh
network that connects nodes either over existing IPv4/IPv6 Internet connection or over multicast discovery on LAN.
All connections are encrypted and routing is configured automatically using DHT.

Requirements
------------

The role cannot currently create cryptographic keys for each node, so you need to provide them in configuration
variables. How I'm deploying it to servers:

1. Install Yggdrasil package on my laptop
2. Run `yggdrasil -genconf` which will produce a new configuration, including new cryptographic keys. I pick up
    the following variables and save them as Ansible variables for first server e.g. `host_vars/server1`
3. Repeat step 2 for other servers

    
| Yggdrasil config    | Ansible variable        |
|---------------------|-------------------------|
| EncryptionPublicKey | `yggdrasil_enc_pubkey`  |
| EncryptionPrivateKey| `yggdrasil_enc_privkey` |
| SigningPublicKey    | `yggdrasil_sig_pubkey`  |
| SigningPrivateKey   | `yggdrasil_sig_privkey` |

To connect to the public Yggdarsil network set at least one peer in `yggdrasil_peers` - use one that
is close geographically and latency-wise. You can find them at [public-peers](https://github.com/yggdrasil-network/public-peers)
repository. 

Role Variables
--------------

```yamlex
yggdrasil_peers: []
#- "tcp://185.164.138.18:1001"

yggdrasil_listen: []
#- "tcp://[::]:8080"

admin_listen: "unix:///var/run/yggdrasil.sock"
multicast_interfaces:
- ".*"

yggdrasil_enc_pubkey: MANDATORY
yggdrasil_enc_privkey: MANDATORY
yggdrasil_sig_pubkey: MANDATORY
yggdrasil_sig_privkey: MANDATORY

yggdrasil_linklocal_port: 0
yggdrasil_ifname: auto
yggdrasil_iftapmode: no
yggdrasil_ifmtu: 65535

yggdrasil_allowed_public_keys: []

yggdrasil_session_firewall_enable: no
yggdrasil_session_firewall_allow_direct: yes
yggdrasil_session_firewall_allow_remote: yes
yggdrasil_session_firewall_allow_outbound: yes
yggdrasil_session_firewall_whitelist: []
yggdrasil_session_firewall_blacklist: []

yggdrasil_tunnel_enable: no
yggdrasil_tunnel_ipv6_destinations: []
#- subnet: "fd64:642b:1a20::/48"
#  public_key: "ef78da7fc983c6c210609529921a701ca3e43fa5cfd79f5f20cc67bf66e45c1a",

yggdrasil_tunnel_ipv6_sources: []
# - "aaaa:aaaa:aaaa:aaaa::/64"

yggdrasil_tunnel_ipv4_destinations: []
# - subnet: "b.b.b.b/24"
#   public_key: "xxxxxxxxxxxxxx"

yggdrasil_tunnel_ipv4_sources: []
# - "a.a.a.a/24"

yggdrasil_max_queue_size: 4194304

yggdrasil_nodeinfo_privacy: no

yggdrasil_node_info: {}
#  name: mynode.y.example.com

```

License
-------

GPL-3+

Author Information
------------------

Pawel Krawczyk https://krvtz.net/
