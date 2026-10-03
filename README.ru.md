# rdk-discoverability

[English](README.md) | **Русский**

GitHub Action-обёртка над [repo-aeo](https://github.com/WhiteBite/repo-aeo) — Repo Discoverability Kit. Запускает аудит обнаруживаемости на каждый pull request, публикует скор и находки комментарием в PR, гейтит прогон по минимальному скору и умеет открывать autofix-PR, если проверяемый репозиторий это разрешил.

## Зачем

Разовый аудит discoverability деградирует. Через полгода контрибьютор
«причёсывает» README, quickstart уезжает за 60-ю строку, тесты остаются
зелёными — и репозиторий тихо исчезает из поиска и AI-ответов. Этот экшен
превращает аудит в CI-гейт: каждый pull request получает скор и находки
комментарием, а прогон падает, когда скор опускается ниже `min_score`.

```markdown
<!-- rdk-discoverability-audit -->
## Discoverability audit — 71/100 (grade D)

`quickchart` · 38/44 checks passed · 1 error · 2 warnings

### Top findings

🔴 **No install/run commands in the first 60 lines**
   - why: Readers (and agents summarising the repo) decide within seconds
     whether the project works for them.
   - fix: Move a copy-pasteable install + run block above the fold. Use the
     quickstart section of .discoverability/project.yml as the source of truth.
```

## Использование

```yaml
name: rdk-audit
on:
  pull_request:
  schedule:
    - cron: '0 6 * * 1'

permissions:
  contents: read
  pull-requests: write

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: WhiteBite/rdk-discoverability@v1
        with:
          min_score: ${{ vars.RDK_MIN_SCORE || 0 }}
```

Это вся настройка: GitHub сам скачивает экшен, экшен сам тянет движок из
npm. Ничего форкать и устанавливать не нужно — в вашем репозитории появятся
ровно два файла: этот воркфлоу и сгенерированный `.discoverability/project.yml`
(создаётся командой `npx repo-aeo init`).

## Входы

| Вход | По умолчанию | Описание |
| --- | --- | --- |
| `min_score` | `0` | Уронить прогон, если скор обнаруживаемости ниже значения |
| `online` | `true` | Проверять исходящие ссылки и читать живые метаданные GitHub |
| `cli` | `npx --yes repo-aeo@^1.0.0` | Команда запуска CLI (подойдёт и копия внутри репозитория) |
| `comment` | `true` | Публиковать/обновлять комментарий с отчётом в PR |

Аудит работает только на чтение; единственная запись — комментарий в PR. Autofix-PR открывается только если проверяемый репозиторий выставил `safety.allow_autofix: true` в `.discoverability/project.yml`.

## Версии

Экшен — тонкий раннер над CLI `repo-aeo`, его теги следуют за версией
движка: воркфлоу `sync-engine` следит за релизами repo-aeo и автоматически
бампит закреплённую карет-спеку, ставит тег версии движка и двигает
мажорный тег — без ручного синка. Используйте
`WhiteBite/rdk-discoverability@v1`, чтобы оставаться на текущем мажоре,
или закрепите точный тег версии.

## Лицензия

MIT
