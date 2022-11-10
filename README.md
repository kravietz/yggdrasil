ansible-yggdrasil
=================

Installs and configures an [Yggdrasil](https://yggdrasil-network.github.io/) node. Yggdrasil is an overlay mesh
network that connects nodes either over existing IPv4/IPv6 Internet connection or over multicast discovery on LAN.
All connections are encrypted and routing is configured automatically using DHT.

Role Variables
--------------
The primary role variable is `yggdrasil_config` which should contain full Yggdrasil configuration written in YAML.
You can use any options Yggdrasil supports now or in future, the configuration will be written directly to the
configuration file in JSON format. You can quickly generate new configuration and convert it to YAML using
Python:

```bash
$ yggdrasil -genconf -json | python3 -c 'import json, sys, yaml; print(yaml.dump({"yggdrasil_config":json.load(sys.stdin)}));'
```

If no `yggdrasil_config` is defined for a server, configuration file is not created or modified. If `yggdrasil_config`
is defined, it must also have `yggdrasil_config.PrivateKey` and `yggdrasil_config.PublicKey` defined. Remember to
always protect `PrivateKey` with `ansible-vault`.

In most cases you will also want to add public peers from [public-peers](https://github.com/yggdrasil-network/public-peers)
to `yggdrasil_config.Peers` so that your node can join the public network. If you have many Yggdrasil nodes in the same
LAN segment, this is only required on on one of them as the rest will connect using local node discovery.

Remember that other nodes will try to connect to your node on port `9001/udp`, so firewall needs to be managed accordingly.

Service state is determined by `yggdrasil_service_state` variable (default `started`) and `yggdrasil_service_enabled`
(default `true`). The `yggdrasil_apparmor` variable controls whether an AppArmor profile for Yggdrasil should be installed
and enabled, which will only works on systems with AppArmor enabled (thus default `false`).
 

```yamlex
yggdrasil_service_state: started
yggdrasil_service_enabled: true

# configure AppArmor profile for Yggdrasil daemon
yggdrasil_apparmor: false

# update gai.conf(5) with Yggdrasil prefix
yggdrasil_gai: false

# basic configuration as produced by `yggdrasil -genconf`
yggdrasil_config:
  Peers: []
  InterfacePeers: {}
  Listen: []
  AdminListen: "unix:///var/run/yggdrasil.sock"
  MulticastInterfaces:
    - Regex: ".*"
      Beacon: true
      Listen: true
      Port: 0
  AllowedPublicKeys: []
  PublicKey: 18a2521cd2a8513b15b99d9cea2250905a5d9dfd81e7a64e7c203af0b0340dcb
  # always protect PrivateKey with ansible-vault
  PrivateKey: !vault |
                $ANSIBLE_VAULT;1.1;AES256
                35396235386664373130663361323461336436633264393136636162343766366461616234356631
                ...
                326565323166393730353736313731356661
  IfName: auto
  IfMTU: 65535
  NodeInfoPrivacy: false
  NodeInfo: null
```

License
-------

GPL-3+

Author Information
------------------

Pawel Krawczyk https://krvtz.net/
