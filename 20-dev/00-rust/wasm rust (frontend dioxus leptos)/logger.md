```rust
use dioxus::prelude::*;
use dioxus_logger::tracing::{Level, info};

fn main() {
  dioxus_logger::init(Level::INFO).expect("logger failed to init");
  dioxus::launch(App);
}

#[component]
fn App() -> Element {
  info!("App rendered");
  rsx! {
    p { "hi" }
  }
}
```

```rust
use dioxus::prelude::*;
use dioxus::logger::tracing::{Level, info};

fn main() {
  dioxus::logger::init(Level::INFO).expect("logger failed to init");
  dioxus::launch(App);
}

#[component]
fn App() -> Element {
  info!("App rendered");
  rsx! {
    p { "hi" }
  }
}
```

```rust
fn main() {
    // Включает стандартный форматер с временем в UTC
    tracing_subscriber::fmt::init();

    tracing::info!("Это сообщение будет с временной меткой.");
}
```

https://crates.io/crates/dioxus-logger