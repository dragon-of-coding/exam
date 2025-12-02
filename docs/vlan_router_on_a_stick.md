# VLAN + 2 коммутатора + роутер (Router-on-a-stick)

Типовое экзаменационное задание:

> Реализовать сеть из двух подсетей VLAN 20 и VLAN 30.  
> 2 коммутатора, 1 маршрутизатор и 4 компьютера.  
> ПК0 и ПК1 во VLAN 20, ПК2 и ПК3 во VLAN 30.  
> Обеспечить маршрутизацию между VLAN через роутер.

---

с 1. Схема подключения

Switch1:
- PC0 → Fa0/1 (VLAN 20)
- PC1 → Fa0/2 (VLAN 20)

Switch2:
- PC2 → Fa0/1 (VLAN 30)
- PC3 → Fa0/2 (VLAN 30)

Связь между свитчами:
- Switch1 Fa0/24 ↔ Switch2 Fa0/24 (trunk)

Связь со роутером:
- Switch1 Fa0/23 ↔ Router G0/0 (trunk)

---

# 2. Адресация (удобный шаблон)

- VLAN 20: `192.168.20.0/24`
  - шлюз: `192.168.20.1`

- VLAN 30: `192.168.30.0/24`
  - шлюз: `192.168.30.1`

PC0: 192.168.20.2 /24, GW 192.168.20.1  
PC1: 192.168.20.3 /24, GW 192.168.20.1  
PC2: 192.168.30.2 /24, GW 192.168.30.1  
PC3: 192.168.30.3 /24, GW 192.168.30.1  



# 3. Настройка VLAN на обоих коммутаторах

```text
enable
conf t
vlan 20
 name VLAN20
vlan 30
 name VLAN30
exit

# 4. Назначение access-портов
Switch1
interface fa0/1
 switchport mode access
 switchport access vlan 20
exit

interface fa0/2
 switchport mode access
 switchport access vlan 20
exit

Switch2
interface fa0/1
 switchport mode access
 switchport access vlan 30
exit

interface fa0/2
 switchport mode access
 switchport access vlan 30
exit

# 5. Trunk между коммутаторами

На Switch1:

interface fa0/24
 switchport mode trunk
 switchport trunk allowed vlan 20,30
exit


На Switch2:

interface fa0/24
 switchport mode trunk
 switchport trunk allowed vlan 20,30
exit

6. Trunk между Switch1 и Router

На Switch1:

interface fa0/23
 switchport mode trunk
 switchport trunk allowed vlan 20,30
exit

7. Настройка маршрутизатора (Router-on-a-stick)

Активируем интерфейс:

enable
conf t
interface g0/0
 no shutdown
exit

Создание подинтерфейсов
VLAN 20
interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

VLAN 30
interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
exit

8. Проверка работы

На PC0:

ping 192.168.20.3    ← PC1
ping 192.168.30.2    ← PC2


Оба должны работать.

На PC2:

ping 192.168.30.3    ← PC3
ping 192.168.20.2    ← PC0
