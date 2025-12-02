# Одна сеть: 1 DNS, 1 DHCP, 8 ПК  
Экзаменационный вариант (простой уровень)

Задание:

> Настроить одну локальную сеть:  
> 1 DNS-сервер, 1 DHCP-сервер и 8 ПК.  
> Опубликовать сайт `www.computernetworks.ru`  
> и изменить его содержимое на ФИО и номер билета.

---

# 1. Схема

- 1 коммутатор
- 1 DHCP-сервер
- 1 DNS-сервер
- 8 ПК

Все устройства → в один Switch (2960).  
Тип кабеля: **Copper Straight-Through**.

---

# 2. Адресный план (универсальный шаблон)

Сеть: `192.168.10.0/24`

- DHCP-сервер → `192.168.10.2`
- DNS-сервер  → `192.168.10.3`
- ПК будут получать IP по DHCP: `192.168.10.100+`
- Шлюз можно поставить `192.168.10.1` (роутера нет — но не мешает)

---

# 3. Настройка IP на серверах

## DHCP-сервер  
(Desktop → IP Configuration)

- IP Address: `192.168.10.2`  
- Subnet Mask: `255.255.255.0`  
- Default Gateway: `192.168.10.1`  
- DNS Server: `192.168.10.3`

## DNS-сервер  
(Desktop → IP Configuration)

- IP Address: `192.168.10.3`  
- Subnet Mask: `255.255.255.0`  
- Default Gateway: `192.168.10.1`  
- DNS Server: `192.168.10.3` (сам себе DNS)

---

# 4. DHCP (Services → DHCP на DHCP-сервере)

1. Services → DHCP  
2. Вверху переключатель **DHCP: On**  
3. Ввести:

Pool Name: LAN10
Default Gateway: 192.168.10.1
DNS Server: 192.168.10.3
Start IP Address: 192.168.10.100
Subnet Mask: 255.255.255.0
Maximum Number of Users: 50


4. Нажать **Add**

Теперь сервер раздаёт IP.

---

# 5. DNS + Сайт (на DNS-сервере)

## DNS
(Services → DNS)

1. DNS: **On**
2. Добавить запись:

Name: www.computernetworks.ru

Address: 192.168.10.3


Нажать **Add**.

## HTTP (сайт)
(Services → HTTP)

1. HTTP: **On**
2. В поле `index.html` заменить содержимое на:

```html
<html>
  <body>
    <h1>www.computernetworks.ru</h1>
    <p>ФИО: Ваша Фамилия Имя Отчество</p>
    <p>Номер билета: XX</p>
  </body>
</html>

Нажать Save

Теперь сайт доступен в сети.

6. Настройка ПК

Для каждого ПК:

Desktop → IP Configuration

Выбрать DHCP

ПК должен получить:

IP: 192.168.10.100+

Mask: 255.255.255.0

Gateway: 192.168.10.1

DNS: 192.168.10.3

7. Проверка
7.1. Проверка DNS-сервера

На любом ПК → Command Prompt:

ping 192.168.10.3
Должны быть Reply.

7.2. Проверка сайта

Открываем: Desktop → Web Browser:

Вводим:
www.computernetworks.ru

Должна открыться HTML-страница с твоими ФИО и номером билета.
