В Helix управление буферами очень удобно. Вот основные команды и действия, чтобы:

---

## Как открыть новый буфер

- Открыть файл в новом буфере:
    
    text
    
    `:open filename`
    
    или клавиша быстрого вызова:
    
    text
    
    `<space> f`
    
    Вызовет **file picker**, где можно выбрать или ввести имя файла.
    
- Открыть новый пустой (scratch) буфер:
    
    text
    
    `:new`
    
    Можно использовать также `:vnew` (вертикальный сплит) или `:hsplit` (горизонтальный сплит).
    

---

## Как переключаться между буферами

- Открыть список буферов (buffer picker):
    
    text
    
    `<space> b`
    
    Навигация стрелками для выбора.
    
- Следующий буфер:
    
    text
    
    `gn`
    
- Предыдущий буфер:
    
    text
    
    `gp`
    
- Перейти к конкретному буферу по номеру (если есть номера в конфиге):
    
    text
    
    `:buffer N`
    

---

## Как закрыть буфер

- Закрыть текущий буфер:
    
    text
    
    `:buffer-close`
    
    или короче
    
    text
    
    `bc`
    
- Закрыть все другие буферы кроме текущего:
    
    text
    
    `:buffer-close-others`
    
    или
    
    text
    
    `bca`
    
- Закрыть буфер с сохранением:
    
    text
    
    `:write-buffer-close`
    
    или
    
    text
    
    `wbc`
    

---

## Полезные команды для буферов

|Команда|Назначение|
|---|---|
|`<space> b`|Открыть picker буферов|
|`gn`|Следующий буфер|
|`gp`|Предыдущий буфер|
|`bc`|Закрыть текущий буфер|
|`bca`|Закрыть все, кроме текущего|
|`:open filename`|Открыть файл в новом буфере|
|`:new`|Новый пустой буфер (scratch)|
|`wbc`|Сохранить и закрыть буфер|

---

## Быстрая шпаргалка по буферам

|Действие|Команда|
|---|---|
|Открыть файл в буфере|`<space> f` / `:open filename`|
|Переключиться|`gn` / `gp` / `<space> b`|
|Закрыть буфер|`bc`|
|Закрыть другие буферы|`bca`|
|Новый пустой буфер|`:new`|
|Сохранить и закрыть|`wbc`|

---

Это покрывает весь базовый и продвинутый workflow вокруг работы с буферами в Helix, удобно и быстро! 🚀

1. [https://github.com/helix-editor/helix/discussions/5865](https://github.com/helix-editor/helix/discussions/5865)
2. [https://github.com/helix-editor/helix/issues/475](https://github.com/helix-editor/helix/issues/475)
3. [https://github.com/helix-editor/helix/discussions/10430](https://github.com/helix-editor/helix/discussions/10430)
4. [https://rushter.com/blog/helix-editor/](https://rushter.com/blog/helix-editor/)
5. [https://www.youtube.com/watch?v=2P8GP1LEfKE](https://www.youtube.com/watch?v=2P8GP1LEfKE)
6. [https://github.com/helix-editor/helix/discussions/6057](https://github.com/helix-editor/helix/discussions/6057)
7. [https://www.reddit.com/r/HelixEditor/comments/1i2m50c/i_keep_closing_the_whole_editor_when_i_want_to/](https://www.reddit.com/r/HelixEditor/comments/1i2m50c/i_keep_closing_the_whole_editor_when_i_want_to/)
8. [https://github.com/helix-editor/helix/discussions/6983](https://github.com/helix-editor/helix/discussions/6983)
9. [https://docs.helix-editor.com/commands.html](https://docs.helix-editor.com/commands.html)
10. [https://www.youtube.com/watch?v=S4tTZjz-NTc](https://www.youtube.com/watch?v=S4tTZjz-NTc)