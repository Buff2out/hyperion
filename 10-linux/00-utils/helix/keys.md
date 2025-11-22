**:w** или **:write**
**:bc** или **:buffer-close**

Отлично! В Helix есть мощная LSP-интеграция для навигации по коду. Вот полный гайд:

## Переход к определению и обратно

## Основные команды навигации (goto mode)

Нажми **g** затем:[helix-editor+2](https://docs.helix-editor.com/keymap.html)​

**К определению:**

- **gd** — Go to **definition** (переход к определению функции/переменной)[helix-editor+2](https://docs.helix-editor.com/commands.html)​
    
- **gy** — Go to **type definition** (переход к определению типа)[huqingye-1798.xlog+1](https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-)​
    
- **gi** — Go to **implementation** (переход к реализации трейта/интерфейса)[helix-editor+1](https://docs.helix-editor.com/keymap.html)​
    

**К использованиям:**

- **gr** — Go to **references** (показать все места, где используется функция)[kapeli+2](https://kapeli.com/cheat_sheets/Helix.docset/Contents/Resources/Documents/index)​
    

## Возврат назад (Jumplist)

После перехода по `gd`, Helix автоматически сохраняет твою предыдущую позицию в **jumplist** (список переходов):[helix-editor](https://docs.helix-editor.com/master/jumplist.html)​youtube​

- **Ctrl+o** — jump **Out** (вернуться назад по jumplist)[reddit+1](https://www.reddit.com/r/HelixEditor/comments/1hhae1n/undo_go_to_definition/)​youtube​
    
- **Ctrl+i** — jump **In** (вернуться вперёд по jumplist)youtube​[helix-editor](https://docs.helix-editor.com/master/jumplist.html)​
    
- **Ctrl+s** — вручную сохранить текущую позицию в jumplist[helix-editor](https://docs.helix-editor.com/master/jumplist.html)​youtube​
    

**Пример воркфлоу:**

1. Ты на вызове функции `calculate_sum()`
    
2. Жмёшь **gd** → переходишь к определению
    
3. Смотришь код, делаешь правки
    
4. Жмёшь **Ctrl+o** → возвращаешься к вызову
    
5. Если нужно снова к определению — **Ctrl+i**
    

## Просмотр всего jumplist

**Space+j** — открыть picker со всеми сохранёнными переходамиyoutube​[helix-editor](https://docs.helix-editor.com/master/jumplist.html)​

Это интерактивное меню, где видны все позиции, куда ты прыгал. Можешь выбрать любую стрелками и нажать Enter.youtube​

## Навигация между вызовами функции

**Найти все использования:**

1. Встань на имя функции
    
2. Нажми **gr** (goto references)[kapeli+1](https://kapeli.com/cheat_sheets/Helix.docset/Contents/Resources/Documents/index)​
    
3. Откроется picker со всеми местами, где функция вызывается
    
4. Используй стрелки для выбора, Enter для перехода[huqingye-1798.xlog](https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-)​
    

**Переход между функциями в файле:**

- **]f** — следующая функция (next function)[helix-editor+1](https://docs.helix-editor.com/keymap.html)​
    
- **[f** — предыдущая функция (previous function)[huqingye-1798.xlog+1](https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-)​
    

Это работает через Tree-sitter (TS), не требует LSP.[helix-editor+1](https://docs.helix-editor.com/keymap.html)​

## Навигация по диагностике (ошибки/предупреждения)

Бонус для дебага:[huqingye-1798.xlog+1](https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-)​

- **]d** — следующая ошибка/предупреждение
    
- **[d** — предыдущая ошибка
    
- **[D** — первая ошибка в файле
    
- **]D** — последняя ошибка в файле
    

## Полный workflow для Rust-разработки

**Сценарий 1: Изучение чужого кода**

text

`1. Встал на функцию → gd (перешёл к определению) 2. Посмотрел типы → gy (перешёл к type definition) 3. Хочу вернуться → Ctrl+o (назад к вызову) 4. Хочу посмотреть все вызовы → gr (список references) 5. Выбрал другой вызов → Enter 6. Возвращаюсь к первому → Space+j (jumplist picker)`

**Сценарий 2: Рефакторинг**

text

`1. Встал на функцию → gr (показать все вызовы) 2. Перешёл к первому → Enter 3. Сделал правки → Ctrl+s (сохранил позицию в jumplist) 4. Следующий вызов → выбрал из picker 5. Вернулся к предыдущему → Ctrl+o`

**Сценарий 3: Исследование трейтов**

text

`1. Встал на трейт → gy (type definition) 2. Хочу увидеть реализации → gi (implementations) 3. Picker показал все impl блоки → выбираю нужный 4. Возврат → Ctrl+o`

## Горячие клавиши для закладок (опционально)

Если хочешь быстро прыгать между важными местами, используй **marks** (закладки):[helix-editor](https://docs.helix-editor.com/remapping.html)​

- **m** затем **буква** — установить закладку (например, `ma` для закладки 'a')
    
- **` ** (backtick) затем **буква** — перейти к закладке (например, `` ` a``)
    

Это полезно для долгой работы над одним участком кода.[helix-editor](https://docs.helix-editor.com/remapping.html)​

1. [https://docs.helix-editor.com/keymap.html](https://docs.helix-editor.com/keymap.html)
2. [https://docs.helix-editor.com/commands.html](https://docs.helix-editor.com/commands.html)
3. [https://kapeli.com/cheat_sheets/Helix.docset/Contents/Resources/Documents/index](https://kapeli.com/cheat_sheets/Helix.docset/Contents/Resources/Documents/index)
4. [https://www.reddit.com/r/HelixEditor/comments/1hhae1n/undo_go_to_definition/](https://www.reddit.com/r/HelixEditor/comments/1hhae1n/undo_go_to_definition/)
5. [https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-](https://huqingye-1798.xlog.app/Helix-an-jian-ying-she--zhuan-)
6. [https://docs.helix-editor.com/master/jumplist.html](https://docs.helix-editor.com/master/jumplist.html)
7. [https://www.youtube.com/watch?v=B3vQDHzsU0g](https://www.youtube.com/watch?v=B3vQDHzsU0g)
8. [https://docs.helix-editor.com/remapping.html](https://docs.helix-editor.com/remapping.html)
9. [https://github.com/helix-editor/helix/issues/3167](https://github.com/helix-editor/helix/issues/3167)
10. [https://zed.dev/docs/key-bindings](https://zed.dev/docs/key-bindings)

В Helix для удаления строки используется **двухнажатийная комбинация** (не одна клавиша, как в некоторых редакторах):

## Основная команда

**x d**

- **x** — выделить текущую строку целиком
    
- **d** — удалить выделение
    

Это самый быстрый и стандартный способ в Helix.[](https://pikabu.ru/story/osnovyi_helix_mini_gayd_13142410)

​

## Удаление нескольких строк

Если нужно удалить несколько строк подряд:

- **5x d** — удалить 5 строк (начиная с текущей вниз)
    
- **x 3k d** — выделить строку, расширить на 3 строки вверх, удалить
    

## Удаление и переход в режим вставки

**x c** — удалить строку и сразу войти в insert mode (аналог vim's `cc`)

shift + A - это сразу войти в режим i