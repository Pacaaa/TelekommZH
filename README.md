# Telekommunikációs Hálózatok – Kathara Labor Tudásbázis

## 1. Környezet és Kathara Parancsok

A laborfeladatok elindítása és kezelése a gazdagépen.

```bash
# Docker image elkészítése (Csak egyszer kell futtatni a félév elején)
docker build -f telkom_labor.dockerfile -t telkom_labor .

# Belépés a feladat mappájába
cd lab

# Hálózat elindítása (terminál ablakok automatikus megnyitása nélkül)
kathara lstart --noterminal

# Csatlakozás egy adott eszközhöz (pl. r1 router) új terminálban
kathara connect r1

# Hálózati topológia információk (melyik interface hova van kötve)
kathara linfo -n r1

# Hálózat leállítása és a környezet takarítása (törlése)
kathara lclean

# Interfész felkapcsolása (L2)
ip link set dev eth0 up

# IPv4 cím beállítása (L3)
# Szintaxis: ip addr add [IP/MASK] dev [INTERFACE]
ip addr add 172.16.0.1/24 dev eth0

# IPv4 cím törlése (ha elírtad)
ip addr del 172.16.0.1/24 dev eth0

# Beállítások ellenőrzése (rövidített nézet)
ip -br addr

# Default Gateway beállítása (pl. PC-ken vagy végponti routereken)
# "Minden ismeretlen forgalmat küldj a 172.16.0.1 címre"
ip route add default dev eth0 via 172.16.0.1

# Specifikus hálózat elérése (Routereken)
# Szintaxis: ip route add [CÉL_HÁLÓZAT] dev [KIMENŐ_IF] via [NEXT_HOP_IP]
ip route add 12.0.0.0/24 dev eth0 via 13.0.0.1

# Hibás route törlése
ip route del 12.0.0.0/24 dev eth0 via 13.0.0.1

# Routing tábla megtekintése
ip route

# ICMP Request (ping kérés) tiltása adott IP-ről
iptables -I FORWARD -t filter -p icmp --icmp-type echo-request -s 95.140.44.2 -j DROP

# ICMP Reply (ping válasz) tiltása
iptables -I FORWARD -t filter -p icmp --icmp-type echo-reply -s 95.140.44.2 -j DROP

# Szabályok listázása
iptables-save

# A 172.16.0.0/24 hálózatból jövő, eth0-n távozó csomagok NAT-olása
iptables -A POSTROUTING -t nat -s 172.16.0.0/24 -o eth0 -j MASQUERADE

# Az eth0-ra érkező, 80-as TCP port forgalmat irányítsuk a belső 172.16.1.2-re
iptables -A PREROUTING -t nat -i eth0 -d <ROUTER_KÜLSŐ_IP> -p tcp --dport 80 -j DNAT --to-destination 172.16.1.2:80

# STP állapot lekérdezése (van-e loop/blocking?)
brctl show

# STP bekapcsolása (bridge neve általában br0)
ip link set br0 type bridge stp_state 1

# Részletes port információk (Blocking, Forwarding, Learning állapotok)
brctl showstp br0
