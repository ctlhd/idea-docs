# Настройка IPsec (MikroTik ↔ MikroTik)

## 1. Обмен ключами (PSK)
```bash
/ip ipsec peer
add address=REMOTE_PUBLIC_IP/32 exchange-mode=ike2 secret="MyStrongPSK"
```

## 2. Политики шифрования
```bash
/ip ipsec proposal
set [ find default=yes ] auth-algorithms=sha256 enc-algorithms=aes-256-cbc pfs-group=modp2048
```

## 3. Профиль
```bash
/ip ipsec profile
set [ find default=yes ] dh-group=modp2048 enc-algorithm=aes-256 hash-algorithm=sha256
```

## 4. Политика (Policy)
```bash
/ip ipsec policy
add src-address=192.168.1.0/24 dst-address=192.168.2.0/24 sa-dst-address=REMOTE_PUBLIC_IP sa-src-address=LOCAL_PUBLIC_IP tunnel=yes action=encrypt proposal=default
```

## Пример IP-адресов

| Сторона | Локальная сеть | Публичный IP | 
|---------|----------------|--------------|
| HQ      | 192.168.1.0/24 | 1.1.1.1      |
| Branch  | 192.168.2.0/24 | 2.2.2.2      |

## Проверка соединения
```bash
/ip ipsec installed-sa print
ping 192.168.2.1
```
