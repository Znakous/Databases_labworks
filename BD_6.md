## Task 1
**Подсчитать общее количество всех лексем в базе данных.**

_Ожидаемый результат: число_
```
for v in Lexeme
  collect with count into _count
  return _count
```

## Task 2
**Найти все существительные в базе данных. Категория существительных имеет category_id = 'Q1084'.**

_Ожидаемый результат: список лемм всех существительных_
```
for v in 1..1 inbound "Category/Q1084" HAS_CATEGORY
  return v.lemma
```

## Task 3
**Получить все формы лексемы lexeme_id=L82809. Вывести текстовые представления форм (representation).**

_Ожидаемый результат: список текстовых представлений форм_
```
for v in Lexeme
  filter v.lexeme_id == "L82809"
  for form in 1..1 outbound v HAS_FORM
      return form.representation
```

## Task 4
**Найти все лексемы с их количеством форм и значений. Вывести лемму, количество форм и количество значений. Отсортировать по количеству форм по убыванию. Вывести первые 10.**

_Ожидаемый результат: список объектов с полями: лемма, количество форм, количество значений_
```
for v in Lexeme
  let len_form = length(for form in 1..1 outbound v HAS_FORM
      return 1)
  let len_sense = length(for sense in 1..1 outbound v HAS_SENSE
      return 1)
  sort len_form desc
  limit 10
  return {
      lemma:v.lemma, 
      forms: len_form, 
      senses: len_sense
  
```

## Task 5
**Найти все лексемы с максимальным количеством связей (степень вершины). Вывести топ-10 лексем с их леммами и количеством связей.**

_Ожидаемый результат: список из 10 лексем с их леммами и количеством всех связей, отсортированный по убыванию_
```
for v in Lexeme
  let len_neighbours = length(
          for neighbour in 1..1 any v HAS_FORM, HAS_SENSE, HAS_CATEGORY, HAS_CLAIM, IN_LANGUAGE
            return 1)
  sort len_neighbours desc
  limit 10
  return { 
        lemma: v.lemma,
        neighbours: len_neighbours
  }
```

## Task 6
**Найти все лексемы, которые имеют определённое значение (sense) с толкованием, содержащим подстроку “специальность”, и вывести их категории. Результат должен содержать пары (лемма, название категории).**

_Ожидаемый результат: список пар (лемма, название категории) для лексем с указанным значением_
```
for sense in Sense
  filter contains(sense.gloss, "специальность")
  for lex in 1..1 inbound sense HAS_SENSE
      for cat in 1..1 outbound lex HAS_CATEGORY
          return [lex.lemma, cat.name]
```

## Task 7
**Найти все лексемы без форм или без значений. Вывести их леммы и lexeme_id.**

_Ожидаемый результат: список пар (лемма, lexeme_id) для лексем без форм или без значений_
```
for lex in Lexeme
  let len_form = length(for form in 1..1 outbound lex HAS_FORM
    return 1)
  let len_sense = length(for sense in 1..1 outbound lex HAS_SENSE
    return 1)
  filter len_form == 0 or len_sense == 0
  return [lex.lemma, lex.lexeme_id]
```

## Task 8
**Найти все лексемы с максимальным количеством связей (степень вершины). Вывести топ-10 лексем с их леммами и количеством связей.**

_Ожидаемый результат: список из 10 лексем с их леммами и количеством всех связей (рёбер), отсортированный по убыванию_

```Это копия 5-го```