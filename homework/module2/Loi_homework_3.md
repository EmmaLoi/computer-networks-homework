# Домашнє завдання №3
## Веб-протоколи та прикладний рівень

### Завдання 1. Аналіз HTTP-заголовків та статус-кодів

```bash
curl -I http://example.com
```

Фрагмент результату:

```text
HTTP/1.1 200 OK
Date: Sun, 18 Oct 2026 14:47:50 GMT
Content-Type: text/html; charset=utf-8
Connection: keep-alive
Server: cloudflare
```

- **Статус-код:** `200 OK` — запит успішно оброблено.
- **Location:** відсутній, перенаправлення немає.
- **Протокол:** `HTTP/1.1`.

### Завдання 2. Робота з методами HTTP

```bash
curl -i -X POST https://dummyjson.com/posts/add \
  -H "Content-Type: application/json" \
  -d '{"title":"Test post","body":"Hello","userId":1}'
```

Фрагмент результату:

```text
HTTP/2 201
content-type: application/json; charset=utf-8

{"id":252,"title":"Test post","body":"Hello","userId":1}
```

- **Статус-код:** `201 Created` — API повідомив про успішне створення ресурсу (DummyJSON імітує його створення).
- **GET:** отримує дані; параметри зазвичай передаються в URL, відповіді можуть кешуватися.
- **POST:** надсилає дані, зазвичай у тілі запиту; відповіді зазвичай не кешуються.

### Завдання 3. Перевірка TLS-сертифіката

```bash
openssl s_client -connect github.com:443 -servername github.com
```

Фрагмент результату:

```text
subject=CN=github.com
issuer=C=GB, O=Sectigo Limited, CN=Sectigo Public Server Authentication CA DV E36
Verification: OK
New, TLSv1.3, Cipher is TLS_AES_128_GCM_SHA256
Protocol: TLSv1.3
Verify return code: 0 (ok)
```

- **Issuer:** Sectigo Public Server Authentication CA DV E36 (Sectigo Limited).
- **TLS:** `TLSv1.3`.
- **Cipher Suite:** `TLS_AES_128_GCM_SHA256`.

### Завдання 4. Аналіз безпеки Cookies

**Сайт:** `github.com` (DevTools → Network → Response Headers).

Фрагменти заголовків `Set-Cookie` (значення приховані):

```text
Set-Cookie: ui_eligible=[приховано]; domain=github.com; path=/; secure; HttpOnly; SameSite=Lax
Set-Cookie: _gh_sess=[приховано]; path=/; secure; HttpOnly; SameSite=Lax
```

| Cookie ID | HttpOnly | Secure | SameSite |
|---|---|---|---|
| `ui_eligible` | Так | Так | Lax |
| `_gh_sess` | Так | Так | Lax |

- **HttpOnly:** не дозволяє JavaScript читати Cookie через `document.cookie`, що зменшує ризик викрадення Cookie під час XSS-атаки.
- **Secure:** передає Cookie лише через захищене HTTPS-з'єднання.

### Завдання 5. Архітектурне моделювання

**Сценарій А — WebSocket.** Для оновлення графіків цін у браузері WebSocket дозволяє серверу надсилати зміни в реальному часі без постійних повторних HTTP-запитів.

**Сценарій Б — gRPC.** Для обміну даними між білінгом та обліком акцій підходить gRPC: HTTP/2 і Protocol Buffers допомагають зменшити затримки та обсяг переданих даних.

