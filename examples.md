#MicroXPath examples

## XPath 2 equivalents (from spec)

### From [3.3 Sequence Expressions](https://www.w3.org/TR/xpath20/#id-sequence-expressions)

```
⚠️ (10, 1 to 4)
```

In MicroXPath:

```
(10, range(1, 4))
```

----

```
⚠️ 10 to 10
```

*This example constructs a sequence of length one containing the single integer 10.*

In MicroXPath:

```
range(10, 10)
```

----

```
⚠️ fn:reverse(10 to 15)
```

*This example uses the fn:reverse function to construct a sequence of six integers in decreasing order. It evaluates to the sequence 15, 14, 13, 12, 11, 10.*

In MicroXPath:

```
range(15, 10, -1)
```


## XPath 3 equivalents (from spec)

### From [section 3.14 Simple map operator (!)](https://www.w3.org/TR/xpath-30/#id-map-operator)

```
⚠️ child::div1 / child::para / string() ! concat("id-", .)
```

*Selects the para element children of the div1 element children of the context node; that is, the para element grandchildren of the context node that have div1 parents. It then outputs the strings obtained by prepending "id-" to each of the string values of these grandchildren.*

In MicroXPath:

```
map(div1/para, 'concat("id-", .)')
```

----

```
⚠️ $emp ! (@first, @middle, @last)
```

*Returns the values of the attributes first, middle, and last for element $emp, in the order given. (The / operator here [would] return the attributes in an unpredictable order.)*

In MicroXPath:

```
map($emp, '(@first, @middle, @last)')
```

----

```
⚠️ avg( //employee / salary ! translate(., '$', '') ! number(.))
```

*Returns the average salary of the employees, having converted the salary to a number by removing any $ sign and then converting to a number. (The second occurrence of ! could not be written as / because the left-hand operand of / cannot be an atomic value.)*

In MicroXPath:

```
avg(map(//employee / salary, 'number(translate(., '$', ''))'))
```


