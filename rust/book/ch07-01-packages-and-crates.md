## Пакеты и крейты

Первые части модульной системы, которые мы рассмотрим — это пакеты и крейты.

*Крейт* — это наименьший объем кода, который компилятор Rust рассматривает за раз. Даже если вы запустите `rustc` вместо `cargo` и передадите один файл с исходным кодом (как мы уже делали в разделе «Написание и запуск программы на Rust» Главы 1), компилятор считает этот файл крейтом. Крейты могут содержать модули, и модули могут быть определены в других файлах, которые компилируются вместе с крейтом, как мы увидим в следующих разделах.

Крейт может быть одним из двух видов: бинарный крейт или библиотечный крейт. *Бинарные крейты* — это программы, которые вы можете скомпилировать в исполняемые файлы, которые вы можете запускать, например программу командной строки или сервер. У каждого бинарного крейта должна быть функция с именем `main`, которая определяет, что происходит при запуске исполняемого файла. Все крейты, которые мы создали до сих пор, были бинарными крейтами.

*Библиотечные крейты* не имеют функции `main` и не компилируются в исполняемый файл. Вместо этого они определяют функциональность, предназначенную для совместного использования другими проектами. Например, крейт `rand`, который мы использовали в [Главе 2]<!-- ignore --> обеспечивает функциональность, которая генерирует случайные числа. В большинстве случаев, когда Rustaceans говорят «крейт», они имеют в виду библиотечный крейт, и они используют «крейт» взаимозаменяемо с общей концепцией программирования «библиотека».

*Корневой модуль крейта* — это исходный файл, из которого компилятор Rust начинает собирать корневой модуль вашего крейта (мы подробно объясним модули в разделе [«Определение модулей для контроля видимости и закрытости»]<!-- ignore -->).

*Пакет* — это набор из одного или нескольких крейтов, предоставляющий набор функциональности. Пакет содержит файл *Cargo.toml*, в котором описывается, как собирать эти крейты. На самом деле Cargo — это пакет, содержащий бинарный крейт для инструмента командной строки, который вы использовали для создания своего кода. Пакет Cargo также содержит библиотечный крейт, от которого зависит бинарный крейт. Другие проекты тоже могут зависеть от библиотечного крейта Cargo, чтобы использовать ту же логику, что и инструмент командной строки Cargo.

Пакет может содержать сколько угодно бинарных крейтов, но не более одного библиотечного крейта. Пакет должен содержать хотя бы один крейт, библиотечный или бинарный.

Давайте пройдёмся по тому, что происходит, когда мы создаём пакет. Сначала введём команду `cargo new`:

```console
$ cargo new my-project
     Created binary (application) `my-project` package
$ ls my-project
Cargo.toml
src
$ ls my-project/src
main.rs
```

После того, как мы запустили `cargo new`, мы используем `ls`, чтобы увидеть, что создал Cargo. В каталоге проекта есть файл *Cargo.toml*, дающий нам пакет. Также есть каталог *src*, содержащий *main.rs*. Откройте *Cargo.toml* в текстовом редакторе и обратите внимание, что в нём нет упоминаний о *src/main.rs*. Cargo следует соглашению о том, что *src/main.rs* — это корневой модуль бинарного крейта с тем же именем, что и у пакета. Точно так же Cargo знает, что если каталог пакета содержит *src/lib.rs*, пакет содержит библиотечный крейт с тем же именем, что и пакет, а *src/lib.rs* является корневым модулем этого крейта. Cargo передаёт файлы корневого модуля крейта в `rustc` для сборки библиотечного или бинарного крейта.

Здесь у нас есть пакет, который содержит только *src/main.rs*, что означает, что он содержит только бинарный крейт с именем `my-project`. Если пакет содержит *src/main.rs* и *src/lib.rs*, он имеет два крейта: бинарный и библиотечный, оба с тем же именем, что и пакет. Пакет может иметь несколько бинарных крейтов, помещая их файлы в каталог *src/bin*: каждый файл будет отдельным бинарным крейтом.


[«Определение модулей для контроля видимости и закрытости»]: ch07-02-defining-modules-to-control-scope-and-privacy.html
[Главе 2]: ch02-00-guessing-game-tutorial.html#generating-a-random-number