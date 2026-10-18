# Домашнє завдання №2
## Транспортний рівень та служби мережі

### Завдання 1. Аналіз відкритих портів

```bash
sudo lsof -nP -iTCP -sTCP:LISTEN
sudo lsof -nP -iUDP
```

Фрагменти результатів:

```text
postgres   2472 emmaloj         8u IPv4 ... TCP 127.0.0.1:5432 (LISTEN)
mysqld     2674 emmaloj        31u IPv4 ... TCP 127.0.0.1:3306 (LISTEN)
mDNSRespo   402 _mdnsresponder   6u IPv4 ... UDP *:5353
netbiosd  70356 _netbios        3u IPv4 ... UDP *:137
```

| Протокол | Порт | Стан | Сервіс |
|---|---:|---|---|
| TCP | 5432 | LISTEN | PostgreSQL |
| TCP | 3306 | LISTEN | MySQL |
| UDP | 5353 | Не відображається в `lsof` | mDNS |
| UDP | 137 | Не відображається в `lsof` | NetBIOS |

TCP-порти у стані `LISTEN` очікують на з’єднання. UDP не встановлює з’єднання, тому `lsof` не показує для нього `LISTEN` (у Linux `ss` зазвичай відображає `UNCONN`).

### Завдання 2. Аналіз процесів, що слухають порти

```bash
sudo lsof -nP -iTCP -sTCP:LISTEN
```

Фрагменти результату:

```text
postgres 2472 emmaloj  8u IPv4 ... TCP 127.0.0.1:5432 (LISTEN)
mysqld   2674 emmaloj 31u IPv4 ... TCP 127.0.0.1:3306 (LISTEN)
Postman  2478 emmaloj 39u IPv4 ... TCP 127.0.0.1:10533 (LISTEN)
```

| Процес | PID | Порт |
|---|---:|---:|
| `postgres` | 2472 | 5432 |
| `mysqld` | 2674 | 3306 |
| `Postman` | 2478 | 10533 |

Усі три процеси прослуховують TCP-порти на локальному комп’ютері (`127.0.0.1`).

### Завдання 3. Перевірка TCP-з’єднання

```bash
nc -vz google.com 80
```

```text
Connection to google.com port 80 [tcp/http] succeeded!
```

- Протокол: TCP
- Порт: 80 (HTTP)
- Результат: з’єднання встановлено успішно.

### Завдання 4. Порівняння TCP та UDP

**TCP:**

```bash
nc -vz -G 5 8.8.8.8 53
```

```text
Connection to 8.8.8.8 port 53 [tcp/domain] succeeded!
```

**UDP:**

```bash
nc -u -vz 8.8.8.8 53
```

```text
Connection to 8.8.8.8 port 53 [udp/domain] succeeded!
```

В обох випадках отримано succeeded!. Для TCP це означає, що з’єднання встановлено, а для UDP такого підтвердження немає, оскільки він працює без встановлення з’єднання.

### Завдання 5. DNS-резолвінг

```bash
nslookup github.com
```

```text
Server:         1.1.1.1
Address:        1.1.1.1#53

Non-authoritative answer:
Name:   github.com
Address: 140.82.121.4
```

- Домен: `github.com`
- IP-адреса: `140.82.121.4`
- DNS-сервер: `1.1.1.1`
