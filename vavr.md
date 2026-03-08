
title: Vavr Sales Talk
class: middle, center, inverse

# Vavr Sales Talk
### A Perspective for the "Classical" Java Programmer


---
class: middle, center, inverse

# Vavr Sales Talk
### A Perspective for the "Classical" Java Programmer

Vavr has its root in FP (Functional Programmming), but what is FP?

---
class: center
#Some values of FP


---

#.center[Some values of FP]


* everything is a value
--

* favor immutability
--

* pure functions: same input always returns same output
--

* functions are first-class citizens
--

* declarative: what not how
--

* ...
---

#.center[Monads]

* a "container"
--

* following certain rules for operating on the values inside of it

---

layout: true
#.center[Monadic Operations]

---

* **map**
--
: safely change the value inside the container
--

```java
Option<String> s = Option.of("foo");
Option<Integer> l = s.map(s -> s.length)
```
---

* **flatmap**
--
: chain operations that could also return a container.
--

```java
Option<String> s = Option.of("userName");
Option<User> u = s.flatMap(s -> userRepository.find(s))
```

---

* **filter**
--
: changing the container depending on the value inside of it
--

```java
Option<User> u = s.flatMap(s -> userRepository.find(s));
Option<User> adult = s.filter(v -> v.age() >= 18);
```

---
layout: true
#.center[Some Monadic Datatypes]

---
* **Option**
--
: absence of a value

--
* **Try**
--
: exceptional failure of an operation

--
* **Either**
--
: one of two possible results, left is the failure / least favorable / exception value

--
* **Validation**
--
: a value that may be valid or invalid with errors

--
* **Future**
--
: a value is not yet guaranteed to be present


---

layout: true
#.center[Why Vavr?]
---

--

* my stock needs to go up
--

* has the most important datatypes

--
* better API for Collections vs java.util.Stream

---

layout: true
#.center[Examples]

---
class: small-text

| Operation | `java.util.List` + `Stream` | `Vavr List` |
| :--- | :--- | :--- |
| **Transform** | `list.stream().map(f).toList()` | `list.map(f)` |
| **Filter** | `list.stream().filter(p).toList()` | `list.filter(p)` |
| **Flatten** | `list.stream().flatMap(f).toList()` | `list.flatMap(f)` |
| **Grouping** | `list.stream().collect(Collectors.groupingBy(f))` | `list.groupBy(f)` |
| **Distinct** | `list.stream().distinct().toList()` | `list.distinct()` |
| **Slicing** | `list.stream().skip(10).limit(5).toList()` | `list.drop(10).take(5)` |
| **Zip** | Not easily supported in standard Java. | `list.zip(otherList)` |
| **ZipWithIndex** | Not easily supported in standard Java. | `list.zipWithIndex()` |

---

### List to Map to mapping the values in the Map

```java
List<String> l = List.of(...);
Map<String, Integer> lengths = l.stream().collect(
  Collectors.toMap(Function.identity(),String::length)
);
Map<String, Integer> doubledMap = javaMap.entrySet().stream()
        .collect(Collectors.toMap(
                Map.Entry::getKey,
                e -> e.getValue() * 2
        ));

```

vs

```java
List<String> l = List.of(...);
Map<String,Integer> lengths = l.toMap(Function.identity(), String::length);
Map<String,Integer> doubledMap = lengths.mapValues(i -> i * 2);

```

---

### Summing a list

```java
List<Integer> l = new List.of(...);
l.stream().collect(Collectors.summingInt(Function.identity()));

```

vs

```java
List<Integer> l = List.of();
l.sum();

```

---

### Find biggest group of 3

```java
List<Integer> numbers = List.of(1,2,3,3,2,2);
OptionalInt max = IntStream.range(0, numbers.size() - 2)
    .map(i -> numbers.get(i) + numbers.get(i + 1) + numbers.get(i + 2))
    .max();

```

vs

```java
List<Integer> numbers = List.of(1,2,3,3,2,2);
Option<Number> max = numbers.sliding(3).map(List::sum).max();

```

---

### Keep only users we can find

```java
Optional<String> findById(int id) {
    ...
}

List<Integer> ids=  List.of();
List<String> userNames = ids.stream()
  .map(this::findById)
  .filter(Optional::isPresent)
  .map(Optional::get)
  .map(User::name)
  .toList();

```

vs

```java
Option<String> findByIdVavr(int id) {
  ...
}

List<Integer> ids = List.of();
List<String> res = ids.flatMap(this::findByIdVavr);

```

---
layout: false
#.center[When to use Vavr?]

* Vavr all the things!

--
: not really possible

--
* interop with standard libraries such as JPA / Jackson

--
* Possible but needs config

--
* When using operations on Collections

--
* Only internally or also as return types?

--
* easy interop through .toJavaList / List.ofAll(java.util.List/Stream/Array/...)
