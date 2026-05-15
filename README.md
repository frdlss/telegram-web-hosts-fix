# Telegram Web Fix

> Когда **web.telegram.org** не открывается, а VPN ставить не хочется — утилита подсказывает Windows ходить на Telegram по **другому IP**. Только файл `hosts`, без прокси и лишних программ.

[![Windows](https://img.shields.io/badge/Windows-10%2F11-blue?logo=windows)]()
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)]()
[![Hosts only](https://img.shields.io/badge/method-hosts%20file-orange)]()

---

## Знакомая история?

В браузере Telegram Web — тишина: таймаут, вечная загрузка, пустая страница.

DNS часто отдаёт «нормальный» IP, а **TCP 443 до него** у провайдера не проходит. Zapret и GoodbyeDPI тут часто не спасают: режут **конкретный адрес** дата-центра, а не SNI.

**Telegram Web Fix** находит **другой доступный IP** из списка и прописывает его в `hosts` **только для доменов Web** — остальной интернет не трогается.

| Ситуация | Поможет? |
|----------|----------|
| Заблокирован один IP Telegram | Да |
| Заблокирован весь Telegram / DPI по SNI | Нет, нужен другой обход |
| Нужен Telegram Desktop | Нет, только Web в браузере |

---

## Что умеет

- Только **hosts** — 10 доменов Web, маркер `# zapret-telegram-web-fix`
- **Автопоиск IP** — проверка TCP 443, сохранение в `telegram-web-ip.cfg`
- **Автозапуск** при входе в Windows
- **Бэкап** hosts при первом включении
- Без VPN, прокси, реестра и файрвола

---

## Требования

- Windows 10 / 11
- Права администратора
- Браузер с WebSocket (Chrome, Edge, Firefox)

---

## Быстрый старт

1. Скачай или клонируй репозиторий.
2. Запусти **`telegram-web-fix.bat`** → UAC → **Да**.
3. В меню: **`4`** (найти IP), затем **`1`** (включить).
4. Открой **https://web.telegram.org** в **режиме инкогнито**.

> Первый раз всегда **4 → 1**. Пункт 4 только сохраняет IP, `hosts` сам не меняет.

### Как выглядит скан IP

```text
  +================================================================+
  |                    TELEGRAM WEB FIX                            |
  |                  hosts-file toggle                             |
  +================================================================+

  -- AUTO IP SCAN --

  Telegram Web - auto IP scan

  DNS (1.1.1.1): web.telegram.org -> 149.154.167.99
  (chasto zablokirovan - ishhem drugoi IP)

  Proverka 16 adresov (TCP 443)...

  149.154.167.99     skip (DNS)
  149.154.167.220    OK

  Saved: ...\telegram-web-ip.cfg
  IP dlya hosts: 149.154.167.220

  === GOTOV: 149.154.167.220 ===
  teper nazhmi 1 - Enable
```

Окно **cmd** — моноширинный шрифт (Consolas / Courier). В интерфейсе только ASCII, без смены кодировки, чтобы текст не «плыл» и не появлялись цветные блоки.

---

## Меню

| Клавиша | Действие |
|:-------:|----------|
| **1** | Enable — применить fix |
| **2** | Disable — убрать наши строки |
| **3** | Show — строки Telegram в hosts |
| **4** | Auto IP — скан и запись в cfg |
| **5** | Boot — автозапуск при входе |
| **0** | Exit |

### Тихие режимы

```text
telegram-web-fix.bat autostart
telegram-web-fix.bat enable-silent
telegram-web-fix.bat disable-silent
```

---

## Структура проекта

```text
telegram-web-fix/
|-- telegram-web-fix.bat
|-- telegram-web-ip.cfg      (sozdayotsya posle skana)
|-- utils/
|   |-- telegram-autodetect.ps1
|   `-- tg-hosts.ps1
|-- lists/
|   |-- telegram-hosts-domains.txt
|   `-- telegram-ip-candidates.txt
`-- backup/
    `-- hosts.backup
```

Домены только из `lists/telegram-hosts-domains.txt` — например `web.telegram.org`, `kws2.web.telegram.org`, `venus.web.telegram.org` и др. Остальные сайты и приложения не затрагиваются.

---

## Безопасность

- Меняется только `C:\Windows\System32\drivers\etc\hosts`
- Строки с маркером `# zapret-telegram-web-fix`
- Disable снимает только наши записи
- Папку проекта можно удалить в любой момент

---

## Автозапуск

Пункт **5 Boot** создаёт задачу **TelegramWebFix** в Планировщике: при входе подставляется IP из cfg без повторного скана.

Если перестало работать — снова **4**, потом **1**.

---

## Как отключить

1. **2** Disable
2. **5** Boot — выключить автозапуск (если был)
3. Удалить папку проекта

---

## Если не работает

| Проблема | Что сделать |
|----------|-------------|
| Сайт не открывается | 4 → 1, инкогнито, `ipconfig /flushdns` |
| QR не грузится | Enable включён; F12 → Network — новый домен в Issues |
| После обновления Windows | снова 1 или 5 + перелогин |
| Раньше работало | снова 4 — другой IP |
| Нужен Desktop | проект только для Web |

Обновление IP: [core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt) → дописать в `lists/telegram-ip-candidates.txt`.

---

## Важно

- Не связан с Telegram FZ-LLC.
- «Как есть», без гарантий.
- Обход ограничений — на вашей ответственности, по законам страны.

---

## Вклад

Приветствуются Issues и PR: новые домены Web, свежие IP, баги меню и автозапуска.

---

## Лицензия

MIT.

---

Если помогло — звезда на GitHub. Вопросы — в Issues репозитория.
