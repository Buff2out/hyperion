```rust
use std::thread;

pub fn sum_squares_scope(data: &[i32]) -> i32 {
    let num_threads = 4;
    let chunk_size = data.len().div_ceil(num_threads); // (len + n - 1) / n

    // thread::scope гарантирует, что все потоки внутри него 
    // завершатся до закрывающей скобки '}'
    thread::scope(|s| {
        let mut handles = Vec::with_capacity(num_threads);

        for chunk in data.chunks(chunk_size) {
            // Мы передаем ссылку `chunk` (&[i32]) внутрь потока.
            // Это разрешено ТОЛЬКО внутри scope, потому что Rust знает:
            // данные (data) точно переживут эти потоки.
            handles.push(s.spawn(move || {
                chunk.iter().map(|&x| x * x).sum::<i32>()
            }));
        }

        // Собираем результаты
        handles.into_iter().map(|h| h.join().unwrap()).sum()
    })
}

```

```rust
use std::sync::Arc;
use std::thread;
use std::iter::Sum;
use std::ops::Mul;

// T: Copy + Mul<Output=T> + Sum + Send + Sync + 'static
// Copy - чтобы числа копировались (дешево)
// Mul - чтобы умножать
// Sum - чтобы складывать
// Send + Sync - чтобы передавать между потоками
// 'static - нужно для thread::spawn (если без scope)

pub fn sum_squares_generic<T>(data: Vec<T>) -> T 
where 
    T: Copy + Mul<Output = T> + Sum + Send + Sync + 'static
{
    let num_threads = thread::available_parallelism()
        .map(|n| n.get())
        .unwrap_or(1);
        
    // Zero-copy: превращаем Vec<T> в Arc<[T]>
    // Это дешево, не копирует данные (просто меняет метаданные аллокации)
    let data_arc: Arc<[T]> = Arc::from(data); 

    let chunk_size = data_arc.len().div_ceil(num_threads);
    let mut handles = Vec::with_capacity(num_threads);

    for i in 0..num_threads {
        let data_ref = Arc::clone(&data_arc);
        
        handles.push(thread::spawn(move || {
            let start = i * chunk_size;
            let end = std::cmp::min(start + chunk_size, data_ref.len());
			
            if start >= data_ref.len() { 
                // Нам нужно вернуть "ноль" (нейтральный элемент суммы).
                // Так как T - дженерик, мы не можем просто написать 0.
                // Хитрость: сумма пустого итератора дает ноль типа T.
                return std::iter::empty::<T>().sum(); 
            }

            data_ref[start..end].iter()
                .map(|&x| x * x)
                .sum()
        }));
    }

    handles.into_iter().map(|h| h.join().unwrap()).sum()
}

```

