vim: set filetype=scheme:

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
Define a procedure that takes three numbers as arguments and returns the sum of the squares of the two larger numbers

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
Observe that our model of evaluation allows for combinations whose operators are compound expressions. Use this observation to describe the behavior of the following procedure

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

Applicative Order Evaluation will loop forever because it will try to evaluate
all of its operators and operands and reduce them to their values.
Because `(p)` is defined as `(p)` it will attempt to reduce forever but will
never reach its 'value'.

Normal Order Evaluation however will return `0` because it only evaluates
its parameters as needed.  Since `x = 0` the interpreter doesn't need to
evaluate '<alternative>'.
