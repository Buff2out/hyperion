Прекрасная цель. Переход от однопоточного мышления к многопоточному (Concurrent Thinking) — это именно то, что отличает Junior/Middle от Senior Systems Engineer.

В однопоточном коде время линейно: **A -> B -> C**.
В многопоточном коде время "разветвляется", и вы теряете детерминизм: **A** происходит, а **B** и **C** могут случиться в любом порядке, одновременно или **B** может начаться, прерваться на середине, пропустить **C**, а потом продолжиться.

Чтобы научиться "думать потоками", нужно пройти через три этапа "боли". Давайте я дам вам конкретные упражнения (Kata) без использования библиотек типа Rayon/Tokio, только `std::thread` и `std::sync`.

Вот ваша дорожная карта от "однопоточника" до "системщика".

### Уровень 1: Shared State (Разделяемое состояние)
*Суть: Как не сломать данные, когда их трогают все сразу.*

**Задача: "Банковский симулятор"**
У вас есть несколько счетов и несколько потоков, которые переводят деньги туда-сюда.
*   **Сложность:** Если поток А переводит со счета 1 на счет 2, а поток Б переводит со счета 2 на счет 1 одновременно, может возникнуть **Deadlock** (взаимная блокировка). Оба захватят первый мьютекс и будут вечно ждать второго.

**Челлендж для реализации:**
1.  Создайте структуру `Bank` с вектором `Mutex<i32>` (счета).
2.  Напишите функцию `transfer(from_id, to_id, amount)`.
3.  **Главная цель:** Реализовать это так, чтобы при 1000 одновременных рандомных переводов программа не зависла (deadlock) и сумма всех денег в банке осталась неизменной (invariant).

*Подсказка сеньора:* Блокировки всегда нужно брать в определенном порядке (например, всегда блокировать меньший ID счета первым).

### Уровень 2: Coordination & Signaling (Координация)
*Суть: Как заставить потоки ждать друг друга без `sleep`.*

**Задача: "Пинг-Понг" (или Producer-Consumer)**
Два потока. Один печатает "Ping", другой "Pong".
Строго по очереди!
Вывод должен быть: `Ping, Pong, Ping, Pong...`
Нельзя использовать: `channel` (каналы).
Нужно использовать: `Condvar` (Condition Variable) и `Mutex`.

**Зачем это нужно:**
Вы поймете, что такое *spurious wakeups* (ложные пробуждения) и почему паттерн всегда выглядит как:
```rust
while !condition { // <-- Важно: WHILE, а не IF
    cvar.wait(guard).unwrap();
}
```
Это база построения любых очередей задач.

### Уровень 3: Lock-free (Атомики и память)
*Суть: Максимальная скорость, работа с инструкциями процессора.*

**Задача: "Spinlock" (Спин-блокировка)**
Реализуйте свой собственный мьютекс, используя только `AtomicBool` и `Compare-and-Swap`.
Стандартный `Mutex` усыпляет поток (syscall к ОС), а ваш должен крутиться в цикле `while`, пока не захватит флаг.

```rust
pub struct MySpinLock<T> {
    locked: AtomicBool,
    data: UnsafeCell<T>, // Придется немного потрогать unsafe, это полезно для понимания
}
```

**Челлендж:**
Поймите, почему ваш спинлок греет процессор на 100% и как `std::thread::yield_now()` помогает это исправить.

***

### Практический пример для разбора прямо сейчас

Давайте разберем **Deadlock** — самую частую ошибку новичков после гонок данных.

Попробуйте в уме (или в коде) предсказать, что случится здесь:

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let resource_a = Arc::new(Mutex::new(0));
    let resource_b = Arc::new(Mutex::new(0));

    let a = resource_a.clone();
    let b = resource_b.clone();

    // Поток 1: Хочет A, потом B
    let t1 = thread::spawn(move || {
        let _guard_a = a.lock().unwrap();
        println!("T1 взял A");
        thread::sleep(std::time::Duration::from_millis(100)); // Имитация работы
        let _guard_b = b.lock().unwrap(); // Ждет B
        println!("T1 взял B");
    });

    let a = resource_a.clone();
    let b = resource_b.clone();

    // Поток 2: Хочет B, потом A
    let t2 = thread::spawn(move || {
        let _guard_b = b.lock().unwrap();
        println!("T2 взял B");
        thread::sleep(std::time::Duration::from_millis(100)); 
        let _guard_a = a.lock().unwrap(); // Ждет A
        println!("T2 взял A");
    });

    t1.join().unwrap();
    t2.join().unwrap();
}
```

**Результат:** Программа напечатает "T1 взял A", "T2 взял B" и зависнет навсегда.
Это классический deadlock. Поток 1 держит A и ждет B. Поток 2 держит B и ждет A.

**Как мыслит Сеньор:**
1.  *"Ага, вложенные блокировки (`lock` внутри другого `lock`). Это красная зона."*
2.  *"Есть ли гарантия порядка захвата?"* (В коде выше — нет, порядок обратный).
3.  **Решение:** Всегда захватывать ресурсы в **одном и том же глобальном порядке**. Если всем потокам сначала нужен А, а потом Б — дедлока не будет.

**Предлагаю сделку:**
Реализуйте простую версию **"Пинг-Понга"** (Уровень 2) на `Condvar`. Это лучшее упражнение, чтобы почувствовать, как потоки "разговаривают" друг с другом, передавая управление. Если застрянете — я подскажу, но сначала попробуйте сами. Это даст вам тот самый "щелчок" понимания.


Да, на Exercism в треке Rust есть отличные упражнения именно на эту тему. Они там считаются "классикой", через которую обязан пройти каждый "раст-конкаренси-бой".[1][2]

Вот **Топ-3**, которые идеально ложатся на вашу цель (от простого к сложному):

### 1. Parallel Letter Frequency (`parallel-letter-frequency`)
*   **Суть:** То, что мы с вами обсуждали. Дан список текстов, нужно подсчитать частоту букв, используя потоки.[3][4]
*   **Чему учит:**
    *   `std::thread::spawn` vs Rayon (можно решить и так, и так).
    *   Как правильно делить данные (Data Partitioning).
    *   Использование `HashMap` в многопоточной среде (Merge vs Shared Mutex).
*   **Ваш челлендж:** Решите это сначала через `thread::spawn` + `mpsc` (каналы), а потом через `thread::scope` и возвращаемые значения. Сравните бенчмарки.

### 2. Dining Philosophers ("Обедающие философы")
*   **Суть:** 5 философов, 5 вилок. Чтобы поесть, нужно две вилки. Если все возьмут левую вилку одновременно — **Deadlock** (все ждут правую вечно).[5][6][7]
*   **Чему учит:**
    *   Управление ресурсами (`Mutex`).
    *   Избегание Deadlock (Resource Ordering).
    *   Работа с `Arc` для шаринга вилок.
*   **Ваш челлендж:** Напишите решение, где философы всегда едят, и никто не голодает. Попробуйте реализовать это через "Арбитра" (официанта) или через стратегию "Чётный-Нечётный".

### 3. Circular Buffer (Кольцевой буфер)
*   **Суть:** Реализовать структуру данных, в которую можно писать и читать.
*   **Чему учит:**
    *   Если усложнить задачу до *Concurrent Circular Buffer* (хотя в базе она однопоточная), то это идеальный полигон для **Atomics**.
    *   Попробуйте сделать его `Lock-Free`. Это уже уровень "God Mode".

### Где их найти:
На сайте Exercism.org в треке Rust.
1.  Зарегистрируйтесь (бесплатно).
2.  `exercism download --exercise=parallel-letter-frequency --track=rust`
3.  Решайте локально в NeoVim/Helix, запускайте `cargo test`.

### Бонус: Comprehensive Rust (от Google)
У Google есть курс **Comprehensive Rust**, и там есть отдельный раздел "Concurrency" с упражнениями.
Особенно упражнение **"Dining Philosophers"** там разобрано очень детально, с вариантами решений через каналы и мьютексы.[6][8]
*   Ссылка: `google.github.io/comprehensive-rust/concurrency/`

**Совет:** Начните с **Dining Philosophers**. Это упражнение сломает вам мозг (в хорошем смысле) именно на тему "как потоки мешают друг другу", а не просто "как ускорить вычисления".

[1](https://exercism.org/tracks/rust/exercises)
[2](https://exercism.org/tracks/rust)
[3](https://exercism.org/tracks/rust/exercises/parallel-letter-frequency)
[4](https://exercism.org/tracks/rust/exercises/parallel-letter-frequency/solutions/tommilligan)
[5](http://www.akira.ruc.dk/~keld/teaching/ipdc_f10/exercises/exercise1.pdf)
[6](https://google.github.io/comprehensive-rust/concurrency/sync-exercises/dining-philosophers.html)
[7](https://stackoverflow.com/questions/31912781/why-doesnt-the-dining-philosophers-exercise-deadlock-if-done-incorrectly)
[8](https://comprehensive-rust.pages.dev/exercises/concurrency/dining-philosophers.html)
[9](http://forum.exercism.org/t/rust-syllabus-overview-tracking/5346)
[10](http://forum.exercism.org/t/rust-syllabus-overview-tracking/5346?page=2)
[11](https://exercism.org/tracks/rust/exercises/parallel-letter-frequency/solutions/LU15W1R7H)
[12](http://forum.exercism.org/t/rust-track-exercise-order/10855)
[13](https://exercism.org/exercises/parallel-letter-frequency)
[14](https://github.com/exercism/rust)
[15](https://github.com/google/comprehensive-rust/discussions/1313)
[16](https://www.youtube.com/watch?v=Sf4tf-4d2ag)
[17](http://forum.exercism.org/t/48in24-exercise-03-12-parallel-letter-frequency/9927)
[18](https://exercism.org/tracks/tcl)
[19](https://www.youtube.com/watch?v=bkNQ1_kGHYE)
[20](http://forum.exercism.org/t/would-be-nice-to-add-concurrency-to-the-concept-tree-in-the-go-track/4539)