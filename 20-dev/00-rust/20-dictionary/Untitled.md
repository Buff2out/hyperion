```rust
methods:
	.to_lowercase()
	.flat_map()
	.par_iter()
	.par_bridge()
	.map()
	.filter()
	.fold()
	.find()
	.collect()
	.copied()
	.filter_map()
	.windows()
	.any()
	.all()
	.position()
	.fetch_add()
	.load()
	.ends_with()
	.enumerate()
	.saturating_sub()
	.try_fold()
	
	
crates:
	use memchr::memmem;
	
traits:
	AsRef<str>
	PartialEq
	
	
```

|Префикс|Что делает (Технический смысл)|Пример|Стоимость (Cost)|
|---|---|---|---|
|as_|View / Borrow. Бесплатное преобразование типа, работающее с ссылкой.|as_bytes()|Нулевая (Zero-cost)|
|to_|Clone / Allocate. Создает новую копию данных (тяжелая операция).|to_string(),to_vec()|Аллокация памяти|
|into_|Consume. Потребляет (съедает) переменную. После этого старая переменная недоступна.|into_iter()|Обычно дешево (перемещение указателя)|
