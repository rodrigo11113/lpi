Project: 'arp' created on 2023-05-12

Projeto ARP LPI

Configs:

-Configuração Router:
conf t
int gi0/0
ip add addr 192.168.1.1 255.255.255.0
no sh
ip dhcp excluded-address 192.168.1.1 192.168.1.9 
ip dhcp excluded-address 192.168.1.254 
ip dhcp pool LAN-POOL-1 
network 192.168.1.0 255.255.255.0 
default-router 192.168.1.1 
domain-name span.com
exit
exit

configuração telnet: cisco/cisco
conf t
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
wr

Definir Ip netutils via DHCP 


Definir ip para eth1 no kali:
Configurar a rede eth1 do kali para receber ip via DHCP do router

Ethercap:
add target1 router 192.168.1.1
add target2 netutils

Procurar no ethercap os hosts:
AE:0D:4F:71:40:76-> netutils

00:50:56:C0:00:64 -> kali 

192.168.1.1/0C:C1:9F:E3:00:00 -> ROUTER NOVO

Depois de ter tudo configurado siga estes passos:
-> Usar o Wireshark no link entre o kali e o switch
-> No cmd do netutils: telnet 192.168.1.1 23 
-> Password: cisco
-> enable
-> Password: cisco
-> No wireshark pesquisar por telnet nos filtros, depois num dos pacotes carregar com o botão direito do rato e executar o follow tcp stream
-> Neste momento veremos a password do router e poderemos acedê-lo remotamente

O ethercap envia pacotes de arp para a rede dizendo que tem os MAC address do target 1 e do target 2 criando assim uma ligação em que todos os pacotes enviados por estes dois vão passar por ele.
