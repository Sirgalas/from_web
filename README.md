## Gitflow.

Для win окружения: проследите чтобы при коммитах проставлялись unix\macOS окончание строк `\n (LF)`.  
`git config core.autocrlf false` - чтобы win не конвертировала `LF` -> `CRLF` при коммитах и обратно.  

Ветка `develop` - основная точка сборки кода.  

Работа ведется в `develop` ветке.  
Вернее делается `checkout -b feature/what-you-do` от её актуальной версии для своих задач.  

**Именование веток**:  
`feature/what-you-do` - при добавлении функционала.  
`fix/what-you-fix` - для исправления функционала.  
`hotfix/what-you-fix` - для внесения хотфиксов, только `checkout -b` от мастера.  

Перед тем как отправить ПР на слияние `feature/what-you-do` или `fix/what-you-fix` в `develop` делаем 
`git pull --rebase origin develop` - это позволит избежать лишних мердж коммитов и сделает вашу ветку чище, выявит проблемные участки сразу и так далее.  

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css" integrity="sha384-zCbKRCUGaJDkqS1kPbPd7TveP5iyJE0EjAuZQTgFLD2ylzuqKfdKlfG/eSrtxUkn" crossorigin="anonymous">
<div class="text-center">
   <img src="https://i.imgur.com/i7fe5G6.png" alt="rebase" class="rounded">
</div>

Это же касается ПР `hotfix/what-you-fix` в `master`, только `git pull --rebase origin master`.  
Изменения из `hotfix/what-you-fix` в `develop` доставляются через `cherry pick` коммита(ов).

**Именование коммитов**:  
`[номер задачи или раздел] название задачи или описание работы.`  

Примеры:  
`[123] Авторизация пользователя.`  
`[refactor] deprecated methods and unused variable.`  
`[fix] validate value length.`


## Сборка приложения.

У вас должен быть установлен [docker](https://docs.docker.com/get-docker/), [docker-compose](https://docs.docker.com/compose/install/) и утилита [make](http://gnuwin32.sourceforge.net/packages/make.htm).  

[backend](http://localhost/api/doc) - api  
[mailer](http://localhost:8025) - mailer  

1) Генерируем `.env` файлы.
   * `./build-env-file` - собирает все переменные и генерит файлы окружений `dev/stage`.
   * `cp ./.env.dev.local ./.env` - определяем для окружения-контейнеров.
   * `cp ./app/.env.dev.dist ./app/.env` - определяем для приложения.
2) `make install` - установит git hook на pre-commit.
3) Запуск.
   * `make init` - Сбилдить и запустить докер-контейнеры, установить пакеты, сгенерировать JWT ключи и так далее.  
      В общем сделать всё что бы приложение работало.

### Основной workflow.
* Находимся в ветке `develop`. `git pull` - актуализировали локальную ветку. Отпочковались - `git checkout -b feature/foo-bar-baz`
* Запустили. `make init`.
* Поработали.
  * Если нужны миграции:
    * `make doctrine-migration-generate` - Генерация пустой миграции.
    * `make doctrine-migration-diff` - Генерация миграции на основании `doctrine mapping`.
    * `make doctrine-migration-migrate` - Применить все миграции.
    * `make doctrine-migration-up` - Накатить миграцию (аргументом будет полное имя класса миграции).
    * `make doctrine-migration-down` - Откатить миграцию (аргументом будет полное имя класса миграции).
* Перед фиксацией изменений в гит:
  * `make php-analyze` - Статический анализ `php` кода (`phpstan`, `phplint`, `psalm`).
  * `make paratest` - Прогнать тесты.
* Поправили по надобности ошибки тестов и анализа.
* Зафиксировали изменения в своей ветке.
* Доставили свои изменения на `stage`.
* `make die` - Остановить.
* Выключить компьютер и пойти в бар.

### Остальные make команды

| Команда | Описание |
| ------ | --------- |
| `make docker-init` | Запустить систему на докер-контейнерах  |
| `make docker-down-clear` | Остановить докер-контейнеры  | 
| `make composer-install` | Установка необходимых пакетов в приложение |
| `make doctrine-migration-generate` | Генерация пустой миграции |
| `make doctrine-migration-diff` | Генерация миграции на основании `doctrine mapping` |
| `make doctrine-migration-migrate` | Применить миграции |


**TODO**: детальное описание.
- [ ] Описание всех `make` команд.
- [ ] Описание работы с `.env` файлами.
- [ ] etc.# from_web
