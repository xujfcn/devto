---
title: 'Руководство по установке Claude Code на Windows: Git, PATH, переменные среды, PowerShell, WSL и полное устранение неполадок (2026)'
published: true
description: 'Подробное руководство по установке Claude Code на Windows для начинающих. Охватывает PowerShell, winget, Git для Windows, Node.js, PATH, переменные среды, командную строку, PowerShell и WSL, а также решение распространённых проблем после установки.'
tags: 'ai, cli, tutorial, windows'
canonical_url: null
cover_image: 'https://lsky.zhongzhuan.chat/i/2025/12/29/69523b0cd9f0d.png'
id: 3511048
date: '2026-04-16T14:49:04Z'
---

Пользователи Windows обычно сталкиваются с другим набором проблем при работе с Claude Code, чем пользователи macOS.

Не потому что Claude Code невозможен на Windows, а потому что Windows предлагает больше вариантов окружения:

- Command Prompt
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node установлен с MSI
- Node установлен через winget
- Git установлен, но не добавлен в PATH
- переменные среды установлены для одной оболочки, но не для другой

Именно поэтому это руководство более подробное и объясняет всю цепочку настройки с нуля.

## Перед установкой: выберите ваш путь на Windows

Есть два реалистичных варианта.

### Вариант A: нативная установка на Windows

Используйте:
- Windows Terminal
- PowerShell
- Git для Windows
- Node.js для Windows
- глобальная установка npm

Это проще для большинства начинающих.

### Вариант B: установка на WSL

Используйте:
- WSL2
- Ubuntu или Debian внутри WSL
- шаги установки для Linux внутри WSL

Это часто чище для разработчиков, но добавляет дополнительный уровень сложности. Если вы совсем новичок, начните с нативного Windows, если только вы уже не используете WSL.

## Рекомендуемая настройка для начинающих

Для большинства новых пользователей я рекомендую:

- Windows 11 или обновленный Windows 10
- Windows Terminal
- PowerShell
- winget для установки пакетов
- Git для Windows
- Node.js LTS
- Claude Code через npm

## Шаг 1: откройте правильный терминал

Установите или запустите **Windows Terminal** если это возможно.

Затем откройте **PowerShell**.

Проверьте, в какой оболочке вы находитесь:

```powershell
$PSVersionTable.PSVersion
```

Если PowerShell открывается и работает, оставайтесь в нём на протяжении всей установки. Не смешивайте PowerShell, cmd, Git Bash и WSL во время первоначальной установки, если вы не знаете, зачем это нужно.

## Шаг 2: проверьте доступность `winget`

`winget` — это самый простой способ установить Git и Node на современном Windows.

```powershell
winget --version
```

Если это работает, отлично.

Если нет:
- обновите App Installer из Microsoft Store
- или установите пакеты вручную с официальных сайтов

## Шаг 3: установите Git

Сначала проверьте:

```powershell
git --version
```

Если Git отсутствует, установите его с помощью winget:

```powershell
winget install --id Git.Git -e --source winget
```

Затем **закройте и снова откройте PowerShell**.

Проверьте:

```powershell
git --version
where.exe git
```

### Зачем нужен Git на Windows

По той же причине, что и на macOS:
- работа с осведомлённостью о репозитории
- дифы
- более безопасное редактирование
- откат версий
- многие инструменты разработчика предполагают наличие Git

Если у вас ещё нет репозитория:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Опционально — глобальная идентификация:

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Шаг 4: установите Node.js и npm

Проверьте существующие версии:

```powershell
node --version
npm --version
```

Если отсутствуют, установите Node.js:

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Закройте и снова откройте PowerShell.

Проверьте:

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Шаг 5: установите Claude Code

Проверьте, уже ли он установлен:

```powershell
where.exe claude
claude --version
```

Если нет, установите его глобально:

```powershell
npm install -g @anthropic-ai/claude-code
```

Затем проверьте:

```powershell
where.exe claude
claude --version
```

## Шаг 6: решите проблемы с PATH на Windows

Самая распространённая жалоба на Windows:

- npm говорит, что установка прошла успешно
- но `claude` всё ещё не распознан

Типичная ошибка:

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### Сначала найдите глобальный префикс npm

```powershell
npm config get prefix
```

Обычно это указывает на каталог, чей `bin` или исполняемое местоположение должно быть обнаружено через PATH.

Проверьте, где npm установил `claude`:

```powershell
npm list -g --depth=0
```

Также попробуйте:

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Типичное решение: перезагрузите оболочку

Многие проблемы с PATH — это просто устаревшие сессии. Полностью закройте PowerShell, откройте его заново и попробуйте снова.

### Если PATH всё ещё неправильный

Инспектируйте пользовательский PATH:

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

И машинный PATH:

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

Если глобальное исполняемое местоположение npm отсутствует, вы можете добавить его через UI переменных среды Windows или с помощью PowerShell.

Будьте осторожны при программном редактировании PATH. Сначала сделайте резервную копию.

## Шаг 7: правильно установите переменные среды

Пользователи Windows часто устанавливают переменные в одном месте и предполагают, что каждая оболочка их увидит. Это не всегда верно.

### Переменная только для текущей сессии в PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "your_key_here"
$env:OPENAI_API_KEY = "your_crazyrouter_key"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

Они действуют только для текущей сессии.

### Постоянные переменные среды на уровне пользователя

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "your_key_here", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your_crazyrouter_key", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Затем закройте и снова откройте PowerShell.

Проверьте:

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Шаг 8: поймите разницу между PowerShell, cmd, Git Bash и WSL

Это важно, потому что переменные и PATH могут вести себя по-разному.

| Окружение | Хорошо для начинающих? | Примечания |
|-----------|----------------------|-----------|
| PowerShell | Да | Лучший выбор для нативного Windows |
| Command Prompt | Нормально | Менее удобен чем PowerShell |
| Git Bash | Смешано | Работает, но добавляет ещё один слой оболочки |
| WSL | Хорошо для разработчиков | Лучше если вы хотите поведение как в Linux |

Если вы установили Claude Code в нативный Windows PowerShell, не тестируйте его первый раз в WSL и не предполагайте, что то же окружение применяется.

WSL имеет собственную систему пакетов, пути, файлы оболочки и переменные.

## Шаг 9: опциональный путь WSL

Если вы хотите наиболее чистое долгосрочное окружение разработчика на Windows, установите WSL.

Проверьте WSL:

```powershell
wsl --status
```

Установите если необходимо:

```powershell
wsl --install
```

Затем перезагрузитесь если Windows попросит.

После этого откройте Ubuntu и работайте с ней как с Linux машиной:

- установите Git внутри WSL
- установите Node внутри WSL
- установите Claude Code внутри WSL
- установите переменные среды внутри файлов оболочки WSL

Не предполагайте, что ваша установка Node на стороне Windows автоматически охватывает WSL.

## Шаг 10: проверьте всё от начала до конца

Запустите эти проверки:

```powershell
git --version
node --version
npm --version
claude --version
where.exe git
where.exe node
where.exe npm
where.exe claude
```

Затем создайте безопасную тестовую папку:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

И сначала протестируйте CLI с неопасными командами.

## Распространённые проблемы Windows и решения

## 1. `claude` не распознан

Причина:
- директория глобального исполняемого файла npm не в PATH
- оболочка не перезагружена
- установка не удалась

Решение:
- перезагрузите PowerShell
- проверьте `npm config get prefix`
- убедитесь что пакет существует в глобальном npm list
- инспектируйте PATH

## 2. Git устанавливается, но PowerShell всё ещё не может его найти

Причина:
- сессия терминала открыта до установки
- PATH не обновлён

Решение:
- полностью закройте и снова откройте терминал
- проверьте с помощью `where.exe git`

## 3. Node устанавливается, но npm отсутствует или сломан

Причина:
- неполная установка
- конфликтирующая старая версия Node

Решение:
- удалите конфликтующие версии Node если необходимо
- переустановите LTS чисто
- проверьте обе версии `node --version` и `npm --version`

## 4. Переменная среды установлена в PowerShell, но не в другом терминале

Причина:
- переменная была только для сессии

Решение:
- используйте постоянные переменные среды на уровне пользователя
- перезагрузите терминал после их установки

## 5. WSL работает, но PowerShell нет, или наоборот

Причина:
- вы установили два разных окружения

Решение:
- решите являются ли нативный Windows или WSL вашим главным окружением Claude Code
- полностью завершите настройку внутри этого окружения

## 6. Корпоративный прокси блокирует npm install

Вам может понадобиться:

```powershell
npm config set proxy http://proxy.example.com:8080
npm config set https-proxy http://proxy.example.com:8080
```

И возможно переменные сессии тоже.

## 7. Антивирус или программное обеспечение безопасности помешает

Иногда инструменты безопасности мешают новоустановленным CLI инструментам или скриптам.

Если логи установки выглядят нормально, но исполняемые файлы ведут себя странно, протестируйте в чистом терминале, убедитесь что файл существует, и проверьте историю Windows Security или endpoint protection.

## Безопасная стандартная установка Windows

Если вы хотите самый простой путь, который проще всего поддерживать, используйте ровно этот стек:

- Windows Terminal
- PowerShell
- winget
- Git для Windows
- Node.js LTS
- Claude Code через глобальную установку npm
- постоянные переменные среды на уровне пользователя

Эта установка скучная, и именно поэтому она хороша.

## FAQ

### Должны ли начинающие использовать PowerShell или WSL для Claude Code?

Если вы новичок, начните с PowerShell. Если вы уже предпочитаете инструменты Linux или уже ежедневно используете WSL, WSL может быть чище в долгосрочной перспективе.

### Почему Claude Code установился успешно, но всё ещё не запускается?

Чаще всего: устаревший PATH, неправильная оболочка, или npm установил пакет в место, которое ваш текущий терминал не читает.

### Нужен ли мне Git перед использованием Claude Code на Windows?

Для серьёзного использования — да. Даже если CLI запускается без Git, нормальные рабочие процессы кодирования намного удобнее с установленным и настроенным Git.

### Где я должен хранить переменные среды Claude Code на Windows?

Для сохранения — установите их на уровне пользовательской среды, а не только в текущей сессии оболочки.

### Является ли Git Bash хорошим местом для запуска Claude Code?

Это может сработать, но для начинающих это добавляет больше переменных. PowerShell проще документировать и поддерживать.

## Итоговый взгляд

История установки на Windows сложна не потому что Claude Code сложен. Она сложна потому что Windows предлагает вам много перекрывающихся окружений.

Если вы сохраняете установку последовательной — PowerShell, winget, Git, Node, npm, Claude Code, затем переменные среды — установка становится намного проще для отладки и обучения.
