# Умные указатели

*Указатель* — это общая концепция для переменной, которая содержит адрес участка памяти. Этот адрес «относится к», или «указывает на» некоторые другие данные. Наиболее общая разновидность указателя в Rust — это ссылка, о которой вы узнали из главы 4. Ссылки обозначаются символом `&` и заимствуют значение, на которое указывают. Они не имеют каких-либо специальных возможностей, кроме как ссылаться на данные, и не имеют никаких накладных расходов.

*Умные указатели*, с другой стороны, являются структурами данных, которые не только действуют как указатель, но также имеют дополнительные метаданные и возможности. Концепция умных указателей не уникальна для Rust: умные указатели возникли в C++ и существуют в других языках. В Rust есть разные умные указатели, определённые в стандартной библиотеке, которые обеспечивают функциональность, выходящую за рамки ссылок. Одним из примеров, который мы рассмотрим в этой главе, является тип умного указателя *reference counting* (подсчёт ссылок). Этот указатель позволяет иметь несколько владельцев с помощью отслеживания количества владельцев и, когда владельцев не остаётся, очищает данные.

Rust с его концепцией владения и заимствования имеет дополнительное различие между ссылками и умными указателями: в то время, как ссылки только заимствуют данные, умные указатели часто *владеют* данными, на которые указывают.

Ранее мы уже сталкивались с умными указателями в этой книге, хотя и не называли их так, например `String` и `Vec<T>` в главе 8. Оба этих типа считаются умными указателями, потому что они владеют некоторой областью памяти и позволяют ею манипулировать. У них также есть метаданные и дополнительные возможности или гарантии. `String`, например, хранит свой размер в виде метаданных и гарантирует, что содержимое строки всегда будет в кодировке UTF-8.

Умные указатели обычно реализуются с помощью структур. Характерной чертой, которая отличает умный указатель от обычной структуры, является то, что для умных указателей реализованы типажи `Deref` и `Drop`. Типаж `Deref` позволяет экземпляру умного указателя вести себя как ссылка, так что вы можете написать код, работающий с ним как со ссылкой, так и как с умным указателем. Типаж `Drop` позволяет написать код, который будет запускаться когда экземпляр умного указателя выйдет из области видимости. В этой главе мы обсудим оба типажа и продемонстрируем, почему они важны для умных указателей.

Учитывая, что паттерн умного указателя является общим паттерном проектирования, часто используемым в Rust, эта глава не описывает все существующие умные указатели. Множество библиотек имеют свои умные указатели, и вы также можете написать свои. Мы охватим наиболее распространённые умные указатели из стандартной библиотеки:

- `Box<T>` для распределения значений в куче (памяти)
- `Rc<T>` тип счётчика ссылок, который допускает множественное владение
- Типы `Ref<T>` и `RefMut<T>`, доступ к которым осуществляется через тип `RefCell<T>`, который обеспечивает правила заимствования во время выполнения вместо времени компиляции

Дополнительно мы рассмотрим паттерн *внутренней изменчивости (interior mutability)*, где неизменяемый тип предоставляет API для изменения своего внутреннего значения. Мы также обсудим *ссылочные зацикленности (reference cycles)*: как они могут приводить к утечке памяти и как это предотвратить.

Приступим!