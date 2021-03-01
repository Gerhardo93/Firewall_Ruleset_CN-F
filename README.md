# SYNTAX:
# iptables [-t <table>] <commando> <match> <target>

# As a system engineer your expertise is asked to create a firewall ruleset for a hosting server.
# The server is provided with the following services: Apache, ProFTPd and bind9. 
# Please, do not allow zonetransfers (think twice). 
# Also protect the server against ping flooding. 
# The server is not allowed to make outgoing connections, except for the installation of security updates.
# Present your result through a git commit on Gitlab or Github.



# Please, do not allow zonetransfers (think twice)

#transfer = TCP| zonetransfers = UDP (port 53)

iptables -A INPUT -p tcp -m state --state NEW tcp --dport 53 -j ACCEPT
iptables -A INPUT -p udp -m state --state NEW udp --dport 53 -j DROP



# Also protect the server against ping flooding

# kan alle pings blokkeren of limiteren; limiteren is beter (anders kun je zelf niet meer testen)
# In table, filter, protocol icmp (icmp zijn de pings), echo-request (= ping), limit 30/minute (elke 2 seconden 1 ping is acceptabel), -j ACCEPT = accept packet

iptables -t filter -A INPUT -p icmp --icmp-type echo-request -m limit --limit 30/minute -j ACCEPT
iptables -t filter -A INPUT -p icmp -j DROP
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT



# The server is not allowed to make outgoing connections, except for the installation of security updates:
# enkel http protocol voor security.debian.org toelaten; TCP 80 = http

#Transfer files using TCP 80

iptables -A OUTPUT -d security.debian.org --dport 80 -j ACCEPT



# Veranderingen opslaan
service iptables save
