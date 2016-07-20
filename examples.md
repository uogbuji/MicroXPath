


= XPath 3 equivalents (from spec) =

== From section 3.14 Simple map operator (!) ==

<div style="border-size:2px; border-style:solid; border-color:red;">
```
child::div1 / child::para / string() ! concat("id-", .)
```
</div>

Selects the para element children of the div1 element children of the context node; that is, the para element grandchildren of the context node that have div1 parents. It then outputs the strings obtained by prepending "id-" to each of the string values of these grandchildren.

