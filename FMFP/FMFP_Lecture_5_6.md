# Lists and Abstraction
## List Type
We have seen already base types such as `Int, Char, Bool, Double, ..`. What about lists?

If $T$ is a type, then $[T]$ is a type.

Elements of $[T]$ :
- **Emtpy list** `[] :: [T]`
- **Non-Empty list** `(x : xs) :: [T]` if `x :: T` and `xs :: [T]`

Here's an example
```haskell
? [7,6 .. 3]
[7, 6, 5, 4, 3] :: [Int]
```

## Functions on lists
A function that has a list as an argument i.e. `sumList :: [Int] -> Int]` must specifiy :
- how to compute with the **empty list []**
- how to compute with the **non-empty list (x :xs)**

Here's a function that computes the sum of the list's elements 
```haskell
    sumList []      = 0 
    sumList (x :xs) = x + sumList xs
    ----
    ? sumList [1..100] -- (100 * 101)/ 2
    5050 :: Int 
```
Function that computes the length 
```haskell
    length []       = 0 
    length (x:xs)   = 1 + length xs
```
Appending lists. Note that (++) and : have different types
```haskell
    []     ++ ys = ys
    (x:xs) ++ ys = x:(xs++ys)
```
```haskell
    ? [2] ++ [3,4,5] == [2,3,4,5]
    True

    ? 2 : [3,4,5] == [2,3,4,5]
    True

    ? [2] : [3,4,5]
    ... Error ...
```

## Patterns (lists and in general)
Pattern matching has two purposes
1. checks if an argument has the proper form
2. binds values to variables
   
Example (x : xs) matches with [2, 3, 4] (= 2 : 3 : 4 : [] = 2 : [3, 4])
$$
\begin{align*}
x &= 2 \\
xs &= [3,4] 
\end{align*}
$$

Another example (pattern matching during let-binding)
```haskell
    ? let ([x,y,z],t) = ([1,2,3],(20,30)) in x + y
    3 :: Int
    
    ? let ([x,y,z],t) = ([1,2,3,4],(20,30)) in x + y    
    Pattern match fails...
```