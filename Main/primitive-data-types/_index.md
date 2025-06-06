---
order: 1
title: Базовые типы данных
---

Типы данных в Go делятся на две категории: **базовые** и **составные**.

К базовым относятся: целые, вещественные и комплексные числа, строки, байты и руны.

К составным типам данных относятся: массивы, структуры, срезы, мапы, каналы, указатели, функции и интерфейсы.

**Здесь мы разберёмся с базовыми**, так как составные нужно разбирать каждый по отдельности

## Целые числа:

-  **Int8** (от -128 до 127)

-  **Int16** (от -32768 до 32767)

-  **Int32** (от -2147483648 до 2147483647)

-  **Int64** (от –9 223 372 036 854 775 808 до 9 223 372 036 854 775 807)

-  **Int** (в зависимости от разрадности ОС, 32 или 64 бита)



-  **uint8** (от 0 до 255)

-  **uint16** (от 0 до 65535)

-  **uint32** (0 до 4294967295)

-  **uint64** (от 0 до 18 446 744 073 709 551 615)



-  **byte** - alias для типа `uint8` используется для представления ASCII-символов

-  **rune**: alias для типа `int` используется для представления Unicode символов.

Руны, как и другие типы, можно объявить не только явно, но и записав 1 символ внутри одинарных кавычек

```go
r := ‘a’ // эквивалент var r rune = ‘a'
```

**int** -- **тип по умолчанию для целого числа.** Если при инициализации переменной вы не укажете тип, он по умолчанию будет int

## Числа с плавающей точкой:

-  **float32** (от 1.4\*10-45 до 3.4\*1038)

-  **float64** (от 4.9\*10-324 до 1.8\*10308)

Тип `float32` обеспечивает шесть десятичных цифр точности, в то время как точность, обеспечиваемая типом `float64`, составляет около 15 цифр

**float64** -- **тип по умолчанию для вещественного числа.** Если при инициализации переменной вы не укажете тип, он по умолчанию будет float64

## Комплексные числа:

-  **complex64 -** вещественная и мнимая части представляют числа `float32`

-  **complex128** - вещественная и мнимая части представляют числа `float64`

Говорят, что на практике с ними не сталкиваются, надеюсь и мне не придётся)



Циферки  в названии типа - обозначение размера памяти, выделяемого под переменную данного типа в битах, то есть `int8` весит 8 бит/1 байт

На практике же все пользуются в основном `int`, `unit`, `byte` и `rune`

## Булевый тип:

**bool** - может принимать только два значения: `true` или `false`

## Строки:

Строки - это неизменяемая последовательность байтов, по сути массив `byte`.

Внутри строка - это структура, в которой 2 элемента: **указатель** на первый элемент массива и значение **длины** строки, по сути 2 **int**’а.

```go
type stringStruct struct {
    data uintptr // указатель на массив байтов
    len  int     // длина строки
}
```

То есть фиксированный размер строки в 64 битных системах - **16 байт**, а в 32 битных - **8 байт**.

Неизменяемая означает то, что если мы и попытаемся изменить строку, то по сути мы создадим новый массив байт и переменная уже будет ссылаться на новую область памяти.

Но символы в GO используют кодировку UTF-8 (кстати, её создал один из основателей GO), так что один символ может занимать больше одного байта.

Например русские буквы занимают 2 байта, а иероглифы вообще 4. Получается, для строки из русских букв “йух” в массиве байт будет 6 байт: по 2 подряд на каждую букву. То есть первые 2 для буквы “х” и так далее.

Поэтому, чтобы итерироваться по символам, обычно строку приводят к слайсу типа **rune** и итерируются по нему.

```go
str := "йух"

for _, v := range []rune(str) {
	fmt.Println(string(v))
}
```

Может показаться, что код норм, но нет, `range` не явно приводит байты в строке к рунам, поэтому правильно будет не приводить строку к рунам:

```go
str := "хуй"

for _, v := range str {
	fmt.Println(string(v))
}
```

А вот реально полезно приводить к рунам, когда итерируемся не через `range`, например:

```go
str := "хуй"
conStr := []rune(str)

for i := 0; i < len(conStr); i++ {
	fmt.Println(string(conStr[i]))
}
```

Тут и длина будет верно посчитана, и отображение символов корректное.

Для того, чтобы компилятор Go игнорировал спецсимволы и при этом сохранял оригинальное форматирование строк, нужно использовать **обратные** кавычки.

```go
import "fmt"

... 

// переносы строк в переменной указывается явно без добавления спец символов

var some_variable = `first line
second line
third line`

fmt.Println(some_variable) // выводим строку в консоль

// ВЫВОД:

// first line
// second line
// third line
```

## **Конвертация типов данных**

### **Конвертация числовых типов**

Перевод между целыми числами разными разрядности осуществляется следующим образом:

```go
var smallNum int8 = 2
var bigNum = int64(smallNum) // 2
```

Стоит учитывать, что преобразование из большего по разрядности типа данных в меньший может привести к переполнению:

```go
var bigNum int64 = 130
var smallNum = int8(bigNum) // -126 
```

### **Конвертация целых чисел в дробные и наоборот**

Так можно преобразовать тип int в float:

```go
var intNum int = 63
var floatNum = float64(intNum) // 63
```

При переводе дробных чисел в целые происходит отбрасывание дробной части:

```go
var floatNum2 = 12.34
var intNum2 = int(floatNum2) // 12
```

Следует помнить о переполнении и при необходимости увеличивать разрядность типа:

```go
var floatNum float64 = 32768
var intNumBad = int16(floatNum) // -32768
var intNumGood = int64(floatNum) // 32768
```

### **Конвертация чисел в строки**

Для перевода числовых типов (int, float) в строку применяются функции `fmt.Sprint` и `fmt.Sprintf` из стандартного пакета fmt. Обе они возвращают строку, но отличаются тем, что первая форматирует аргументы с использованием стандартого формата, а вторая – в соответствии с передаваемым шаблоном.

```go
intToString := fmt.Sprintf("%d", 12) // шаблон %d означает целое число в десятичной системе счисления
floatToString := fmt.Sprint(34.56) // "34.56"
```

У шаблонов есть множество вариантов применения. Например, так можно без использования дополнительных функций перевести число в восьмеричную систему счисления:

```go
decToOct := fmt.Sprintf("%o", 1234) // 2322
```

Полный список шаблонов можно найти в [официальной документации go](https://pkg.go.dev/fmt#hdr-Printing).

Альтернативный способ перевести целое число в строку – воспользоваться функцией `strconv.Itoa`из стандартного пакета strconv:

```go
s := strconv.Itoa(1234) // "1234"
```

### **Конвертация строк в числа**

Для конвертации строк в числа используется функция `strconv.Atoi` , которая возвращает два значения – результат и ошибку. Для обработки ошибок в Go используется специальная конструкция:

```go
num, err := strconv.Atoi("1234") // конвертация строки в число

// обработка ошибки
if err != nil {
	fmt.Println(err)
}
fmt.Println(num) // 1234   
```

Если вместо числа передать другие символы, то возникнет ошибка, а переменной num присвоится 0:

```go
num, err := strconv.Atoi("строка") // конвертация строки в число

// обработка ошибки
if err != nil {
	fmt.Println(err) // strconv.Atoi: parsing "строка": invalid syntax
}
fmt.Println(num) // 0
```

### Нулевые значения

Все типы данных в Go имеют нулевые значения. Для чисел это 0, для строки ““ (пустая строка), для bool это false

## nil (да почему не  null?)

nil - это обзначение отсуствия какого-либо значения. Точка.

Это однозначное применение nil в Go. Как программисту, тебе уже не сильно важно, как работает оно под капотом, но интересно, конечно, поэтому расскажу.

nil **не** является **прямым** указателем на «нулевой» адрес в памяти.

-  Внутренне nil может быть представлен **специальным значением**, которое интерпретируется как отсутствие ссылки.

-  Это значение **не обязательно** совпадает с **фактическим нулевым адресом** в памяти.

Нулевой адрес явно указывает на отсутствие объекта или данных,  на отсутствие ссылки на какой-либо объект в памяти. Это может быть специальное значение, которое операционная система и язык программирования определяют как «ничего».

Внутренне нулевой адрес обычно представляется фактическим нулевым значением, которое в шестнадцатеричной системе счисления выглядит как `0x00000000` (в 32-битных системах) или `0x0000000000000000` (в 64-битных системах).