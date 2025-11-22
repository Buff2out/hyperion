❯ ssh kv-root@176.108.250.130 
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:OM+qtti3S35aXRRkTn4qjtc9to7ykn0nBb5AumOTsi8.
Please contact your system administrator.
Add correct host key in /home/wave/.ssh/known_hosts to get rid of this message.
Offending RSA key in /home/wave/.ssh/known_hosts:35
Host key for 176.108.250.130 has changed and you have requested strict checking.
Host key verification failed.

# SSH Host Key Changed — быстрое решение 🔑

## Что произошло?

Сервер был переустановлен → новый SSH ключ → SSH не доверяет старому

**Это нормально после redeploy!**

---

## Решение (2 способа) ⚡

## Способ 1: Удали старый ключ (быстро)

bash

`# Удали старую запись хоста ssh-keygen -R 176.108.250.130 # Подтверди (yes) # Старый ключ удалён ✅ # Теперь подключайся ssh kv-root@176.108.250.130 # Спросит "Are you sure you want to continue connecting?" # Напиши: yes # Новый ключ сохранится ✅`

## Способ 2: Вручную редактируй known_hosts

bash

`# Открой файл nano ~/.ssh/known_hosts # Найди строку с 176.108.250.130 (по строке 35) # Удали эту строку (Ctrl+K в nano) # Сохрани (Ctrl+O → Enter → Ctrl+X) # Подключись как в способе 1 ssh kv-root@176.108.250.130`

---

## В один лайн 🎯

bash

`ssh-keygen -R 176.108.250.130 && ssh kv-root@176.108.250.130`

**Это всё!** ✅

---

## Что происходит?

text

`ssh-keygen -R 176.108.250.130     ↓ Удаляет старый ED25519 ключ из ~/.ssh/known_hosts     ↓ ssh kv-root@176.108.250.130     ↓ Спрашивает: "Continue? (yes/no)"     ↓ yes     ↓ Сохраняет новый ключ в known_hosts ✅     ↓ Готово!`

---

**Готово? Проверяй второй сервер!** 🚀

Если ещё какие-то ошибки — докладывай!

команда для вставки ssh публичного ключа с локалки: 

`nano /root/.ssh/authorized_keys`