Отлично! В Helix есть **несколько способов** увидеть несохранённые изменения:

## 1. Индикатор в статус-баре (самый простой)

**Смотри на статус-бар внизу экрана**:[github+1](https://github.com/helix-editor/helix/issues/4686)​

text

`NOR path/to/file.rs [+]  12:34  rust                      ↑                  несохранённые изменения!`

**`[+]`** (или `[modified]`) означает, что файл изменён но не сохранён.[dev+1](https://dev.to/rajasegar/the-helix-way-36mh)​

Если индикатора нет — файл сохранён ✅

## 2. Git diff gutter (если файл в git)

**Цветные полоски слева от номеров строк**:[helix-editor](https://docs.helix-editor.com/master/editor.html)​

text

`│ 1  fn main() { ~ 2      println!("modified");  ← оранжевая волна = изменено + 3      let x = 5;             ← зелёная полоса = добавлено - 4      // deleted line        ← красная полоса = удалено │ 5  }`

Включено по умолчанию для git-репозиториев.[helix-editor](https://docs.helix-editor.com/master/editor.html)​

## 3. Команды навигации по изменениям

**`]g`** — следующее изменение (goto next change)[helix-editor](https://docs.helix-editor.com/commands.html)​  
**`[g`** — предыдущее изменение (goto prev change)[helix-editor](https://docs.helix-editor.com/commands.html)​  
**`]G`** — последнее изменение (goto last change)[helix-editor](https://docs.helix-editor.com/commands.html)​  
**`[G`** — первое изменение (goto first change)[helix-editor](https://docs.helix-editor.com/commands.html)​

Прыгает курсором между изменёнными строками, чтобы ты мог быстро посмотреть, что менялось.[helix-editor](https://docs.helix-editor.com/commands.html)​

## 4. Проверка при выходе

Если пытаешься выйти с несохранёнными изменениями:

**`:q`** или **`:quit`** — Helix покажет **предупреждение**:

text

`Error: Unsaved changes! Use :q! to force quit.`

**`:q!`** или **`:quit!`** — выйти БЕЗ сохранения (потеря изменений).[helix-editor](https://docs.helix-editor.com/commands.html)​

## 5. Список открытых буферов (buffer line)

Включи **buffer line** в конфиге:[dev](https://dev.to/rajasegar/the-helix-way-36mh)​

text

`# ~/.config/helix/config.toml [editor] bufferline = "always"  # или "multiple"`

Тогда сверху появится панель со всеми открытыми файлами:

text

`file1.rs  file2.rs [+]  file3.c                     ↑                несохранённый файл`

## 6. Diff с сохранённой версией (нет из коробки)

К сожалению, Helix **пока не поддерживает** встроенную команду "show unsaved changes" (как в VSCode или Sublime). Это feature request.[github+1](https://github.com/helix-editor/helix/discussions/8864)​

**Workaround**: используй git diff:

bash

`# В терминале (вне Helix) git diff file.rs`

Или внутри Helix через shell:  
**Space+c** → напечатай `!git diff %` (% = текущий файл)

## Лучшая практика

Комбинируй:

1. **Смотри на `[+]`** в статус-баре — главный индикатор[github](https://github.com/helix-editor/helix/issues/4686)​
    
2. **Используй git diff gutter** — видишь изменения прямо в редакторе[helix-editor](https://docs.helix-editor.com/master/editor.html)​
    
3. **Прыгай по `]g` / `[g`** — быстро проверить все правки[helix-editor](https://docs.helix-editor.com/commands.html)​
    
4. **Включи bufferline** — видеть все несохранённые файлы сверху[dev](https://dev.to/rajasegar/the-helix-way-36mh)​
    

Теперь никогда не потеряешь изменения!

1. [https://github.com/helix-editor/helix/issues/4686](https://github.com/helix-editor/helix/issues/4686)
2. [https://dev.to/rajasegar/the-helix-way-36mh](https://dev.to/rajasegar/the-helix-way-36mh)
3. [https://docs.helix-editor.com/master/editor.html](https://docs.helix-editor.com/master/editor.html)
4. [https://docs.helix-editor.com/commands.html](https://docs.helix-editor.com/commands.html)
5. [https://github.com/helix-editor/helix/discussions/8864](https://github.com/helix-editor/helix/discussions/8864)
6. [https://forum.sublimetext.com/t/improve-the-diff-shown-by-show-unsaved-changes/62198](https://forum.sublimetext.com/t/improve-the-diff-shown-by-show-unsaved-changes/62198)
7. [https://github.com/helix-editor/helix/issues/1125](https://github.com/helix-editor/helix/issues/1125)
8. [https://stackoverflow.com/questions/65874120/see-unsaved-changes-in-vscode](https://stackoverflow.com/questions/65874120/see-unsaved-changes-in-vscode)
9. [https://github.com/helix-editor/helix/issues/7481](https://github.com/helix-editor/helix/issues/7481)
10. [https://github.com/helix-editor/helix/issues/9655](https://github.com/helix-editor/helix/issues/9655)