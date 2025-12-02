# VLAN + 2 коммутатора, без роутера (только trunk)

Задание:

> Создать две VLAN, подключить ПК к разным коммутаторам,  
> настроить trunk между коммутаторами.  
> ПК в одной VLAN должны пинговаться, ПК из разных VLAN — нет.

Это базовый вариант экзамена, встречается очень часто.

---

# 1. Схема

Switch1:
- PC0 → Fa0/1 (VLAN 10)
- PC1 → Fa0/2 (VLAN 20)

Switch2:
- PC2 → Fa0/1 (VLAN 10)
- PC3 → Fa0/2 (VLAN 20)

Между коммутаторами:
- Switch1 Fa0/24 ↔ Switch2 Fa0/24 (trunk)

---

# 2. Адресация

Используем простые сети:

- VLAN 10 → `192.168.10.0/24`
- VLAN 20 → `192.168.20.0/24`

ПК:

- PC0 → 192.168.10.2 /24  
- PC1 → 192.168.20.2 /24  
- PC2 → 192.168.10.3 /24  
- PC3 → 192.168.20.3 /24  

Шлюз (gateway) **не нужен** — роутера нет.

---

# 3. Создание VLAN на обоих коммутаторах

На **Switch1**:

```text
enable
conf t
vlan 10
 name VLAN10
vlan 20
 name VLAN20
exit

На Switch2:

enable
conf t
vlan 10
vlan 20
exit

4. Настройка access-портов
Switch1
PC0 (VLAN 10):

interface fa0/1
 switchport mode access
 switchport access vlan 10
exit

PC1 (VLAN 20):

interface fa0/2
 switchport mode access
 switchport access vlan 20
exit

Switch2
PC2 (VLAN 10):

interface fa0/1
 switchport mode access
 switchport access vlan 10
exit
PC3 (VLAN 20):

interface fa0/2
 switchport mode access
 switchport access vlan 20
exit
5. Настройка trunk между коммутаторами
На обоих коммутаторах:

interface fa0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20
exit
6. Проверка
Работает:
PC0 → PC2 (оба VLAN 10)
ping 192.168.10.3
PC1 → PC3 (оба VLAN 20)

ping 192.168.20.3
НЕ работает:
PC0 → PC1 (разные VLAN)

PC2 → PC3 (разные VLAN)

Это правильно, потому что без роутера VLAN изолированы.


