## Использование `Box<T>` для ссылки на данные в куче

Наиболее простой умный указатель - это *box*, чей тип записывается как `Box<T>`. Такие переменные позволяют хранить данные в куче, а не в стеке. То, что остаётся в стеке, является указателем на данные в куче. Обратитесь к Главе 4, чтобы рассмотреть разницу между стеком и кучей.

У Box нет проблем с производительностью, кроме хранения данных в куче вместо стека. Но он также и не имеет множества дополнительных возможностей. Вы будете использовать его чаще всего в следующих ситуациях:

- Когда у вас есть тип, размер которого невозможно определить во время компиляции, а вы хотите использовать значение этого типа в контексте, требующем точного размера.
- Когда у вас есть большой объем данных и вы хотите передать владение, но при этом быть уверенным, что данные не будут скопированы
- Когда вы хотите получить значение во владение и вас интересует только то, что оно относится к типу, реализующему определённый трейт, а не то, является ли оно значением какого-то конкретного типа

Мы продемонстрируем первую ситуацию в разделе ["Реализация рекурсивных типов с помощью Box"](#%D0%92%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5-%D1%80%D0%B5%D0%BA%D1%83%D1%80%D1%81%D0%B8%D0%B2%D0%BD%D1%8B%D1%85-%D1%82%D0%B8%D0%BF%D0%BE%D0%B2-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-Boxes)<!-- ignore -->. Во втором случае, передача владения на большой объем данных может занять много времени, потому что данные копируются через стек. Для повышения производительности в этой ситуации, мы можем хранить большое количество данных в куче с помощью Box. Затем только небольшое количество данных указателя копируется в стеке, в то время как данные, на которые он ссылается, остаются в одном месте кучи. Третий случай известен как *типаж объект* (trait object) и глава 17 посвящает целый раздел ["Использование типаж объектов, которые допускают значения разных типов"]<!-- ignore --> только этой теме. Итак, то, что вы узнаете здесь, вы примените снова в Главе 17!

### Использование `Box<T>` для хранения данных в куче

Прежде чем мы обсудим этот вариант использования `Box<T>`, мы рассмотрим синтаксис и то, как взаимодействовать со значениями, хранящимися в `Box<T>`.

В листинге 15-1 показано, как использовать поле для хранения значения `i32` в куче:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-01/src/main.rs}}
```

<span class="caption">Листинг 15-1: Сохранение значения <code>i32</code> в куче с использованием box</span>

Мы объявляем переменную `b` со значением `Box`, указывающим на число `5`, размещённое в куче. Эта программа выведет `b = 5`; в этом случае мы получаем доступ к данным в box так же, как если бы эти данные находились в стеке. Как и любое другое значение, когда box выйдет из области видимости, как `b` в конце `main`, он будет удалён. Деаллокация происходит как для box ( хранящегося в стеке), так и для данных, на которые он указывает (хранящихся в куче).

Размещать одиночные значения в куче не слишком целесообразно, поэтому вряд ли вы будете часто использовать box'ы таким образом. В большинстве ситуаций более уместно размещать такие значения, как `i32`, в стеке, где они и сохраняются по умолчанию. Давайте рассмотрим ситуацию, когда box позволяет нам определить типы, которые мы не могли бы иметь, если бы у нас не было box.

### Включение рекурсивных типов с помощью Boxes

Значение *рекурсивного типа* может иметь другое значение такого же типа как свой компонент. Рекурсивные типы представляют собой проблему, поскольку во время компиляции Rust должен знать, сколько места занимает тип. Однако вложенность значений рекурсивных типов теоретически может продолжаться бесконечно, поэтому Rust не может определить, сколько места потребуется. Поскольку box имеет известный размер, мы можем включить рекурсивные типы, добавив box в определение рекурсивного типа.

В качестве примера рекурсивного типа рассмотрим *cons list*. Это тип данных, часто встречающийся в функциональных языках программирования. Тип cons list, который мы определим, достаточно прост, за исключением наличия рекурсии; поэтому концепции, заложенные в примере, с которым мы будем работать, пригодятся вам в любой более сложной ситуации, связанной с рекурсивными типами.

#### Больше информации о cons списке

*cons list* - это структура данных из языка программирования Lisp и его диалектов, представляющая собой набор вложенных пар и являющаяся Lisp-версией связного списка. Его название происходит от функции `cons` (сокращение от "construct function") в Lisp, которая формирует пару из двух своих аргументов. Вызывая `cons` для пары, которая состоит из некоторого значения и другой пары, мы можем конструировать списки cons, состоящие из рекурсивных пар.

Вот, пример cons list в виде псевдокода, содержащий список 1, 2, 3, где каждая пара заключена в круглые скобки:

```text
(1, (2, (3, Nil)))
```

Каждый элемент в cons списке содержит два элемента: значение текущего элемента и следующий элемент. Последний элемент в списке содержит только значение называемое `Nil` без следующего элемента. Cons список создаётся путём рекурсивного вызова функции `cons`. Каноничное имя для обозначения базового случая рекурсии - `Nil`. Обратите внимание, что это не то же самое, что понятие “null” или “nil” из главы 6, которая является недействительным или отсутствующим значением.

Cons list не является часто используемой структурой данных в Rust. В большинстве случаев, когда вам нужен список элементов при использовании Rust, лучше использовать `Vec<T>`. Другие, более сложные рекурсивные типы данных *полезны* в определённых ситуациях, но благодаря тому, что в этой главе мы начнём с cons list, мы сможем выяснить, как box позволяет нам определить рекурсивный тип данных без особого напряжения.

Листинг 15-2 содержит объявление перечисления cons списка. Обратите внимание, что этот код не будет компилироваться, потому что тип `List` не имеет известного размера, что мы и продемонстрируем.

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-02/src/main.rs:here}}
```

<span class="caption">Листинг 15-2: Первая попытка определить перечисление в качестве структуры данных cons list, состоящей из <code>i32</code> значений.</span>

> Примечание: В данном примере мы реализуем cons list, который содержит только значения `i32`. Мы могли бы реализовать его с помощью generics, о которых мы говорили в главе 10, чтобы определить тип cons list, который мог бы хранить значения любого типа.

Использование типа `List` для хранения списка `1, 2, 3` будет выглядеть как код в листинге 15-3:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-03/src/main.rs:here}}
```

<span class="caption">Листинг 15-3: Использование перечисления <code>List</code> для хранения списка <code>1, 2, 3</code></span>

Первое значение `Cons` содержит `1` и другой `List`. Это значение `List` является следующим значением `Cons`, которое содержит `2` и другой `List`. Это значение `List` является ещё один значением `Cons`, которое содержит `3` и значение `List`, которое наконец является `Nil`, не рекурсивным вариантом, сигнализирующим об окончании списка.

Если мы попытаемся скомпилировать код в листинге 15-3, мы получим ошибку, показанную в листинге 15-4:

```console
{{#include ../listings/ch15-smart-pointers/listing-15-03/output.txt}}
```

<span class="caption">Листинг 15-4: Ошибка, которую мы получаем при попытке определить рекурсивное перечисление</span>

Ошибка говорит о том, что этот тип "имеет бесконечный размер". Причина в том, что мы определили `List` в форме, которая является рекурсивной: она непосредственно хранит другое значение своего собственного типа. В результате Rust не может определить, сколько места ему нужно для хранения значения `List`. Давайте разберёмся, почему мы получаем эту ошибку. Сначала мы рассмотрим, как Rust решает, сколько места ему нужно для хранения значения нерекурсивного типа.

#### Вычисление размера нерекурсивного типа

Вспомните перечисление `Message` определённое в листинге 6-2, когда обсуждали объявление enum  в главе 6:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-02/src/main.rs:here}}
```

Чтобы определить, сколько памяти выделять под значение `Message`, Rust проходит каждый из вариантов, чтобы увидеть, какой вариант требует наибольшее количество памяти. Rust видит, что для `Message::Quit` не требуется места, `Message::Move` хватает места для хранения двух значений `i32` и т.д. Так как будет использоваться только один вариант, то наибольшее пространство, которое потребуется для значения `Message`, это пространство, которое потребуется для хранения самого большого из вариантов перечисления.

Сравните это с тем, что происходит, когда Rust пытается определить, сколько места необходимо рекурсивному типу, такому как перечисление `List` в листинге 15-2. Компилятор смотрит на вариант `Cons`, который содержит значение типа `i32` и значение типа `List`. Следовательно, `Cons` нужно пространство, равное размеру `i32` плюс размер `List`. Чтобы выяснить, сколько памяти необходимо типу `List`, компилятор смотрит на варианты, начиная с `Cons`. Вариант `Cons` содержит значение типа `i32` и значение типа `List`, и этот процесс продолжается бесконечно, как показано на рисунке 15-1.

 <img alt="Бесконечный список Cons" src="img/trpl15-01.svg" class="center" style="width: 50%;">

<span class="caption">Рисунок 15-1: Бесконечный <code>List</code>, состоящий из нескончаемого числа вариантов <code>Cons</code></span>

#### Использование `Box<T>` для получения рекурсивного типа с известным размером

Поскольку Rust не может определить, сколько места нужно выделить для типов с рекурсивным определением, компилятор выдаёт ошибку с этим полезным предложением:

<!-- manual-regeneration
after doing automatic regeneration, look at listings/ch15-smart-pointers/listing-15-03/output.txt and copy the relevant line
-->

```text
help: insert some indirection (e.g., a `Box`, `Rc`, or `&`) to make `List` representable
  |
2 |     Cons(i32, Box<List>),
  |               ++++    +
```

В данном предложении "перенаправление" означает, что вместо того, чтобы непосредственно хранить само значение, мы должны изменить структуру данных, так чтобы хранить его косвенно - хранить указатель на это значение.

Поскольку `Box<T>` является указателем, Rust всегда знает, сколько места нужно `Box<T>`: размер указателя не меняется в зависимости от объёма данных, на которые он указывает. Это означает, что мы можем поместить `Box<T>` внутрь экземпляра `Cons` вместо значения `List` напрямую. `Box<T>` будет указывать на значение очередного `List`, который будет находиться в куче, а не внутри экземпляра `Cons`. Концептуально у нас все ещё есть список, созданный из списков, содержащих другие списки, но эта реализация теперь больше похожа на размещение элементов рядом друг с другом, а не внутри друг друга.

Мы можем изменить определение перечисления `List` в листинге 15-2 и использование `List` в листинге 15-3 на код из листинга 15-5, который будет компилироваться:

<span class="filename">Файл: src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch15-smart-pointers/listing-15-05/src/main.rs}}
```

<span class="caption">Листинг 15-5: Определение <code>List</code>, которое использует <code>Box&lt;T&gt;</code> для того, чтобы иметь вычисляемый размер</span>

`Cons` требуется объём `i32` плюс место для хранения данных указателя box. `Nil` не хранит никаких значений, поэтому ему нужно меньше места, чем `Cons`. Теперь мы знаем, что любое значение `List` займёт размер `i32` плюс размер данных указателя box. Используя box, мы разорвали бесконечную рекурсивную цепочку, поэтому компилятор может определить размер, необходимый для хранения значения `List`. На рисунке 15-2 показано, как теперь выглядит `Cons`.

 <img alt="Бесконечный список Cons" src="img/trpl15-02.svg" class="center" style="width: 50%;">

<span class="caption">Рисунок 15-2: <code>List</code>, который не является бесконечно большим, потому что <code>Cons</code> хранит <code>Box</code>.</span>

Box-ы обеспечивают только перенаправление и выделение в куче; у них нет никаких других специальных возможностей, подобных тем, которые мы увидим у других типов умных указателей. У них также нет накладных расходов на производительность, которые несут эти специальные возможности, поэтому они могут быть полезны в таких случаях, как cons list, где перенаправление - единственная функция, которая нам нужна. В главе 17 мы также рассмотрим другие случаи использования box.

Тип `Box<T>` является умным указателем, поскольку он реализует трейт `Deref`, который позволяет обрабатывать значения `Box<T>` как ссылки. Когда значение `Box<T>` выходит из области видимости, данные кучи, на которые указывает box, также очищаются благодаря реализации типажа `Drop`. Эти два трейта будут ещё более значимыми для функциональности, предоставляемой другими типами умных указателей, которые мы обсудим в оставшейся части этой главы. Давайте рассмотрим эти два типажа более подробно.


["Использование типаж объектов, которые допускают значения разных типов"]: ch17-02-trait-objects.html#using-trait-objects-that-allow-for-values-of-different-types