## Привет, Cargo!

Cargo - это система сборки и менеджер пакетов Rust. Большая часть разработчиков используют данный инструмент для управления проектами, потому что Cargo выполняет за вас множество задач, таких как сборка кода, загрузка библиотек, от которых зависит ваш код, и создание этих библиотек. (Мы называем библиотеки, которые нужны вашему коду, *зависимостями*.)

Самые простые программы на Rust, подобные той, которую мы написали, не имеют никаких зависимостей. Если бы мы сделали проект «Hello, world!» с Cargo, он бы использовал только ту часть Cargo, которая отвечает за компиляцию вашего кода. По мере написания более сложных программ на Rust вы будете добавлять зависимости, а если вы начнёте проект с использованием Cargo, добавлять зависимости станет намного проще.

Поскольку значительное число проектов Rust используют Cargo, оставшаяся часть книги подразумевает, что вы тоже используете Cargo. Cargo входит в комплект поставки Rust, если вы использовали официальные программы установки, рассмотренные в разделе ["Установка"]<!-- ignore -->. Если вы установили Rust другим способом, проверьте, установлен ли Cargo, введя в терминале следующее:

```console
$ cargo --version
```

Если команда выдала номер версии, то значит Cargo установлен. Если вы видите ошибку, вроде `command not found` ("команда не найдена"), загляните в документацию для использованного вами способа установки, чтобы выполнить установку Cargo отдельно.

### Создание проекта с помощью Cargo

Давайте создадим новый проект с помощью Cargo и посмотрим, как он отличается от нашего начального проекта "Hello, world!". Перейдите обратно в папку *projects* (или любую другую, где вы решили сохранять код). Затем, в любой операционной системе, запустите команду:

```console
$ cargo new hello_cargo
$ cd hello_cargo
```

Первая команда создаёт новый каталог и проект с именем *hello_cargo*. Мы назвали наш проект *hello_cargo*, и Cargo создаёт свои файлы в каталоге с тем же именем.

Перейдём в каталог *hello_cargo* и посмотрим файлы. Увидим, что Cargo сгенерировал два файла и одну директорию: файл  *Cargo.toml* и каталог *src* с файлом *main.rs* внутри.

Кроме того, cargo инициализировал новый репозиторий Git вместе с файлом *.gitignore*. Файлы Git не будут сгенерированы, если вы запустите `cargo new` в существующем репозитории Git; вы можете изменить это поведение, используя `cargo new --vcs=git`.

> Примечание. Git — это распространённая система контроля версий. Вы можете изменить `cargo new`, чтобы использовать другую систему контроля версий или не использовать систему контроля версий, используя флаг `--vcs`. Запустите `cargo new --help`, чтобы увидеть доступные параметры.

Откройте файл *Cargo.toml* в любом текстовом редакторе. Он должен выглядеть как код в листинге 1-2.

<span class="filename">Файл: Cargo.toml</span>

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

<span class="caption">Листинг 1-2: Содержимое файла <em>Cargo.toml</em>, сгенерированное командой <code>cargo new</code></span>

Это файл в формате [*TOML*](https://github.com/toml-lang/toml)<!--  --> (*Tom’s Obvious, Minimal Language*), который является форматом конфигураций Cargo.

Первая строка, `[package]`, является заголовочной секцией, которая указывает что следующие инструкции настраивают пакет. По мере добавления больше информации в данный файл, будет добавляться больше секций и инструкций (строк).

Следующие три строки задают информацию о конфигурации, необходимую Cargo для компиляции вашей программы: имя, версию и редакцию Rust, который будет использоваться. Мы поговорим о ключе `edition` в [Приложении E]<!-- ignore -->.

Последняя строка, `[dependencies]` является началом секции для списка любых зависимостей вашего проекта. В Rust, это внешние пакеты кода, на которые ссылаются ключевым словом *crate*. Нам не нужны никакие зависимости в данном проекте, но мы будем использовать их в первом проекте главы 2, так что нам пригодится данная секция зависимостей потом.

Откройте файл *src/main.rs* и загляните в него:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo сгенерировал для вас программу "Hello, world!", подобную той, которую мы написали в Листинге 1-1! Пока что различия между нашим предыдущим проектом и проектом, сгенерированным при помощи Cargo, заключаются в том, что Cargo поместил исходный код в каталог *src*, и у нас есть конфигурационный файл *Cargo.toml* в верхнем каталоге проекта.

Cargo ожидает, что ваши исходные файлы находятся внутри каталога *src*. Каталог верхнего уровня проекта предназначен только для файлов README, информации о лицензии, файлы конфигурации и чего то ещё не относящего к вашему коду. Использование Cargo помогает организовывать проект. Есть место для всего и все находится на своём месте.

Если вы начали проект без использования Cargo, как мы делали для "Hello, world!" проекта, то можно конвертировать его в проект с использованием Cargo. Переместите код в подкаталог *src* и создайте соответствующий файл *Cargo.toml* в папке.

### Сборка и запуск Cargo проекта

Посмотрим, в чем разница при сборке и запуске программы "Hello, world!" с помощью Cargo. В каталоге *hello_cargo* соберите проект следующей командой:

```console
$ cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

Эта команда создаёт исполняемый файл в *target/debug/hello_cargo* (или *target\debug\hello_cargo.exe* в Windows), а не в вашем текущем каталоге. Поскольку стандартная сборка является отладочной, Cargo помещает двоичный файл в каталог с именем *debug*. Вы можете запустить исполняемый файл с помощью этой команды:

```console
$ ./target/debug/hello_cargo # or .\target\debug\hello_cargo.exe on Windows
Hello, world!
```

Если все хорошо, то `Hello, world!` печатается в терминале. Запуск команды `cargo build` в первый раз также приводит к созданию нового файла *Cargo.lock* в папке верхнего уровня. Данный файл хранит точные версии зависимостей вашего проекта. Так как у нас нет зависимостей, то файл пустой. Вы никогда не должны менять этот файл вручную: Cargo сам управляет его содержимым для вас.

Только что мы собрали проект командой `cargo build` и запустили его из `./target/debug/hello_cargo`. Но мы также можем при помощи команды `cargo run` сразу и скомпилировать код, и затем запустить полученный исполняемый файл всего лишь одной командой:

```console
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Использование `cargo run` более удобно, чем необходимость помнить и запускать `cargo build`, а затем использовать весь путь к бинарному файлу, поэтому большинство разработчиков используют `cargo run`.

Обратите внимание, что на этот раз мы не видели вывода, указывающего на то, что Cargo компилирует `hello_cargo`. Cargo выяснил, что файлы не изменились, поэтому не стал пересобирать, а просто запустил бинарный файл. Если бы вы изменили свой исходный код, Cargo пересобрал бы проект перед его запуском, и вы бы увидели этот вывод:

```console
$ cargo run
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/hello_cargo`
Hello, world!
```

Cargo также предоставляет команду, называемую `cargo check`. Эта команда быстро проверяет ваш код, чтобы убедиться, что он компилируется, но не создаёт исполняемый файл:

```console
$ cargo check
   Checking hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.32 secs
```

Почему вам не нужен исполняемый файл? Часто `cargo check` выполняется намного быстрее, чем `cargo build`, поскольку пропускает этап создания исполняемого файла. Если вы постоянно проверяете свою работу во время написания кода, использование `cargo check` ускорит процесс информирования вас о том, что ваш проект всё ещё компилируется! Таким образом, многие Rustacean периодически запускают `cargo check`, когда пишут свои программы, чтобы убедиться, что она компилируется. Затем они запускают `cargo build`, когда готовы использовать исполняемый файл.

Давайте подытожим, что мы уже узнали о Cargo:

- Мы можем создать проект с помощью `cargo new`.
- можно собирать проект, используя команду `cargo build`,
- можно одновременно собирать и запускать проект одной командой `cargo run`,
- можно собрать проект для проверки ошибок с помощью `cargo check`, не тратя время на кодогенерацию исполняемого файла,
- cargo сохраняет результаты сборки не в директорию с исходным кодом, а в отдельный каталог *target/debug*.

Дополнительным преимуществом использования Cargo является то, что его команды одинаковы для разных операционных систем. С этой точки зрения, мы больше не будем предоставлять отдельные инструкции для Linux, macOS или Windows.

### Сборка финальной версии (Release)

Когда проект, наконец, готов к релизу, можно использовать команду `cargo build --release` для его компиляции с оптимизацией. Данная команда создаёт исполняемый файл в папке *target/release* в отличии от папки *target/debug*. Оптимизации делают так, что Rust код работает быстрее, но их включение увеличивает время компиляции. По этой причине есть два отдельных профиля: один для разработки, когда нужно осуществлять сборку быстро и часто, и другой, для сборки финальной программы, которую будете отдавать пользователям, которая готова к работе и будет выполняться максимально быстро. Если вы замеряете время выполнения вашего кода, убедитесь, что собрали проект с оптимизацией `cargo build --release` и тестируете исполняемый файл из папки *target/release*.

### Cargo как Конвенция

В простых проектах Cargo не даёт больших преимуществ по сравнению с использованием `rustc`, но он проявит себя, когда ваши программы станут более сложными. Когда программы вырастают до нескольких файлов или нуждаются в зависимостях, гораздо проще позволить Cargo координировать сборку.

Не смотря на то, что проект `hello_cargo` простой, теперь он  использует большую часть реального инструментария, который вы будете повседневно использовать в вашей карьере, связанной с Rust. Когда потребуется работать над проектами размещёнными в сети, вы сможете просто использовать следующую последовательность команд для получения кода с помощью Git, перехода в каталог проекта, сборку проекта:

```console
$ git clone example.org/someproject
$ cd someproject
$ cargo build
```

Для получения дополнительной информации о Cargo ознакомьтесь с [его документацией] .

## Итоги

Теперь вы готовы начать своё Rust путешествие! В данной главе вы изучили как:

- установить последнюю стабильную версию Rust, используя `rustup`,
- обновить Rust до последней версии,
- открыть локально установленную документацию,
- написать и запустить программу типа "Hello, world!", используя напрямую компилятор `rustc`,
- создать и запустить новый проект, используя соглашения и команды Cargo.

Это отличное время для создания более существенной программы, чтобы привыкнуть читать и писать код на языке Rust. Итак, в главе 2 мы построим программу для игры в угадай число. Если вы предпочитаете начать с изучения того, как работают общие концепции программирования в Rust, обратитесь к главе 3, а затем вернитесь к главе 2.


["Установка"]: ch01-01-installation.html#installation
[Приложении E]: appendix-05-editions.html
[его документацией]: https://doc.rust-lang.org/cargo/