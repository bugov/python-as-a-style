# Упражнения на функции

Предлагаю рассмотреть примеры простых функций, которые решают
простые задачи, имеют простые реализации. Однако, имеют определённые проблемы.

## Не забываем о стоимости операций

Функция получает на вход 2 строки. Возвращает же строку,
разделённую пробелом, с двумя словами. Первое слово склеено из
первых двух символов 2-ой строки и всех, кроме первых двух символов 1-ой строки.
Второе -- аналогично, но 1-ая и 2-ая строки меняются местами.
Слова должны быть больше 2-х символов.

Например:

```
'Привет', 'Питон' -> 'Пиивет Пртон'
'dog', 'dinner' -> 'dig donner'
```

Предлагаю каждому написать реализацию этой функции, чтобы можно было
сравнить с вариантами, предложенными далее.

Наивная реализация:

```Py
def mix_up(a, b):
    return b[:2] + a[2:] + ' ' + a[:2] + b[2:]
```

У данной реализации есть несколько проблем:
- Название функции слишком общее. Однако, сама задача довольно искуственная --
  сложно подобрать краткое и ёмкое название функции.
- Отсутствующее описание (docstring) также не помогает пониманию сути функции.
- Название переменных параметров не наводит ни на какие ассоциации. Можно было
  использовать "str_a", однако, ещё лучше -- использовать type hinting.
- Возвращаемое значение также непонятно из описания функции. Всё, что нам остаётся
  -- непосредственно читать код.
- Конкатенация (объединение) строк производится с помощью операции сложения.
  А каждый раз, когда мы складываем строки Python создаёт новый объект строки.
  Для этого Python просит у операционной системы ещё немного памяти, а это долго.
- Наконец, никакой проврки на то, что строки не короче 2-х символов, не проводится.
  Не описаны и возможные исключения.
- Тесты / примеры также отсутствуют, даже встроенные в docstring.

Попробуем слегка улучшить эту функцию, следуя тем замечаниям, что мы указали:

```Py
def mix_words_parts(a: str, b: str) -> str:
    """
    Mix two strings into one string with two words separated by space.
    Swaps first 2 symbols of each string.
    
    >>> mix_words_parts('Привет', 'Питон')
    'Пиивет Пртон'
    >>> mix_words_parts('dog', 'dinner')
    'dig donner'
    """
    assert len(a) >= 2
    assert len(b) >= 2
    
    return f'{b[:2]}{a[2:]} {a[:2]}{b[2:]}'
```

Функция стала больше, что, безусловно, минус. Однако, работает она быстрее
благодаря тому, что новая строка создаётся 1 раз, а не 4. У функции появилось
описание и более чёткий контракт (набор ограничений к входным данным и
возвращаемым).

Функция стала больше не потому, что мы простые вещи написали сложно, а потому
что явно прописали все "устные" договорённости.

## Не пишем "велосипеды"

Думаю, многие заметили, что функция довольно странная -- само её описание кажется
вычурным и искуственным. Однако, большинство кода, который нужно писать
программисту сводится к таким странным функциям, ведь логичные и общеупотребимые
функции уже давно написаны и оптимизированы. Не надо придумывать то, что
уже хорошо сделано.

Так, например, в следующей функции автор "переизобрёл"
один из методов строки (`str`) -- предлагаю самим разобраться, какой:

```Py
def verbing(s):
    if len(s) < 3:
        return s
    elif s[-3:] == 'ing':
        return s + 'ly'
    else:
        return s + 'ing'
```

## Не пишем сложнее, чем есть

В предыдущем примере в каждой ветке if-elif-else возвращается значение,
поэтому можно упростить код. Воспользуемся тем, что дальше исполнение кода не
пойдёт:

```Py
def verbing(s):
    if len(s) < 3:
        return s
    
    if s.endswith('ing'):
        return f'{s}ly'
    
    return f'{s}ing'
```

-- такой код проще читать: в каждом новом `if` мы не держим в голове, что
это часть сложной системы условий. Ограничиваем наш контекст простой
фразой "если X, то вернём Y". А также условия упорядочены так, что
сначала рассматриваются особые ситуации, а основной пример применения
описан в конце и предельно явно.

Также к распространённым ошибкам усложнения кода можно отнести следующий
подход:

```Py
def is_empty(collection):
    if len(collection) == 0:
        return True
    else:
        return False
```

Для такого кода даже функция не особо нужна, ведь её можно очевидным
образом записать с помощью одного сравнения.

Теперь рассмотрим другой вариант реализации функции `verbing`:

```Py
def verbing(s):
    return s if len(s) < 3 else (
                s + 'ly' if s.endswith('ing') else s + 'ing'
                )
```

-- на сколько удобно читать такой код? А ведь его писал реальный человек,
которому внезапно захотелось проявить свою индивидуальность.

Часто программисты впадают в одну из крайностей. Либо пишут сложные вещи
"коротко", создавая сложные однострочники с неявной логикой. Либо простейшие
операции расписывают в виде функций / методов классов. Умение понимать
сложность задачи -- важный навык программиста. Задумывайтесь об этом ("почему написано именно так?",
"как написать понятнее и проще?"), тогда со временем им овладеете.

# Пишите одинаковые вещи одинаково, разные -- по-разному

В Python есть много способов объединить несколько строк в одну. На вскидку:
- f-строки,
- `str.format`,
- printf-стиль через конструкцию `"format" % (param_1, param_2)`,
- `str.join`,
- сложение строк,
- стандартный модуль `string.Template`.

По сути -- действие одно и то же. Однако, если вы делаете его по-разному,
то каждый раз будете заставлять читателя задумываться над смыслом использования
именно этого способа. И это неплохо, если он есть,,,

С другой стороны, если вы используете в одном месте конструктор `int("1")`
для приведения строки к числу, а в другом для валидации входных параметров --
вы также запутываете читателя.
