# Домашнее задание к занятию "Пакеты и циклы"

## Цель задания

1. Научиться использовать циклы в программах
2. Уметь параметризировать юнит-тесты

------

## Инструкция к заданию

1. Скачайте и установите профессиональный редактор кода [Intellij Idea Community Version](https://www.jetbrains.com/idea/download/)
1. Откройте IDEA и [создайте и настройте новый Maven-проект](QA_Maven_Idea_Create.md) (под каждую задачу следует создавать отдельный проект, если обратное не сказано в условии)
2. Создайте пустой репозиторий на GitHub и свяжите его с папкой вашего проекта (не с какой-либо другой папкой).
3. Правильно настройте репозиторий в плане `.gitignore`. Проигнорируйте папки `.idea` и `target` (раньше была `out`) и `.iml`-файл - их в репозитории быть не должно.
4. Выполните в IDEA требуемую задачу согласно условию.
5. Проверьте соблюдение [правил форматирования кода](QA_Java_Idea_Format.md)
6. Убедитесь что при запуске `mvn clean test` все тесты запускаются, проходят, а сборка завершается успешно
7. Закоммитьте и отправьте в репозиторий содержимое папки проекта.

------

## Материалы, которые пригодятся для выполнения задания

1. [Как создать Maven-проект в IDEA?](QA_Maven_Idea_Create.md)
1. [Как отформатировать код в Java?](QA_Java_Idea_Format.md)

------

## Задание 1 (обязательное)

Один фрилансер, воспользовавшись гибкостью графика своего вида работы, решил смоделировать свой отдых по следующей схеме:
* Изначально у него нет денег;
* Если он решает, что следующий месяц хочет активно поработать, то его деньги увеличиваются на `income` (доход от работы) и уменьшаются на `expense` (обязательные месячные траты);
* Если он решает, что следующий месяц хочет отдохнуть, то за месяц его доходы уменьшаются на `expense`, а затем остаток накоплений ещё уменьшается в три раза (траты на отдых);
* Решение отдохнуть ли в следующем месяце принимается по правилу: если на счету есть хотя бы `threshold` денег, то выбираем отдых, иначе - работу.

Вам нужно написать сервисный класс с методом, который по заданным `income`, `expenses` и `threshold` расчитает количество месяцев отдыха, которые будут в следующий год. Всё считается в целых числах.

Например, если `income` 10 тыс. руб., `expenses` 3 тыс. руб., `threshold` 20 тыс. руб., то отдохнуть удастся три месяца в году согласно следующей эмуляции планируемого поведения:
```text
Месяц 1. Денег 0. Придётся работать. Заработал +10000, потратил -3000
Месяц 2. Денег 7000. Придётся работать. Заработал +10000, потратил -3000
Месяц 3. Денег 14000. Придётся работать. Заработал +10000, потратил -3000
Месяц 4. Денег 21000. Буду отдыхать. Потратил -3000, затем ещё -12000
Месяц 5. Денег 6000. Придётся работать. Заработал +10000, потратил -3000
Месяц 6. Денег 13000. Придётся работать. Заработал +10000, потратил -3000
Месяц 7. Денег 20000. Буду отдыхать. Потратил -3000, затем ещё -11334
Месяц 8. Денег 5666. Придётся работать. Заработал +10000, потратил -3000
Месяц 9. Денег 12666. Придётся работать. Заработал +10000, потратил -3000
Месяц 10. Денег 19666. Придётся работать. Заработал +10000, потратил -3000
Месяц 11. Денег 26666. Буду отдыхать. Потратил -3000, затем ещё -15778
Месяц 12. Денег 7888. Придётся работать. Заработал +10000, потратил -3000
```

А на примере `income` 100 тыс. руб., `expenses` 60 тыс. руб., `threshold` 150 тыс. руб. ответ будет 2:
```text
Месяц 1. Денег 0. Придётся работать. Заработал +100000, потратил -60000
Месяц 2. Денег 40000. Придётся работать. Заработал +100000, потратил -60000
Месяц 3. Денег 80000. Придётся работать. Заработал +100000, потратил -60000
Месяц 4. Денег 120000. Придётся работать. Заработал +100000, потратил -60000
Месяц 5. Денег 160000. Буду отдыхать. Потратил -60000, затем ещё -66667
Месяц 6. Денег 33333. Придётся работать. Заработал +100000, потратил -60000
Месяц 7. Денег 73333. Придётся работать. Заработал +100000, потратил -60000
Месяц 8. Денег 113333. Придётся работать. Заработал +100000, потратил -60000
Месяц 9. Денег 153333. Буду отдыхать. Потратил -60000, затем ещё -62222
Месяц 10. Денег 31111. Придётся работать. Заработал +100000, потратил -60000
Месяц 11. Денег 71111. Придётся работать. Заработал +100000, потратил -60000
Месяц 12. Денег 111111. Придётся работать. Заработал +100000, потратил -60000
```

При реализации вашего сервисного метода можете опираться на пример реализации другого сервиса, считающего сколько месяцев в году отдыхал работник, который в месяц отдыха теряет `expenses` денег, а в месяц работы получает `income` денег, а отдыхает каждый раз, когда денег хватает на следующий месяц:

```java
    public int calculate(int income, int expenses) {
        int count = 0; // счётчик месяцев отдыха
        int money = 0; // количество денег на счету
        for (int month = 0; month < 12; month++) {
            if (money >= expenses) { // можем ли отдыхать?
                count++; // увеличиваем счётчик месяцев отдыха
                money = money - expenses;
            } else {
                money = money + income;
            }
        }
        return count;
    }
```

Протестируйте ваш сервис на примерах из условия.

------

## Задание 2 (НЕобязательное)

:warning: Эта задача делается на основе первой задачи в том же репозитории.

Переделайте тесты на параметризированные на основе CSV-файлов к предыдущей задаче.

------

## Правила приема работы

Прикреплена одна ссылка на первую или обе задачи сразу.

------

## Критерии оценки

1. В каждом репозитории размещено содержимое папки проекта IDEA. Корнем репозитория должна именно папка проекта - не папка `src`, не папка внутри которой лежит папка проекта; т.о. в корне репозитория должна лежать сразу папка `src`.
1. Есть файл `.gitignore`, игнорирующий ненужные файлы и папки, которые должны отсутствовать в репозитории. Если они присутствуют - их нужно оттуда удалить.
1. Программа соответствует всем требованиям из условия задачи
1. Программа использует только те инструменты языка, которые мы проходили или которые прямо разрешены условием задачи
1. Программа работает правильно на всех примерах из условия
1. Программный код отформатирован и соответствует пройденным требованиям к качеству кода
1. При запуске `mvn clean test` тесты запускаются и проходят, а сборка завершается успешно
1. Программа спроектирована достаточно логично и правильно, не противоречит общепринятым в производстве практикам и традициям
1. При наличии недочётов, в зависимости от их серьёзности и количества, работа может быть отправлена на доработку или принята; решение принимается на основе экспертной оценки работы.