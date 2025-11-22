# Полная Шпаргалка Клавиш Helix

## Основные Режимы (Mode Keys)

|Клавиша|Действие|
|---|---|
|`i`|Insert mode (вставка перед курсором)|
|`a`|Append (вставка после курсора)|
|`I`|Insert в начале строки|
|`A`|Append в конце строки|
|`o`|Open line below (новая строка ниже)|
|`O`|Open line above (новая строка выше)|
|`v`|Select/extend mode (режим выделения)|
|`Esc`|Вернуться в Normal mode|
|`:`|Command mode (командный режим)|

## Движение (Movement)

|Клавиша|Действие|
|---|---|
|`h`|Влево на символ|
|`j`|Вниз на строку|
|`k`|Вверх на строку|
|`l`|Вправо на символ|
|`w`|Следующее слово (начало)|
|`b`|Предыдущее слово (начало)|
|`e`|Следующее слово (конец)|
|`W`|Следующее WORD (с пробелами)|
|`B`|Предыдущее WORD|
|`E`|Конец WORD|
|`0`|Начало строки|
|`$`|Конец строки|
|`^`|Первый непробельный символ строки|
|`gg`|Начало файла|
|`ge` или `G`|Конец файла|
|`Ctrl-d`|Полстраницы вниз|
|`Ctrl-u`|Полстраницы вверх|
|`Ctrl-f`|Страница вниз (PageDown)|
|`Ctrl-b`|Страница вверх (PageUp)|
|`%`|Найти парную скобку|

## Выделение (Selection)

|Клавиша|Действие|
|---|---|
|`x`|Выделить строку целиком|
|`X`|Выделить строку (без перевода строки)|
|`w`|Выделить слово вперёд|
|`b`|Выделить слово назад|
|`%`|Выделить весь файл|
|`s`|Split selection (поиск по regex)|
|`;`|Collapse selection (убрать выделение)|
|`,`|Remove primary selection|
|`Alt-,`|Remove all secondary selections|
|`C`|Duplicate cursor down|
|`Alt-C`|Duplicate cursor up|
|`&`|Align selections|

## Match Mode (m + клавиша)

|Клавиша|Действие|
|---|---|
|`mm`|Goto matching bracket|
|`ms`|Surround add (добавить окружение)|
|`mr`|Surround replace|
|`md`|Surround delete|
|`mi(`|Select inside parentheses|
|`ma(`|Select around parentheses|
|`mi{`|Select inside braces|
|`ma{`|Select around braces|
|`mi"`|Select inside quotes|
|`ma"`|Select around quotes|
|`miw`|Select inside word|
|`maw`|Select around word|
|`mif`|Select inside function|
|`maf`|Select around function|

## Редактирование (Edit)

|Клавиша|Действие|
|---|---|
|`c`|Change (удалить выделение и войти в insert)|
|`d`|Delete (удалить выделение)|
|`y`|Yank (копировать)|
|`p`|Paste after cursor|
|`P`|Paste before cursor|
|`R`|Replace with yanked text|
|`u`|Undo|
|`U`|Redo|
|`r`|Replace character|
|`~`|Toggle case (upper/lower)|
|`` ` ``|Convert to lowercase|
|` Alt-`` ` ``|Convert to uppercase|
|`>`|Indent (вправо)|
|`<`|Unindent (влево)|
|`=`|Format selection (форматирование)|
|`J`|Join lines|
|`Alt-J`|Join lines with space|
|`K`|Keep selections matching regex|
|`Alt-K`|Remove selections matching regex|

## Поиск (Search)

|Клавиша|Действие|
|---|---|
|`/`|Search forward|
|`?`|Search backward|
|`n`|Next search match|
|`N`|Previous search match|
|`*`|Search current selection forward|

## Goto Mode (g + клавиша)

|Клавиша|Действие|
|---|---|
|`gh`|Goto line start|
|`gl`|Goto line end|
|`gs`|Goto first non-whitespace|
|`ge`|Goto last line|
|`gg`|Goto first line|
|`gt`|Goto window top|
|`gc`|Goto window center|
|`gb`|Goto window bottom|
|`gd`|Goto definition (LSP)|
|`gy`|Goto type definition|
|`gr`|Goto references|
|`gi`|Goto implementation|
|`ga`|Goto last accessed file|
|`gm`|Goto last modified file|
|`gn`|Goto next buffer|
|`gp`|Goto previous buffer|
|`g.`|Goto last modification|

## View Mode (z + клавиша)

|Клавиша|Действие|
|---|---|
|`zz`|Center cursor vertically|
|`zt`|Align cursor to top|
|`zb`|Align cursor to bottom|
|`zm`|Align cursor to middle|
|`zj`|Scroll down (одна строка)|
|`zk`|Scroll up (одна строка)|
|`Z`|Enter **sticky view mode** (прокрутка без движения курсора)|
|`Ctrl-d`|Scroll half page down|
|`Ctrl-u`|Scroll half page up|

## Window Mode (Ctrl-w + клавиша)

|Клавиша|Действие|
|---|---|
|`Ctrl-w v`|Vertical split|
|`Ctrl-w s`|Horizontal split|
|`Ctrl-w w`|Switch to next window|
|`Ctrl-w h`|Goto left window|
|`Ctrl-w j`|Goto bottom window|
|`Ctrl-w k`|Goto top window|
|`Ctrl-w l`|Goto right window|
|`Ctrl-w q`|Close current window|
|`Ctrl-w o`|Close all windows except current|
|`Ctrl-w H`|Swap window left|
|`Ctrl-w J`|Swap window down|
|`Ctrl-w K`|Swap window up|
|`Ctrl-w L`|Swap window right|

## Space Mode (Space + клавиша)

|Клавиша|Действие|
|---|---|
|`Space f`|File picker (открыть файл)|
|`Space F`|File picker (current directory)|
|`Space b`|Buffer picker (открытые файлы)|
|`Space k`|Hover (показать документацию LSP)|
|`Space s`|Symbol picker (функции/структуры)|
|`Space S`|Workspace symbol picker|
|`Space a`|Code actions (LSP)|
|`Space r`|Rename symbol (LSP)|
|`Space d`|Diagnostics picker (ошибки)|
|`Space D`|Workspace diagnostics|
|`Space h`|Highlight all occurrences|
|`Space /`|Global search (ripgrep)|
|`Space ?`|Command palette|
|`Space y`|Yank (copy) to clipboard|
|`Space p`|Paste from clipboard|
|`Space P`|Paste from clipboard before|
|`Space R`|Replace selections with clipboard|
|`Space w`|Save file (:write)|
|`Space q`|Quit (:quit)|

## LSP Специфичные

|Клавиша|Действие|
|---|---|
|`K`|Hover documentation|
|`gd`|Goto definition|
|`gy`|Goto type definition|
|`gr`|Goto references|
|`gi`|Goto implementation|
|`]d`|Next diagnostic (ошибка)|
|`[d`|Previous diagnostic|
|`Space a`|Code actions|
|`Space r`|Rename symbol|

## Insert Mode Специфичные

|Клавиша|Действие|
|---|---|
|`Ctrl-x`|Autocomplete|
|`Ctrl-w`|Delete word backward|
|`Ctrl-u`|Delete to line start|
|`Ctrl-k`|Delete to line end|
|`Ctrl-h`|Backspace|
|`Ctrl-d`|Delete forward|
|`Ctrl-j`|Insert newline|

## Диагностика и Отладка

|Клавиша|Действие|
|---|---|
|`]d`|Next diagnostic|
|`[d`|Previous diagnostic|
|`]g`|Next git change|
|`[g`|Previous git change|
|`]f`|Next function|
|`[f`|Previous function|
|`]c`|Next class|
|`[c`|Previous class|

## Дополнительные

|Клавиша|Действие|
|---|---|
|`.`|Repeat last change|
|`q`|Record macro (например, `qa` → запись в регистр a)|
|`Q`|Replay macro (например, `@a` в Vim, но в Helix просто `Q`)|
|`@`|Shell pipe (выполнить shell команду)|
|`!`|Shell pipe replace|
|`Alt-!`|Shell append output|
|`$`|Shell pipe each selection|

---

## Командный Режим (после `:`)

|Команда|Действие|
|---|---|
|`:w`|Write (save) file|
|`:q`|Quit|
|`:wq` или `:x`|Write and quit|
|`:q!`|Force quit (без сохранения)|
|`:open file.txt`|Open file|
|`:buffer-close`|Close current buffer|
|`:reload`|Reload file from disk|
|`:set key value`|Set option|
|`:vsplit`|Vertical split|
|`:hsplit`|Horizontal split|
|`:sh command`|Run shell command|
|`:insert-output command`|Insert shell command output|
|`:pipe command`|Pipe selection through command|

---

Эта шпаргалка покрывает **95% ежедневного использования** Helix. Сохрани в Obsidian и распечатай — первые недели будешь часто подглядывать, потом всё в мышечную память! 🚀

**Ключевое отличие от Vim:** в Helix **сначала выделение** (selection-first), **потом действие**. Это делает редактирование **предсказуемым** — ты всегда видишь, что изменишь, перед тем как это сделать.

1. [https://docs.helix-editor.com/keymap.html](https://docs.helix-editor.com/keymap.html)
2. [https://github.com/helix-editor/helix/discussions/12270](https://github.com/helix-editor/helix/discussions/12270)
3. [https://www.reddit.com/r/HelixEditor/comments/11hvikl/in_editor_keyboard_shortcuts_cheatsheet/](https://www.reddit.com/r/HelixEditor/comments/11hvikl/in_editor_keyboard_shortcuts_cheatsheet/)
4. [https://docs.helix-editor.com/remapping.html](https://docs.helix-editor.com/remapping.html)
5. [https://github.com/helix-editor/helix/issues/4864](https://github.com/helix-editor/helix/issues/4864)
6. [https://news.ycombinator.com/item?id=40492300](https://news.ycombinator.com/item?id=40492300)
7. [https://blog.ohheybrian.com/2023/01/this-was-written-with-helix](https://blog.ohheybrian.com/2023/01/this-was-written-with-helix)
8. [https://jonathan-frere.com/posts/helix/](https://jonathan-frere.com/posts/helix/)