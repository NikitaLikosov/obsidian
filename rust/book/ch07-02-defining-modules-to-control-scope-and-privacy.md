## Определение модулей для контроля видимости и закрытости

В этом разделе мы поговорим о модулях и других частях системы модулей, а именно: *путях* (paths), которые позволяют именовать элементы; ключевом слове `use`, которое приносит путь в область видимости; ключевом слове `pub`, которое делает элементы общедоступными. Мы также обсудим ключевое слово `as`, внешние пакеты и оператор glob. А пока давайте сосредоточимся на модулях!

Во-первых, мы начнём со списка правил, чтобы вам было легче ориентироваться при организации кода в будущем. Затем мы подробно объясним каждое из правил.

### Шпаргалка по модулям

Здесь мы даём краткий обзор того, как модули, пути, ключевое слово `use` и ключевое слово `pub` работают в компиляторе и как большинство разработчиков организуют свой код. В этой главе мы рассмотрим примеры каждого из этих правил, и это удобный момент чтобы напомнить о том, как работают модули.

- **Начнём с корня крейта**: при компиляции компилятор сначала ищет корневой модуль крейта (обычно это *src/lib.rs* для библиотечного крейта или *src/main.rs* для бинарного крейта) для компиляции кода.
- **Объявление модулей**: В файле корневого модуля крейта вы можете объявить новые модули; скажем, вы объявляете модуль “garden” с помощью `mod garden;`. Компилятор будет искать код модуля в следующих местах:
    - в этом же файле, между фигурных скобок, которые заменяют точку с запятой после `mod garden`
    - в файле *src/garden.rs*
    - в файле *src/garden/mod.rs*
- **Объявление подмодулей**: В любом файле, кроме корневого модуля крейта, вы можете объявить подмодули. К примеру, вы можете объявить  `mod vegetables;` в *src/garden.rs*. Компилятор будет искать код подмодуля в каталоге с именем родительского модуля в следующих местах:
    - в этом же файле, сразу после `mod vegetables`, между фигурных скобок, которые заменяют точку с запятой
    - в файле *src/garden/vegetables.rs*
    - в файле *src/garden/vegetables/mod.rs*
- **Пути к коду в модулях**: После того, как модуль станет частью вашего крейта и если допускают правила приватности, вы можете ссылаться на код в этом модуле из любого места вашего крейта, используя путь к коду. Например, тип `Asparagus`, в подмодуле vegetables модуля garden, будет найден по пути `crate::garden::vegetables::Asparagus`.
- **Скрытие или общедоступность**: Код в модуле по умолчанию скрыт от родительского модуля. Чтобы сделать модуль общедоступным, объявите его как `pub mod` вместо `mod`. Чтобы сделать элементы общедоступного модуля тоже общедоступными, используйте `pub` перед их объявлением.
- **Ключевое слово `use`**: Внутри области видимости использование ключевого слова `use` создаёт псевдонимы для элементов, чтобы уменьшить повторение длинных путей. В любой области видимости, в которой может обращаться к `crate::garden::vegetables::Asparagus`, вы можете создать псевдоним `use crate::garden::vegetables::Asparagus;` и после этого вам нужно просто писать `Asparagus`, чтобы использовать этот тип в этой области видимости.

Мы создали бинарный крейт `backyard`, который иллюстрирует эти правила. Директория крейта, также названная как `backyard`, содержит следующие файлы и директории:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

Файл корневого модуля крейта в нашем случае  *src/main.rs*, и его содержимое:

<span class="filename">Файл: src/main.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/main.rs}}
```

Строка `pub mod garden;` говорит компилятору о подключении кода, найденном в *src/garden.rs*:

<span class="filename">Файл: src/garden.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden.rs}}
```

А здесь `pub mod vegetables;` указывает на подключаемый код в *src/garden/vegetables.rs*. Этот код:

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden/vegetables.rs}}
```

Теперь давайте рассмотрим детали этих правил и продемонстрируем их в действии!

### Группировка связанного кода в модулях

*Модули* позволяют упорядочивать код внутри крейта для удобочитаемости и лёгкого повторного использования. Модули также позволяют нам управлять *приватностью* элементов, поскольку код внутри модуля по умолчанию является закрытым. Частные элементы — это внутренние детали реализации, недоступные для внешнего использования. Мы можем сделать модули и элементы внутри них общедоступными, что позволит внешнему коду использовать их и зависеть от них.

В качестве примера, давайте напишем библиотечный крейт предоставляющий функциональность ресторана. Мы определим сигнатуры функций, но оставим их тела пустыми, чтобы сосредоточиться на организации кода, вместо реализации кода для ресторана.

В ресторанной индустрии некоторые части ресторана называются *фронтом дома*, а другие *задней частью дома*. Фронт дома это там где находятся клиенты; здесь размещаются места клиентов, официанты принимают заказы и оплаты, а бармены делают напитки. Задняя часть дома это где шеф-повара и повара работают на кухне,  работают посудомоечные машины, а менеджеры занимаются административной деятельностью.

Чтобы структурировать крейт аналогично тому, как работает настоящий ресторан, можно организовать размещение функций во вложенных модулях. Создадим новую библиотеку (библиотечный крейт) с именем `restaurant` выполнив команду `cargo new restaurant --lib`; затем вставим код из листинга 7-1 в *src/lib.rs* для определения некоторых модулей и сигнатур функций. Это секция фронта дома:

<span class="filename">Файл: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-01/src/lib.rs}}
```

<span class="caption">Листинг 7-1: Модуль <code>front_of_house</code> , содержащий другие модули, которые в свою очередь содержат функции</span>

Мы определяем модуль, начиная с ключевого слова  `mod`, затем определяем название модуля (в данном случае `front_of_house`) и размещаем фигурные скобки вокруг тела модуля. Внутри модулей, можно иметь другие модули, как в случае с модулями `hosting` и `serving`. Модули также могут содержать определения для других элементов, таких как структуры, перечисления, константы, типажи или — как в листинге 7-1 — функции.

Используя модули, мы можем сгруппировать связанные определения вместе и сказать почему они являются связанными. Программистам будет легче найти необходимую функциональность в сгруппированном коде, вместо того чтобы искать её в одном общем списке. Программисты, добавляющие новые функции в этот код, будут знать, где разместить код для поддержания порядка в программе.

Как мы упоминали ранее, файлы *src/main.rs* и *src/lib.rs* называются *корневыми модулями крейта*. Причина такого именования в том, что содержимое любого из этих двух файлов образует модуль с именем `crate` в корне структуры модулей крейта, известной как *дерево модулей*.

В листинге 7-2 показано дерево модулей для структуры модулей, приведённой в коде листинга 7-1.

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

<span class="caption">Листинг 7-2: Древо модулей для программы из Листинга 7-1</span>

Это дерево показывает, как некоторые из модулей вкладываются друг в друга; например, `hosting` находится внутри `front_of_house`. Дерево также показывает, что некоторые модули являются  *братьями* (siblings) друг для друга, то есть они определены в одном модуле; `hosting` и `serving` это братья которые определены внутри `front_of_house`. Если модуль A содержится внутри модуля B, мы говорим, что модуль A является *потомком* (child) модуля B, а модуль B является *родителем* (parent) модуля A. Обратите внимание, что родителем всего дерева модулей является неявный модуль с именем `crate`.

Дерево модулей может напомнить вам дерево каталогов файловой системы на компьютере; это очень удачное сравнение! По аналогии с каталогами в файловой системе, мы используется модули для организации кода. И так же, как нам надо искать файлы в каталогах на компьютере, нам требуется способ поиска нужных модулей.