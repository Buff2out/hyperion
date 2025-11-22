

## Как компилировались самые первые ядра операционных систем

**Ключевой момент:** До Linux было множество операционных систем, и все они встали перед одной и той же проблемой bootstrap.

---

## Цепочка: От машинного кода до Multics и Unix

## Самое начало (1940s-1950s): Машинный код вручную

Первые компьютеры (ENIAC, EDSAC) **не программировались в привычном смысле**. Они либо перепроводились вручную (ENIAC), либо использовались прямые переключатели и перфокарты. Программы писались в **чистом машинном коде в двоичной или шестнадцатеричной форме**, и люди вручную рассчитывали адреса ветвлений.[reddit+1](https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/)​

Это был настоящий ад: один из программистов того времени вспоминал, как писал монитор для ROM, записывая **hex-коды вручную в тетрадь**.[reddit](https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/)​

## 1950s: Ассемблеры и первые компиляторы

Затем появились **ассемблеры** — программы, которые переводят мнемоничные инструкции (MOVE, ADD) в машинный код. Первые ассемблеры были написаны **вручную в машинном коде**.[reddit](https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/)​

После этого, примерно в начале 1950-х, пришли первые компиляторы высокого уровня. Они тоже были написаны в ассемблере, который уже существовал.[reddit](https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/)​

## Multics (1964-1969): Первая сложная ОС на высокоуровневом языке

Bell Labs, MIT и General Electric разрабатывали **Multics** для GE-645 mainframe. Это была революционная система — она была написана на **PL/I** (высокоуровневом языке). Multics показала, что операционные системы можно писать не только на ассемблере.[wikipedia+1](https://en.wikipedia.org/wiki/History_of_Unix)​

Однако Multics стала настолько сложной и дорогой, что Bell Labs вышла из проекта в 1969 году.

## Unix (1969-1970): Bootstrapping на PDP-7 и PDP-11

Когда Ken Thompson начинал писать Unix, он столкнулся с классической bootstrap-проблемой. Вот как это выглядело:[tuhs](https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html)​

1. **Сначала макросы**: Thompson **не писал на самом PDP-7**, а использовал **GEMAP ассемблер на машине GE-635**. Он писал макросы для этого ассемблера, которые потом постпроцессор переводил в **бумажную ленту**, читаемую PDP-7. Эти ленты физически переносились на PDP-7 для тестирования.[tuhs](https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html)​
    
2. **Простой kernel + инструменты**: Thompson вручную написал на ассемблере **примитивный Unix kernel, редактор, ассемблер и простую shell**. Всё это — на ассемблере PDP-7.[tuhs](https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html)​
    
3. **Self-hosting на PDP-11**: Когда Bell Labs получили PDP-11, Thompson **перекодировал Unix kernel и базовые команды в PDP-11 assembly**. Только тогда система смогла **компилировать саму себя** на своей же машине.[tuhs](https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html)​
    
4. **Переход на C (1973)**: К 1973 году Dennis Ritchie и Ken Thompson переписали **большую часть Unix kernel на C** — язык, который они сами разработали. Это была революция: **ядро операционной системы на высокоуровневом языке**, вопреки общему убеждению, что это невозможно.[startertutorials+2](https://www.startertutorials.com/blog/history-of-unix.html)​
    

---

## Общий паттерн bootstrap

Все первые операционные системы проходили через этот цикл:

|Уровень|Способ компиляции|
|---|---|
|**Машинный код**|Люди вручную в двоичной/hex форме, перфокарты, переключатели|
|**Ассемблеры**|Написаны вручную в машинном коде|
|**Первые компиляторы высокого уровня**|Написаны на ассемблере|
|**Операционные системы**|Написаны на ассемблере (или на высокоуровневом языке после C)|
|**Self-hosting**|Система компилирует саму себя|

---

## Почему Unix смог перейти на C, а Multics — нет

Multics, будучи огромной системой на mainframe-е, была слишком сложна для переписывания. Unix был **сознательно** сделан **маленьким и простым**, что позволило Thompson и Ritchie переписать его на C за одно лето (1973). Это сделало Unix **портируемым** на любую машину, у которой была C-компилятор.[wikipedia+1](https://en.wikipedia.org/wiki/History_of_Unix)​

Это было ключевой причиной успеха Unix — он был написан на языке, а не на железе.

1. [https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/](https://www.reddit.com/r/learnprogramming/comments/181gtei/if_programming_requires_a_compiler_how_was_the/)
2. [https://en.wikipedia.org/wiki/Booting](https://en.wikipedia.org/wiki/Booting)
3. [https://en.wikipedia.org/wiki/History_of_Unix](https://en.wikipedia.org/wiki/History_of_Unix)
4. [https://en.wikipedia.org/wiki/Multics](https://en.wikipedia.org/wiki/Multics)
5. [https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html](https://www.tuhs.org/Mirror/Hauben/unix-Part_I.html)
6. [https://www.startertutorials.com/blog/history-of-unix.html](https://www.startertutorials.com/blog/history-of-unix.html)
7. [https://users.dimi.uniud.it/~antonio.dangelo/LabOS/2007/lessons/helper/history/earlyunix.html](https://users.dimi.uniud.it/~antonio.dangelo/LabOS/2007/lessons/helper/history/earlyunix.html)
8. [http://amits-notes.readthedocs.io/en/latest/study/taoup/taoup-ch2-history.html](http://amits-notes.readthedocs.io/en/latest/study/taoup/taoup-ch2-history.html)
9. [https://www.reddit.com/r/askscience/comments/4gjvt4/how_were_the_very_first_computer/](https://www.reddit.com/r/askscience/comments/4gjvt4/how_were_the_very_first_computer/)
10. [https://www.redhat.com/en/blog/unix-linux-history](https://www.redhat.com/en/blog/unix-linux-history)