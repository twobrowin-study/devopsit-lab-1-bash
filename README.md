# Основы DevOps и DataOps | ЛБ 1 | Работа с Linux с использованием коммандной строки

**Дубровин Е.Н.**

МГТУ им. Н.Э. Баумана, 2024 г.

---

[TOC]

---

## Цель и задачи лабораторной работы

Цель лабораторной работы: Изучить принципы работы bash и git

Задачи лабораторной работы:
1. Создать рабочую директорию и файлы в ней при помощи bash и стандартных утилит Linux
2. Использовать основные утилиты Linux
3. Создать репозиторий git, выполнить коммиты в двух ветках их слияние с решением конфликтов

## Часть 1 | Команды bash и утилиты Linux

### Введение

Основной способ взамодействия с сервером Linux - командная оболочка bash. Умение взаимодействовать с оболочкой является критичным для быстрого и качественного устранения проблем при развёртывания сервисов.

Можно описать взаимодействие с ОС следующей диаграммой:

```
Оболочка (bash)
 v
ОС (Linux)
 |-> приложения: контейнеры и/или сервисы (systemd)
 | |-> конфиги <--- доставка
 | |-> код     <-|
 | |-> логирование/журналирование
 |-> файловая система
 |-> сетевое взаимодействие
 |-> драйверное взаимодействие
```

В настоящей лабораторной работе будет в основном рассмотрена работа с файловой системой (ФС).

Прочие методы взаимодействия основнываются на взаимодействия с ФС поскольку как правило настраиваются при помощи ФС и специализированных утилит.

Одним из важных принципов создания утилит в Linux является т.н. UNIX-way, подход UNIX для создания утилит и программ. Одна утилита должна осущствлять одно действие, но делать это хорошо.

### Работа с директориями и файлами

Для начала работы создадим директорию:

```bash
mkdir "[dirname]"
```

`[dirname]` следует задать соответствующим имени пользователя во внутренней системе МГТУ. Например, это имя пользователя используется при подклчении к WIFI на территории МГТУ им. Н.Э. Баумана.

После создания директории, пользователю не будет возвращён никакой текстовый вывод, однако директория будет создана.

Linux является POSIX-операционной системой, и каждая вызываемая комана возвращает результат своего выполнения в виде целого числа. Например, возврат `0` обозначает нормальное завершение программы, `-1` - ошибочное завершение программы.

Посмотрим результат выполнения команды выше:

```bash
echo $?
```

Далее, следует изменить текущую директорию на созданную нами выше:

```bash
cd "[dirname]"
```

Будем далее называть эту директорию **рабочей директорией**.

При необходимости, можно переместиться выше:

```bash
cd ..
```

Стандартные пути `.` и `..` обозначают текущую директорию и директорию выше.

Создадим новый текстовый файл:

```bash
touch text.txt
```

И проверим содержимое директории:

```bash
ls
```

В результате выполнения команды выше будет выведено название файла `text.txt`.

Для редактирования файла можно воспользоваться утилитой nano, предоставляющей удобный интерфейс редактирования файлов в терминале:

```bash
nano text.txt
```

Вывод консоли будет очищен и на его месте будет отображён редактор nano. Редактор позволет вводом с клавиатуры ввести содержимое файла. Для сохранения и закрытия файла следует использовать сочетания клави `CTRL+O`, `CTRL+Q`.

Введём содержимое файла:

```
test
test1
test2
```

После закрытия редактора nano, текстовый вывод консоли, который был пораждён ранее, будет возвращён на место.

Для просмотра содержимого файла не обязательно каждый раз открывать его в редакторе, можно использовать функцию отображения содержимого файлов:

```bash
cat text.txt
```

Будет выведено содержимого файла.

Вывод более длинных файлов помогут осуществить утилиты:

```bash
more text.txt 
less text.txt 
```

Утилита `more` позволяет вывести файл и начать его просмотр с начала. При этом будет замещать своим вывовдом предыдущий вывод в консоли.

Утилита `less` позволяет вывести файл и начать его просмотр как вверх, так и вниз. При этом не будет замещать предыдущий вывод в консоли.

Для поиска по клчевым словам можно воспользоваться специальной утилитой:

```bash
grep 'test' text.txt
```

Очистим экран при помощи команды:

```bash
clear
```

При этом, история выполненных нами команд будет доступна:

```bash
history
```

### Базовая работа с процессами

Linux для каждой пользовательской сессии терминала подготавливает отдельную терминальную сессию. Уточнить текущую сессию можно при помощи команды:

```bash
tty
```

Текущие запущенные в рамках текущей терминальной сессии можно при помощи команды:

```bash
ps
```

Будет выведены только два текущих процесса: `bash` и `ps`.

Чтобы вывести все процессы, запущенные в ОС следует использовать утилиту:

```bash
pstree
```

Во вне-контейнерных ОС это приведёт к выводу большого числа процессов в иерархичеком виде. Стоит обратить внимание что процессом, пораждающим все остальные, в современных дистрибутивах ОС Linux является systemd.

Также можно ограничить вывод при помощи использования pipe передачи данных в bash:

```bash
pstree | more
pstree | less
```

Этот тип передачи данных позволяет перенаправить stdout (стандартного вывода) результатов выполнения одной утилиты в качестве stdin (стандартного ввода) следующей утилиты.

Также можно передавать результат работы (стандарты вывод) в файл:

```bash
echo test > text.txt
echo test >> text.txt
```

Первый и второй вариант отличаются тем, что `>` перезапишет файл, а `>>` добавит новую строчку в конец файла.

При помощи `<` можно передавать файлы в стандартный ввод программ.

Язык bash содержат также основные команды ветвления и циклов, например, в консоли в одной строке можно задать выражение, которое будет в цыкле выводить счёт от 1 до 10 с ожиданием в 1 секунду:

```bash
for i in {1..10}; do sleep $i; echo $i; done
```

Виртуальный терминал позволяет создавать процессы на фоне, однако их вывод будет выводиться в этот же терминал.

Например, результат выполнения команды ниже будет мешать выполнять операции далее, но при их вводе они будут работать и выдавать свой результат:

```bash
for i in {1..10}; do sleep $i; echo $i; done &
```

Как видно, что для отправки процесса в фоновый режим, достаточно добавить амперсант в конце строки.

Для возврата к процессу сохранённому на фоне следует использовать утилиту:

```bash
fg
```

Эта команда передаст управление из текущей сессии терминала, в запущенный на фоне процесс. В том числе, это позволит завершить его при помощи сочетания клавич `CTRL+C`.

Однако, не следует путать отправку выполнения процесса на фон и отделение его от текущей сессии терминала.

В первом случае, при закрытии сессии терминала (закрытие терминального клиента, отключение от сессии ssh) процесс будет завершён.

Для отвязывания исполнения процесса от текущей терминальной сессии используются клиенты оконного режима: `screen/tmux`:

```bash
screen
```

Это переведёт в новое окно screen, содержащее подсказки об использовании приложения.

Следует нажать `Enter` и запустить скрипт подсчёта ещё раз.

Далее, при помощи сочетания клавишь `CTRL+a d` (в начале выполняется `CTRL+a` для перехода в режим передачи команд и далее, `d` для выполнения действия) можно покинуть текущую сессию screen.

Процесс останется в рабочем состоянии, при помощи команды `screen -x` можно подключиться обратно.

### Получение помощи по использованию утилит

Большинство утилит поставляются вместе с man-страницами (сокращение от слова manual, руководство пользователя) на разных языках, которые в подробностях рассказывают о назначении и возможных применениях утилит.

```bash
man screen
```

Также больщинство утилит имеют краткую помощь, доступную при передаче флага `--help`:

```bash
screen --help
```

Флаги (задётся при помощи одного слова после `--` или одной буквы после `-`) могут весьма значительно влиять на поведение утилит.

Например, указание флага `-l` для утилиты `ls` позволит увидеть владельцов файлов, размеры файлов и даты их создания.

### Создание скриптов

Скриптом (или сценарием) называется файл, содержащий команды bash. Такие файлы можно сохранить и выполнять повторно.

Создадим файл `nano script.sh` со следующим содержимым:

```bash
#!/bin/bash

for i in {1..10}
do
    echo $i
done
```

Первая строка файла является указанием на используемый интерпретатор (нам это потребуется далее), остальные - циклом, который мы описывали выше, но в развёрнутом виде.

Скрипт можно выполнить при помощи запуска непосредственно в интерпретаторе:

```bash
bash script.sh
```

Однако, такой способ не удобен для написания собственных утилит (~60% утилит Linux на самом делеявляются скриптами). Для решения этой проблемы мы написали используемый интерпретатор в начале файла, однако на данный момент воспользоваться им невозможно, файл не доступен для запуска.

Доступ на запуск файла является частью системы Linux по управлению правами. Она состоит из 3 групп прав и 3 видов прав.

```
Владелец | Группа владельцев | Прочие
   rwx            rwx           rwx
```

Владелец, это тот пользователь, который владеет файлом, для его изменения можно воспользоваться функцией (не следует это делать в рамках проведения ЛБ):

```bash
chown "[user]" script.sh
```

А узнать текущее имя пользователя можно при помощи утилиты:

```bash
whoami
```

Группа владельцец определяется схожим образу владельцев, но это группа Linux, в которую могжет входить множество пользователей.

Прочие - все пользователи, не попадающие под две предыдущие категории.

Права задаются при помощи следующего формата:
* `r` - число 4 - право на чтение файла
* `w` - число 2 - право на запись в файл
* `x` - число 1 - право на исполнение файла

Каждое из соответсвий чисел `rwx` позволяют в стиле бинарного файла определить его цифровое значение.

Например, право `r`, `w` будет иметь число 6, а `r`, `w`, `x` - 7.

Три таких числа будут задавать полные права на файл.

Чаще всего встречаются сочетания 644, 664, 744.

Изменить права на файл можно следующим образом:

```bash
chmod +x script.sh
```

Выполенение такой команды даст право всем пользователям исполнять этот файл.

Утилита `chmod` также принимает формат цифровой задачи прав.

Теперь мы можем вызвать скрит из текущей директории:

```bash
./script.sh
```

### Работа с переменными и переменными окружения

Язык bash предполагает задание переменных без пробела:

```bash
a=5
b=6
```

Эти переменные можно использовать в выражаениях bash в текущем терминале:

```
echo $a+$b
echo $(($a+$b))
```

Две строчки выше отличаются интерпретацией переменных.

Первая строчка - стандартный режим bash, который расценивает содержимое любых переменных как текст.

Вторая строчка - математический режим bash, где числа преобразуются в целые, в результате доступны математические функции.

Изменим текст `script.sh`:

```bash
#!/bin/bash

for i in {1..10}
do
    echo ${a}+${b}
done
```

Выполнив команду `./script.sh ` вместо значения переменных мы получим пустые строчки, поскольку незаданные переменные в bash интерпретируются как пустые строки и не приводят к ошибкам.

Однако, если мы выполним скрипт в одном из форматов ниже:

```bash
. script.sh
# или
source script.sh
```

Получим ожидаемый результат.

Команда выполнения скрипта в текущей директории (`source`, можно сократить до указания текущей директории) выполнит скрипт без запуска дополнительного процесса bash, в рамках текущего процесса, что и даст доступ к переменным.

Однако, можно дать доступ к переменным из-вне, экспеортировав эти переменные в переменные окружения:

```bash
export a=5
export b=6
```

Теперь выполнение `./script.sh ` покажет ождаемый результат.

Все текущие переменные окружения можно получить при помощи команды:

```bash
env
```

### Базовое сетевое взаимодействие

Для получения настроек сетевых интерфейсов следует использовать утилиту:

```bash
ip addr show
```

Для выполнения пинга (т.е. проверки доступности) удалённого сервиса следует использовать утиилиту:

```bash
ping ya.ru
```

Чтобы узнать ip адреса, соответствющие доменному имени, следует использовать утилиту:

```bash
host ya.ru
```

Чтобы выполнить запрос к удалённому серсиву, следует использовать утилиту:

```bash
curl ya.ru
```

### Задание 1 | Команды bash и утилиты Linux

Следует описать назначение и использование команды bash или утилиты Linux по варианту из таблицы ниже:

| №  | Утилита | | №  | Утилита    | | №  | Утилита  |
| -- | ------- |-| -- | ---------- |-| -- | -------- |
|  1 | mkdir   | | 10 | ps         | | 19 | tty      |
|  2 | echo    | | 11 | pstree     | | 20 | screen   |
|  3 | touch   | | 12 | man        | | 21 | chmod    |
|  4 | cat     | | 13 | ls         | | 22 | chown    |
|  5 | nano    | | 14 | cd         | | 23 | whoami   |
|  6 | sleep   | | 15 | ping       | | 24 | env      |
|  7 | clear   | | 16 | host       | | 25 | export   |
|  8 | less    | | 17 | source     | | 26 | curl     |
|  9 | more    | | 18 | fg         | | 27 | grep     |

### Задание 2 | Автоматизация создания директорий и файлов

Задание:
* Cоздать 100 директорий `foo00..foo99`
* В каждой из директорий создать 100 текстовых файлов `bar00..bar99`
* В каждом файле написать 100 строк `test00..test99`

В отчёте следует указать итоговый скрипт создания файлов и добавить текстовое описание его работы.

## Часть 2 | Система контроля версий Git

### Описание и начало работы

Система контроля версия Git предназначена для обеспечения сохранения истории изменений текстовых файлов и их синхронизацию с удалёнными хранилищами.

Для начала работы следует установить git:

```bash
sudo apt install git
```

Далее, в рабочей директории следует инициализировать git репозиторий:

```bash
git init
```

Это создаст директорию `.git`, которая будет содержать системные файлы репозитория.

Репозиторий состоит из рабочей директории (актуальной версии файлов, с которой работает пользователь) и системной директории `.git`.

Для рассматриваемого примера:
```
Рабочая директория:
  |
  |--> script.sh --|--> Рабочая директория
  |--> text.txt  --|
  |--> .git      -----> Директория с системными файлами
```

В эти файлы входят:
* Объекты хранимых файлов
* Дельты разницы с предыдущими версиями файлов
* Указатели на текущее состояние рабочей директории 

Принип работы git основывается на использовании утилиты diff, которая определяет разницу между 2 или большим числом файлов на основе переносов строк (символов `\n`).

Именно по этой причине в git не рекомендуется хранить бинарные файлы. Бинарные файлы не содержат переносы строк и поэтому при внесении любого изменения новая версия будет целиком перезаписывать новую.

Например, docx файл будет целиком изменяться при каждом сохранении (даже если не было произведено модификации файла), поскольку в бинарном виде содержит запись о дате и времени последнего сохранения.

Однако, у каждого правила бывают исключения. В репозитории, использованном для хранения этих методических указаний, хранится бинарный файл `git-cheat-sheet.png`. Этот файл был изменён лишь единижды, при его создании, и используется для вставки ниже в текст методических указаний.

### Линейная работа в git

После создания репозитория можно сразу же зафиксировать текущее состояние рабочей директории в индексе git:

```bash
git add .
```

Данная команда приведёт к расчёту изменений текущей рабочей директории относительно предыдущего коммита.

Поскольку у нас нет предыдущего коммита, это изменение будет пустым.

Этот расчёт не приведёт к фиксации изменений в репозитории (комиту).

Чтобы создать коммит следует ввести команду:

```bash
git commit
```

Эта команда откроет выбранный текстовый редактор для редактирования файла, содержащего коммит-сообщение (**Это первый способ создать коммит сообщение**).

Коммит сообщение - это текстовое описание внесённых изменений, предназначенное для человека. Эти сообщения выводятся при просмотре истории изменения репозитория.

Однако, если git запускается в первый раз и не был настроен, то будет выведно сообщение о необходимости настройки git:

```bash
git config --global user.name "[имя]"
git config --global user.email "[адрес электронной почты]"
```

Команды выше установят имя пользоватля git (предполагается использовать реальное имя) и его адрес электронной почты. 

Для работы во многих системах, использующих git (GitHub, GitLab), следует помнить - связывание автора изменения и пользователя системы, сделавших их, выполняется на основе адреса электронной почты. Следует везде использовать одинаковый адрес электронной почты. В системах GitHub и GitLab можно указывать несколько адресов электронной почты.

Также коммит сообщение можно создать без открытия редактора (**Это второй способ создать коммит сообщение**):

```bash
git commit -m "[коммит сообщение]"
```

После создания первого коммита, выполним команду для просмотра истории изменений:

```bash
git log
```

Вывод команды просмотра будет иметь вид:

```
commit 3d93a9b3a3a37e1ff2161c4153f5665ea7874bc0
Author: Egor Dubrovin <dubrovin.en@ya.ru>
Date:   Fri Sep 6 19:50:14 2024 +0300

    Первый коммит
```

Где `3d93a9b3a3a37e1ff2161c4153f5665ea7874bc0` - sha256 хеш-сумма коммита, она является уникальным идентификатором сохранённого состояния рабочей директории для этого коммита.

К каждому коммиту можно вернуться при помощи команды:

```bash
git checkout "[хеш-сумма коммита]"
```

Изменим файл `text.txt`, пусть его содержимое теперь будет:
```
test
test1
test2
test3
```

Как видно, была добавлена строка `test3`. Отличие текущего состояния файла и последнего сохарённого коммита можно выполнить при помощи функции:

```bash
git diff
```

Зафиксируем и эти изменения:

```bash
git add .
git commit -m "Второй коммит"
```

### Типовая последовательность коммита модифицированных файлов

Типовая последовательность коммита модифицированных файлов:
1. Добавить в индекс все файлы
2. Выполнить коммит изменений

### Нелинейная работа в git

Нелинейная работа в git предназначена для работы в команде и упрощения совместного управления репозиторием.

Главная возможность нелинейной работы в git - использование ответвлений в репозиториях.

Общая схема следущая:
* Для синхронизации работы используется единый сервер хранения репозиториев (например, GitHub или GitLab)
* Несколько разработчиков работают в разных ветках
* Слияние веток производится в едином сервере хранения репозитория
* Следует стараться избегать конфликтов в ветах, однако механизмы git позволяют их удобно разрешать в ручном режиме

Текущие ветки репозитория можно получить при помощи команды:
```bash
git branch
```

Это выведет текущую единственную ветку `main` - создаваемую по-умолчанию при создании репозитория.

Сделаем ответвление от текущей ветки:

```bash
git checkout -b test
```

Эта команда создаст и переключится в ветку `test` (аналогично команде возврата к коммиту по хеш-сумме).

Изменим файл `text.txt` в новой ветке:

```
test
test1
testj
test3
```

Выполним коммит внесённых изменений.

Вернёмся в ветку `main`:

```bash
git checkout main
```

В рабочей директории будет возвращено состояние, соотвтствующее предыдущему коммиту.

При этом будет переключён указатель `HEAD`, предназначенный для сохранения текущего коммита, которому соответствует рабочая директория.

Выполним симулацию "плохой" совместной работы в git, внесём изменения в ветке `main`, которые пораждают конфликт с веткой `test`.

Файл `text.txt` должен содержать строчки:

```
test
test1
testi
test3
```

Как видно, третьи строчки файла не позволят в автоматическом режиме без вмешательства объединить эти файлы в один.

Выполним попытку слияния веток:

```bash
git merge test
```

Команда выше попытается вполнить автоматическое слияние ветки test в ветку main так чтобы получить единую историю коммитов.

Однако, в связи с внесёнными изменениями, это окажется невозможным, в результате git предложит исправить конфликты в ручном режиме.

Исправление следует выполнить в рабочей директории и затем выполнить коммит результата. Файл `text.txt` будет иметь вид:

```
test
test1
<<<<<<< HEAD
testi
=======
testj
>>>>>>> test
```

Верхняя строчка, обозначенная как `<<<<<<< HEAD` и до `=======` соответствует той версии файла, что была в ветке main (текущее состояние).

Нижняя строчка, обозначенная как `>>>>>>> test` и после `=======` соответствует версии файла из ветки test (приходящие изменения).

Следует выбрать одну из строк, удалить лишную строку, а также обозначения `<<<<<<< HEAD`, `=======`, `>>>>>>> test`, относящиеся к разрещению конфликта слияния.

После выполнения коммита будет сохранен коммит, выполняющий слияние двух веток вместе.

### Шпаргалка по всем командам git

![Шпаргалка по всем командам git](git-cheat-sheet.png)

### Задание 3 | Система контроля версий Git

Следует привести команды для операций типовой последовательности коммита модифицированных файлов.

Следует описать способы создания коммит-сообщения.

## Содержание отчёта

1. [`Цель и задачи лабораторной работы`](#цель-и-задачи-лабораторной-работы)

2. [`Задание 1 | Команды bash и утилиты Linux`](#задание-1--команды-bash-и-утилиты-linux)

3. [`Задание 2 | Автоматизация создания директорий и файлов`](#задание-2--автоматизация-создания-директорий-и-файлов)

4. [`Задание 3 | Система контроля версий Git`](#задание-3--система-контроля-версий-git)

