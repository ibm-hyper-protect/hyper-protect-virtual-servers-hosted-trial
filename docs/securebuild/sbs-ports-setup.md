# Enabling ports

Before you build a docker image by using the Hyper Protect base images, you must open the required ports for your application.

The following information shows an example of how you can open the ports before building the docker image.

```
#Here is the example on how you can re-write the iptable rules and open the required ports
*filter
:INPUT DROP [4:180]
:FORWARD DROP [0:0]
:OUTPUT DROP [0:0]
-A INPUT -i lo -j ACCEPT
-A INPUT -s 127.0.0.0/8 -j DROP
-A INPUT -p tcp -m state --state ESTABLISHED -j ACCEPT
-A INPUT -p udp -m state --state ESTABLISHED -j ACCEPT
-A INPUT -p icmp -m state --state ESTABLISHED -j ACCEPT
#
-A OUTPUT -o lo -j ACCEPT
-A OUTPUT -p tcp -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -p udp -m state --state NEW,ESTABLISHED -j ACCEPT
-A OUTPUT -p icmp -m state --state NEW,ESTABLISHED -j ACCEPT
#
# Since by default all ports are blocked on HPVS Base image you could open the required ports by doing the following.
# This is an example where you can open port 22 which is required for SSH access.
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
COMMIT
```

Place this iptables.conf in your Docker build path and add an entry in the Docker file to use the iptables.conf, as shown below.
```
COPY iptables.conf /etc/iptables/
```    

If you are using base images as parent images, then you must initialize the `systemd` service by including following line in your Docker file:
```
CMD ["/sbin/init"]
```

After the Docker file is updated, you can build your docker image by using Secure Build.
