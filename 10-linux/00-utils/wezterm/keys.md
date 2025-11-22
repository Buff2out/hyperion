## Как это работает

## 1. **Copy Mode** (аналог Helix selection/view mode)

**Вход:**[](https://dev.to/lovelindhoni/make-wezterm-mimic-tmux-5893)

​

- **Ctrl+Shift+X** — войти в Copy Mode
    
- Или настрой LEADER (например, **Ctrl+Space**, затем **Space**)
    

**Движение (как в Helix):**[](https://github.com/wez/wezterm/issues/4471)

​

- `h/j/k/l` — стрелки
    
- `w/b/e` — прыжки по словам
    
- `0/$` — начало/конец строки
    
- `g/G` — начало/конец файла
    
- `Ctrl-d/u` — полстраницы вниз/вверх
    
- `Ctrl-f/b` — страница вниз/вверх
    

**Выделение:**[](https://github.com/wezterm/wezterm/issues/993)

​

- `v` → char-wise selection (как в Helix)
    
- `V` → line-wise selection
    
- `Ctrl-v` → block selection
    

**Копирование:**[](https://github.com/wezterm/wezterm/issues/993)

​

- `y` → скопировать и выйти (как в Helix/Vim)
    

**Выход:**

- `Escape` или `q` или `Ctrl-c`
    

## 2. **QuickSelect** (аналог Helix multiple cursors)

**Вход:**[](https://dev.to/burnskp/wezterm-quickselect-51gh)

​

- **LEADER+f** (если настроил LEADER)
    

**Что делает:**

- Показывает **label'ы** на всех совпадениях regex паттернов[](https://dev.to/burnskp/wezterm-quickselect-51gh)
    

- ​
    
- Печатаешь label → выделяется и копируется