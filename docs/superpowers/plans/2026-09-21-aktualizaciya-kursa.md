# План актуализации курса МДК 01.02 (репозиторий mdk0102)

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Привести курс к 2026/27: заполнить Разделы 8–9, обновить Разделы 1–7 (VS Code/.NET 10, Selenium Manager, RestSharp 114), README с КТП 26/33/3, Экзамен.md на C#.

**Architecture:** Только документация (Markdown). Новые материалы пишутся по шаблонам курса; весь код из практик предварительно прогоняется в эталонном проекте (`C:\Users\prep\AppData\Local\Temp\opencode\mdk0102-ref`), проверенный код вставляется в материалы.

**Tech Stack:** .NET SDK 10.0.300 (net10.0), xUnit 2.9.3, Moq 4.20.72, Selenium 4.49.0, RestSharp 114.0.0, EF Core Sqlite 10.0.12, Serilog 4.4.0, k6 v2.2.0, GitHub Actions.

**Spec:** `docs/superpowers/specs/2026-09-21-aktualizaciya-kursa-design.md`

## Global Constraints

- Язык всех материалов, комментариев, сообщений коммитов — русский.
- Проекты: `net10.0`; окружение — VS Code + C# Dev Kit + `dotnet` CLI (Visual Studio не упоминаем как основную среду).
- Docker запрещён к использованию в материалах (TestContainers — только обзор в лекции 23).
- Версии: xunit 2.9.3, xunit.runner.visualstudio 3.1.4, Microsoft.NET.Test.Sdk 17.14.1, coverlet.collector 6.0.4, Moq 4.20.72, Selenium.WebDriver/Support 4.49.0, RestSharp 114.0.0, Serilog 4.4.0, Serilog.Sinks.Console 6.1.1, Microsoft.EntityFrameworkCore.Sqlite 10.0.12.
- Драйверы браузеров НЕ ставятся NuGet-пакетами (Selenium Manager).
- Шаблоны: лекция — `# Лекция N. Название` + `### **План лекции**` + `### **Подробное рассмотрение каждого пункта плана**` + `### **Резюме**` + `### **Контрольные вопросы**`; практика — `# Практическая работа N. Название` + `### **Тема**`, `### **Цель**`, `### **Теоретическая часть**`, `### **Ход работы**`, `### **Практический пример**`, `### **Варианты заданий**`, `### **Критерии оценки**`, `### **Контрольные вопросы**`, `### **Требования к репозиторию и README.md**`; СР — `## **Тема**`, `## **Цель**`, `## **Ход работы**`, `Критерии оценки`, `Контрольные вопросы`.
- Git: `git add -A`; префиксы `feat:`/`docs:`; ветка `feature/actualize-2026`; commit после каждой задачи.
- Push origin — только при установленном `GITHUB_TOKEN`/`GH_TOKEN` (в текущем shell не заданы — уточнить у пользователя перед push).

---

### Task 1: Лекция 23 — интеграционные тесты на SQLite (Раздел 8)

**Files:**
- Rename: `course/Раздел 8. Интеграционное и нагрузочное тестирование/Лекция 23. Интеграционные тесты. Библиотека TestContainers для поднятия тестовой БД.md` → `…/Лекция 23. Интеграционные тесты. Реальная база данных: EF Core и SQLite.md`
- Write: тот же файл (полное содержимое)

**Содержание (план лекции):** 1) пирамида тестирования; 2) интеграционные vs юнит-тесты; 3) чем подменять БД (in-memory провайдер, SQLite, реальная СУБД, обзор TestContainers: требует Docker — в колледже не используем); 4) SQLite in-memory: `DataSource=:memory:`, удержание открытого соединения, `EnsureCreated`; 5) EF Core 10 + `UseSqlite`; 6) изоляция данных между тестами; 7) когда юнит, когда интеграционный тест. Резюме + 6–8 контрольных вопросов.

- [ ] **Step 1: Переименовать файл**
`git mv "course/Раздел 8. Интеграционное и нагрузочное тестирование/Лекция 23. Интеграционные тесты. Библиотека TestContainers для поднятия тестовой БД.md" "course/Раздел 8. Интеграционное и нагрузочное тестирование/Лекция 23. Интеграционные тесты. Реальная база данных: EF Core и SQLite.md"`
- [ ] **Step 2: Написать материал** по плану выше (10–16 КБ)
- [ ] **Step 3: Проверить** `Select-String -Path <файл> -Pattern 'План лекции','Резюме','Контрольные вопросы'` — 3 совпадения; `(Get-Item <файл>).Length` > 8000
- [ ] **Step 4: Commit** `feat: лекция 23 — интеграционные тесты на EF Core и SQLite (раздел 8)`

### Task 2: Практика 27 — интеграционный тест с SQLite + эталонный проект

**Files:**
- Reference: `C:\Users\prep\AppData\Local\Temp\opencode\mdk0102-ref\LibraryApp\` (эталон, вне репозитория)
- Rename: `…/Практическая работа 27. Написание интеграционного теста с использованием TestContainers и xUnit.md` → `…/Практическая работа 27. Интеграционный тест сервиса с базой данных SQLite.md`
- Write: тот же файл

**Interfaces (эталон):** `Book { Guid Id; string Title; string Author; string Isbn; bool IsBorrowed; }`, `LibraryDbContext : DbContext` (`DbSet<Book> Books`, `OnModelCreating`: HasIndex(Isbn).IsUnique(), HasMaxLength), `LibraryService(LibraryDbContext)` методы `AddBook(Book)`, `GetAllBooks()`, `BorrowBook(Guid)`, `DeleteBook(Guid)` (бросает `InvalidOperationException`, если книга выдана). Тесты: добавление, выборка, дубликат ISBN (исключение), запрет удаления выданной книги, `[Theory]` на ISBN.

- [ ] **Step 1: Собрать эталон** в temp: `dotnet new classlib -n LibraryApp`, `dotnet new xunit -n LibraryApp.Tests`, `dotnet add LibraryApp package Microsoft.EntityFrameworkCore.Sqlite --version 10.0.12`, `dotnet add LibraryApp.Tests reference LibraryApp`, `dotnet add LibraryApp.Tests package Microsoft.EntityFrameworkCore.Sqlite --version 10.0.12`
- [ ] **Step 2: Написать код** по Interfaces; фикстура `DatabaseFixture : IDisposable` (SqliteConnection `DataSource=:memory:` + `EnsureCreated`)
- [ ] **Step 3: Прогнать** `dotnet test` — все тесты PASS (зафиксировать вывод)
- [ ] **Step 4: Переименовать файл** `git mv` (старое → новое имя, см. Files)
- [ ] **Step 5: Написать материал** с проверенным кодом (шаблон практики, «Практический пример» = эталон, «Варианты заданий» — 3–4 предметные области)
- [ ] **Step 6: Проверить** наличие всех 10 заголовков шаблона практики; размер > 10000
- [ ] **Step 7: Commit** `feat: практика 27 — интеграционный тест с SQLite (раздел 8)`

### Task 3: Лекция 24 — нагрузочное тестирование k6

**Files:**
- Rename: `…/Лекция 24. Введение в нагрузочное тестирование. Обзор инструмента k6.md` → `…/Лекция 24. Нагрузочное тестирование. Инструмент k6.md`
- Write: тот же файл

**Содержание:** метрики (RPS, latency, p90/p95/p99, error rate); виды (smoke/load/stress/spike/soak); обзор JMeter/NBomber/Locust; k6: VU, итерации, `options`, `thresholds`, `checks`, `sleep`; установка (winget/choco/portable ZIP, k6 v2.x); запуск `k6 run`, чтение вывода. Резюме + контрольные вопросы.

- [ ] **Step 1: Переименовать** `git mv`
- [ ] **Step 2: Написать материал** (9–14 КБ)
- [ ] **Step 3: Проверить** заголовки шаблона лекции; размер > 8000
- [ ] **Step 4: Commit** `feat: лекция 24 — нагрузочное тестирование k6 (раздел 8)`

### Task 4: Практика 28 — нагрузочный тест API с k6 + прогон k6

**Files:**
- Reference: `…\mdk0102-ref\LibraryApi\` (minimal API)
- Rename: `…/Практическая работа 28. Написание простого нагрузочного теста для API.md` → `…/Практическая работа 28. Нагрузочный тест API с помощью k6.md`
- Write: тот же файл

- [ ] **Step 1: Собрать SUT** `dotnet new web -n LibraryApi`; endpoints: `GET /api/books` (список), `POST /api/books` (создание), in-memory `List<Book>`; `dotnet build`
- [ ] **Step 2: Написать k6-скрипт** `load-test.js`: `options { vus: 10, duration: '30s', thresholds: { http_req_duration: ['p(95)<500'], checks: ['rate>0.95'] } }`, `default()` — GET + check
- [ ] **Step 3: Установить k6** `choco install k6 -y` (fallback: winget/portable ZIP) — проверить `k6 version`
- [ ] **Step 4: Прогнать** API (`Start-Process dotnet run`, порт 5xxx) + `k6 run --summary-export=summary.json load-test.js`; зафиксировать вывод; остановить API
- [ ] **Step 5: Переименовать** файл практики `git mv`
- [ ] **Step 6: Написать материал** (SUT, скрипт, запуск, разбор вывода, варианты, критерии)
- [ ] **Step 7: Проверить** заголовки шаблона; размер > 9000
- [ ] **Step 8: Commit** `feat: практика 28 — нагрузочный тест API на k6 (раздел 8)`

### Task 5: Лекции 25–26 (Раздел 9)

**Files:**
- Rename: `…/Лекция 25. Логирование в тестах. использование библиотеки log4net.md` → `…/Лекция 25. Логирование в тестах. Библиотека Serilog.md`
- Create: `…/Лекция 26. Непрерывная интеграция. GitHub Actions.md`
- Write: оба файла

**Лекция 25:** зачем логи в тестах; уровни и structured logging; обзор log4net/NLog/ILogger; Serilog: LoggerConfiguration, Sinks.Console/File, шаблоны; `ITestOutputHelper`; Serilog в ASP.NET Core; безопасность логов.
**Лекция 26 (новая):** идея CI; GitHub Actions: workflow, triggers (push/pull_request/workflow_dispatch), jobs, steps, runners, matrix, cache, artifacts, secrets, badges; разбор YAML; обзор Jenkins/GitLab CI/Allure.

- [ ] **Step 1: Переименовать** Лекцию 25 `git mv`
- [ ] **Step 2: Написать Лекцию 25** (10–15 КБ)
- [ ] **Step 3: Создать и написать Лекцию 26** (10–15 КБ)
- [ ] **Step 4: Проверить** заголовки шаблона у обеих; размеры > 8000
- [ ] **Step 5: Commit** `feat: лекции 25–26 — Serilog и GitHub Actions (раздел 9)`

### Task 6: Практики 29–30 (Serilog, TRX/ReportGenerator) + прогон

**Files:**
- Reference: продолжить `LibraryApp.Tests` (Serilog 4.4.0 + Sinks.Console 6.1.1; ITestOutputHelper)
- Write: `…/Практическая работа 29. Интеграция логирования в тестовый фреймворк.md`
- Rename + Write: `…/Практическая работа 30. Настройка и генерация Allure-отчета для проекта.md` → `…/Практическая работа 30. Отчетность в тестах. TRX и артефакты CI.md`

- [ ] **Step 1: Эталон:** добавить Serilog в тестовый проект, логгер через `ITestOutputHelper`, `dotnet test` — логи видны; зафиксировать
- [ ] **Step 2: Эталон:** `dotnet test --logger "trx;LogFileName=results.trx" --collect:"XPlat Code Coverage"`; установить `dotnet-reportgenerator-globaltool`, сгенерировать HTML; зафиксировать команды и результат
- [ ] **Step 3: Написать Практику 29** (шаблон практики)
- [ ] **Step 4: Переименовать и написать Практику 30**
- [ ] **Step 5: Проверить** заголовки шаблонов; размеры > 9000
- [ ] **Step 6: Commit** `feat: практики 29–30 — Serilog и TRX-отчётность (раздел 9)`

### Task 7: Практики 31–33 (GitHub Actions) + actionlint

**Files:**
- Rename+Write: `…/Практическая работа 31. Настройка простого Jenkins pipeline для запуска юнит-тестов.md` → `…/Практическая работа 31. Первый workflow GitHub Actions для юнит-тестов.md`
- Rename+Write: `…/Практическая работа 32. Интеграция UI-тестов в pipeline.md` → `…/Практическая работа 32. Интеграция API- и UI-тестов в pipeline.md`
- Rename+Write: `…/Практическая работа 33. Создание workflow на GitHub Actions для запуска тестов.md` → `…/Практическая работа 33. Итоговый CI-пайплайн проекта.md`
- Reference (только для проверки): `…\mdk0102-ref\workflows\*.yml`

- [ ] **Step 1: Проверить версии actions** через GitHub API (`releases/latest`): `actions/checkout`, `actions/setup-dotnet`, `actions/upload-artifact`, `actions/cache` — зафиксировать точные мажорные теги
- [ ] **Step 2: Составить 3 workflow YAML** (31: юнит-тесты + TRX-артефакт; 32: jobs api/ui + headless Chrome + скриншоты; 33: кэш NuGet, matrix, сводные артефакты, бейдж) — сохранить в `…\mdk0102-ref\workflows\`
- [ ] **Step 3: Проверить YAML** actionlint (portable ZIP с GitHub releases): `actionlint.exe workflows/*.yml` — без ошибок
- [ ] **Step 4: Переименовать и написать все три практики** (YAML — проверенные из шага 2–3; для UI — headless `ChromeOptions`)
- [ ] **Step 5: Проверить** заголовки шаблонов; размеры > 9000
- [ ] **Step 6: Commit** `feat: практики 31–33 — CI на GitHub Actions (раздел 9)`

### Task 8: СР 3

**Files:**
- Write: `…/Самостоятельная работа 3. Создание набора автоматических тестов.md`

**Содержание:** индивидуальный проект: юнит-тесты + интеграционные (SQLite) + API-тесты; Serilog; TRX; workflow GitHub Actions + бейдж; README. Шаги, критерии, контрольные вопросы.

- [ ] **Step 1: Написать материал** (8–12 КБ, шаблон СР)
- [ ] **Step 2: Проверить** заголовки; размер > 7000
- [ ] **Step 3: Commit** `feat: СР 3 — набор автотестов с CI (раздел 9)`

### Task 9: Лекции 9 и 12 — переписывание

**Files:**
- Rename+Write: `…/Лекция 9. Настройка окружения. Visual Studio, .NET SDK, управление пакетами NuGet.md` → `…/Лекция 9. Настройка окружения. Visual Studio Code, .NET SDK и управление пакетами NuGet.md`
- Rename+Write: `…/Лекция 12. Атрибуты Setup и TearDown.md` → `…/Лекция 12. Жизненный цикл и параметризация тестов в xUnit.md`

- [ ] **Step 1: Переименовать и переписать Лекцию 9** (VS Code, C# Dev Kit, .NET SDK 10, `dotnet new/build/test`, NuGet CLI и обозреватель)
- [ ] **Step 2: Переименовать и переписать Лекцию 12** (`[Fact]`, `[Theory]/[InlineData]/[MemberData]`, конструктор, `IDisposable`, `IClassFixture<T>`, `ICollectionFixture<T>`)
- [ ] **Step 3: Проверить** заголовки; размеры > 10000
- [ ] **Step 4: Commit** `feat: лекция 9 — VS Code и .NET 10; лекция 12 — жизненный цикл xUnit`

### Task 10: Разделы 4–5 — версии и формат (массовые правки)

**Files:** Лекции 10–11, Практики 4–9, СР 1 (Раздел 4); Лекции 13–14, Практики 10–11 (Раздел 5)

- [ ] **Step 1: Найти устаревшее** `Select-String -Path course/Раздел 4.* -Pattern 'net8.0','\.NET 8','8\.0'`; то же для Раздела 5
- [ ] **Step 2: Заменить** `net8.0` → `net10.0`, «.NET 8» → «.NET 10»; актуализировать версии пакетов (xunit 2.9.3, Moq 4.20.72, coverlet 6.0.4) в текстах практик и СР
- [ ] **Step 3: Проверить** повторный grep — 0 совпадений `net8.0`/«.NET 8» в Разделах 4–5; все примеры csproj используют net10.0
- [ ] **Step 4: Commit** `docs: разделы 4–5 — .NET 10 и актуальные версии пакетов`

### Task 11: Раздел 6 — Selenium Manager

**Files:** Лекция 15 (rename+write), Лекции 16–19, Практики 12–22

- [ ] **Step 1: Найти устаревшее** `Select-String -Path course/Раздел 6.* -Pattern 'ChromeDriver','GeckoDriver','установка через NuGet'`
- [ ] **Step 2: Переименовать Лекцию 15** → `…/Лекция 15. Selenium WebDriver. Принципы работы и первый тест.md`; убрать из заголовка «установка через NuGet» и из текста пакеты драйверов
- [ ] **Step 3: Обновить все файлы:** пакеты только `Selenium.WebDriver` + `Selenium.Support` 4.49.0; добавить абзац про Selenium Manager; в Лекции 19 — краткий обзор Playwright
- [ ] **Step 4: Проверить** grep: `ChromeDriver`/`GeckoDriver` остаются только в контексте «драйвер скачивается автоматически» (или 0); повторный проход по всем практикам
- [ ] **Step 5: Commit** `docs: раздел 6 — Selenium 4.49 и Selenium Manager`

### Task 12: Раздел 7 — RestSharp 114

**Files:** Лекция 22, Практики 23–26, СР 2

- [ ] **Step 1: Найти устаревшее** `Select-String -Path course/Раздел 7.* -Pattern 'new RestClient\(','AddParameter\(','\.Content','StatusCode'`
- [ ] **Step 2: Обновить:** `new RestClient(url)` → `new RestClient(new RestClientOptions(url))`; `AddJsonBody`; актуальные свойства ответа (`response.Content`, `response.StatusCode`, `response.IsSuccessful`); версия 114.0.0
- [ ] **Step 3: Проверить** grep: 0 вхождений `new RestClient(` без `RestClientOptions`
- [ ] **Step 4: Commit** `docs: раздел 7 — RestSharp 114 (RestClientOptions)`

### Task 13: README.md

**Files:** `README.md` (перезапись)

- [ ] **Step 1: Написать:** описание курса/специальности; структура репозитория (разделы 1–9); КТП **26 лекций / 33 практики / 3 СР**; блок «Текущий семестр» (плейсхолдеры: группы, даты, ссылки); требования к окружению (.NET SDK 10, VS Code + C# Dev Kit, Git, Chrome/Edge, k6, GitHub)
- [ ] **Step 2: Проверить:** нет маркеров «ктп старое»/«материал новый»; все 33 практики и 26 лекций перечислены; таблица КТП согласована с именами файлов
- [ ] **Step 3: Commit** `docs: README — структура курса и КТП 2026/27`

### Task 14: Экзамен.md → C#

**Files:** `Экзамен.md` (перезапись)

- [ ] **Step 1: Перевести 20 заданий на C#:** PascalCase, xUnit-ассерты, `[Theory]/[InlineData]`, Moq; исправить задание 17 (ссылка на `multiply`)
- [ ] **Step 2: Проверить:** нет Python-синтаксиса (`def `, `True/False`, `ValueError`, snake_case); все 20 заданий на месте; задание 17 самосогласовано
- [ ] **Step 3: Commit** `docs: экзаменационные задания на C#`

### Task 15: Финальная верификация

- [ ] **Step 1:** `dotnet build` + `dotnet test` эталонного проекта — PASS; k6-прогон выполнен (Task 4)
- [ ] **Step 2:** grep-чек-лист по всему репо: `net8.0`, `.NET 8`, `ChromeDriver`, `GeckoDriver`, `new RestClient(`, `TestContainers` (только лекция 23 обзор), `log4net` (только лекция 25 обзор), `Allure` (только лекции 26/практика 30 обзор)
- [ ] **Step 3:** проверить, что у всех 10+ новых файлов шаблонные заголовки и размер > 7000; у пустых файлов нет 0 байт
- [ ] **Step 4:** Commit фиксов (если были): `docs: финальная верификация курса`

### Task 16: Merge и push

- [ ] **Step 1:** `git checkout master; git merge feature/actualize-2026`
- [ ] **Step 2:** Проверить `$env:GITHUB_TOKEN`/`$env:GH_TOKEN`; если не задан — спросить пользователя
- [ ] **Step 3:** `git push origin master`; при доступности — `git push gogs master` (192.168.4.90)
- [ ] **Step 4:** Показать пользователю итог: коммиты, файлы, статус push
