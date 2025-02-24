+++
date = '2025-02-24T21:40:27+05:45'
draft = false
title = 'Regex Cheatsheet'
+++

#### _Regex_ is used for pattern matching or string matching

```
[abc]      a,b or c
[^abc]     any character except a,b,c
[a-z]      a to z
[A-Z]      A to Z
[a-zA-Z]   a to z, A to Z
[0-9]      0 to 9
```

#### Quantifiers

```
[]?     Occurs 0 or 1 times
[]+     Occurs 1 or more times
[]*     Occurs 0 or more times
[]{n}   Occurs n times
[]{n,}  Occurs n or more times
[]{y,z} Occurs atleast y times but less than z times
```

#### Regex Meta Characters

```
\d [0-9]
\D [^0-9]
\w [a-zA-Z0-9]
\W [^\w]
```

> \ - tells computer to treat following character as search character. e.g. '+' '.'

> Used to specify special characters

```
\s white space
\? Question mark
```

```
| logical OR
() Special grouping
(Bob|Alice) Bob or Alice
```

#### Regex for email address

```
/[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}/
```
