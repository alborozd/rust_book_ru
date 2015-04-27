% Перечисления

В Rust есть "типы-суммы", или *перечисления* (тип-сумма - это термин из теории
типов). Перечисления - это очень полезная возможность Rust, и они очень много
используются в стандартной библиотеке языка. Они объявляются с помощью ключевого
слова `enum`. `enum` - это тип, который соотносит набор неких вариантов одному
имени. Например, ниже мы определяем перечисление `Character` (символ),
представляющее собой или цифру (`Digit`), или что-то другое.

```rust
enum Character {
    Digit(i32),
    Other,
}
```

Б'ольшая часть обычных типов могут быть вариантами перечисления. Вот несколько
примеров:

```rust
struct Empty;
struct Color(i32, i32, i32);
struct Length(i32);
struct Stats { Health: i32, Mana: i32, Attack: i32, Defense: i32 }
struct HeightDatabase(Vec<i32>);
```

Здесь мы видим, что, в зависимости от типа, вариант перечисления может содержать
вложенные данные, а может и не иметь таковых. Например, в перечислении
`Character` (символ), вариант `Digit` (цифра) даёт значимое имя числу типа
`i32`. А вот вариант `Other` представляет собой лишь имя, без значения. Однако
наиболее полезно именно то, что отдельные варианты представляют собой отдельные
виды символов (`Character`).

Как и структуры, варианты перечислений по умолчанию не сравнимы операциями
сравнения (`==`, `!=`), не упорядочены (не реализуют `<`, `>=` и другие) и не
поддерживают другие двухместные операции, такие как умножение (`*`) и сложение
(`+`). Нижеследующий код, как таковой, не верен (если мы используем приведённый
выше тип-перечисление `Character`):

```rust,ignore
// Оба этих присваивания успешны
let ten  = Character::Digit(10);
let four = Character::Digit(4);

// Error: `*` is not implemented for type `Character`
let forty = ten * four;

// Error: `<=` is not implemented for type `Character`
let four_is_smaller = four <= ten;

// Error: `==` is not implemented for type `Character`
let four_equals_ten = four == ten;
```

Наверное, это выглядит неудобным. Но мы можем преодолеть данное ограничение.
Есть два способа сделать это: реализовать сравнение самим, или использовать
сопоставление вариантов с образцом с помощью выражений [`match`][match]. Мы
узнаем о них в следующей главе. Пока мы не имеем достаточных знаний Rust для
реализации сравнения. Но мы можем использовать перечисление `Ordering` (порядок)
из стандартной библиотеки, которое выглядит так:

```
enum Ordering {
    Less,
    Equal,
    Greater,
}
```

Поскольку перечисление `Ordering` уже определено, мы импортируем его с помощью
ключевого слова `use`. Вот пример его использования:

```{rust}
use std::cmp::Ordering;

fn cmp(a: i32, b: i32) -> Ordering {
    if a < b { Ordering::Less }
    else if a > b { Ordering::Greater }
    else { Ordering::Equal }
}

fn main() {
    let x = 5;
    let y = 10;

    let ordering = cmp(x, y); // ordering: Ordering

    if ordering == Ordering::Less {
        println!("меньше");
    } else if ordering == Ordering::Greater {
        println!("больше");
    } else if ordering == Ordering::Equal {
        println!("равно");
    }
}
```

Мы используем символ `::` для обозначения пространства имён. В данном случае,
перечисление `Ordering` находится в под-модуле модуля `std`. Мы подробнее
поговорим о модулях позже. Пока же достаточно знать, что вы можете использовать
(`use`) вещи из стандартной библиотеки, если они вам понадобятся.

Отлично, теперь давайте поговорим о самом коде примера. `cmp` - это функция,
которая сравнивает два объекта, и возвращает значение типа "порядок"
(`Ordering`). Мы возвращаем одно из значений `Ordering::Less`,
`Ordering::Greater` или `Ordering::Equal`, когда первое значение меньше, больше
или равно второму, соответственно. Заметьте, что варианты перечисления находятся
в пространстве имён самого перечисления: к нему нужно обращаться
`Ordering::Greater`, а не `Greater`.

Переменная `ordering` имеет тип `Ordering`, и содержит одно из трёх значений.
Затем мы делаем несколько сравнений с помощью `if`/`else`, чтобы проверить,
какое из значений мы получили.

Запись `Ordering::Greater` неудобна из-за своей длины. Давайте используем другой
вид оператора `use` и импортируем сами варианты перечисления. В таком коде не
нужно полностью специфицировать имена вариантов:

```{rust}
use std::cmp::Ordering::{self, Equal, Less, Greater};

fn cmp(a: i32, b: i32) -> Ordering {
    if a < b { Less }
    else if a > b { Greater }
    else { Equal }
}

fn main() {
    let x = 5;
    let y = 10;

    let ordering = cmp(x, y); // ordering: Ordering

    if ordering == Less { println!("меньше"); }
    else if ordering == Greater { println!("больше"); }
    else if ordering == Equal { println!("равно"); }
}
```

Импорт вариантов удобен и компактен, но он может вызывать конфликты имён,
поэтому будьте осторожны при его использовании. Обычно стилистически лучше
импортировать перечисление, а не его варианты.

Как видите, перечисления довольно удобны, и особенно полезны когда они
[обобщены][generics] относительно вложенных в них типов. Однако, прежде чем мы
перейдём к рассмотрению обобщённых типов, давайте поговорим об использовании
перечислений при сопоставлении с образцом. Сопоставление с образцом - это
инструмент, позволяющий нам элегантно разбирать типы-суммы вроде `Ordering`.
Данная техника позволит нам избежать всех этих хрупких сравнений с помощью
`if`/`else`.


[match]: ./match.html
[generics]: ./generics.html