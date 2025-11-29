```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: u8,
}

impl Default for Person {
    fn default() -> Self {
        Self {
            name: String::from("John"),
            age: 30,
        }
    }
}
```

```rust
impl From<&str> for Person {
    fn from(s: &str) -> Self {
        s.split_once(',')
            .filter(|(name, _)| !name.is_empty())
            .and_then(|(name, age_str)| {
                age_str.parse::<u8>().ok().map(|age| (name, age))
            })
            .map(|(name, age)| Person {
                name: name.to_string(),
                age,
            })
            .unwrap_or_default()
    }
}
```

как это написать через match:

```rust
impl From<&str> for Person {
    fn from(s: &str) -> Self {
        let Some((name, age_str)) = s.split_once(',') else {
            return Person::default();
        };
        if name.is_empty() {
            return Person::default();
        }
        let Ok(age) = age_str.parse::<u8>() else {
            return Person::default();
        };
        Person {
            name: name.to_string(),
            age,
        }
    }
}
```

