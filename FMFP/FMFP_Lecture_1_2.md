##Introduction

###Basic Concepts in Functional Programming
Functions compute values but Functions can also be values. One basic concept in functional programming is that functions have no side effects. For example if $f(0) = 2$ then $f(0)+f(0) = 2+2 = 4$. This properity is called `referential transparency`.

Also recursion instead of iteration:
```haskell
    gcd x y
        | x == y    = x
        | x > y     = gcd (x-y) y
        | otherwise = gcd x     (y-x)
```
```java
    public static int gcd(int x , int y) {
        while (x != y) {
            if (x>y) x = x-y;
            else y = y -x;
        }
    }
```
###Introduction to functional Programming
####Expression Evaluations
There are two different types of evaluation strategies:
- `Eager Evaluation` : also called "call by value" - evaluate arguments first i.e. the _green_ path in the picture
- `Lazy Evaluation` : also called "call-by-need" - evaluate arguments only when needed (Haskell) i.e. the _blue_ path in the picture

<img src = "./Images/Image_1.PNG" width="700px">

###Syntax and Types
`Functions and arguments start with lower-case letter`
- Function consists of different cases and look like this in general

```haskell
    functionName x1 ... xn
        | guard1 = expr1
        | ...
        | guardm = exprm
```
- Program consists of several definitions
```haskell
    myconstant = 5

    aFunction y1 ... ym
        | guard1 = expr1
        | guard2 = expr2

    anotherFunction z1 ... zk = ...
```
###2D Layout
Indentation determines seperation of definitions :
- All function definitions must start at same indentation level
- If a definition requires n > 1 lines, indent lines 2 to n further
  
```haskell
    f1 x1 x2
        | a long guard which may go over over a 
        number of lines 
            = a long expression that also can go over some lines
            and looks like this
        | g2 = e2
    f2 x1 x2 x3 = ...
```
> Spaces are important. **Do not use TABs**

###Types
**Integers** :
- Int $\in \{-2^{29}, ..., 2^{29}-1\}$ 
- Functions : $+, *, \^, -, \text{div}, \text{mod}, \text{abs}$ - `? mod 7 2` 
- An Infix binary function is alaso called an operator - `? 7 'mod' 2`
- Operators can be written in prefix notation - `? (+) 3 4`
- Operators have different binding strength : `^ binds stronger than +`
- Order and Equality return True or False of Type `Bool` i.e. >, >=, ==, /= ...

**Bool**
- Values: True or False
- Binary Operators `&&`, `||` and unary function `not` as usual

**Char** : 'a', 'b', ..., '0', '1', ..., '\t', '\n'
**String** : "hello", "123", "a"
**Double** : 0.3456, -2.85e03 = -2.85 * $10^3$ with functions like : +, -, *, abs, acos, asin, ceiling...

See here for more : [Documentation](https://hackage.haskell.org/package/base/docs/Prelude.html)

**Tuple**
Used to model composite objects ("records"). Tuples are represented in `()` brackets. Example : Student has name, ID number, starting year, where the first line is a constructor and the 2nd an element of that type.
```haskell
    Type         : (String,      Int,  Int)
    with element : ("Ueli Naef", 1234, 2016)
```
- Functions can take tuples as arguments or return tupled values
```haskell
    addPair :: (Int, Int) -> Int
    addPair (x,y) = x + y
    ? addPair(3,4) 
    7
```
- Patterns can be nested
```haskell
    shift :: ((Int, Int), Int) -> (Int, (Int, Int))
    shift ((x, y), z) = (x, (y, z))
```
- Pattern matching can be usesd to decompose tuples
```haskell
    name(s, id, y) = s
    studentNumber (s, id, y) = id
    year (s, id, y) = y
``` 
###Patterns and function Definition
Functions definition are built from both patterns `mi` and guards `gi`
```haskell
    fun m1 m2 ... mn
        | g1          = e1
        ...
        | gm          = em
        | otherwise   = e  --optional!
```
- Patterns `mi` are variabales, constants or built from data constructors 
- Guards `gi` are Boolean expressions
- Pattern mataching forces evaluation i.e. if a boolean expression is true the evaluation according to that guard will be evaluated
```haskell
    silly b (x,y)                   Initialisation :
        | b         = x + y             pair = (4,2)
        | otherwise = x * y             silly (not True) pair
```

### Function Scope