Regular Expressions with R
========================================================
author: Nick Salkowski
date: October 20, 2016
autosize: true

What are Regular Expressions?
========================================================


- Regular expressions define patterns of characters
- Once you've defined a pattern, you can:
  - Check to see if it exists
  - Extract it
  - Replace it
- They can be really useful, but can be a challenge to learn.

Building Regular Expressions
========================================================
- The simplest regular expression is a single character, e.g. "a".  
- More complex expressions are built by combining simpler expressions together.
- This presentation will use functions from the `stringr` package, but you can alternatively use the `base` package functions.



Detecting and Extracting Regular Expressions
============================================
- `str_detect` returns a logical vector indicating whether a pattern was detected or not.  It takes two arguments:
  - string: a character vector
  - pattern: a regular expression to find
- `str_extract` returns the expression if a matching expression is detected.  It takes two arguments:
  - string: a character vector
  - pattern: a regular expression to find
  
str_detect()
============

```r
str_detect(string = "abc",
            pattern = "ab")
```

```
[1] TRUE
```

```r
str_detect(string = "123",
            pattern = "ab")
```

```
[1] FALSE
```

```r
c("abc", "123") %>% 
  str_detect("ab")
```

```
[1]  TRUE FALSE
```

str_extract()
=============

```r
str_extract(string = "abc",
            pattern = "ab")
```

```
[1] "ab"
```

```r
str_extract(string = "123",
            pattern = "ab")
```

```
[1] NA
```

```r
c("abc", "123") %>% 
  str_extract("ab")
```

```
[1] "ab" NA  
```

Alternative Patterns
====================
The vertical bar (`|`) can be used as a logical "or" within a regular expression:


```r
c("abc", "123", "abc123") %>% 
  str_extract("ab|12")
```

```
[1] "ab" "12" "ab"
```

str_extract_all()
=================
`str_extract_all()` returns every match as a list:

```r
c("abc", "123", "abc123") %>% 
  str_extract_all("ab|12")
```

```
[[1]]
[1] "ab"

[[2]]
[1] "12"

[[3]]
[1] "ab" "12"
```

Parentheses
===========

Parentheses can be used to organize your expressions:


```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_extract("(post|mail)(man|woman)")
```

```
[1] "mailman"   "postwoman" NA         
```

Replacement
===========
The `str_replace()` function can be used to replace matching text:

```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_replace(
    pattern = "(post|mail)(man|woman)",
    replacement = "mail carrier")
```

```
[1] "The mail carrier always rings twice"
[2] "The mail carrier always rings twice"
[3] "The telephone always rings twice"   
```

Multiple Replacement
====================
The `str_replace_all()` function replaces every instance of the pattern:

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "a|b|c",
    replacement = "=")
```

```
[1] "===123^"
```


Expression Capture
==================
Expressions in parentheses are captured for possible reuse during replacement. `str_match()` returns all the captured expressions as a matrix:

```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_match(
    pattern = "(post|mail)(man|woman)")
```

```
     [,1]        [,2]   [,3]   
[1,] "mailman"   "mail" "man"  
[2,] "postwoman" "post" "woman"
[3,] NA          NA     NA     
```

Expression Reference
====================
To reuse a captured expression, insert two backslashes followed by the captured expression index into the replacement.  You can reference up to 9 captured expressions:

```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_replace(
    pattern = "(post|mail)(man|woman)",
    replacement = "\\2 who delivers the \\1")
```

```
[1] "The man who delivers the mail always rings twice"  
[2] "The woman who delivers the post always rings twice"
[3] "The telephone always rings twice"                  
```

Skipping Expression Capturing
=============================
To skip expression capturing put `?:` after the left parenthesis:

```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_match(
    pattern = "(?:post|mail)(man|woman)")
```

```
     [,1]        [,2]   
[1,] "mailman"   "man"  
[2,] "postwoman" "woman"
[3,] NA          NA     
```

Skipped Expressions Don't Count
===============================

```r
c("The mailman always rings twice",
  "The postwoman always rings twice",
  "The telephone always rings twice") %>% 
  str_replace(
    pattern = "(?:post|mail)(man|woman)",
    replacement = "\\1 who delivers the mail")
```

```
[1] "The man who delivers the mail always rings twice"  
[2] "The woman who delivers the mail always rings twice"
[3] "The telephone always rings twice"                  
```

Brackets
========

Brackets define *character classes*.  Any character within the brackets will be matched:


```r
"abc123^" %>% 
  str_extract_all("[abc]")
```

```
[[1]]
[1] "a" "b" "c"
```

Brackets vs. Vertical Bars
==================
Brackets are a lot like putting a vertical bar in between every individual character:

```r
"abc" %>% 
  str_extract_all("a|b|c")
```

```
[[1]]
[1] "a" "b" "c"
```

Bracket Negation
================
If the carat (`^`) is the *first* character inside the bracket, then any character that *doesn't* match the other characters in the brackets will be matched:


```r
c("abc123^") %>% 
  str_extract_all("[^abc]")
```

```
[[1]]
[1] "1" "2" "3" "^"
```

Matching Carats
===============
If you want to match the *carat*, don't put it first:

```r
"abc123^" %>% 
  str_extract_all("[abc^]")
```

```
[[1]]
[1] "a" "b" "c" "^"
```

Negating Carats
===============
Of course, you can also match characters other than carats, too:

```r
"abc123^" %>% 
  str_extract_all("[^^]")
```

```
[[1]]
[1] "a" "b" "c" "1" "2" "3"
```

Character Ranges
================
Inside brackets, a hyphen can be used to specify a character range:

```r
"abc123^" %>% 
  str_extract_all("[a-z]")
```

```
[[1]]
[1] "a" "b" "c"
```

```r
"abc123^" %>% 
  str_extract_all("[0-9]")
```

```
[[1]]
[1] "1" "2" "3"
```


Special Character Classes
=========================
There are a bunch of special character classes that you can use to avoid having to write expressions like `[ABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890]`

- `[:lower:]` Lower-case letters
- `[:upper:]` Upper-case letters
- `[:digits:]` Numeric digits 0-9
- `[:xdigits:]` Hexidecimal digits: 0-9, A-F, a-f
- `[:blank:]` Blank characters, like space and tab
- `[:space:]` Characters like tab, newline, space, etc.
- `[:punct:]` Punctuation characters
- `[:cntrl:]` Control characters

Special Combination Character Classes
=====================================

There are special character classes that are combinations of other character classes:

- `[:alpha:]` Alphabetic characters: `[:lower:]` + `[:upper:]`
- `[:alnum:]` Alphanumeric characters: `[:alpha:]` + `[:digits:]`
- `[:graph:]` Graphical characters: `[:alnum:]` + `[:punct:]`
- `[:print:]` Printable characters: `[:graph:]` + `space`

Using Special Character Classes
===============================
Special Character Classes need to be used inside brackets:

```r
"abc123^" %>% 
  str_extract_all("[[:alpha:]]")
```

```
[[1]]
[1] "a" "b" "c"
```

```r
"abc123^" %>% 
  str_extract_all("[[:digit:]]")
```

```
[[1]]
[1] "1" "2" "3"
```

Quantifiers
===========
The following notation can be used to describe how an expression might repeat:

- ? &nbsp;&nbsp; Match at most one time
- * &nbsp;&nbsp; Match zero or more times
- + &nbsp;&nbsp; Match one or more times
- `{n}` &nbsp;&nbsp; Match exactly n times
- `{n,}` &nbsp;&nbsp; Match n or more times
- `{n,m}` &nbsp;&nbsp; Match at least n times, but no more than m times

Using ?
=======
Put `?` after the expression to match at most one time:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]?",
    replacement = "=")
```

```
[1] "=bc123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]?",
    replacement = "=")
```

```
[1] "====1=2=3=^="
```

Using *
=======
Put `*` after the expression to match zero or more times:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]*",
    replacement = "=")
```

```
[1] "=123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]*",
    replacement = "=")
```

```
[1] "==1=2=3=^="
```

Using +
=======
Put `+` after the expression to match one or more times:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]+",
    replacement = "=")
```

```
[1] "=123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]+",
    replacement = "=")
```

```
[1] "=123^"
```

Using {n}
=========
Put `{n}` after the expression to match exactly `n` times:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]{2}",
    replacement = "=")
```

```
[1] "=c123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]{2}",
    replacement = "=")
```

```
[1] "=c123^"
```

Using {n,}
==========
Put `{n,}` after the expression to match `n` or more times:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]{2,}",
    replacement = "=")
```

```
[1] "=123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]{2,}",
    replacement = "=")
```

```
[1] "=123^"
```

Using {n,m}
===========
Put `{n,m}` after the expression to match at least `n` but no more than `m` times:

```r
"abc123^" %>% 
  str_replace(
    pattern = "[[:alpha:]]{1,2}",
    replacement = "=")
```

```
[1] "=c123^"
```

```r
"abc123^" %>% 
  str_replace_all(
    pattern = "[[:alpha:]]{1,2}",
    replacement = "=")
```

```
[1] "==123^"
```

Beginnings and Endings
======================
The carat (`^`) and the dollar sign (`$`) represent the beginning and ending of a string, respectively:


```r
"abc123abc123" %>% 
  str_replace_all(
    pattern = "^a",
    replacement = "=")
```

```
[1] "=bc123abc123"
```

```r
"abc123abc123" %>% 
  str_replace_all(
    pattern = "3$",
    replacement = "=")
```

```
[1] "abc123abc12="
```

Wildcards
=========
The period (.) is a "wildcard" -- it represents any character:

```r
"abc123^" %>% 
  str_replace(
    pattern = ".",
    replacement = "=")
```

```
[1] "=bc123^"
```

```r
"abc123^" %>% 
  str_replace(
    pattern = "a.c",
    replacement = "=")
```

```
[1] "=123^"
```

Escaping
========
The backslash (`\`) is the escape character -- use it when you want to match a special character:

```r
"abc.123" %>% 
  str_replace(
    pattern = "\\.",
    replacement = "{period}")
```

```
[1] "abc{period}123"
```

```r
"[abc123]" %>% 
  str_replace_all(
    pattern = "\\[|\\]",
    replacement = "{bracket}")
```

```
[1] "{bracket}abc123{bracket}"
```

More Wildcards
==============
- `\w` &nbsp;&nbsp; Matches any "word" character: `[[:alnum:]_]`
- `\W` &nbsp;&nbsp; Matches any "non-word" character: `[^[:alnum:]_]`
- `\d` &nbsp;&nbsp; Matches any "digit" character
- `\D` &nbsp;&nbsp; Matches any "non-digit" character
- `\s` &nbsp;&nbsp; Matches any "space" character
- `\S` &nbsp;&nbsp; Matches any "non-space" character

Words / Non-Words
======================


```r
zz <- "HAL 9000 from 2001: A Space Odyssey"
str_extract_all(zz, "\\w+") %>% unlist
```

```
[1] "HAL"     "9000"    "from"    "2001"    "A"       "Space"   "Odyssey"
```

```r
str_extract_all(zz, "\\W+") %>% unlist
```

```
[1] " "  " "  " "  ": " " "  " " 
```

Digits / Non-Digits
===================

```r
zz <- "HAL 9000 from 2001: A Space Odyssey"
str_extract_all(zz, "\\d+") %>% unlist
```

```
[1] "9000" "2001"
```

```r
str_extract_all(zz, "\\D+") %>% unlist
```

```
[1] "HAL "              " from "            ": A Space Odyssey"
```

Spaces / Non-Spaces
===================

```r
zz <- "HAL 9000 from 2001: A Space Odyssey"
str_extract_all(zz, "\\s+") %>% unlist
```

```
[1] " " " " " " " " " " " "
```

```r
str_extract_all(zz, "\\S+") %>% unlist
```

```
[1] "HAL"     "9000"    "from"    "2001:"   "A"       "Space"   "Odyssey"
```

Phone Numbers
=============
One phone number pattern:
- Not a digit
- 3 digits
- 1 hyphen
- 3 digits
- 1 hyphen
- 4 digits
- Not a digit

`(\\D|^)\\d{3}-\\d{3}-\\d{4}(\\D|$)`


Phone Numbers
=============
Another phone number pattern:
- Not a digit
- 3 digits enclosed in parentheses
- 1 Space
- 3 digits
- 1 hyphen
- 4 digits
- Not a digit

`(\\D|^)\\(\\d{3}\\) \\d{3}-\\d{4}(\\D|$)`
Phone Number Example
====================
Let's try putting together a bunch of concepts:


```r
c("Home: 651-555-1234",
  "(612) 555-4321 (Work)") %>% 
  str_replace(
    "(\\D|^)(\\d{3})-(\\d{3}-\\d{4})(\\D|$)",
    "\\1(\\2) \\3\\4") %>% 
  str_extract(
    "(\\D|^)\\(\\d{3}\\) \\d{3}-\\d{4}(\\D|$)") %>% 
  str_extract("\\(\\d{3}\\) \\d{3}-\\d{4}")
```

```
[1] "(651) 555-1234" "(612) 555-4321"
```

Alternative Phone Number Example
================================
Let's try it the other way:

```r
c("Home: 651-555-1234",
  "(612) 555-4321 (Work)") %>% 
  str_replace(
    "(\\D|^)(\\(\\d{3})\\) (\\d{3}-\\d{4})(\\D|$)",
    "\\1\\2-\\3\\4") %>% 
  str_extract(
    "(\\D|^)\\d{3}-\\d{3}-\\d{4}(\\D|$)") %>% 
  str_extract("\\d{3}-\\d{3}-\\d{4}")
```

```
[1] "651-555-1234" "612-555-4321"
```

Phone Number Breakdown
======================
Separate area codes!

```r
c("Home: 651-555-1234",
  "(612) 555-4321 (Work)") %>% 
  str_replace(
    "(\\D|^)(\\(\\d{3})\\) (\\d{3}-\\d{4})(\\D|$)",
    "\\1\\2-\\3\\4") %>% 
  str_extract(
    "(\\D|^)\\d{3}-\\d{3}-\\d{4}(\\D|$)") %>% 
  str_match("(\\d{3})-(\\d{3}-\\d{4})")
```

```
     [,1]           [,2]  [,3]      
[1,] "651-555-1234" "651" "555-1234"
[2,] "612-555-4321" "612" "555-4321"
```

base Functions
==============
- You can certainly use the `base` functions
- Some `base` functions are very similar to `stringr` functions, but others are different
- Which functions work best depends on what you are trying to do

grepl()
=======
`grepl()` is a lot like `str_detect()`

```r
zz <- c("Hi, everybody!",
        "I'm Dr. Nick.")
str_detect(string = zz, 
           pattern = "Dr\\.")
```

```
[1] FALSE  TRUE
```

```r
grepl(pattern = "Dr\\.",
      x = zz)
```

```
[1] FALSE  TRUE
```

grep()
======
`grep()` returns the indices of strings that contain the pattern

```r
grep(pattern = "Dr\\.",
     x = c("Hi, everybody!",
           "I'm Dr. Nick."))
```

```
[1] 2
```

grep() with value = TRUE
========================
`grep()` with `value = TRUE` returns the strings that contain the pattern

```r
grep(pattern = "Dr\\.",
     x = c("Hi, everybody!",
           "I'm Dr. Nick."),
     value = TRUE)
```

```
[1] "I'm Dr. Nick."
```

Remembering Column Names
========================
Whenever I work with a really wide dataset, I forget the name of some variable that I need to reference.  `grep()` is useful for helping me find it:

```r
grep("(L|l)ength", names(iris), value = TRUE)
```

```
[1] "Sepal.Length" "Petal.Length"
```

sub() and gsub()
================
- `sub()` replaces patterns, like `str_replace()`
- `gsub()` replaces all the patterns, like `str_replace_all()`

```r
zz <- c("Hi, everybody!",
        "I'm Dr. Nick.")
sub(pattern = "Dr\\.",
    replacement = "Doctor",
    x = zz)
```

```
[1] "Hi, everybody!"   "I'm Doctor Nick."
```

```r
str_replace(string = zz,
            pattern = "Dr\\.",
            replacement = "Doctor")
```

```
[1] "Hi, everybody!"   "I'm Doctor Nick."
```

regexpr(): Starting Position and Length of the First Match
==========================================================

```r
regexpr(pattern = "\\.",
        text = c("Hi, everybody!",
                 "I'm Dr. Nick."))
```

```
[1] -1  7
attr(,"match.length")
[1] -1  1
attr(,"useBytes")
[1] TRUE
```

gregexpr(): Starting Positions and Lengths of Matches
=====================================================

```r
gregexpr(pattern = "\\.",
        text = c("Hi, everybody!",
                 "I'm Dr. Nick."))
```

```
[[1]]
[1] -1
attr(,"match.length")
[1] -1
attr(,"useBytes")
[1] TRUE

[[2]]
[1]  7 13
attr(,"match.length")
[1] 1 1
attr(,"useBytes")
[1] TRUE
```

regexec(): Starting Position of Match & Captured Expressions
============================================================

```r
regexec(pattern = "(post|mail)(man|woman)",
        text = c("My mailman", "Your postwoman"))
```

```
[[1]]
[1] 4 4 8
attr(,"match.length")
[1] 7 4 3
attr(,"useBytes")
[1] TRUE

[[2]]
[1]  6  6 10
attr(,"match.length")
[1] 9 4 5
attr(,"useBytes")
[1] TRUE
```

=====

```r
"<center><h1><b>I'm done.</b></h1></center>" %>% 
  str_replace(pattern = "I'm",
              replacement = "Any") %>% 
  str_replace(pattern = "done",
              replacement = "Questions") %>% 
  str_replace(pattern = "\\.",
              replacement = "?") %>% 
  cat
```

<center><h1><b>Any Questions?</b></h1></center>
