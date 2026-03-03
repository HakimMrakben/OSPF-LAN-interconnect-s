# OSPF-LAN-interconnect-s
 Configuration réseau complet avec OSPF sur 3 routeurs, propagation des routes et tests ping entre LAN

## 1️⃣ Schéma réseau

```
LAN R1
192.168.10.0/24
PC1:192.168.10.10
|
G0/1
R1
G0/0
|
10.0.0.0/30
|
G0/0
R2
G0/2
LAN R2
192.168.20.0/24
PC2:192.168.20.10
G0/1
|
10.0.1.0/30
|
G0/0
R3
G0/1
LAN R3
192.168.30.0/24
PC3:192.168.30.10
```

---

## 2️⃣ Plan IP

| Lien / LAN | Équipement | Interface | IP | Masque | Gateway / Remarques |
|------------|------------|-----------|----|-------|--------------------|
| R1-R2 | R1 | G0/0 | 10.0.0.1 | 255.255.255.252 | Lien point-à-point |
| R1-R2 | R2 | G0/0 | 10.0.0.2 | 255.255.255.252 | Lien point-à-point |
| R2-R3 | R2 | G0/1 | 10.0.1.1 | 255.255.255.252 | Lien point-à-point |
| R2-R3 | R3 | G0/0 | 10.0.1.2 | 255.255.255.252 | Lien point-à-point |
| LAN R1 | PC1 | – | 192.168.10.10 | 255.255.255.0 | GW = 192.168.10.1 |
| LAN R1 | R1 G0/1 | 192.168.10.1 | 255.255.255.0 | LAN R1 |
| LAN R2 | PC2 | – | 192.168.20.10 | 255.255.255.0 | GW = 192.168.20.1 |
| LAN R2 | R2 G0/2 | 192.168.20.1 | 255.255.255.0 | LAN R2 |
| LAN R3 | PC3 | – | 192.168.30.10 | 255.255.255.0 | GW = 192.168.30.1 |
| LAN R3 | R3 G0/1 | 192.168.30.1 | 255.255.255.0 | LAN R3 |

---

## 3️⃣ Configuration interfaces

### R1

```bash
enable
configure terminal
interface g0/0
ip address 10.0.0.1 255.255.255.252
no shutdown
interface g0/1
ip address 192.168.10.1 255.255.255.0
no shutdown
```

### R2

```bash
enable
configure terminal
interface g0/0
ip address 10.0.0.2 255.255.255.252
no shutdown
interface g0/1
ip address 10.0.1.1 255.255.255.252
no shutdown
interface g0/2
ip address 192.168.20.1 255.255.255.0
no shutdown
```

### R3

```bash
enable
configure terminal
interface g0/0
ip address 10.0.1.2 255.255.255.252
no shutdown
interface g0/1
ip address 192.168.30.1 255.255.255.0
no shutdown
```

---

## 4️⃣ Configuration OSPF

### R1

```bash
router ospf 1
network 10.0.0.0 0.0.0.3 area 0
network 192.168.10.0 0.0.0.255 area 0
```

### R2

```bash
router ospf 1
network 10.0.0.0 0.0.0.3 area 0
network 10.0.1.0 0.0.0.3 area 0
network 192.168.20.0 0.0.0.255 area 0
```

### R3

```bash
router ospf 1
network 10.0.1.0 0.0.0.3 area 0
network 192.168.30.0 0.0.0.255 area 0
```

---

## 5️⃣ Vérifications

1. **Interfaces up/up**
```bash
show ip interface brief
```

2. **Neighbors OSPF FULL**
```bash
show ip ospf neighbor
```

3. **Table de routage OSPF**
```bash
show ip route
```

- R1 doit voir O 192.168.20.0 et O 192.168.30.0
- R2 doit voir O 192.168.30.0
- R3 doit voir O 192.168.10.0 et O 192.168.20.0

4. **Ping final pour tester la connectivité**

- PC1 → PC3 : `ping 192.168.30.10` ✅
- PC3 → PC1 : `ping 192.168.10.10` ✅

---

## 6️⃣ Astuces pro CCNA

- Chaque lien point-à-point → **/30 unique**, pas d’overlap
- OSPF ne fonctionne que si interface est **up/up** et IP correcte
- Toujours vérifier neighbor **FULL** avant de tester ping
- Si une route n’apparaît pas → vérifie **network OSPF + masque + area**

---

## 7️⃣ Conclusion

Ce projet te permet de :

- Configurer un réseau multi-LAN avec **3 routeurs et OSPF**
- Vérifier la **connectivité inter-LAN**
- Comprendre la propagation OSPF et la **table de routage dynamique**
