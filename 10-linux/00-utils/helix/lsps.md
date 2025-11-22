Отлично! Держи полный список **ключевых LSP** для всех основных языков — это всё, что нужно для профессиональной разработки в Helix.[helix-editor+1](https://docs.helix-editor.com/lang-support.html)​

---

## 🦀 Systems Programming

text

```
# Rust rust-analyzer           # Официальный LSP от Rust Foundation # C/C++ clangd                  # LLVM-based LSP (рекомендуется) # или ccls              # Альтернатива clangd # Zig zls                     # Официальный Zig Language Server # Go gopls                   # Официальный LSP от Google # D serve-d                 # D Language Server
```
---

## 🌐 Web Development

text

`# JavaScript/TypeScript nodePackages.typescript-language-server # или nodePackages.vscode-langservers-extracted  # HTML/CSS/JSON тоже # Deno (альтернатива Node) deno                    # Deno встроенный LSP # Vue.js nodePackages.volar      # Vue 3 LSP # Svelte nodePackages.svelte-language-server # Tailwind CSS nodePackages."@tailwindcss/language-server"`

---

## 🐍 Scripting Languages

text

`# Python python3Packages.python-lsp-server  # pylsp (рекомендуется) # или pyright                       # Microsoft LSP (быстрее) # или basedpyright                  # Fork pyright # Ruby solargraph              # Ruby LSP # Lua lua-language-server     # sumneko_lua # Perl perlnavigator          # Perl LSP`

---

## ⚙️ DevOps & Config

text

`# Nix nil                     # Nix LSP (рекомендуется) # или nixd              # Альтернатива с лучшей поддержкой flakes # YAML nodePackages.yaml-language-server # TOML taplo                   # Taplo LSP # JSON nodePackages.vscode-json-languageserver # Terraform terraform-ls            # HashiCorp Terraform LSP # Docker nodePackages.dockerfile-language-server-nodejs # Ansible ansible-language-server`

---

## 🎨 Functional & Academic

text

`# Haskell haskell-language-server  # HLS # OCaml ocamlPackages.ocaml-lsp  # ocamllsp # Elixir elixir-ls               # Elixir Language Server # Erlang erlang-ls               # Erlang LS # Clojure clojure-lsp             # Clojure LSP # Racket racket-langserver       # Racket LS # PureScript nodePackages.purescript-language-server`

---

## 🏢 Enterprise & JVM

text

`# Java jdt-language-server     # Eclipse JDT # Kotlin kotlin-language-server # Scala metals                  # Scalameta LSP # Groovy groovy-language-server`

---

## 📝 Markup & Data

text

`# Markdown marksman                # Markdown LSP # LaTeX texlab                  # LaTeX LSP # GraphQL nodePackages.graphql-language-service-cli # Protobuf buf-language-server     # Protobuf LSP`

---

## 🔥 Special Mention

text

`# Helix-GPT (AI completion) helix-gpt               # OpenAI/Copilot в Helix # Emmet (HTML/CSS snippets) emmet-ls                # Emmet expansion`

---

## 📋 Для копирования в Obsidian

text

``# Language Servers для Helix ## Systems - `rust-analyzer` — Rust - `clangd` — C/C++ - `zls` — Zig - `gopls` — Go ## Web - `typescript-language-server` — JS/TS - `vscode-langservers-extracted` — HTML/CSS/JSON - `volar` — Vue.js - `svelte-language-server` — Svelte - `@tailwindcss/language-server` — Tailwind ## Scripting - `python-lsp-server` — Python (или `pyright`) - `solargraph` — Ruby - `lua-language-server` — Lua ## DevOps - `nil` — Nix (или `nixd`) - `yaml-language-server` — YAML - `taplo` — TOML - `terraform-ls` — Terraform - `dockerfile-language-server` — Docker ## Functional - `haskell-language-server` — Haskell - `ocaml-lsp` — OCaml - `elixir-ls` — Elixir - `clojure-lsp` — Clojure ## JVM - `jdt-language-server` — Java - `kotlin-language-server` — Kotlin - `metals` — Scala ## Markup - `marksman` — Markdown - `texlab` — LaTeX - `graphql-language-service-cli` — GraphQL ## Bonus - `helix-gpt` — AI completion (OpenAI/Copilot) - `emmet-ls` — HTML/CSS snippets``

---

## Проверка установленных LSP

bash

`hx --health`

Покажет, какие LSP найдены и работают.[github+1](https://github.com/helix-editor/helix/discussions/7067)​

1. [https://docs.helix-editor.com/lang-support.html](https://docs.helix-editor.com/lang-support.html)
2. [https://langserver.org](https://langserver.org)
3. [https://github.com/helix-editor/helix/discussions/7067](https://github.com/helix-editor/helix/discussions/7067)
4. [https://discourse.nixos.org/t/helix-lsp-servers/34833](https://discourse.nixos.org/t/helix-lsp-servers/34833)
5. [https://github.com/helix-editor/helix/discussions/8474](https://github.com/helix-editor/helix/discussions/8474)
6. [https://www.reddit.com/r/NixOS/comments/1gu5q3q/what_lsp_do_you_use/](https://www.reddit.com/r/NixOS/comments/1gu5q3q/what_lsp_do_you_use/)
7. [https://en.wikipedia.org/wiki/Language_Server_Protocol](https://en.wikipedia.org/wiki/Language_Server_Protocol)
8. [https://docs.codelite.org/plugins/lsp/](https://docs.codelite.org/plugins/lsp/)
9. [https://mynixos.com/nixpkgs/package/helix-gpt](https://mynixos.com/nixpkgs/package/helix-gpt)
10. [https://github.com/isaacphi/mcp-language-server](https://github.com/isaacphi/mcp-language-server)