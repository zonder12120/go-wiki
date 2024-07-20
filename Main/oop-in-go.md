---
order: 6
title: ООП в Go
---

В Go нет классического представления ООП, но есть некоторый схожий функционал

## Композиция/встраивание

**Композиция** - это возможность встроить однин структурный тип в другой с наследованием методов и полей.

В Go инициализация вложенных структур должна выполняться через явное указание полей структуры.

```
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
	Animal Animal
	Name   string
	Age    float32
	Gender Sex
}

type Cat struct {
	Pet   Pet
	Breed string
}

func (a Animal) Say(sound string) string {
	fmt.Println(sound)
	return sound
}

var asya = Cat{
	Pet: Pet{
		Animal: Animal{
			WeightKg: 3.5,
			SpeedKmH: 8,
		},
		Name:   "Ася",
		Age:    0.6,
		Gender: Female,
	},
	Breed: "бенгал",
}

func main() {
	fmt.Println(asya.Pet.Gender)
	fmt.Println(asya.Breed)

	asya.Pet.Animal.Say("мяу")
}
```