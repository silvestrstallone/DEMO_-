 
•	Это схема сети

У каждого роутера по 2 интерфейса:
•	enp0s3
•	enp0s8
эти интерфейсы расположены закономерно, enp0s3 всегда смотрит вверх, enp0s8 всегда смотрит вниз.

но ISP с какого то хуя имеет интерфейсы:
enp0s8 – Смотрит в сторону HQ-R
enp0s9 – Смотрит в сторону BR-R
enp0s10 – Хуй знает куда смотрит, но вроде в интернет

У серверов всего один интерфейс (по крайней мере который мы будем юзать) – enp0s3. Он всегда смотрит в сторону маршрутников (наверх)

•	Глова I
При первом включении каждого устройства, всегда пишите команду «control sudowheel enabled»
ТАК ЖЕ НА КАЖДОМ УСТРОЙСТВЕ ДОБАВЛЯЕМ DNS:
- nano /etc/resolv.conf
 nameserver 8.8.8.8
Настройка ISP:
-	control sudowheel enabled
-	ip -c a 
-	nano /etc/net/ifaces/enp0s3/options
в переменной «BOOTPROTO» меняем static на dhcp,
в переменной «SYSTEMD_BOOTPROTO» меняем static на dhcp4
-	systemctl restart network
-	ping 8.8.8.8 (проверяем доступность днс сервака)
-	mkdir /etc/net/ifaces/enp0s8
-	mkdir /etc/net/ifaces/enp0s9
-	mkdir /etc/net/ifaces/enp0s10
создали значит папки для интерфейсов. В дальнейшем они нужны будут, запомните их путь.
Далее в КАЖДОЙ папке создаем файл «options»
-	nano /etc/net/ifaces/enp0s8(9,10)/options
и в каждом файле «options» пишем следуюшие загогулины:

BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_IPV4=yes
CONFIG_IPV6=yes
ДАЛЬШЕ АДРЕСАЦИЯ (ISP)!
1 команда – айпи
2 команда - шлюх
-	echo 192.168.0.165/30 > /etc/net/ifaces/enp0s8/ipv4address
- echo default via 192.168.0.166 > /etc/net/ifaces/enp0s8/ipv4route

-	echo 192.168.0.161/30 > /etc/net/ifaces/enp0s9/ipv4address
- echo default via 192.168.0.162 > /etc/net/ifaces/enp0s9/ipv4route
-	echo 192.168.0.249/30 > /etc/net/ifaces/enp0s10/ipv4address
-	systemctl restart network
Исходя из видоса, далее хостнеймы:
-	HQ-R:
hostnamectl set-hostname hq-r.hq.work;exec bash
-	BR-R:
hostnamectl set-hostname br-r.branch.work;exec bash
-	HQ-SRV:
hostnamectl set-hostname hq-srv.hq.work;exec bash
-	BR-SRV:
hostnamectl set-hostname br-srv.branch.work;exec bash

Маршрутнеки:
На интерфейсе «enp0s3» по дефолту задница какая то, отсутствует строка «CONFIG_IPV6=yes» 
-	nano /etc/net/ifaces/enp0s3/options
встречать нас будут строки
BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_IPV4=yes
CONFIG_IPV6=yes – (этой строки не будет наверное)
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
Добавляем стрчоку «CONFIG_IPV6=yes» на ее законное место!!!!
ЭТО НЕОБХОДИМО СДЕЛАТЬ ДЛЯ ВСЕХ МАРШРУТНИКОВ (enp0s3 интерфейс)
ДЛЯ (HQ-R)!
-	mkdir /etc/net/ifaces/enp0s8
-	mkdir /etc/net/iface---s/enp0s9
-	nano /etc/net/ifaces/enp0s(8,9)/options
и в файле «options» пишем следуюшие загогулины:

BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_IPV4=yes
CONFIG_IPV6=yes
ДЕВЯТЫЙ ПОХОДУ ДЛЯ ПРИКОЛАААА АХАХАХАХАХАХХАХА
ДАЛЬШЕ АДРЕСАЦИЯ (HQ-R)!
-	echo 192.168.0.166/30 > /etc/net/ifaces/enp0s3/ipv4address
- echo default via 192.168.0.165 > /etc/net/ifaces/enp0s3/ipv4route

-	echo 192.168.0.1/25 > /etc/net/ifaces/enp0s8/ipv4address
- echo default via 192.168.0.40 > /etc/net/ifaces/enp0s8/ipv4route
И ЕЩЕ АДРЕСАЦИЯ (BR-R)! лучше сразу это сделать
но сперва \/
ДЛЯ (BR-R)!
-	mkdir /etc/net/ifaces/enp0s8
-	mkdir /etc/net/ifaces/enp0s9
-	nano /etc/net/ifaces/enp0s(8,9)/options
и в файле «options» пишем следуюшие загогулины:

BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_IPV4=yes
CONFIG_IPV6=yes
ДЕВЯТЫЙ ПОХОДУ ДЛЯ ПРИКОЛАААА АХАХАХАХАХАХХАХА
-	echo 192.168.0.162/30 > /etc/net/ifaces/enp0s3/ipv4address
- echo default via 192.168.0.161 > /etc/net/ifaces/enp0s3/ipv4route

-	echo 192.168.0.129/27 > /etc/net/ifaces/enp0s8/ipv4address
- echo default via 192.168.0.140 > /etc/net/ifaces/enp0s8/ipv4route

А ТЕПЕРЬ НАСТРОИМ СЕРВЕРЫЫЫЫЫ
Не забываем про хостнеймы:
-	HQ-SRV:
hostnamectl set-hostname hq-srv.hq.work;exec bash
-	BR-SRV:
hostnamectl set-hostname br-srv.branch.work;exec bash
На интерфейсе «enp0s3» по дефолту задница какая то, отсутствует строка «CONFIG_IPV6=yes» - ОНА ЕСТЬ И НА СЕРВЕРЕ! ВОТ ЕТО ДА
-	nano /etc/net/ifaces/enp0s3/options
встречать нас будут строки
BOOTPROTO=static
TYPE=eth
NM_CONTROLLED=no
DISABLED=no
CONFIG_IPV4=yes
CONFIG_IPV6=yes – (этой строки не будет наверное)
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
Добавляем стрчоку «CONFIG_IPV6=yes» на ее законное место!!!! – ЭТО ДЕЛАТЬ НА ОБОИХ СЕРВЕРАХ

Маршрутизация на HQ-SRV:
-	echo 192.168.0.40/25 > /etc/net/ifaces/enp0s3/ipv4address
- echo default via 192.168.0.1 > /etc/net/ifaces/enp0s3/ipv4route
-	systemctl restart network

Маршрутизация на BR-SRV:
-	echo 192.168.0.140/27 > /etc/net/ifaces/enp0s3/ipv4address
- echo default via 192.168.0.129 > /etc/net/ifaces/enp0s3/ipv4route
-	systemctl restart network

ДАЛЕЕ ISP (nftables):
Щас мы настроим доступ в интернет.
-	apt-get update
-	apt-get install -y nftables
-	nano /etc/nftables/isp.nft
В этом файле необходимо прописать следующее:
table inet my_nat {
chain my_masquerade {
type nat hook postrouting priority srcnat;
oifname “enp0s3” masquerade
}
}
Дальше идем:
-	nano /etc/sysconfig/nftables.conf
И туда пишем:
include
include “/etc/nftables/isp.nft”
-	systemctl enable --now nftables

Далее на маршрутниках (HQ-R, BR-R) необходимо включить пересылку пакетов:
-	nano /etc/net/sysctl.conf
в строке «net.ipv4.ip_forward = 0» нолик меняем на 1
-	reboot
УСТАНОВКА FIREWALL НА ISP:
-	apt-get update
-	apt-get install -y firewalld
-	systemctl enable firewalld
-	firewall-cmd --permanent --zone=public --add-interface=enp0s8
-	firewall-cmd --permanent --zone=public --add-interface=enp0s9
-	firewall-cmd –permanent --zone=public --add-masquerade
-	reboot

УСТАНОВКА NFTABLE НА (HQ-R & BR-R)
-	apt-get update
-	apt-get install nftables
-	nft flush ruleset
-	nft add table nat
-	nft -- add chain nat prerouting { type nat hook prerouting priority -100 \; }
-	nft -- add chain nat postrouting { type nat hook postrouting priority 100 \; }
-	nft add rule nat postrouting oifname “enp0s3” masquerade
-	echo “flush ruleset” >> /etc/nftables/nftables.nft
-	nft list ruleset >> /etc/nftables/nftables.nft
-	systemctl restart nftables
 
НАСТРОЙКА EIGRP
Глова 2.
Перед этим надо поставить пакет «FRR» на все маршрутники (включая ISP)

-------------------------------------------------------------------------
ЭТО НАДО СДЕЛАТЬ НА ВСЕХ 3х РОУТЕРАХ – установка FRR
-	apt-get update 
-	apt-get install -y frr
теперь настройка фрр
-	nano /etc/frr/daemons
в этом текстовике ищем «eigrpd=no» и no меняем на yes
-	systemctl enable --now frr.service
-------------------------------------------------------------------------
и че то команду не понял, походу вход в фрр:
-	vtysh
sh ip route eigrp – посмотреть че получилось в еигрп

-	ISP –
EIGRP настройка
-	vtysh
-	configure terminal
-	router eigrp 1
-	network 192.168.0.164/30
-	network 192.168.0.160/30
-	do wr
-	ex
-	BR-R –
EIGRP настройка
-	vtysh
-	configure terminal
-	router eigrp 1
-	network 192.168.0.160/30
-	network 192.168.0.128/27
-	do wr 
-	ex
-	HQ-R –
EIGRP настройка
-	vtysh
-	configure terminal
-	router eigrp 1
-	network 192.168.0.164/30
-	network 192.168.0.0/25
-	do wr 
-	ex
-	ВАЖНО –

Перед проверкой EIGRP, проверьте nftables
-	status nftables.service


