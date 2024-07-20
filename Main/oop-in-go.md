---
order: 6
title: ООП в Go
---

В Go нет классического представления ООП, но есть некоторый схожий функционал

## Методы

В Go можно объявлять методы для пользовательских типов данных, определенных в текущем пакете, в том числе и для структур.

Примеры:

```Go
// Пользовательский тип на основе встроенного типа int
type MyInt int

func (i MyInt) Add(n MyInt) MyInt {
	return i + n
}

// Структура
type Person struct {
	Name  string
	Age uint8
}

// Использование указателя для передачи структуры, чтобы можно было изменить значение поля
func (p *Person) growUp(y uint8) {
	p.Age += y
}

func main() {
	var num MyInt = 1
	danya := Person{"Даня", 26}

	danya.growUp(1)

	fmt.Println(num.Add(1), danya.Age)	
}
```

## Композиция/встраивание

Аналог наследованная

**Композиция** - это возможность встроить однин структурный тип в другой с наследованием методов и полей.

В Go инициализация вложенных структур должна выполняться через явное указание полей структуры.

```Go
package main

import (
	"fmt"
)

type Sex int

const (
	Male Sex = iota + 1
	Female
)

func (s Sex) String() string {
	switch s {
	case Male:
		return "самец"
	case Female:
		return "самка"
	}
	return "ошибка"
}

type Animal struct {
	WeightKg float32
	SpeedKmH uint8
}

type Pet struct {
	Animal *Animal
	Name   string
	Age    float32
	Gender Sex
}

type Cat struct {
	Pet   *Pet
	Breed string
}

func (a *Animal) Say(sound string) string {
	fmt.Println(sound)
	return sound
}

func main() {
	asya := &Cat{
		Pet: &Pet{
			Animal: &Animal{
				WeightKg: 3.5,
				SpeedKmH: 8,
			},
			Name:   "Ася",
			Age:    0.6,
			Gender: Female,
		},
		Breed: "бенгал",
	}

	fmt.Println(asya.Pet.Gender)
	fmt.Println(asya.Breed)

	asya.Pet.Animal.Say("мяу")
}
```