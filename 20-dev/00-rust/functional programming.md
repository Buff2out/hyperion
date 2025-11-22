Последовательный вызов функций через точку или передача результата одной функции в другую называется по-разному в зависимости от направления и синтаксиса:[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

## Основные термины

**Function composition (композиция функций)** — вызов функций справа налево, где результат одной функции передается в следующую. В математической записи: f∘g∘hf \circ g \circ hf∘g∘h означает f(g(h(x)))f(g(h(x)))f(g(h(x))). В Haskell используется оператор `.` для композиции.[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

**Pipeline (конвейер, pipelining)** — передача данных слева направо через последовательность функций. Более естественен для чтения кода, так как порядок совпадает с порядком выполнения. Примеры операторов:[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

- `|>` в F#, OCaml, Elixir (pipe operator)
    
- `->` и `->>` в Clojure (threading macros)
    
- `.then()` в JavaScript Promises
    

**Method chaining (цепочка методов)** — это ООП-паттерн, когда методы возвращают `self`/`this`, позволяя писать `obj.method1().method2().method3()`. Технически не чисто функциональный подход, так как часто мутирует объект.[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

## Различия

Композиция записывается в обратном порядке выполнения: `compose(three, two, one)(x)` выполнится как `three(two(one(x)))`. Конвейер в прямом: `pipe(one, two, three)(x)` — сначала `one`, потом `two`, потом `three`.[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

В Rust методы можно чейнить через точку (например, iterator chains: `vec.iter().map(...).filter(...).collect()`), что семантически близко к pipeline. Это **fluent interface** паттерн, который заимствует идеи из функционального программирования.[habr](https://habr.com/ru/companies/yandex/articles/547786/)​

1. [https://ru.wikipedia.org/wiki/%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D0%BE%D0%BD%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5](https://ru.wikipedia.org/wiki/%D0%A4%D1%83%D0%BD%D0%BA%D1%86%D0%B8%D0%BE%D0%BD%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B5_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5)
2. [https://blog.skillfactory.ru/glossary/funkczionalnye-yazyki-programmirovaniya/](https://blog.skillfactory.ru/glossary/funkczionalnye-yazyki-programmirovaniya/)
3. [https://habr.com/ru/companies/yandex/articles/547786/](https://habr.com/ru/companies/yandex/articles/547786/)
4. [https://it.kgsu.ru/Lisp/lisp0051.html](https://it.kgsu.ru/Lisp/lisp0051.html)
5. [https://studfile.net/preview/7802106/page:3/](https://studfile.net/preview/7802106/page:3/)
6. [https://gb.ru/blog/funktsionalnoe-programmirovanie/](https://gb.ru/blog/funktsionalnoe-programmirovanie/)
7. [https://al.cs.msu.ru/system/files/21-FPL.pdf](https://al.cs.msu.ru/system/files/21-FPL.pdf)
8. [https://gitverse.ru/blog/articles/development/168-funkcionalnoe-programmirovanie](https://gitverse.ru/blog/articles/development/168-funkcionalnoe-programmirovanie)
9. [https://ipc.susu.ru/fp.html](https://ipc.susu.ru/fp.html)
10. [https://www.itweek.ru/themes/detail.php?ID=209957](https://www.itweek.ru/themes/detail.php?ID=209957)