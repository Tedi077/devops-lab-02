# Runbook

Това ръководство описва как да стартираме и проверим учебната услуга Notes API локално. Командите се изпълняват от основната папка на хранилището. Необходим е **Python 3.12**. По подразбиране приложението работи без PostgreSQL и използва паметта за съхранение на бележките.

## Start locally / Локално стартиране

### Windows (PowerShell)

Проверка на версията, създаване на виртуална среда и инсталиране на зависимостите:

```powershell
py -3.12 --version
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
```

Стартиране на услугата на порт **8000**:

```powershell
.\.venv\Scripts\python.exe -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

### Linux / macOS (терминал)

```bash
python3.12 --version
python3.12 -m venv .venv
./.venv/bin/python -m pip install -r requirements-dev.txt
./.venv/bin/python -m uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Оставете терминала със сървъра отворен. За спиране натиснете `Ctrl+C`.

## Check that it works / Проверка, че работи

Отворете **втори терминал**, докато сървърът продължава да работи. Изпълнете подходящите команди:

**Windows (PowerShell):**

```powershell
curl.exe -i http://127.0.0.1:8000/health
curl.exe -i http://127.0.0.1:8000/ready
curl.exe -i http://127.0.0.1:8000/version
curl.exe -i http://127.0.0.1:8000/notes
```

**Linux / macOS:**

```bash
curl -i http://127.0.0.1:8000/health
curl -i http://127.0.0.1:8000/ready
curl -i http://127.0.0.1:8000/version
curl -i http://127.0.0.1:8000/notes
```

Очаквани резултати при стандартна конфигурация без база данни:

| Адрес | Очакван отговор |
|---|---|
| `/health` | HTTP 200 и `{"status":"ok"}` |
| `/ready` | HTTP 200 и `{"status":"ready"}` |
| `/version` | HTTP 200 и `{"version":"0.1.0"}`, ако `APP_VERSION` не е зададена |
| `/notes` | HTTP 200 и `[]`, ако все още няма бележки |

Автоматичната Swagger документация е достъпна на `http://127.0.0.1:8000/docs`. Отворете адреса в браузър, за да видите операциите и техните параметри.

Пример за създаване на бележка от PowerShell:

```powershell
Invoke-RestMethod -Method Post -Uri http://127.0.0.1:8000/notes -ContentType "application/json" -Body '{"title":"Test note","body":"Hello"}'
```

Пример за Linux / macOS:

```bash
curl -i -X POST http://127.0.0.1:8000/notes -H 'Content-Type: application/json' -d '{"title":"Test note","body":"Hello"}'
```

Успешното създаване връща HTTP 201 и JSON с `id`, `title`, `body` и `category`. Бележките в паметта се губят при рестарт на услугата.

## Run the tests / Тестове

От корена на хранилището (в отделен терминал, без да е необходимо сървърът да е стартиран):

**Windows (PowerShell):**

```powershell
.\.venv\Scripts\python.exe -m pytest
.\.venv\Scripts\python.exe -m ruff check .
```

**Linux / macOS:**

```bash
./.venv/bin/python -m pytest
./.venv/bin/python -m ruff check .
```

Успешният резултат от `pytest` е тестовете да преминат без грешки, а от `ruff` — липса на открити проблеми.

## When something breaks / Когато нещо се счупи

Проверете последователно следните **три неща**:

1. **Версия на Python.** Уверете се, че е наличен Python 3.12:

   ```powershell
   py -3.12 --version
   ```

   За Linux / macOS: `python3.12 --version`.

2. **Инсталирани зависимости.** Ако виждате `ModuleNotFoundError`, проверете пакетите и ги инсталирайте отново:

   ```powershell
   .\.venv\Scripts\python.exe -m pip show fastapi uvicorn psycopg
   .\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
   ```

   За Linux / macOS използвайте `./.venv/bin/python` вместо `.\.venv\Scripts\python.exe`.

3. **Достъпност на услугата и хранилището.** Проверете дали сървърът е стартиран на порт 8000 и дали `/ready` връща успешен отговор:

   ```powershell
   curl.exe -i http://127.0.0.1:8000/health
   curl.exe -i http://127.0.0.1:8000/ready
   ```

   За Linux / macOS заменете `curl.exe` с `curl`. Ако `/health` работи, но `/ready` връща HTTP 503, проверете връзката към PostgreSQL, когато е зададен `DATABASE_URL`. При грешка „connection refused“ проверете дали Uvicorn е стартиран и дали порт 8000 не се използва от друг процес.
