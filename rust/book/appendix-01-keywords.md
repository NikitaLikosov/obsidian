## Приложение A: Ключевые слова

Следующий список содержит ключевые слова, зарезервированные для текущего или будущего использования в языке Rust. Как таковые их нельзя использовать в качестве идентификаторов (за исключением сырых идентификаторов, которые мы обсудим в разделе [«Сырые идентификаторы]<!-- ignore -->»). Идентификаторы — это имена функций, переменных, параметров, полей структур, модулей, крейтов, констант, макросов, статических значений, атрибутов, типов, свойств или времён жизни.

### Используемые в настоящее время ключевые слова

Ниже приведён список используемых в настоящее время ключевых слов с их описанием.

-  `as` — выполнить примитивное преобразование, уточнить конкретную характеристику, которую содержит объект, или переименовать элемент в выражении `use`
- `async` — возврат `Future` вместо блокировки текущего потока
- `await` — остановка выполнения до готовности результата `Future`
- `break` — немедленный выход из цикла
- `const` — определение константного элемента или неизменяемого сырого указателя
- `continue` — досрочный переход к следующей итерации цикла
- `crate` — ссылка на корень пакета в пути к модулю
- `dyn` — динамическая отсылка к типажу объекта
- `else` — альтернативные ветви для конструкций управления потока `if` и `if let`
- `enum` — определение перечислений
- `extern` — связывание внешней функции или переменной
- `false` — логический ложный литерал
- `fn` — определение функции или типа указателя на функцию
- `for` — циклически перебирать элементы из итератора, реализовывать признак или указывать время жизни с более высоким рейтингом.
- `if` — ветвление на основе результата условного выражения
- `impl` — реализация встроенной функциональности или функциональности типажа
- `in` — часть синтаксиса цикла `for`
- `let` — объявление (связывание) переменной
- `loop` — безусловный цикл
- `match` — сопоставление значения с шаблонами
- `mod` — определение модуля
- `move` — перекладывание владения на замыкание всеми захваченными элементами
- `mut` — обозначение изменчивости в ссылках, сырах указателей и привязках к шаблону
- `pub` — модификатор публичной доступность полей структур, блоков `impl` и модулей
- `ref` — привязка по ссылке
- `return` — возвращает результат из функции
- `Self` — псевдоним для определяемого или реализуемого типа
- `self` — объект текущего метода или модуля
- `static` — глобальная переменная или время жизни, продолжающееся на протяжении всего выполнения программы
- `struct` — определение структуры
- `super` — родительский модуль текущего модуля
- `trait` — определение типажа
- `true` — логический истинный литерал
- `type` — определение псевдонима типа или связанного типа
- `union` - определить [объединение]<!-- ignore -->; является ключевым словом только при использовании в объявлении объединения
- `unsafe` — обозначение небезопасного кода, функций, типажей и их реализаций
- `use` — ввод имён в область видимости
- `where` — ограничение типа
- `while` — условный цикл, основанный на результате выражения

### Ключевые слова, зарезервированные для будущего использования

Следующие ключевые слова ещё не имеют никакой функциональности, но зарезервированы Rust для возможного использования в будущем.

- `abstract`
- `become`
- `box`
- `do`
- `final`
- `macro`
- `override`
- `priv`
- `try`
- `typeof`
- `unsized`
- `virtual`
- `yield`

### Сырые идентификаторы

*Сырые идентификаторы* — это синтаксис, позволяющий использовать ключевые слова там, где обычно они не могут быть. Для создания и использования сырого идентификатора к ключевому слову добавляется префикс `r#`.

Например, ключевое слово `match`. Если вы попытаетесь скомпилировать следующую функцию, использующую в качестве имени `match`:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore,does_not_compile
fn match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}
```

вы получите ошибку:

```text
error: expected identifier, found keyword `match`
 --> src/main.rs:4:4
  |
4 | fn match(needle: &str, haystack: &str) -> bool {
  |    ^^^^^ expected identifier, found keyword
```

Ошибка говорит о том, что вы не можете использовать ключевое слово `match` в качестве идентификатора функции. Чтобы получить возможность использования слова `match` в качестве имени функции, нужно использовать синтаксис «сырых идентификаторов», например так:

<span class="filename">Файл: src/main.rs</span>

```rust
fn r#match(needle: &str, haystack: &str) -> bool {
    haystack.contains(needle)
}

fn main() {
    assert!(r#match("foo", "foobar"));
}
```

Этот код скомпилируется без ошибок. Обратите внимание, что префикс `r#` в определении имени функции указан так же, как он указан в месте её вызова в `main`.

Сырые идентификаторы позволяют вам использовать любое слово, которое вы выберете, в качестве идентификатора, даже если это слово окажется зарезервированным ключевым словом. Это даёт нам больше свободы в выборе имён идентификаторов, а также позволяет нам интегрироваться с программами, написанными на языке, где эти слова не являются ключевыми. Кроме того, необработанные идентификаторы позволяют вам использовать библиотеки, написанные в версии Rust, отличной от используемой в вашем крейте. Например, `try` не является ключевым словом в выпуске 2015 года, но является в выпуске 2018 года. Если вы зависите от библиотеки, написанной с использованием версии 2015 года и имеющей функцию `try`, вам потребуется использовать синтаксис сырого идентификатора, в данном случае `r#try`, для вызова этой функции из кода версии 2018 года. См. [Приложение E]<!-- ignore --> для получения дополнительной информации о редакциях Rust.


[«Сырые идентификаторы]: #raw-identifiers
[объединение]: ../reference/items/unions.html
[Приложение E]: appendix-05-editions.html