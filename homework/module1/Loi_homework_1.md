# Домашнє завдання №1
## Основи комп'ютерних мереж

Виконувала завдання на macOS, тому замість команд Linux `ip a` та `ip r` використала `ifconfig` і `netstat -rn -f inet`.

## Завдання 1. Дослідження мережі

```bash
~ ifconfig
```

Серед інтерфейсів обрала `en0`: він активний і має IPv4-адресу. 

```text
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
    ether a2:18:46:XX:XX:XX
    inet6 fe80::XXXX:XXXX:XXXX:XXXX%en0 prefixlen 64 secured scopeid 0xb
    inet 10.61.175.151 netmask 0xffffff00 broadcast 10.61.175.255
    media: autoselect
    status: active
```

Шлюз перевірила окремою командою:

```bash
route -n get default
```

```text
route to: default
destination: default
mask: default
gateway: 10.61.175.1
interface: en0
flags: <UP,GATEWAY,DONE,STATIC,PRCLONING,GLOBAL>
```

Параметри мережі:

| Параметр | Значення |
|---|---|
| Інтерфейс | `en0` |
| Тип підключення | Імовірно Wi-Fi |
| MAC-адреса | `a2:18:46:XX:XX:XX` |
| IP-адреса (3-й рівень OSI) | `10.61.175.151` |
| Маска (3-й рівень OSI) | `255.255.255.0` (`/24`) |
| Адреса мережі | `10.61.175.0` |
| Gateway | `10.61.175.1` |
| Broadcast | `10.61.175.255` |
| Адреси хостів | `10.61.175.1` – `10.61.175.254` |
| Кількість доступних адрес хостів | 254 |

## Завдання 2. Дослідження маршрутизації

```bash
~ netstat -rn -f inet
```

Основні записи з таблиці маршрутизації:

```text
Routing tables

Internet:
Destination        Gateway            Flags               Netif Expire
default            10.61.175.1        UGScg                 en0
10.61.175/24       link#11            UCS                   en0      !
10.61.175.151/32   link#11            UCS                   en0      !
127                127.0.0.1         UCS                   lo0
127.0.0.1          127.0.0.1         UH                    lo0
169.254            link#11            UCS                   en0      !
224.0.0/4          link#11            UmCS                  en0      !
```

Маршрут `default` веде через шлюз `10.61.175.1` та інтерфейс `en0`. Запис `10.61.175/24` відповідає локальній мережі, а `127.0.0.1` — адресі зворотного зв’язку (`lo0`).

Окремо перевірила, як комп'ютер отримав IP-адресу:

```bash
ipconfig getpacket en0
```

Частина результату:

```text
op = BOOTREPLY
ciaddr = 10.61.175.151
yiaddr = 10.61.175.151
siaddr = 10.61.175.1
chaddr = a2:18:46:XX:XX:XX
dhcp_message_type (uint8): ACK 0x5
server_identifier (ip): 10.61.175.1
lease_time (uint32): 0x708
subnet_mask (ip): 255.255.255.0
router (ip_mult): {10.61.175.1}
domain_name_server (ip_mult): {1.1.1.1, 8.8.8.8}
```

IP-адресу отримано автоматично через **DHCP**, що підтверджує повідомлення `ACK`. DHCP-сервер `10.61.175.1` також надав маску, шлюз і DNS-сервери.

## Завдання 3. Визначення публічної IP-адреси

Публічну IP-адресу перевірила командою:

```bash
~ curl ifconfig.io
```

Результат:

```text
212.89.XXX.XXX
```

Моя локальна IP-адреса `10.61.175.151`, а публічна `212.89.XXX.XXX`. Вони відрізняються

Найімовірніше, причина в NAT. Маршрутизатор або обладнання провайдера транслює приватну адресу в публічну, завдяки чому пристрої локальної мережі можуть виходити в Інтернет.
