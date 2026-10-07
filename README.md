# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) |Рублевська Поліна Євгенівна |
| Група |F5 2.02 |
| Номер варіанта |23 |
| Індивідуальний домен |bank.gov.ua |
| «Чужий» домен для завдання A.3.1 (варіант ± 15) |netbsd.org  |
| Середовище виконання |macOS |
| Дата виконання |06.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```
nc -C bank.gov.ua 80
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: bank.gov.ua
Connection: close
```

**Відповідь:**

```
HTTP/1.1 301 Moved Permanently
Date: Wed, 07 Oct 2026 14:03:45 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: close
Server: cloudflare
Nel: {"report_to":"cf-nel","success_fraction":0.01,"max_age":604800}
Location: https://bank.gov.ua/
cf-cache-status: DYNAMIC
set-cookie: __cf_bm=8NIMPz9YGQA3wA5axQjisBp0URQD9XCNXX1fA.F2uzc-1791381825.0154006-1.0.1.1-iuVRoA3qrkw8_AEcLa2IvWkbqzq.RNAj9sxCqcSiuQTjQ01VN2BqcqgI2sH4x1BWEgLYUrYiIMyCE4WKdy0iPht8JGinYnlijQ6sYb4dBnNMVpMHAhsbQVGkeY4L3ZqF1raZR73r6QxFm3crPobVow; HttpOnly; Path=/; Domain=bank.gov.ua; Expires=Wed, 07 Oct 2026 14:33:45 GMT
Server-Timing: cfCacheStatus;desc="DYNAMIC"
Server-Timing: cfEdge;dur=13,cfOrigin;dur=2
Report-To: {"group":"cf-nel","max_age":604800,"endpoints":[{"url":"https://a.nel.cloudflare.com/report/v4?s=nsRzLa%2FX%2B04ggEsOkyLvcZlF1HA%2B7Thnz%2BK3Gsv7KLpOoul3W4U1hq92xyqs2eO7S6DTOPc3Rn6HdWVwv3s29HFFVLbSJp5RGHb%2BlMuyfrvwAgEk1A6hPM67D1Ag"}]}
CF-RAY: a46d73709c8477b6-KBP

20b
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
<script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/v31edd6df95cf4e85bb4c19e7a9bdbcba1788362987495" integrity="sha512-iIg7k2xntmwu6/uSb5tpc/hySgZc4eoL31yB29W6tJFo2akwjPWcEqnCEdJvGexCL0KEQwVYv5BlowfhVz26hg==" data-cf-beacon='{"version":"2024.11.0","token":"3fa009e6cc25489fa32ceab4853be8e2","spa":2}' crossorigin="anonymous"></script>
</body>
</html>

0
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```
printf 'GET / HTTP/1.1\r\nConnection: close\r\n\r\n' | nc bank.gov.ua 80
```

**Вивід:**

```
HTTP/1.1 400 Bad Request
Server: cloudflare
Date: Wed, 07 Oct 2026 14:09:31 GMT
Content-Type: text/html
Content-Length: 155
Connection: close
CF-RAY: -

<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: netbsd.org\r\nConnection: close\r\n\r\n' | nc bank.gov.ua 80
```

**Вивід:**

```
HTTP/1.1 409 Conflict
Date: Wed, 07 Oct 2026 15:49:23 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 16
Connection: close
X-Frame-Options: SAMEORIGIN
Referrer-Policy: same-origin
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Server: cloud-flare
CF-RAY: a46e0df4ca88c916-KBP

error code: 1001%
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: opism-pr02.invalid\r\nConnection: close\r\n\r\n' | nc bank.gov.ua 80
```

**Вивід:**

```
HTTP/1.1 409 Conflict
Date: Wed, 07 Oct 2026 16:15:22 GMT
Content-Type: text/plain; charset=UTF-8
Content-Length: 16
Connection: close
X-Frame-Options: SAMEORIGIN
Referrer-Policy: same-origin
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Server: cloudflare
CF-RAY: a46e340f1f57ca56-KBP

error code: 1001% 
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```
printf 'GET / HTTP/1.0\r\n\r\n' | nc bank.gov.ua 80
```

**Вивід:**

```
HTTP/1.1 403 Forbidden
Date: Wed, 07 Oct 2026 16:20:46 GMT
Content-Length: 57
Connection: close
Cache-Control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Referrer-Policy: same-origin
Expires: Thu, 01 Jan 1970 00:00:01 GMT
CF-RAY: a46e3c003b6d77a4-KBP

Cloudflare encountered an error processing this request: %   
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```
printf 'GET /opism-pr02-12345 HTTP/1.1\r\nHost: bank.gov.ua\r\n\r\nGET / HTTP/1.1\r\nHost: bank.gov.ua\r\nConnection: close\r\n\r\n' | nc -C bank.gov.ua 80
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
Date: Wed, 07 Oct 2026 16:54:54 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: close
Server: cloudflare
Nel: {"report_to":"cf-nel","success_fraction":0.01,"max_age":604800}
Location: https://bank.gov.ua/opism-pr02-12345
cf-cache-status: DYNAMIC
set-cookie: __cf_bm=T2emrB_MNcUkR.s9k19XphZwJH.8_TSJHArHfxX0YTQ-1791392094.3028374-1.0.1.1-Orx.BVMTSbl1NoLBXvMSjojg9Gviyk1HJxAYQSx21y1LC7Wc.Z4i5yANJ3uPUA435ZzNVfyn1E_qS6xNXoML5mK1YeBGGvouZ.OQt9d61Alr9kRsY136OrUho2rhbVXxG2CCf48PrWi0_QvF0rwMeA; HttpOnly; Path=/; Domain=bank.gov.ua; Expires=Wed, 07 Oct 2026 17:24:54 GMT
Server-Timing: cfCacheStatus;desc="DYNAMIC"
Server-Timing: cfEdge;dur=15,cfOrigin;dur=3
Report-To: {"group":"cf-nel","max_age":604800,"endpoints":[{"url":"https://a.nel.cloudflare.com/report/v4?s=X5iKq6kbLu53rq3Wr8hDrefgrP7fGq3rWGLckq8JO1VxYeFYUqVRBHqTSmIhhuaC94Fl7u4vihrzmokPiCKvhQW9eiqGzuaZR2D0CEmD6o3tKtZuVSs1r59X3qZt"}]}
CF-RAY: a46e6e248a9c2492-KBP

20b
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
<script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/v31edd6df95cf4e85bb4c19e7a9bdbcba1788362987495" integrity="sha512-iIg7k2xntmwu6/uSb5tpc/hySgZc4eoL31yB29W6tJFo2akwjPWcEqnCEdJvGexCL0KEQwVYv5BlowfhVz26hg==" data-cf-beacon='{"version":"2024.11.0","token":"3fa009e6cc25489fa32ceab4853be8e2","spa":2}' crossorigin="anonymous"></script>
</body>
</html>

0
```

**Кількість отриманих відповідей: 1**

**Коди стану отриманих відповідей: 301 Moved Permanently**

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```
<текст команди>
```

**Вивід:**

```
<повний вивід>
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** <власний домен / `iana.org`>

**Підстава для використання резервного ресурсу (заповнюють за потреби):**

**Команда:**

```
<текст команди>
```

**Набраний запит:**

```
<текст запиту>
```

**Вивід:**

```
<повний вивід>
```

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:**

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |
| 6 | | | | | |
| 7 | | | | | |
| 8 | | | | | |

> Рядок наводять на кожне поле, яке реально надійшло. Зайві рядки вилучають, за потреби додають нові. Поле, походження якого встановити не вдалося, зазначають із позначкою «не визначено» та поясненням утруднення.

---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

<текст>

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

<текст>

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

<текст>

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

<відповідь>

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

<відповідь>

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

<відповідь>

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

<відповідь>

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

<відповідь>

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):**

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | | 1.1 | | | — |
| A.2 | поле відсутнє | 1.1 | | | |
| A.3.1 | | 1.1 | | | |
| A.3.2 | `opism-pr02.invalid` | 1.1 | | | |
| A.3.3 | поле відсутнє | 1.0 | | | |

**Висновок за таблицею (2–4 речення):** що саме змінювалося у запиті від проби до проби і як на це реагував сервер.

<текст>

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** <так / ні>

Якщо так, заповнюють таблицю. Якщо ні, таблицю вилучають.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
