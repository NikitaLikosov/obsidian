# Финальный проект: создание многопоточного веб-сервера

Это был долгий путь, но мы дошли до финала книги. В этой главе мы сделаем ещё один проект, чтобы закрепить несколько тем из последних глав и резюмировать то, что прошли в самом начале.

В качестве нашего финального проекта мы напишем веб-сервер, который выводит надпись “hello” в веб-браузере, как на рисунке 20-1.

![hello from rust](https://github.com/ruRust/book/blob/master/rustbook-en/src/img/trpl20-01.png?raw=true)

<span class="caption">Рисунок 20-1: Наш последний совместный проект</span>

Для создания веб-сервера нам понадобится:

1. Узнать немного о протоколах TCP и HTTP.
2. Сделать прослушивание TCP соединения у сокета.
3. Создать функциональность для парсинга небольшого количества HTTP-запросов.
4. Научить сервер отдавать корректный HTTP-ответ.
5. Улучшить пропускную способность нашего сервера с помощью пула потоков.

Прежде чем мы начнём, заметим: метод, который мы будем использовать - не лучшим способ создания веб-сервера на Rust. Члены сообщества уже опубликовали на [crates.io](https://crates.io/) несколько готовых к использованию крейтов, которые предоставляют более полные реализации веб-сервера и пула потоков, чем те, которые мы создадим. Однако наша цель в этой главе — научиться новому, а не идти по лёгкому пути. Поскольку Rust — это язык системного программирования, мы можем выбирать тот уровень абстракции, который нам подходит, и можем переходить на более низкий уровень, что может быть невозможно или непрактично в других языках. Поэтому мы напишем базовый HTTP-сервер и пул потоков вручную, чтобы вы могли изучить общие идеи и методы, лежащие в основе крейтов, которые, возможно, вы будете использовать в будущем.