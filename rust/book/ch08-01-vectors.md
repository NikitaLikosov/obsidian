## Хранение списков значений в векторах

Первым типом коллекции, который мы разберём, будет `Vec<T>`, также известный как <em>вектор</em> (vector). Векторы позволяют хранить более одного значения в единой структуре данных, хранящей элементы в памяти один за другим. Векторы могут хранить данные только одного типа. Их удобно использовать, когда нужно хранить список элементов, например, список текстовых строк из файла, или список цен товаров в корзине покупок.

### Создание нового вектора

Чтобы создать новый пустой вектор, мы вызываем функцию `Vec::new`, как показано в листинге 8-1.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-01/src/main.rs:here}}
```

<span class="caption">Листинг 8-1: Создание нового пустого вектора для хранения значений типа <code>i32</code></span>

Обратите внимание, что здесь мы добавили аннотацию типа. Поскольку мы не вставляем никаких значений в этот вектор, Rust не знает, какие элементы мы собираемся хранить. Это важный момент. Векторы реализованы с использованием обобщённых типов; мы рассмотрим, как использовать обобщённые типы с вашими собственными типами в Главе 10. А пока знайте, что тип `Vec<T>`, предоставляемый стандартной библиотекой, может хранить любой тип. Когда мы создаём новый вектор для хранения конкретного типа, мы можем указать этот тип в угловых скобках. В листинге 8-1 мы сообщили Rust, что `Vec<T>` в `v` будет хранить элементы типа `i32`.

Чаще всего вы будете создавать `Vec<T>` с начальными значениями и Rust может определить тип сохраняемых вами значений, но иногда вам всё же придётся указывать аннотацию типа. Для удобства Rust предоставляет макрос `vec!`, который создаст новый вектор, содержащий заданные вами значения. В листинге 8-2 создаётся новый `Vec<i32>`, который будет хранить значения `1`, `2` и `3`. Числовым типом является `i32`, потому что это тип по умолчанию для целочисленных значений, о чём упоминалось в разделе [“Типы данных”] Главы 3.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-02/src/main.rs:here}}
```

<span class="caption">Листинг 8-2: Создание нового вектора, содержащего значения</span>

Поскольку мы указали начальные значения типа `i32`, Rust может сделать вывод, что тип переменной `v` это `Vec<i32>` и аннотация типа здесь не нужна. Далее мы посмотрим как изменять вектор.

### Изменение вектора

Чтобы создать вектор и затем добавить к нему элементы, можно использовать метод `push` показанный в листинге 8-3.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-03/src/main.rs:here}}
```

<span class="caption">Листинг 8-3: Использование метода <code>push</code> для добавления значений в вектор</span>

Как и с любой переменной, если мы хотим изменить её значение, нам нужно сделать её изменяемой с помощью ключевого слова `mut`, что обсуждалось в Главе 3. Все числа которые мы помещаем в вектор имеют тип `i32` по этому Rust с лёгкостью выводит тип вектора, по этой причине нам не нужна здесь аннотация типа вектора `Vec<i32>`.

### Чтение данных вектора

Есть два способа сослаться на значение, хранящееся в векторе: с помощью индекса или метода `get` . В следующих примерах для большей ясности мы указали типы значений, возвращаемых этими функциями.

В листинге 8-4 показаны оба метода доступа к значению в векторе: либо с помощью синтаксиса индексации и с помощью метода `get`.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-04/src/main.rs:here}}
```

<span class="caption">Листинг 8-4. Использование синтаксиса индексации и метода <code>get</code> для доступа к элементу в векторе</span>

Обратите внимание здесь на пару деталей. Мы используем значение индекса `2` для получения третьего элемента: векторы индексируются начиная с нуля. Указывая `&` и `[]` мы получаем ссылку на элемент по указанному индексу. Когда мы используем метод `get` содержащего индекс, переданный в качестве аргумента, мы получаем тип `Option<&T>`, который мы можем проверить с помощью `match`.

Причина, по которой Rust предоставляет два способа ссылки на элемент, заключается в том, что вы можете выбрать, как программа будет себя вести, когда вы попытаетесь использовать значение индекса за пределами диапазона существующих элементов. В качестве примера давайте посмотрим, что происходит, когда у нас есть вектор из пяти элементов, а затем мы пытаемся получить доступ к элементу с индексом 100 с помощью каждого метода, как показано в листинге 8-5.

```rust,should_panic,panics
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-05/src/main.rs:here}}
```

<span class="caption">Листинг 8-5. Попытка доступа к элементу с индексом 100 в векторе, содержащем пять элементов</span>

Когда мы запускаем этот код, первая строка с `&v[100]` вызовет панику программы, потому что происходит попытка получить ссылку на несуществующий элемент. Такой подход лучше всего использовать, когда вы хотите, чтобы ваша программа аварийно завершила работу при попытке доступа к элементу за пределами вектора.

Когда методу `get` передаётся индекс, который находится за пределами вектора, он без паники возвращает `None`. Вы могли бы использовать такой подход, если доступ к элементу за пределами диапазона вектора происходит время от времени при нормальных обстоятельствах. Тогда ваш код будет иметь логику для обработки наличия `Some(&element)` или `None`, как обсуждалось в Главе 6. Например, индекс может исходить от человека, вводящего число. Если пользователь случайно введёт слишком большое число, то программа получит значение `None` и у вас будет возможность сообщить пользователю, сколько элементов находится в текущем векторе, и дать ему возможность ввести допустимое значение. Такое поведение было бы более дружелюбным для пользователя, чем внезапный сбой программы из-за опечатки!

Когда у программы есть действительная ссылка, borrow checker (средство проверки заимствований), обеспечивает соблюдение правил владения и заимствования (описанные в Главе 4), чтобы гарантировать, что эта ссылка и любые другие ссылки на содержимое вектора остаются действительными. Вспомните правило, которое гласит, что у вас не может быть изменяемых и неизменяемых ссылок в одной и той же области. Это правило применяется в листинге 8-6, где мы храним неизменяемую ссылку на первый элемент вектора и затем пытаемся добавить элемент в конец вектора. Данная программа не будет работать, если мы также попробуем сослаться на данный элемент позже в функции:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-06/src/main.rs:here}}
```

<span class="caption">Листинг 8-6. Попытка добавить некоторый элемент в вектор, в то время когда есть ссылка на элемент вектора</span>

Компиляция этого кода приведёт к ошибке:

```console
{{#include ../listings/ch08-common-collections/listing-08-06/output.txt}}
```

Код в листинге 8-6 может выглядеть так, как будто он должен работать. Почему ссылка на первый элемент должна заботиться об изменениях в конце вектора? Эта ошибка возникает из-за особенности того, как работают векторы: поскольку векторы размещают значения в памяти друг за другом, добавление нового элемента в конец вектора может потребовать выделения новой памяти и копирования старых элементов в новое пространство, если нет достаточного места, чтобы разместить все элементы друг за другом там, где в данный момент хранится вектор. В этом случае ссылка на первый элемент будет указывать на освобождённую память. Правила заимствования предотвращают попадание программ в такую ситуацию.

> Примечание: Дополнительные сведения о реализации типа `Vec<T>` смотрите в разделе ["The Rustonomicon"](https://doc.rust-lang.org/nomicon/vec/vec.html).

### Перебор значений в векторе

Для доступа к каждому элементу вектора по очереди, мы итерируем все элементы, вместо использования индексов для доступа к одному за раз. В листинге 8-7 показано, как использовать цикл `for` для получения неизменяемых ссылок на каждый элемент в векторе значений типа `i32` и их вывода.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-07/src/main.rs:here}}
```

<span class="caption">Листинг 8-7. Печать каждого элемента векторе, при помощи итерирования по элементам вектора с помощью цикла <code>for</code></span>

Мы также можем итерировать изменяемые ссылки на каждый элемент изменяемого вектора, чтобы вносить изменения во все элементы. Цикл `for` в листинге 8-8 добавит `50` к каждому элементу.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-08/src/main.rs:here}}
```

<span class="caption">Листинг 8-8. Итерирование и изменение элементов вектора по изменяемым ссылкам</span>

Чтобы изменить значение на которое ссылается изменяемая ссылка, мы должны использовать оператор разыменования ссылки `*` для получения значения по ссылке в переменной `i` прежде чем использовать оператор `+=`. Мы поговорим подробнее об операторе разыменования в разделе [“Следование по указателю к значению с помощью оператора разыменования”] Главы 15.

Перебор вектора, будь то неизменяемый или изменяемый, безопасен из-за правил проверки заимствования. Если бы мы попытались вставить или удалить элементы в телах цикла `for` в листингах 8-7 и 8-8, мы бы получили ошибку компилятора, подобную той, которую мы получили с кодом в листинге 8-6. Ссылка на вектор, содержащийся в цикле for, предотвращает одновременную модификацию всего вектора.

### Использование перечислений для хранения множества разных типов

Векторы могут хранить значения только одинакового типа. Это может быть неудобно; определённо могут быть случаи когда надо хранить список элементов разных типов. К счастью, варианты перечисления определены для одного и того же типа перечисления, поэтому, когда нам нужен один тип для представления элементов разных типов, мы можем определить и использовать перечисление!

Например, мы хотим получить значения из строки в электронной таблице где некоторые столбцы строки содержат целые числа, некоторые числа с плавающей точкой, а другие - строковые значения. Можно определить перечисление, варианты которого будут содержать разные типы значений и тогда все варианты перечисления будут считаться одним и тем же типом: типом самого перечисления. Затем мы можем создать вектор для хранения этого перечисления и, в конечном счёте, для хранения различных типов. Мы покажем это в листинге 8-9.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-09/src/main.rs:here}}
```

<span class="caption">Листинг 8-9: Определение <code>enum</code> для хранения значений разных типов в одном векторе</span>

Rust должен знать, какие типы будут в векторе во время компиляции, чтобы точно знать сколько памяти в куче потребуется для хранения каждого элемента. Мы также должны чётко указать, какие типы разрешены в этом векторе. Если бы Rust позволял вектору содержать любой тип, то был бы шанс что один или несколько типов вызовут ошибки при выполнении операций над элементами вектора. Использование перечисления вместе с выражением `match` означает, что во время компиляции Rust  гарантирует, что все возможные случаи будут обработаны, как обсуждалось в главе 6.

Если вы не знаете исчерпывающий набор типов, которые программа получит во время выполнения для хранения в векторе, то техника использования перечисления не сработает. Вместо этого вы можете использовать типаж-объект, который мы рассмотрим в главе 17.

Теперь, когда мы обсудили некоторые из наиболее распространённых способов использования векторов, обязательно ознакомьтесь [с документацией по API вектора](https://doc.rust-lang.org/std/vec/struct.Vec.html), чтобы узнать о множестве полезных методов, определённых в `Vec<T>` стандартной библиотеки. Например, в дополнение к методу `push`, существует метод `pop`, который удаляет и возвращает последний элемент.

### Удаление элементов из вектора

Подобно структурам `struct`, вектор высвобождает свою память когда выходит из области видимости, что показано в листинге 8-10.

```rust
{{#rustdoc_include ../listings/ch08-common-collections/listing-08-10/src/main.rs:here}}
```

<span class="caption">Листинг 8-10. Показано как удаляется вектор и его элементы</span>

Когда вектор удаляется, всё его содержимое также удаляется: удаление вектора означает и удаление значений, которые он содержит. Средство проверки заимствования гарантирует, что любые ссылки на содержимое вектора используются только тогда, когда сам вектор действителен.

Давайте перейдём к следующему типу коллекции: `String`!


[“Типы данных”]: ch03-02-data-types.html#data-types
[“Следование по указателю к значению с помощью оператора разыменования”]: ch15-02-deref.html#following-the-pointer-to-the-value-with-the-dereference-operator