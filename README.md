# Introdução

Este procedimento tem como objetivo auxiliar no procedimento de criação de uma sub-rede com máscara de 16 bits, a partir de uma interface WAN conectada ao roteador (computador).

O procedimento deve funcionar em Sistemas operacionais Debian _like_, como Ubuntu, Mint, etc. Antes de iniciar, deve-se possuir pelo menos 2 computadores, um deles será o servidor/roteador da subrede e outro para testar se as configurações de sub-rede e internet foram realizadas corretamente.


```shell
$ 
```

# Dependências

Durante o procedimento, serão utilizados pacotes externos do Debian para que seja possível realizar testes e verificações das configurações de rede. Os pacotes necessários são:

- net-tools
- isc-dhcp-server

De antemão intale o pacote net-tools com a linha de comando:

```shell
$ sudo apt-get install net-tools
```

# Interface de rede

Seguindo o procedimento de configuração, irá-se-á configurar a interface de rede que proverá o serviço. Para tanto execute o comando ```$ ifconfig``` e verifique qual é a interface de rede LAN (ethernet) de seu computador. Um exemplo de LOG do comando executado é dado abaixo:

```shell
enp2s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether b0:25:aa:18:31:04  txqueuelen 1000  (Ethernet)
        RX packets 36  bytes 2234 (2.2 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 110  bytes 9778 (9.7 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 426  bytes 32752 (32.7 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 426  bytes 32752 (32.7 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp3s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.23  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::1ddb:8f4d:ceb1:ab0a  prefixlen 64  scopeid 0x20<link>
        inet6 2804:14c:65e1:4213:86d2:2459:cfbc:92e9  prefixlen 64  scopeid 0x0<global>
        inet6 2804:14c:65e1:4213:8508:81e5:308d:4a20  prefixlen 64  scopeid 0x0<global>
        ether 00:24:d6:c0:4b:44  txqueuelen 1000  (Ethernet)
        RX packets 63430  bytes 62508510 (62.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 30708  bytes 6516885 (6.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

Após verificar sua interface, abra o arquivo ```/etc/network/interfaces```, como superusuário e insira as configurações para sua interface de rede.

```shell
# Network Interface
allow-hotplug <interface>
iface <interface> inet static
address 10.0.0.1
netmask 255.255.0.0
```

Para verificar se tal configuração foi feita corretamente utilize os comandos:

```
# Desabilita a interface de rede
$ sudo ifdown <interface>
# Habilita a interface de rede
$ sudo ifup <interface>
```
Feito isso, verifique que as configurações foram redefinidas em sua interface utilizando o comando ```$ ifconfig```

# Servidor DHCP e DNS

O primeiro passo para configurar o servidor DHCP é baixar o pacote ```isc-dhcp-server```, para isso execute a seguinte linha de comando:
```shell
$ sudo apt-get install isc-dhcp-server
```

Após a instalação deve-se configurar o arquivo localizado em ```/etc/dhcp/dhcpd.conf```, abra-o e insira as seguintes linhas de configurações:

```shell
authoritative;

option domain-name "lds-teste.com"
option domain-name-servers 8.8.8.8, 8.8.4.4, IP_DNS1, IP_DNS2;

subnet 10.0.0.0 netmask 255.255.0.0 {
    range 10.0.0.1 10.0.0.20
    option routers 10.0.0.1
}
```

Após a configuração anterior, abra o arquivo de interface padrão de DHCP, ele fica localizado em ```/etc/default/isc-dhcp-server``` e adicione a seguinte linha a ele:

```shell
INTERFACES="interfaceLAN"
```

Desative o serviço de DHCP
```shell
$ sudo service isc-dhcp-server stop
```
Ative-o novamente 
```shell
$ sudo service isc-dhcp-server start
```
Verifique o status, se foi possível subir o servidor DHCP
```shell
$ sudo service isc-dhcp-server status
```
O Log do comando anterior pode ser exemplificado da seguinte forma:
```shell
● isc-dhcp-server.service - ISC DHCP IPv4 server
   Loaded: loaded (/lib/systemd/system/isc-dhcp-server.service; enabled; vendor preset: enabled)
   Active: active (running) since Qua 2017-10-18 14:08:03 BRST; 1h 17min ago
     Docs: man:dhcpd(8)
 Main PID: 6006 (dhcpd)
   CGroup: /system.slice/isc-dhcp-server.service
           └─6006 dhcpd -user dhcpd -group dhcpd -f -4 -pf /run/dhcp-server/dhcpd.pid -cf /etc/dhcp/dhcpd.conf enp1s0

Out 18 14:23:05 Hornet dhcpd[6006]: DHCPREQUEST for 10.0.0.4 (10.0.0.1) from 74:e5:43:aa:ad:bf (pedro-Aspire-E1-571) via e
Out 18 14:23:05 Hornet dhcpd[6006]: DHCPACK on 10.0.0.4 to 74:e5:43:aa:ad:bf (pedro-Aspire-E1-571) via enp1s0
Out 18 14:25:43 Hornet dhcpd[6006]: DHCPREQUEST for 192.168.0.10 from 88:79:7e:ca:ef:fe via enp1s0: wrong network.
Out 18 14:25:43 Hornet dhcpd[6006]: DHCPNAK on 192.168.0.10 to 88:79:7e:ca:ef:fe via enp1s0
Out 18 14:25:48 Hornet dhcpd[6006]: DHCPREQUEST for 192.168.0.10 from 88:79:7e:ca:ef:fe via enp1s0: wrong network.
Out 18 14:25:48 Hornet dhcpd[6006]: DHCPNAK on 192.168.0.10 to 88:79:7e:ca:ef:fe via enp1s0
Out 18 14:25:48 Hornet dhcpd[6006]: DHCPDISCOVER from 88:79:7e:ca:ef:fe via enp1s0
Out 18 14:25:49 Hornet dhcpd[6006]: DHCPOFFER on 10.0.0.5 to 88:79:7e:ca:ef:fe (android-57948af2d739f5aa) via enp1s0
Out 18 14:25:51 Hornet dhcpd[6006]: DHCPREQUEST for 10.0.0.5 (10.0.0.1) from 88:79:7e:ca:ef:fe (android-57948af2d739f5aa) 
Out 18 14:25:51 Hornet dhcpd[6006]: DHCPACK on 10.0.0.5 to 88:79:7e:ca:ef:fe (android-57948af2d739f5aa) via enp1s0
lines 1-18/18 (END)
```

# NAT

Para que as configurações de rede privada criadas na interface de rede, e os computadores clientes possam acessar a Internet é necessário que configure-se as Tabelas de IP através do NAT. Esse procedimento deve ser executado toda vez que o Servidor for reiniciado, caso o script não seja inicializado com o sistema.

Primeiro crie um arquivo shell:
```shell
$ touch nat.sh
```
Dê as permissões para que ele possa ser executado:
```shell
$ sudo chmod +x nat.sh
```
Agora edite o arquivo ```nat.sh``` da seguinte maneira:

```shell
sudo iptables -X
sudo iptables -F
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
sudo iptables -t nat -A POSTROUTING -o <interfaceWAN> -j MASQUERADE
```
