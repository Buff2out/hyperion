## Механизм работы `sops exec-env secrets.yaml`

**SOPS exec-env** расшифровывает зашифрованный YAML/JSON файл и передаёт все key-value пары как environment variables (переменные окружения) в **child process** (дочерний процесс), который вы указываете в команде.[temofeev+1](https://temofeev.ru/info/articles/mozilla-sops-dlya-upravleniya-sekretami-v-gite/)​

## Архитектура изоляции

Команда работает по следующей схеме:[github](https://github.com/getsops/sops)​

bash

`sops exec-env secrets.yaml 'cargo run'`

1. **SOPS расшифровывает** `secrets.yaml` в памяти (никогда не пишет plaintext на диск)[github](https://github.com/getsops/sops)​
    
2. **Fork текущего процесса** — создаётся child process через Unix `fork()` syscall
    
3. **Устанавливает environment variables** из расшифрованного файла в child process через `execve()` syscall
    
4. **Запускает указанную команду** (`cargo run`) с загруженными переменными[github](https://github.com/getsops/sops)​
    

Это **стандартный Unix process isolation механизм**, а не специальный container или namespace. Environment variables наследуются только в одну сторону: parent → child, но не обратно.[github](https://github.com/getsops/sops)​

## Пример из документации

bash

`# Проверяем содержимое $ sops decrypt out.json {     "database_password": "jf48t9wfw094gf4nhdf023r",     "AWS_ACCESS_KEY_ID": "AKIAIOSFODNN7EXAMPLE" } # Запускаем команду с секретами $ sops exec-env out.json 'echo secret: $database_password' secret: jf48t9wfw094gf4nhdf023r  # Запускаем shell с секретами $ sops exec-env out.json 'sh' sh-3.2# echo $database_password jf48t9wfw094gf4nhdf023r  # Выходим из shell sh-3.2$ exit # Секрет недоступен в parent shell $ echo your password: $database_password your password:`

## Уничтожение видимости переменных

Переменные уничтожаются **автоматически при завершении child процесса**. Это происходит в момент:[github](https://github.com/getsops/sops)​

1. **Нормального завершения команды** — `cargo run` выполнился и вернул exit code
    
2. **Crash процесса** — процесс убит сигналом (SIGTERM, SIGKILL)
    
3. **Явного exit** — вы вызвали `exit` в shell session
    

Kernel автоматически очищает memory space процесса при его завершении, включая environment variables. Секреты не утекают в parent shell, не попадают в `/proc/<pid>/environ` после завершения, не записываются в swap (если использовать `mlock`).[github](https://github.com/getsops/sops)​

## Преимущества для вашего backend проекта

В вашем justfile используется паттерн:[github](https://github.com/getsops/sops)​

text

`sops_run := "sops exec-env secrets.yaml" dev:     @{{sops_run}} 'cargo run'`

Это означает:

- **PostgreSQL credentials** доступны только процессу `cargo run`, но не остаются в shell history
    
- **После Ctrl+C** (завершения backend) переменные мгновенно уничтожаются kernel'ом
    
- **Другие терминалы** не видят секреты — изоляция на уровне process ID
    
- **Docker compose** внутри `sops exec-env` также получает переменные только на время выполнения[github](https://github.com/getsops/sops)​
    

## Сравнение с альтернативами

**Небезопасный способ**:

bash

`export DATABASE_PASSWORD=$(sops -d secrets.yaml | yq .password) cargo run`

Переменная остаётся в текущем shell до `unset` или закрытия терминала, видна в `env` и `/proc/self/environ`.

**Безопасный способ** (ваш текущий):

bash

`sops exec-env secrets.yaml 'cargo run'`

Переменная существует только внутри `cargo run` процесса и автоматически умирает с ним.[temofeev+1](https://temofeev.ru/info/articles/mozilla-sops-dlya-upravleniya-sekretami-v-gite/)​

## NixOS специфика

На NixOS с вашим setup (Hyprland + Wayland) process isolation ещё более строгий через cgroups v2. Каждый терминал в Hyprland работает в отдельном systemd user slice, что даёт дополнительную изоляцию между terminal emulator instances.[github](https://github.com/getsops/sops)​

Для установки SOPS в NixOS:

bash

`nix-env -iA nixpkgs.sops`

Таким образом, `sops exec-env` использует базовый Unix механизм process forking для создания изолированного environment scope, который автоматически уничтожается kernel'ом при exit child процесса — никаких manual cleanup действий не требуется.[temofeev+1](https://temofeev.ru/info/articles/mozilla-sops-dlya-upravleniya-sekretami-v-gite/)​

1. [https://habr.com/ru/articles/590733/](https://habr.com/ru/articles/590733/)
2. [https://temofeev.ru/info/articles/mozilla-sops-dlya-upravleniya-sekretami-v-gite/](https://temofeev.ru/info/articles/mozilla-sops-dlya-upravleniya-sekretami-v-gite/)
3. [https://vc.ru/dev/173060-kubernetes-bezopasnoe-upravlenie-sekretami-s-gitops](https://vc.ru/dev/173060-kubernetes-bezopasnoe-upravlenie-sekretami-s-gitops)
4. [https://habr.com/ru/companies/ru_mts/articles/656351/](https://habr.com/ru/companies/ru_mts/articles/656351/)
5. [https://docs.ensi.tech/devops-guides/principles/mozilla-sops](https://docs.ensi.tech/devops-guides/principles/mozilla-sops)
6. [https://pcnews.ru/blogs/pracem_sekrety_v_repozitorii_s_pomosu_helm_secrets_sops_vault_i_envsubst-1160991.html](https://pcnews.ru/blogs/pracem_sekrety_v_repozitorii_s_pomosu_helm_secrets_sops_vault_i_envsubst-1160991.html)
7. [https://github.com/getsops/sops](https://github.com/getsops/sops)
8. [https://www.reddit.com/r/devops/comments/1eyyqdv/storing_production_secrets_with_sops/?tl=ru](https://www.reddit.com/r/devops/comments/1eyyqdv/storing_production_secrets_with_sops/?tl=ru)
9. [https://client.sbertech.ru/docs/public/DPM/3.7.0/common/documents/installation-guide/installation-k8s_secrets.html](https://client.sbertech.ru/docs/public/DPM/3.7.0/common/documents/installation-guide/installation-k8s_secrets.html)
10. [https://github.com/getsops/sops/issues/1368](https://github.com/getsops/sops/issues/1368)