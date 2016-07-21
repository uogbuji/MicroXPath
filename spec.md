# MicroXPath outline

MicroXPath is a simple expression language for processing [MicroXML](https://www.w3.org/community/microxml/).

## Design of MicroXPath

MicroXPath is designed to be invoked from a Turing-complete host environemnts, such as a modern programming language or DBMS. It is meant to be very simple and focused on delivering nodes from the MicroXML document to the host environment. There is a limited expression language dealing with strings, numbers and boolean values, but this is largely to power predicate operations which are used to narrow down the selection of nodes.

MicroXPath is based on XPath 1, except for sequences, which are borrowed from XPath 2. The results of all MicroXPath expressions are sequences.

## Sequences

MicroXPath provides a syntax for creating sequences, borrowing from XPath 2.0. The following are the examples of expressions that construct sequences, taken from 3.4.1 of the XPath 3 spec. They have the same semantics as in MicroXPath.

* (10, 1, 2, 3, 4) results in a sequence of five integers.

* (10, (1, 2), (), (3, 4), (5)) results in a sequence with six items, 10, 1, 2, 3, 4, 5. The five component sequences of length one, two, zero, two, and one, respectively, are combined.

* (salary, bonus) results in a sequence containing all salary children of the context node followed by all bonus children. The salary and bonus children flow into the result separately in document order, but the resulting sequence may not be in document order.

* ($price, $price) results in a sequence with the value of the variable $price twice over. If $price is bound to the value 10.50, the result of this expression is the sequence 10.50, 10.50. If $price is bound to the a sequence 1, 2, 3, the result of this expression is the sequence 1, 2, 3, 1, 2, 3.

XPath 2.0/3.0 range expressions are not supported, but there are core functions to provide similar features.

### Note on document order

The MicroXPath union operator | behaves much as in XPath 1, and is also a way to arrange a sequence into document order. The first items in the result sequence of $a|$b would be all nodes in either $a or $b, in document order. Next would come all strings, sorted by code points, then all numbers, sorted numerically, then finally booleans, false if it occurs in either of the arguments, followed by true, if it occurs. This is a simple case of the more generalized function of the built-in `union` function discussed below.

## Grammar

(main changes from XPath 1.0: rules 6, 15, 16, 39, as well as references to QName)

	[1]		LocationPath					::= RelativeLocationPath | AbsoluteLocationPath	
	[2]		AbsoluteLocationPath			::= '/' RelativeLocationPath? | AbbreviatedAbsoluteLocationPath	
	[3]		RelativeLocationPath			::=	Step | RelativeLocationPath '/' Step | AbbreviatedRelativeLocationPath
	[4]		Step							::=	AxisSpecifier NodeTest Predicate* | AbbreviatedStep	
	[5]		AxisSpecifier					::=	AxisName '::' | AbbreviatedAxisSpecifier
	[6]		AxisName						::=	"ancestor" | "ancestor-or-self" | "attribute" | "child" | "descendant" | "descendant-or-self" | "following" | "following-sibling" | "parent" | "preceding" | "preceding-sibling" | "self"
	[7]		NodeTest						::=	NameTest | NodeType '(' ')'
	[8]		Predicate						::=	'[' PredicateExpr ']'
	[9]		PredicateExpr					::=	Expr
	[10]	AbbreviatedAbsoluteLocationPath	::=	'//' RelativeLocationPath	
	[11]	AbbreviatedRelativeLocationPath	::=	RelativeLocationPath '//' Step	
	[12]	AbbreviatedStep					::=	'.' | '..'
	[13]	AbbreviatedAxisSpecifier		::=	'@'?
	[14]	Expr							::=	OrExpr
	[15]	PrimaryExpr						::=	ExprSingle ("," ExprSingle)*
	[16]	ExprSingle						::=	VariableReference | '(' Expr ')' | Literal | Number | FunctionCall
	[17]	FunctionCall					::=	FunctionName '(' ( Argument ( ',' Argument )* )? ')'	
	[18]	Argument						::=	Expr
	[19]	UnionExpr						::=	PathExpr | UnionExpr '|' PathExpr
	[20]	PathExpr						::=	LocationPath | FilterExpr | FilterExpr '/' RelativeLocationPath | FilterExpr '//' RelativeLocationPath
	[21]	FilterExpr						::=	PrimaryExpr | FilterExpr Predicate
	[22]	OrExpr							::=	AndExpr | OrExpr 'or' AndExpr
	[23]	AndExpr							::=	EqualityExpr | AndExpr 'and' EqualityExpr
	[24]	EqualityExpr					::=	RelationalExpr | EqualityExpr '=' RelationalExpr | EqualityExpr '!=' RelationalExpr
	[25]	RelationalExpr					::=	AdditiveExpr | RelationalExpr '<' AdditiveExpr | RelationalExpr '>' AdditiveExpr | RelationalExpr '<=' AdditiveExpr | RelationalExpr '>=' AdditiveExpr
	[26]	AdditiveExpr					::=	MultiplicativeExpr | AdditiveExpr '+' MultiplicativeExpr | AdditiveExpr '-' MultiplicativeExpr
	[27]	MultiplicativeExpr				::=	UnaryExpr | MultiplicativeExpr MultiplyOperator UnaryExpr | MultiplicativeExpr 'div' UnaryExpr | MultiplicativeExpr 'mod' UnaryExpr
	[28]	UnaryExpr						::=	UnionExpr | '-' UnaryExpr
	[29]	ExprToken						::=	'(' | ')' | '[' | ']' | '.' | '..' | '@' | ',' | '::' | NameTest | NodeType | Operator | FunctionName | AxisName | Literal | Number | VariableReference
	[30]	Literal							::=	'"' [^"]* '"' | "'" [^']* "'"
	[31]	Number							::=	Digits ('.' Digits?)? | '.' Digits
	[32]	Digits							::=	[0-9]+
	[33]	Operator						::=	OperatorName | MultiplyOperator | '/' | '//' | '|' | '+' | '-' | '=' | '!=' | '<' | '<=' | '>' | '>='
	[34]	OperatorName					::=	'and' | 'or' | 'mod' | 'div'
	[35]	MultiplyOperator				::=	'*'
	[36]	FunctionName					::=	Name - NodeType
	[37]	VariableReference				::=	'$' Name
	[38]	NameTest						::=	'*' | Name
	[39]	NodeType						::=	'node' | 'text'
	[40]	ExprWhitespace					::=	S

## Functions

XPath 1.0 functions minus:

* local-name
* namespace-uri
* id

plus:

* key [from XSLT; host environment is expected to proide key lookup tables in the execution context]
* map [similar to the ! operator in XPath 3]
* range [similar to range expressions in XPath 1]
* union [from EXSLT]
* intersection [from EXSLT]
* matches [from XPath 2]
* replace [from XPath 2]
* tokenize [from XPath 2]
* object-type [from EXSLT]
* evaluate [from EXSLT]
* same-lang [towards the idea of lang() but lang specifiers would not be in xml:lang in MicroXML]

some key behavior modifications:

* count -- Takes a sequence & returns the number of items in the sequence (remember that MicroXPath sequences are implicitly flat)
* string -- always operates on first item in a given sequence, regardless of document order

## Error handling

As with XPath 1 there is no explicit mechanism for error handling. Many operations which produce errors in some general purpose languages generate speacial-case sequences.

## Example

From this sample XML:

    <TEI>
      <teiHeader>
        <title>Chanson Balisage</title>
      </teiHeader>
      <text>
        <lg type="poem">
        <l><ref target="#a1" type="noteAnchor">Bar ‘Ebroyo</ref> cursed CSS, he cursed</l>
        <l>And cursed XForms, he smote his XML</l>
        </lg>
        <note id="a1" type="footnote"><bibl>
          <author>Nathan P. Gibson</author>, <author>Winona Salesky</author> &amp; <author>David A. Michelson</author>, <title>Encoding Western and Non-Western Names for Ancient Syriac Authors</title>, Balisage, 2015</bibl>
        </note>
      </text>
    </TEI>

Sequence of elements, all authors in the footnote:

    TEI/text/note//author

Sequence of elements, first two authors

    (TEI/text/note//author[1], TEI/text/note//author[2])

Sequence of one number, count of two authors in the footnotes

    count(TEI/text/note//author)

Sequence of one string, first author element text content

    string(TEI/text/note//author[1])

See also [examples.md](https://github.com/uogbuji/MicroXPath/blob/master/examples.md)

