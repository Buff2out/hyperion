Трейт `Clone` для структуры состояния `AppState` в Axum необходим для обеспечения **потокобезопасного** (thread-safe) доступа к общим данным, таким как пул соединений с базой данных, из нескольких одновременно обрабатываемых запросов.[temofeev+1](https://temofeev.ru/info/articles/nachalo-raboty-s-axum-samym-populyarnym-veb-freymvorkom-rust/)​

Когда вы запускаете веб-сервер на Axum, он обычно работает в многопоточной среде для обработки нескольких запросов параллельно. Каждому запросу (или, точнее, каждому рабочему потоку, который его обрабатывает) требуется свой собственный экземпляр состояния для работы.[rust-lang](https://users.rust-lang.org/t/axum-state-when-using-clone-and-arc/114349)​

## Подробное объяснение

1. **Расшаривание состояния между потоками**: Axum использует экстрактор `State` для внедрения состояния приложения в обработчики (handlers). Чтобы передать это состояние в разные потоки, которые обрабатывают запросы, Axum должен иметь возможность создавать копии этого состояния. Трейт `Clone` предоставляет стандартизированный способ для такого копирования.[docs+1](https://docs.rs/axum/latest/axum/extract/struct.State.html)​
    
2. **Эффективность клонирования `PgPool`**: В вашем примере поле `db_pool` имеет тип `PgPool` из библиотеки `sqlx`. Важно понимать, что вызов `.clone()` для `PgPool` — это очень **дешевая операция**. Она не создает новый пул соединений и не открывает новые подключения к базе данных. Вместо этого она создает новый _указатель_ на тот же самый, уже существующий внутренний пул, увеличивая **счетчик ссылок** (reference counter). Это позволяет безопасно и эффективно использовать один и тот же пул соединений в разных частях вашего приложения одновременно.[reddit+1](https://www.reddit.com/r/rust/comments/173zzbb/axum_use_state_without_clone/)​
    
3. **Внутренняя работа Axum с `Arc`**: Зачастую, если тип состояния реализует `Clone`, метод `.with_state()` в Axum неявно оборачивает ваше состояние в `Arc` (Atomic Reference Counted pointer). `Arc` — это умный указатель, который позволяет безопасно владеть данными из нескольких потоков. Клонирование `Arc` также является дешевой операцией, так как оно лишь увеличивает атомарный счетчик ссылок, не копируя сами данные.[leapcell+2](https://leapcell.io/blog/robust-state-management-in-actix-web-and-axum-applications)​
    

## Практические подходы

Существует два основных способа управления состоянием в Axum, которые достигают одной и той же цели:

|Подход|Пример кода|Объяснение|
|---|---|---|
|**`#[derive(Clone)]` на структуре** (как в вашем примере)|`#[derive(Clone)]`  <br>`struct AppState { db_pool: PgPool }`  <br>`.with_state(state)`|Это самый простой и распространенный способ. Он работает, потому что `PgPool` и другие подобные "умные" типы уже реализуют `Clone` эффективно (через `Arc` внутри) [temofeev+1](https://temofeev.ru/info/articles/nachalo-raboty-s-axum-samym-populyarnym-veb-freymvorkom-rust/)​.|
|**Явное использование `Arc`**|`type AppState = Arc<MyState>;`  <br>`struct MyState { ... }`  <br>`.with_state(Arc::new(my_state))`|Этот подход делает разделяемое владение более явным в коде. Вы вручную оборачиваете состояние в `Arc`, а затем клонируете `Arc` при передаче в `.with_state()` [compile7+1](https://compile7.org/implement-data-structures/how-to-implement-stack-in-axum/)​. Результат тот же, но код более многословен.|

Таким образом, требование `Clone` — это не о дорогостоящем копировании данных, а о механизме безопасного и эффективного разделения доступа к общему состоянию в асинхронной многопоточной среде Axum. Ваш код с `#[derive(Clone)]` является идиоматичным и правильным для работы с `PgPool` в Axum.[compile7+1](https://compile7.org/implement-data-structures/how-to-implement-stack-in-axum/)​

1. [https://users.rust-lang.org/t/axum-state-when-using-clone-and-arc/114349](https://users.rust-lang.org/t/axum-state-when-using-clone-and-arc/114349)
2. [https://docs.rs/axum/latest/axum/extract/struct.State.html](https://docs.rs/axum/latest/axum/extract/struct.State.html)
3. [https://www.reddit.com/r/rust/comments/173zzbb/axum_use_state_without_clone/](https://www.reddit.com/r/rust/comments/173zzbb/axum_use_state_without_clone/)
4. [https://snix.dev/rustdoc/axum/extract/struct.State.html](https://snix.dev/rustdoc/axum/extract/struct.State.html)
5. [https://leapcell.io/blog/robust-state-management-in-actix-web-and-axum-applications](https://leapcell.io/blog/robust-state-management-in-actix-web-and-axum-applications)
6. [https://temofeev.ru/info/articles/nachalo-raboty-s-axum-samym-populyarnym-veb-freymvorkom-rust/](https://temofeev.ru/info/articles/nachalo-raboty-s-axum-samym-populyarnym-veb-freymvorkom-rust/)
7. [https://compile7.org/implement-data-structures/how-to-implement-stack-in-axum/](https://compile7.org/implement-data-structures/how-to-implement-stack-in-axum/)
8. [https://www.test.guide-it.ru/docs/axum/state-middleware/state.html](https://www.test.guide-it.ru/docs/axum/state-middleware/state.html)
9. [https://www.shuttle.dev/blog/2023/12/06/using-axum-rust](https://www.shuttle.dev/blog/2023/12/06/using-axum-rust)
10. [https://habr.com/ru/articles/770332/](https://habr.com/ru/articles/770332/)
11. [https://github.com/tokio-rs/axum/issues/1374](https://github.com/tokio-rs/axum/issues/1374)
12. [https://github.com/tokio-rs/axum/discussions/2508](https://github.com/tokio-rs/axum/discussions/2508)
13. [https://stackoverflow.com/questions/78855741/is-it-ok-to-clone-a-connection-pool-in-rust-axum](https://stackoverflow.com/questions/78855741/is-it-ok-to-clone-a-connection-pool-in-rust-axum)
14. [https://www.reddit.com/r/rust/comments/16kono9/trying_to_use_mutable_appstate_in_axum_and/](https://www.reddit.com/r/rust/comments/16kono9/trying_to_use_mutable_appstate_in_axum_and/)
15. [https://docs.rs/axum/latest/axum/](https://docs.rs/axum/latest/axum/)
16. [https://users.rust-lang.org/t/how-to-pass-app-state-in-axum/104458](https://users.rust-lang.org/t/how-to-pass-app-state-in-axum/104458)
17. [https://qna.habr.com/q/1382956](https://qna.habr.com/q/1382956)
18. [https://rust-classes.com/chapter_7_4](https://rust-classes.com/chapter_7_4)
19. [https://www.youtube.com/watch?v=XZtlD_m59sM](https://www.youtube.com/watch?v=XZtlD_m59sM)
20. [https://tg-rs.github.io/carapax/axum/extract/struct.State.html](https://tg-rs.github.io/carapax/axum/extract/struct.State.html)