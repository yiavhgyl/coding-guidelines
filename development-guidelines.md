# Конфигурация

На всех девелоперских машинах обязательно иметь настроенными

    git config --global user.name "User Name"
    git config --global user.email "user@example.com"

У каждого коммиттера должна быть своя уникальная пара user.name / user.email,
чтобы знать, кого бить по рукам в случае чего. Коммиттеры не должны использовать
больше одной такой пары, чтобы не портить статистику.

Рекомендуется также настроить (по вкусу)

    git config --global rerere.enabled true
    git config --global color.diff auto
    git config --global color.interactive auto
    git config --global color.status auto

Ставим git-email для отправки готовых фичей на ревью лидам проекта

    sudo apt-get install git-email

Правим конфиг send-email на отсылку патчей (используем gmail'овский smtp сервер)

    git config --global sendemail.smtpserver smtp.gmail.com
    git config --global sendemail.smtpserverport 587
    git config --global sendemail.smtpencryption tls
    git config --global sendemail.smtpuser your_email@gmail.com
    git config --global sendemail.smtppass your_password
    git config --global sendemail.assume8bitEncoding utf-8

(Последний пункт необходим и тем, кто gmail для отсылки патчей не использует.)

Для правильной нумерации патчей в патчсетах, отправляемых на ревью, делаем:

    git config --global format.numbered true

Если в проекте есть etc/git/hooks, то его нужно обязательно подключить: находясь
в корневой директории проекта выполнить

    rm -r .git/hooks
    ln -s ../etc/git/hooks/ .git/hooks

# Коммиты

1. Коммитим помельче — атомарными смысловыми кусками. Пользуемся возможностью
   закоммитить часть файла (hunk). Но без фанатизма — если сильно
   долго разбирать лапшу, можно в качестве исключения закоммитить всё сразу.
   При этом явно отмечаем в commit message, почему так сделано.

1. При разделении на коммиты перед подачей на ревью (см. раздел "Ревью кода")
   следует помнить, что ревьюеру не интересна история разработки. Больше того,
   история разработки мешает ревьюить. Поэтому рекомендуется перед ревью
   сквошить ветку и перекоммичивать заново, атомарными кусками (см. выше).

1. Сообщения о коммитах — на английском (можно на ломанном). Кириллицу иногда
   тяжело просматривать в консоли на сервере.

1. Первая строка в сообщении о коммите — краткое описание того, что случилось
   (до 50-ти символов, не строго). В commit message надо писать не только
   ЧТО поменялось, но и ГДЕ. Очевидные части пути (например .lua в названии
   файла) и точку в конце строки стоит опускать.

   Приветствуется использование [тэгов][tagging].

     [tagging]: https://github.com/martin-eden/coding-guidelines/blob/master/tag-guidelines.md

1. Если нужно детальное описание — оно идёт с отбивкой пустой строкой.
   Стандартные гитовые тулзы заточены на такой формат.
   (см. http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)

1. В одном коммите не должно быть смешения рукописного и генерированного кода.

1. Не допускается объединение в один коммит перемещения (переименования) файла и
   изменения его содержимого. Изменения файла должны идти отдельным коммитом.

1. При перемещении файлов не допускается разделение на два коммита
   удаления файла из старого места и добавления файла в новое. И удаление
   и добавление одного и того же файла обязательно должны идти одним коммитом.

1. При заведении нового репозитория первый коммит должен иметь сообщение
   `initial commit` и иметь определенную структуру. Его описание см. в
   [project-structure-guidelines](project-structure-guidelines.md#%D0%A1%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BD%D0%BE%D0%B2%D0%BE%D0%B3%D0%BE-%D1%80%D0%B5%D0%BF%D0%BE%D0%B7%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D1%8F-%D1%81-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%BE%D0%BC-%D0%BD%D0%B0-lua).

# Добавление внешнего кода / библиотек в репозиторий

При добавлении в репозиторий бинарного файла или кода, внешнего по отношению
к репозиторию, рядом с добавляемым необходимо положить файл VERSION, содержащий
версию и ссылку на источник. (При условии, что указанный код / бинарь
не притянут через `git subtree`.)

Файл VERSION — это метаинформация для восстановления зависимостей. Желательно
дополнительно вносить информацию о версии и источнике в файл COPYRIGHT, а
информацию о лицензии в VERSION.

*Вопросы лицензии подробнее описаны в документе "Правила оформления лицензии
на код".*

# Перенос кода

При переносе кода из других проектов в коммите следует указывать корректное
авторство, а именно:

* Коммит делать с добавлением опции `--author='AUTHOR NAME <author@example.org>'`.
  В качестве автора выступает тот, кто первый внес переносимый код в историю
  проекта-донора.

* Сообщение коммита отбивать пустой строкой, за которой вставлять заголовки,
  перечисляющие всех внесших вклад в переносимый код. Формат заголовков

    Signed-off-by: AUTHOR NAME <author@example.org>

  по одному на строку. Порядок заголовков: автор, затем все соавторы в порядке
  убывания числа их коммитов.

Для получения списка авторов в `pk-tools` существует утилита `pk-git-get-authors`.

Перенос кода между проектами осуществляется по общим правилам, указанным в
секции "Коммиты". Перенос файла оформляется как отдельный коммит. Приведение
перенесенного кода к стандартам и условиям целевого проекта производится
следующим отдельным коммитом.

# Копирование кода

Механическое копирование кода (копипаста) не рекомендуется. В случае
необходимости копирования, независимо от последующих правок в целевом коде,
необходимо оставлять комментарий TODO с указанием тикета на применение принципа
[DRY](http://ru.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself).
Правила оформления комментария см. ниже "Про комментарии в коде (TODO)".

# Версионирование кода

При создании бинарного файла или пакета из кода, находящегося в репозитории,
необходимо поставить тег на коммит, соответствующий версии релиза.

Например так:

    git tag "v0.0.2" -m "v0.0.2"

Можно также воспользовать утилитой `pk-git-tag-version` из пакета `pk-tools`.

Если есть сомнения, то перед простановкой обязательно необходимо
проконсультироваться с мантейнером проекта и АГ для уточнения процедуры релиза
для конкретного проекта.

## Утилита pk-git-tag-version (справочная информация)

pk-git-tag-version это shell-скрипт, позволяющий устанавливать и инкрементировать
значение версий текущего репозитория путем добавления тегов с новой версией.
Хранится в репозитории pk-tools.

Входные параметры:

    $ pk-git-tag-version <suffix> major|minor|build|getlast [--dry-run] [--no-push]

Формат устанавливаемой версии - `<major>.<minor>.<build>-<suffix>`.
Например `0.1.3-test_suffix`.
Каждый суффикс версионируется отдельно. Каждое поле версии суффикса
инкрементируется отдельно.

Параметр major инкрементирует версию major на единицу.

    $ pk-git-tag-version test_suffix major
    v1.1.3-test_suffix

Параметр minor инкрементирует версию minor на единицу.

    $ pk-git-tag-version test_suffix minor
    v1.2.3-test_suffix

Параметр build инкрементирует версию build на единицу.

    $ pk-git-tag-version test_suffix build
    v1.2.4-test_suffix

Параметр getlast отдает информацию по последней версии суффикса.

    $ pk-git-tag-version test_suffix getlast
    1.2.4-test_suffix

    $ pk-git-tag-version test_suffix_not_exist major
    0.0.0-test_suffix_not_exist

При запуске с флагом `no-push` `git push --tags origin <newversion>`
не производится.
При запуске с флагом `dry-run` теги к коммиту не добавляются.

### Что делает pk-git-tag-version при запуске deploy-rocks (справочная информация)

При запуске скрипта `deploy-rocks` обязательным параметром является целевой
кластер для выливки. При локальной выливке он выглядит как `localhost-xx`.

В финале подготовки кода проекта к выливке (первая фаза `deploy-rocks`,
происходящая на машине, где исполняется скрипт), `deploy-rocks` исполняет
`pk-git-tag-version` на всех репозиториях проекта. Без ключей:

    $ pk-git-tag-version localhost-xx build

если `deploy-rocks` исполняется без ключей `--dry-run` и `--local-only`;

    $ pk-git-tag-version localhost-xx build --dry-run

если `deploy-rocks` исполняется c ключём `--dry-run`;

    $ pk-git-tag-version localhost-xx build --no-push

если `deploy-rocks` исполняется c ключём `--dry-run`.

Таким образом в результате выливки в репозитории `server` и `deployment`
появляются инкрементированные на младший разряд теги версии выливки с суффиксом
кластера, на который производилась выливка. Например:

    v0.0.117-localhost-xx
    v0.0.4-production_cluster_name

При штатных выливках теги версии выливаемого проекта на все боевые сервера
обычно применены к одному коммиту.

## Ветки и теги в репозиториях deb-пакетов.
Репозитории с пакетами имеют специальные "парные" ветки:

* **upstream** обычно соотвествует `master` оригинального репозирия
* **master** версия с добавлением `debian/`

Подробно описаны в документе `deb-packaging-guideline.md`

# Передача и хранение бинарных файлов вне репозиториев

*Любой бинарный файл, к которому есть исходники, должен обязательно быть
однозначно аттрибутирован версией этих исходников.* При работе с бинарными
файлами, полученными из кода, расположенного в наших репозиториях, рекомендуется
использовать `git describe` для однозначной маркировки версии.

Перед маркировкой необходимо удостовериться, что проведен как минимум один релиз
(репозиторий содержит хотя бы один тег версии). При отсутствии такового
проконсультироваться с мантейнером проекта и АГ для уточнения процедуры релиза
для конкретного проекта.

Исходники должны обязательно доступны команде. Если у них нет своего
репозитория, нужно либо
(1) запросить новый репозиторий, либо
(2) закоммитить их в etc/ текущего, либо
(3) закоммитить их в другое пригодное место текущего проекта.

При передаче собранных бинарных файлов, в передаваемом архиве / tarball
обязательно должен присутствовать файл VERSION с указанием версий всех
передаваемых бинарных файлов.

*Вопросы лицензии подробнее описаны в документе "Правила оформления лицензии
на код".*

# Работа с master-ом (только для maintainer-ов)

1. При изменении master-a в библиотечном проекте необходимо в обязательном
порядке ставить тикеты на update-subtree майнтейнерам конечных проектов.
2. Необходимо поддерживать линейную историю в общих ветках (master, dev, etc.)
Исключение: результат update-subtrees. Рекоменуется использовать опцию
`--ff-only` для `git pull` и `git merge` для того, чтобы случайно не нарушить
линейность.

# Ветки

Делаем push на сервер почаще. Приятно видеть хотя бы один пуш в день.

Ветку master не трогаем ни при каких обстоятельствах — её трогает только
maintainer.

Все остальные не "системные" ветки называем с префиксом:
`<developer-initials>/<branch-name>` (`ag/mybranch`, `ik/otherbranch`)

Ветки, относящиеся к тикетам (фичебранчи), должны после инциалов содержать номер
тикета: `ag/555/short_summary`.

Ветки, на повторном ревью должны содержать номер итерации:
`ag/mybranch_v2` (см. раздел _Ревью кода_). Для веток вида
`ag/555/short_summary` новая ветка будет `ag/555/short_summary_v2`.
Для веток с правками отдельным патчсетом при отсутствии осмысленного имени для
новой ветки можно использовать суффикс `fixes`: `ag/555/short_summary_fixes`.

Перезаписывать ветки нельзя при любом вердикте. При БПР (см. раздел
"Вердикты по ревью") формально нужно пушить новую версию ветки (чтобы её
обработала автоматика), вливать и стирать.

За исключением особых случаем используем подход с feature (aka topic) branches —
по ветке на фичу. Однако, приветствуется т.н. оппортунистический рефакторинг
— исправление, обобщение, приведение к гайдлайнам старого кода по ходу дела.
Такие изменения необходимо оформлять отдельными коммитами и помечать явно,
с тем, чтобы при ревью возникало меньше вопросов.

Новые ветки начинаем от `master` для библиотечных проектов и от `dev` для остальных
(а еще лучше предварительно уточнить у майнтейнера откуда начинать).

Если майнтейнер тормозит с обновлением мастера, вполне допускается начать ветку
от невлитой ветки с нужными фичами (майнтейнеру при этом нужно выписать пинка,
чтобы не тормозил).

Регулярно перематываем (`git rebase`) ветки, находящиеся в работе, к мастеру, чтобы
облегчить слияние в конце разработки. В идеале — перематываем каждый раз когда
мастер изменяется.

Когда работа над веткой завершена, делаем ей `git rebase master`, тестируем и даём
мейнтейнеру отмашку на вливание ветки в мастер. Влитые в мастер ветки стираем
(либо ресетим к мастеру, если очень хочется сохранить).

(Для прикладных /не библиотечных/ проектов.) На каждую инсталляцию есть по ветке
(обычно называются по имени хоста, без префикса), голова которой указывает на
тот код, который сейчас вылит на эту инсталляцию. Эти ветки контролирует и
обновляет тот, кто производит выливку на соответствующие инсталляции (может быть
несколько человек, если это, например, инсталляция для первичного тестирования).

Во многих проектах есть директория lib, в которой живут зависимости. Некоторые
из этоих зависимостей могут быть притянуты как `git subtree`. *Коммититься в них
из репозитория проекта нельзя ни в коем случае.* Нужно сделать коммит в основной
репозиторий, запушиться и притянуть правки (обычно через `bin/update-subtrees
update`).

Нельзя проводить `update-subtree` для feature branches, предназначенных для
вливания в dev (такая ветка нарушает линейность истории разработки и может
создать проблемы при проведении `git rebase`). Для отладки следует использовать
временные ветки, не предназначенные для вливания в dev.

В зависимости от проекта и библиотеки могут быть шорткаты для тестирования —
спрашивайте лидов :)

# Тестовая выливка кода

Если изменения вносились в любой из библиотечных проектов, притягиваемых в
финальные проекты как subtree, необходимо проверить, что эти изменения не
портят финальные проекты. Тестовую выливку *желательно* делать перед отправкой
кода на ревью и *обязательно* делать после того, как патч был принят и залит в
удаленный репозиторий (если она не делалась ранее).

## Порядок тестовой выливки

1. Запушить изменения в удаленный репозиторий, если этого еще не было сделано.
Для выливки перед ревью пушится рабочая ветка кода.
2. Создать тестовую ветку в финальном проекте.
3. Подтянуть в тестовую ветку изменения как subtree.
4. Подеплоиться на локалхост.
5. Запустить тесты и убедиться, что ничего не сломалось. Как минимум необходимо
запускать "does it work", желательно запускать полный набор тестов.

# Боевая выливка

Ответственный за выливку должен при составлении выливочного тикета искать тикеты
в категории "Deployment Pending" (и убеждаться, что они влиты).

Cвязь 'тикет-в-DP' — 'выливочный тикет' должна быть `blocking -> blocked by`
(выливочные блокируются тикетами в DP).

Конкретные правила и рекоммендации по выливке на бой (продакшн) могут зависеть
от проекта, ознакомьтесь с документацией по проекту.

# Ревью кода

После завершение работы над тикетом соответствующая ветка посылается в виде
patch series на ревью кода лидом проекта. Требования к содержанию коммитов
см. в раздел "Коммиты".

Если разработка по задаче затягивается, то необходимо посылать промежуточные
ревью текущего состояния кода не реже чем раз в три дня, если заранее
не оговорено иное.

Проекты на гитхабе (lua-nucleo, lua-aplicado...) мы ревьюим на общих основаниях,
через почту.

В lua-nucleo, lua-aplicado ВСЕ ревью — БЛОКИРУЮЩИЕ.

Патчсеты, которые содержат правки конфигов системных сервисов (например, nginx),
должны обязательно ревьюиться главным системным администратором.

Любые массовые операции поиска и замены в обязательном порядке нужно
оформлять отдельным коммитом. Иначе невозможно толком ревьюить.

Допускается их оформление отдельным патчсетом (но можно группировать
по нескольку переименований) — чтобы потом не париться с вычленением
правок.

## Git & e-mail

### Формируем patch'и

    git format-patch -n --cover-letter master

С указанием каталога, куда положить файлы:

    git format-patch -n --cover-letter -o ~/projects/review master

Патч должен быть для той ветки, от которой растет ветка с правками, т.е. для
некоторых проектов это будет не `master`, а `dev` (см. раздел _Ветки_)

    git format-patch -n --cover-letter -o ~/projects/review dev

### Редактируем patch

Открываем файл `0000-cover-letter.patch` на редактирование:

1. Правим 4-ю строчку Subject:
   `[PATCH 0/3] *** SUBJECT HERE ***` таким образом, чтобы получилось следующее:
   `[PATCH 0/3] changes_short_sumary | project_name | ag/555/branch_name`

2. Ниже (вместо `*** BLURB HERE ***`) добавляем *ссылку на соответствующий тикет*
   и краткое описание что и как было сделано по задаче.
   Для повторного ревью также - что было исправлено/переделано.

3. Во всех остальных файлах вида `0001-commit-message.patch` дополняем Subject:
   `[PATCH 1/3] commit message` до
   `[PATCH 1/3] commit message | project_name | ag/555/branch_name`

4. Вместо `git-format-patch` рекомендуется использовать утилиту
   `pk-git-format-patch` из комплекта `pk-git-utils`
   http://github.com/logiceditor-com/pk-git-utils

    Утилиты не требуют установки, достаточно добавить `scripts/` оттуда в `PATH`, или
    сделать симлинк в `~/bin`, или иной каталог который уже перечислен в `PATH`:

        mkdir -p ~/bin
        cd ~/projects && git clone http://github.com/logiceditor-com/pk-git-utils
        ln -s ~/projects/pk-git-utils/scripts/pk-git-format-patch ~/bin

    и добавьте в `.bashrc`:

        export PATH=~/bin:$PATH

    Утилита `pk-git-format-patch` является "надстройкой" над стандартным format-patch,
    и форматирует заголовок письма как рекомендовано в данном документе.

### Отправляем на ревью

    git send-email --to 'logiceditor-com-review@googlegroups.com' *.patch

Для новичков — посылать в probation:

    git send-email --to 'logiceditor-com-probation@googlegroups.com' *.patch

*Внимание!* Максимальный размер письма в 4Мб. Будьте аккуратны и не отсылайте
графические ресурсы в бинарном коде на ревью!
Тексты бинарных файлов, а также тексты внешних ресурсов (библиотек) необходимо
заменять на "Здесь был <указать что здесь было>". Например, "Здесь был бинарный файл"
или "Здесь был минифицированный скрипт".

Рекомендуется при подготовке патчсета использовать параметр `--no-binary`
утилиты `pk-git-format-patch` или команды `git format-patch`

Если по какой-либо причине патчсет потерял актуальность, необходимо написать
в ответ на каждый посланный патч письмо том, что патчсет не надо ревьюить.

### Вердикты по ревью

Результатом ревью как правило является список замечаний и резюме, определяющее
дальнейшие работы по патчсету, в виде одного из сокращений:

* **ПП** — патч принимается. Патчсет принят, ветку требуется отдать мантейнеру
  или влить самостоятельно (см. раздел "Ветки").

* **ППЗ БПР** — патч принимается с замечаниями, без повторного ревью. Вносим
  правки по замечаниям *в коммитах, к которым относятся замечания*. Править
  отдельными коммитами допускается только в случае неблокирующего ревью, и
  только если код был влит до ревью; при этом обязательно нужно писать в ответ
  на каждое письмо с таким ошибочным вердиктом: "ревью неблокирующее, правки
  будут сделаны ОПС вместо БПР" (и бить тапком ревьюера за невнимательность).

* **ППЗ БПР У** или **ППЗ БПР, правки утвердить** — патч принимается с
  замечаниями, без повторного ревью, но с обсуждением и утверждением правок.
  Правки в том же треде ответами на замечания.

* **ПП ПОПС** или **ППЗ ПОПС** — патч принимается (с замечаниями), правки
  отдельным патсчетом. Текущую ветку вливаем как есть, правки вносятся
  отдельными коммитам.

* **ПП ОТ** или **ППЗ ОТ** — патч принимается (с замечаниями), правки отдельным
  тикетом. Текущие правки приняты, ставим новый тикет по замечаниями на ревью.

* **ПП ПОПС БПР** — патч принимается, правки отдельным патчсетом без повторного
  ревью. Экзотический вариант, возможный в случае неблокирующего ревью.

* **ГК** — просмотрено поверхностно, патч принимается, аналог ПП для
  сГенерированного Кода.

* **Не принято** — патч не принят, переделываем в отдельной ветке (см. раздел
  "Ветки"). Если один патч не принят, то не принят и весь патчсет, если явно
  не указано обратное. Некоторые непринятые патчи ревьюер может попросить
  исключить   из патчсета (и перевыслать отдельным) -- в этом случае исключённый
  патч не влияет на статус всего патчсета.

В случае отсутствия явного итога ревью по патчу следует переспросить ответом
на соответствующий патч.

Очень желательно не тянуть с отправкой кода на повторное ревью / новое ревью
в режиме ПОПС.

Если в целом по патчсету принят вердикт, не предполагающий его влитие в мастер,
а по какому-то патчу стоит вердикт **ПП. Влить**, то этот патч вливается
в мастер отдельно от патчсета, конечно если это не ломает код.

## Redmine

Алгоритм работы с тикетом:

1. Написать, что отправлено на ревью.

2. Установить соответствущий статус (*On Blocking/Nonblocking Review*) и передать
тикет ревьюеру.

Ревьюер должен отметиться и в тикете и в треде с ревью (в одном из мест можно
написать только результат: принято/не принято,  дублировать в оба места
не нужно) и вернуть тикет автору со статусом *Review Feedback*.

Если ревьюер забыл отметиться в тикете, то автору правок следует самостоятельно
вернуть себе тикет, описав результат прошедшего ревью (достаточно написать
только результат: принято/не принято).

## GitHub

Алгоритм работы с ветками и issues на гитхабе

(Только для тех наших проектах, с открытым исходным которые мы  держим там,
читай lua-nucleo, lua-aplicado и те "не наши" в которые мы активно контибутим
— lua-posix, luarocks etc.)

Тикет на GitHub с одной целью: чтобы оставить в опенсурсном коде
ссылку на тикет рядом с TODO. Если в коде ссылку не оставляем, тикет
не заводим.

Тикет на GitHub обязательно должен быть самодостаточным! Номер в нашем
редмайне — просто как референс. Симметричный тикет в корпоративном редмайне
не обязан быть самодостаточным и может содержать только ссылку на тикет в ГХ.

1. Заводим тикет в "внутреннем" редмайне

2. Заводим парный ему тикет в issues гитхаба, на английском.
   Указываем там номер тикета из пункта 1 в виде #NNN без указания трекера.

3. Идем в корпоративный редмайн и добавляем в тикет ссылку на issue в гитхабе.

# Исправление ошибок (bugfixes)

1. Перед фиксом бага обязательно написание теста, воспроизводящего этот баг.
   Требуется написать тест, воспроиводящий баг в том виде, в котором он был
   зарепорчен. Дополнительно рекомендуется по возможности написать "чистый"
   тест, изолирующий и воспроизводящий первопричину проблемы.

   Тесты не пишутся только в исключительных случаях, с явного разрешения
   проджект-/тимлида

2. Перед тестами на такой баг в коде обязательно пишется комментарии:
   *"Test based on a real bug scenario"* и даётся ссылка на тикет(ы) с багом.

    -- Test based on real bug scenario
    -- http://<redmine_host>/issues/9999

3. На ревью коммиты должны идти в следующем порядке: сначала коммит с фиксом,
   тест к нему должен идти следующим коммитом, и содержать комментарий в
   коммитлоге:

    This test uncover bug fixed by previous commit.

   Такая последовательность требуется, чтобы можно было пользоваться
   `git bisect`.

# Про комментарии в коде (TODO)

Любой комментарий с TODO, обязан содержать ссылку на тикет в редмайне
(проекты на GitHub -- ссылку на issue, подробнее см. раздел про тикеты на GH)

После TODO ставится двоеточие, не дефис.

Относящийся к TODO текст на следующих строках пишется с отступом так,
чтобы он начинался после пробела после двоеточия.

    -- TODO: Roses are red
    --       Violets are blue
    --       I indent TODO like this
    --       And so should you

Комментарии не должны содержать отсылку на автора комментария.

Неверно:

    -- JD: I'll write tests for this later

Верно:

    -- TODO: Write tests
    --       https://github.com/lua-nucleo/lua-nucleo/issues/1000

# Пароли и ключи для доступа к production-подсистемам в коде

1. Пароли хранить в коде строго запрещено.
1. Пароли принимать аргументами комстроки строго запрещено.

Необходимо разделять доступ к паролю (или ключу) и доступ к гиту проекта.

# Общие рекомендации

Не стоит пользоваться заглавными буквами в именах файлов без крайней нужды.

Приводимые в примерах или документации домены, URL, адреса электронной почты
и т.п. должны соответствовать рекомендациям
[http://tools.ietf.org/html/rfc2606](http://tools.ietf.org/html/rfc2606).
IP-адреса должны соответствовать
[http://tools.ietf.org/html/rfc5737](http://tools.ietf.org/html/rfc5737).
