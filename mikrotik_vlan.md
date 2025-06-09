# MikroTik: Простая настройка VLAN

## 📌 Что такое VLAN?
VLAN — это способ **разделить сеть на отдельные части**. Например:
- VLAN 10 — компьютеры
- VLAN 20 — Wi-Fi
- VLAN 30 — камеры

---

## 🧰 Что нужно:
- MikroTik с минимум 2 портами
- Компьютер с Winbox
- Немного терпения 🙂

---

## 🎯 Цель
- Порт ether1 — подключение к ядру (trunk)
- Порт ether2 — VLAN 10 (компьютеры)
- Порт ether3 — VLAN 20 (камеры)

---

## 🧱 Шаг 1: Создание VLAN-интерфейсов

```bash
/interface vlan
add name=vlan10 vlan-id=10 interface=ether1
add name=vlan20 vlan-id=20 interface=ether1

```

---

## 🌐 Шаг 2: Назначение IP-адресов на VLAN-интерфейсы

```bash
/ip address
add address=172.20.110.0/24 interface=vlan10
add address=172.20.111.0/24 interface=vlan20

```

---

## 📦 Шаг 3: DHCP-серверы для VLAN

```bash
/ip pool
add name=pool10 ranges=172.20.110.1-172.20.110.250
add name=pool20 ranges=172.20.111.1-172.20.111.50


/ip dhcp-server
add name=dhcp10 interface=vlan10 address-pool=pool10 disabled=no
add name=dhcp20 interface=vlan20 address-pool=pool20 disabled=no


/ip dhcp-server network
add address=172.20.110.0/24 gateway=172.20.110.254 dns-server=8.8.8.8
add address=172.20.111.0/24 gateway=172.20.111.254 dns-server=8.8.8.8

```

---

## 🔧 Шаг 4: Настройка портов (access/trunk)

```bash
/interface bridge
add name=bridge1 vlan-filtering=yes

/interface bridge port
add interface=ether2 bridge=bridge1 pvid=10
add interface=ether3 bridge=bridge1 pvid=20
add interface=ether1 bridge=bridge1

/interface bridge vlan
add bridge=bridge1 vlan-ids=10 tagged=ether1 untagged=ether2
add bridge=bridge1 vlan-ids=20 tagged=ether1 untagged=ether3

```

---

## ✅ Готово!
- `ether1` — trunk (все VLAN'ы идут сюда с тегами)
- `ether2` — VLAN 10 (компьютеры)
- `ether3` — VLAN 20 (камеры)



