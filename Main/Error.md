---
order: 7
title: Ошибки (доделать)
---

Ошибки в Go - это встроенный тип данных (т.е. не находится в каком-то пакете), которые сами по себе являются интерфейсом

```go
type error interface {
	Error() string
}
```

Как наверное известно всем, кто даже не пишет на Go, в этом языке нет исключений. Есть только ошибки и паники. Причём обработку ошибок тебе надо реализовывать самому. Кого-то это бесит, я же считаю это оч хорошей чертой языка. Про исключения

Вот материал:

<https://habr.com/ru/articles/272383/>

<https://habr.com/ru/articles/269909/>

<https://habr.com/ru/articles/270027/>