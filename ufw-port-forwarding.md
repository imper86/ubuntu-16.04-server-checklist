# /etc/default/ufw
```DEFAULT_FORWARD_POLICY="ACCEPT"```

# /etc/sysctl.conf
```
net.ipv4.ip_forward=1
sudo sysctl -p
```

# /etc/ufw/before.rules
```
#
# rules.before
#
# Rules that should be run before the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-before-input
#   ufw-before-output
#   ufw-before-forward
#

*nat
:PREROUTING ACCEPT [0:0]
# forward posnet-jusia
-A PREROUTING -i tun0 -d 10.8.0.22   -p tcp --dport 6666 -j  DNAT --to-destination 192.168.8.201:6666
# setup routing
-A POSTROUTING -s 10.8.0.0/24 -d 192.168.8.0/24 -j MASQUERADE
COMMIT

(...)
```

```sudo systemctl restart ufw.service```
