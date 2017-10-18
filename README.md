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

Após verificar sua interface, abra o arquivo ```/etc/network/interfaces``` e insira as configurações para sua interface de rede.

```shell
# Network Interface
allow-hotplug enp1s0
iface enp1s0 inet static
address 10.0.0.1
netmask 255.255.0.0
```


# Servidor DHCP

# DNS 

# NAT
