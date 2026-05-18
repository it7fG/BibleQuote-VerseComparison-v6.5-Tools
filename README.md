# BibleQuote 6.5 — Verse Comparison Tools

> Сторонние HTA-утилиты для сборки BibleQuote 6.5 mod83: параллельное сравнение стихов между переводами и менеджер модулей.
> Third-party HTA utilities for the BibleQuote 6.5 mod83 build: side-by-side verse comparison and a module manager.

[Русский](#russian) · [English](#english)

![Сравнение стиха — основное окно](screenshots/compare-main.png)

---

<a id="russian"></a>

## Русский

### Что это

Две независимые утилиты для портативной сборки **BibleQuote 6.5 mod83**:

- **`СравнениеСтиха.hta`** — открывает один и тот же стих в нескольких переводах одновременно. Пять режимов выбора (избранные / все русские / все украинские / RU+UK / RU+UK+EN), сохранение настроек, поддержка номеров Стронга.
- **`mod_manager.hta`** — управление установленными модулями: просмотр, фильтр, поиск, добавление, удаление в Корзину, мягкий сброс состояния BibleQuote, проверка конфликтов с `%APPDATA%`.

Утилиты **не модифицируют BibleQuote**. Это HTA-приложения (HTML+JavaScript), запускаемые встроенным в Windows `mshta.exe`. Никакой установки не требуется.

### Совместимость

- ✔ **BibleQuote 6.5 mod83** (целевая сборка) — портативный вариант с модулями в `Resources\BibleBooks\` и состоянием в `users\<имя_Windows>\`
- ✔ Должно работать с близкими 6.x-сборками той же структуры
- ✘ **Не работает с BibleQuote 7.x** — для этой ветки есть [отдельный репозиторий](https://github.com/it7fG/BibleQuote-VerseComparison-v7.5-Tools)

### Установка

1. Установите/распакуйте сборку BibleQuote 6.5 mod83 (обычно `BibleQuote6.5mod83\`).
2. Скопируйте из этого репозитория `СравнениеСтиха.hta` и `mod_manager.hta` в корень папки BibleQuote — туда же, где `BibleQuote6.exe` и подпапка `Resources\`.
3. Запускайте двойным кликом. Никаких прав администратора не нужно.

При первом запуске Windows может спросить разрешение для `mshta.exe` — это стандартный системный движок HTML-приложений, ничего ставить не нужно.

### `СравнениеСтиха.hta`

Открывает один стих параллельно во всех выбранных переводах в одном окне.

![Основное окно сравнения](screenshots/compare-main.png)

**Пять режимов сравнения:**

| # | Режим | Что в выводе |
|---|---|---|
| 1 | Избранные | Только модули, отмеченные ★ в окне «Избранные…» |
| 2 | Все русские | Все модули с языком `ru` |
| 3 | Все украинские | Все `uk` |
| 4 | RU + UK | Объединение |
| 5 | RU + UK + EN | Плюс английский |

Чекбокс «Чисто» убирает номера Стронга и служебную разметку. В окне «Избранные…» два списка: ★ (избранные для режима 1) и ⊘ (исключённые из всех режимов).

![Окно «Избранные…» с двумя списками](screenshots/compare-favorites.png)

**Особенности:**

- Кросс-скриптовое сопоставление имён книг (Jn ↔ Ин ↔ Iв)
- Поддержка кодировок UTF-8 (с BOM/без) и Windows-1251
- Автоматическая склейка длинных стихов, разбитых на несколько `<p>`
- Двухпроходный поиск книги: сначала по полному имени, затем по сокращениям — защищает от модулей с битым `ShortName`

### `mod_manager.hta`

Таблица всех модулей из `Resources\BibleBooks\` с фильтрами по языку, типу и текстовым поиском.

![Менеджер модулей](screenshots/manager.png)

**Действия:** обновить список, открыть папку модулей, добавить модуль (через выбор папки), удалить модуль (в Корзину), сбросить состояние BibleQuote, проверить конфликтующие папки в `%APPDATA%`.

**Сброс состояния** очищает в `users\<ваш_имя_Windows>\` следующие файлы (с резервными копиями `.bak` рядом):

- `viewtabs.cfg`, `bibleqt_history.ini`, `hotmodules.lst`, `mru.lst`
- Удаляется строка `LastAddress=` из `bibleqt.ini`
- Удаляется общий кэш `users\cached.lst` (BibleQuote пересоздаст)

Полезно если BibleQuote крашится при запуске или показывает «Не удалось разрешить избранный модуль».

**Проверка AppData.** Сборка mod83 портативна и не должна использовать `%APPDATA%`. Если там остались старые папки `BibleQuote*` от предыдущей установки официальной BibleQuote 6, они могут мешать. Кнопка «🔍 Проверить AppData» находит их и предлагает переименовать в `*_old_backup_<дата>` (без удаления данных).

### Где хранится состояние

В отличие от BibleQuote 7, эта сборка **полностью портативна**: всё состояние хранится внутри папки установки, в подпапке `users\<имя_Windows>\`. На каждом пользователе Windows автоматически своя независимая папка.

| Файл | Что хранит |
|---|---|
| `users\<имя>\_compare_favorites.txt` | Избранные модули (★) |
| `users\<имя>\_compare_excludes.txt` | Исключённые модули (⊘) |
| `users\<имя>\_compare_state.txt` | Последний выбор (модуль/глава/стих/режим) |

Файлы — обычные UTF-8, по одной папке модуля на строку. При первом запуске утилита проверяет также общую папку `users\` (без подпапки) — если там лежат файлы от предыдущей версии без разделения по пользователям, они будут использованы как fallback.

### Решение проблем

- **«Загружено модулей: 0»** — `.hta` лежит не в корне BibleQuote или нет подпапки `Resources\BibleBooks\`.
- **Модуля нет в списке `СравнениеСтиха.hta`** — проверьте, что в его `bibleqt.ini` есть `Bible=Y`. Если нет (это коммент. или словарь), он попадёт в `mod_manager.hta`, но не в сравнение.
- **Язык определился как `[?]`** — добавьте имя папки в `LANG_MAP` в верхней части `СравнениеСтиха.hta`.
- **После удаления модуля BibleQuote крашится** — нажмите «🧹 Сбросить состояние» в `mod_manager.hta` и перезапустите BibleQuote.

### О BibleQuote и сборке mod83

Это сторонний проект, **не связанный** с командой BibleQuote и автором сборки mod83.

> [BibleQuote 7](https://github.com/BibleQuote/BibleQuote) — официальный репозиторий BibleQuote («Quote the Bible with confidence | Цитируй Библию уверенно»).  
> © BibleQuote.org, 2017–2024

**BibleQuote 6.5 mod83** — это сборка сообщества, основанная на BibleQuote 6. Этот репозиторий содержит только утилиты-компаньоны для неё, не саму программу и не модули. Скачивайте сборку и модули из соответствующих сообществ.

### Лицензия

Код и документация в этом репозитории — под [MIT License](./LICENSE). Лицензия распространяется только на файлы из этого репозитория, не на BibleQuote, сборку mod83 и не на модули.

---

<a id="english"></a>

## English

### What this is

Two standalone HTA utilities for the **BibleQuote 6.5 mod83** portable build:

- **`СравнениеСтиха.hta`** ("VerseComparison.hta") — opens the same Bible verse in multiple translations side-by-side. Five selection modes (favorites / all Russian / all Ukrainian / RU+UK / RU+UK+EN), persistent settings, Strong's number stripping.
- **`mod_manager.hta`** — module management: browse, filter, search, add, recycle-bin delete, soft state reset, `%APPDATA%` conflict check.

The utilities **do not modify BibleQuote**. They are HTA applications (HTML+JavaScript) launched by the Windows-built-in `mshta.exe`. No installation required.

### Compatibility

- ✔ **BibleQuote 6.5 mod83** (target build) — portable layout with modules in `Resources\BibleBooks\` and state in `users\<windows_user>\`
- ✔ Should work with similar 6.x builds that share this layout
- ✘ **Does not work with BibleQuote 7.x** — see the [v7.5 repository](https://github.com/it7fG/BibleQuote-VerseComparison-v7.5-Tools)

### Installation

1. Install or unpack the BibleQuote 6.5 mod83 build (typically `BibleQuote6.5mod83\`).
2. Copy `СравнениеСтиха.hta` and `mod_manager.hta` from this repo into the BibleQuote folder, next to `BibleQuote6.exe` and the `Resources\` subfolder.
3. Double-click to launch. No admin rights needed.

On first launch Windows may prompt for `mshta.exe` permission — this is the standard system HTML application engine, nothing to install.

### `СравнениеСтиха.hta` (VerseComparison)

Shows one verse across all selected translations in a single window.

![Main comparison window](screenshots/compare-main.png)

**Five comparison modes:**

| # | Mode | Output |
|---|---|---|
| 1 | Favorites | Only modules marked ★ in the Favorites dialog |
| 2 | All Russian | All `ru` modules |
| 3 | All Ukrainian | All `uk` modules |
| 4 | RU + UK | Union |
| 5 | RU + UK + EN | Plus English |

The "Clean" checkbox strips Strong's numbers and metadata markup. The Favorites dialog has two lists: ★ (favorites for mode 1) and ⊘ (excluded from all modes).

![Favorites dialog with two lists](screenshots/compare-favorites.png)

**Features:**

- Cross-script book name matching (Jn ↔ Ин ↔ Iв)
- UTF-8 (BOM/no BOM) and Windows-1251 encoding support
- Automatic stitching of long verses split across multiple `<p>` blocks
- Two-pass book lookup (full name first, abbreviations as fallback) — guards against modules with broken `ShortName` fields

### `mod_manager.hta`

Table of all modules from `Resources\BibleBooks\` with language/type filters and free-text search.

![Module manager](screenshots/manager.png)

**Actions:** rescan, open module folder, add a module (via folder picker), delete a module (recycle bin), reset BibleQuote state, check for conflicting `%APPDATA%` folders.

**State reset** clears the following files in `users\<your_windows_user>\` (with `.bak` backups alongside):

- `viewtabs.cfg`, `bibleqt_history.ini`, `hotmodules.lst`, `mru.lst`
- Removes the `LastAddress=` line from `bibleqt.ini`
- Deletes the shared cache `users\cached.lst` (BibleQuote rebuilds it)

Useful when BibleQuote crashes on startup or shows "Could not resolve favorite module".

**AppData check.** The mod83 build is portable and should not use `%APPDATA%`. If old `BibleQuote*` folders from a previous official BibleQuote 6 install remain there, they can interfere. The "🔍 Check AppData" button finds them and offers to rename them to `*_old_backup_<date>` (no data deletion).

### Where state lives

Unlike BibleQuote 7, this build is **fully portable**: all state is kept inside the install folder, under `users\<windows_user>\`. Each Windows user automatically gets a separate folder.

| File | Stores |
|---|---|
| `users\<user>\_compare_favorites.txt` | Favorite modules (★) |
| `users\<user>\_compare_excludes.txt` | Excluded modules (⊘) |
| `users\<user>\_compare_state.txt` | Last selection (module/chapter/verse/mode) |

Plain UTF-8, one module folder per line. On first launch the utility also checks the shared `users\` folder (no per-user subdir) — if files from a previous, non-per-user version are present, they are used as a fallback.

### Troubleshooting

- **"Загружено модулей: 0"** ("0 modules loaded") — `.hta` is not in the BibleQuote root, or `Resources\BibleBooks\` is missing.
- **Module missing from VerseComparison** — check that its `bibleqt.ini` has `Bible=Y`. If not (commentary/dictionary) it appears in `mod_manager.hta` but not here.
- **Language detected as `[?]`** — add the folder name to `LANG_MAP` near the top of `СравнениеСтиха.hta`.
- **BibleQuote crashes after deleting a module** — click "🧹 Reset state" in `mod_manager.hta` and restart BibleQuote.

### About BibleQuote and the mod83 build

This is a third-party project, **not affiliated** with the BibleQuote team or with the author of the mod83 build.

> [BibleQuote 7](https://github.com/BibleQuote/BibleQuote) — the official BibleQuote repository ("Quote the Bible with confidence").  
> © BibleQuote.org, 2017–2024

**BibleQuote 6.5 mod83** is a community build based on BibleQuote 6. This repository contains companion utilities for it, not the program itself and not the modules. Obtain the build and modules from the corresponding communities.

### License

Code and documentation in this repository are under the [MIT License](./LICENSE). The license covers only files in this repository, not BibleQuote, the mod83 build, or the modules.
