# Chapter 01 Exercises

### 1.1
Results as printed by the interpreter

```
10
12
8
3
6
19
#f
4
16
6
16
```

### 1.2
Translate the following expression into prefix form

```
5 + 4 + (2 - (3 - (6 + 4/5)))
-----------------------------
3 (6 - 2) (2 - 7)
```

```
(/ (+ 5 4
    (- 2 (- 3 (+ 6 (/ 4 5)))))
    (* 3 (- 6 2) (- 2 7)))
```

### 1.3
Define a procedure that takes three numbers as arguments and returns the
sum of the squares of the two larger numbers

```
(define (square x)
    (* x x))
(define (sum-of-squares x y)
    (+ (square x) (square y)))
(define (proc x y z)
    (if (>= x y)
        (sum-of-squares x (if (>= y z)
                        y
                        z))
        (sum-of-squares y (if (>= x z)
                        x
                        z))))
```

### 1.4
Observe that our model of evaluation allows for combinations whose operators
are compound expressions. Use this observation to describe the behavior of the
following procedure

```
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
```

If `b` is larger than `0` then it reduces to `(+ a b)`
else it reduces to `(- a b)`

### 1.5
Ben Bitdiddle has invented a test to determine whether the interpreter he is
faced with is using applicative-order evaluation or normal-order evaluation. He
defines the following two procedures:

```
(define (p) (p))

(define (test x y)
  (if (= x 0)
        0
        y))
```

Then he evaluates the expression

`(test 0 (p))`

What behavior will Ben observe with an interpreter that uses applicative-order
evaluation? What behavior will he observe with an interpreter that uses
normal-order evaluation? Explain your answer. (Assume that the evaluation rule
for the special form if is the same whether the interpreter is using normal or
    applicative order: The predicate expression is evaluated first, and the
    result determines whether to evaluate the consequent or the alternative
    expression.)

Answer:

Applicative Order Evaluation will loop forever because it will try to evaluate
all of its operators and operands and reduce them to their values.
Because `(p)` is defined as `(p)` it will attempt to reduce forever but will
never reach its 'value'.

Normal Order Evaluation however will return `0` because it only evaluates
its parameters as needed.  Since `x = 0` the interpreter doesn't need to
evaluate `(p)`.

### 1.6
Alyssa P. Hacker doesn't see why if needs to be provided as a special form.
"Why can't I just define it as an ordinary procedure in terms of cond?" she
asks. Alyssa's friend Eva Lu Ator claims this can indeed be done, and she
defines a new version of if:

```
(define (new-if predicate then-clause else-clause)
  (cond (predicate then-clause)
          (else else-clause)))
```

Eva demonstrates the program for Alyssa:

```
(new-if (= 2 3) 0 5)
5

(new-if (= 1 1) 0 5)
0
```

Delighted, Alyssa uses new-if to rewrite the square-root program:
```
(define (sqrt-iter guess x)
  (new-if (good-enough? guess x)
            guess
                      (sqrt-iter (improve guess x)
                                           x)))
```
What happens when Alyssa attempts to use this to compute square roots? Explain.

Answer:

`new-if` will work just fine with something like

`(new-if (= 1 1) 0 5)`

 Since `5` is already a value, there is no need to reduce it to a value. But if
 you have a recursive function and you use `if` as the guard to return, well ...

 The default evaluation strategy for `cond` uses Applicative Order Evaluation,
 what makes `if` special is that it used Normal Order Evaluation and as
 demonstrated above can short circuit. Since `if` is used as the guard to break
 from a recursive function you must have an evaluation strategy that can break
 early, otherwise you will be in a infinite loop or possibly blow the stack.

###  Exercise 1.7
The `good-enough?` test used in computing square roots will not be very
effective for finding the square roots of very small numbers. Also, in real
computers, arithmetic operations are almost always performed with limited
precision. This makes our test inadequate for very large numbers. Explain these
statements, with examples showing how the test fails for small and large
numbers. An alternative strategy for implementing good-enough? is to watch how
guess changes from one iteration to the next and to stop when the change is a
very small fraction of the guess. Design a square-root procedure that uses this
kind of end test. Does this work better for small and large numbers?

```
(define (square x)
  (* x x))

(define (average x y)
  (/ (+ x y) 2))

(define (improve guess x)
  (average guess (/ x guess)))

(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))

(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x)
                 x)))

(define (sqrt x)
  (sqrt-iter 1.0 x))
```

Answers:

Small numbers is kind of obvious.
```
(square (sqrt .001))
0.0017011851721075596

(square (sqrt 9))
9.00054933317044
```

Large numbers may fail because the loss of precision is magnified.
```
(square (sqrt 9223372036854775))
9223372036854776.0
```

```
(define (square x)
  (* x x))

(define (average x y)
  (/ (+ x y) 2))

(define (improve guess x)
  (average guess (/ x guess)))

(define (good-enough? old-guess new-guess)
  (<= (abs (- old-guess new-guess))
      (* new-guess 0.00001)))

(define (sqrt-iter old-guess new-guess x)
  (if (good-enough? old-guess new-guess)
      new-guess
      (sqrt-iter new-guess (improve new-guess x)
                 x)))

(define (sqrt x)
  (sqrt-iter 0.0 1.0 x))
```

Small
```
(square (sqrt 0.001))
0.001000000000000034

(square (sqrt 9))
9.0
```

Large
```
(square (sqrt 9223372036854775))
9223372036854776.0
```

I'm not I wrote the correct answer above, but it looks like this change works
better for smaller numbers than larger numbers.
