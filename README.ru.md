# rdk-discoverability

[English](README.md) | **Русский**

GitHub Action-обёртка над [repo-aeo](https://github.com/WhiteBite/repo-aeo) — Repo Discoverability Kit. Запускает аудит обнаруживаемости на каждый pull request, публикует скор и находки комментарием в PR, гейтит прогон по минимальному скору и умеет открывать autofix-PR, если проверяемый репозиторий это разрешил.

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
      - uses: WhiteBite/rdk-discoverability@v1.0.0
        with:
          min_score: ${{ vars.RDK_MIN_SCORE || 0 }}
```

## Входы

| Вход | По умолчанию | Описание |
| --- | --- | --- |
| `min_score` | `0` | Уронить прогон, если скор обнаруживаемости ниже значения |
| `online` | `true` | Проверять исходящие ссылки и читать живые метаданные GitHub |
| `cli` | `npx --yes repo-aeo@^1.0.0` | Команда запуска CLI (подойдёт и копия внутри репозитория) |
| `comment` | `true` | Публиковать/обновлять комментарий с отчётом в PR |

Аудит работает только на чтение; единственная запись — комментарий в PR. Autofix-PR открывается только если проверяемый репозиторий выставил `safety.allow_autofix: true` в `.discoverability/project.yml`.

## Версии

Экшен — тонкий раннер над CLI `repo-aeo`, его теги следуют за версией движка. `WhiteBite/repo-aeo/action@vX` — композитный экшен внутри основного репозитория — остаётся рабочим путём для существующих потребителей.

## Лицензия

MIT
