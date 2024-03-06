## 创建vlan和vlanIf的命令如下
ip link add Bridge up type bridge vlan_filtering 1
ip link set ens36 master Bridge
ip link set ens38 master Bridge
##access
bridge vlan add vid 10 dev Bridge self
bridge vlan add vid 20 dev Bridge self
bridge vlan add vid 10 dev ens36 master pvid untagged
bridge vlan add vid 20 dev ens38 master pvid untagged
##trunk
bridge vlan add vid 10 dev ens36 master
bridge vlan add vid 11 dev ens36 master
.
.
.
bridge vlan add vid 10 dev ens36 master pvid untagged
#######

ip link add link Bridge name vlan_10 up type vlan id 10
ip link add link Bridge name vlan_20 up type vlan id 20

ip addr add 192.168.238.221/24 dev vlan_10
ip addr add 192.168.146.221/24 dev vlan_20






nft flush ruleset
ip link del Bridge type bridge
ip addr del 192.168.238.221/24 dev ens36
ip addr del 192.168.146.221/24 dev ens38
