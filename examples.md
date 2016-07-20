


# XPath 3 equivalents (from spec)

## From [section 3.14 Simple map operator (!)](https://www.w3.org/TR/xpath-30/#id-map-operator)

<div style="border-size:2px; border-style:solid; border-color:red;">
<pre>
child::div1 / child::para / string() ! concat("id-", .)
</pre>
</div>

Selects the para element children of the div1 element children of the context node; that is, the para element grandchildren of the context node that have div1 parents. It then outputs the strings obtained by prepending "id-" to each of the string values of these grandchildren.

In MicroXPath:

```
map(div1/para, 'concat("id-", .)')
```

<div style="border-size:2px; border-style:solid; border-color:red;">
<pre>
$emp ! (@first, @middle, @last)
</pre>
</div>

Returns the values of the attributes first, middle, and last for element $emp, in the order given. (The / operator here [would] return the attributes in an unpredictable order.)

In MicroXPath:

```
map($emp, '(@first, @middle, @last)')
```

<div style="border-size:2px; border-style:solid; border-color:red;">
<pre>
avg( //employee / salary ! translate(., '$', '') ! number(.))
</pre>
</div>

Returns the average salary of the employees, having converted the salary to a number by removing any $ sign and then converting to a number. (The second occurrence of ! could not be written as / because the left-hand operand of / cannot be an atomic value.)

In MicroXPath:

```
avg(map(//employee / salary, 'number(translate(., '$', ''))'))
```


