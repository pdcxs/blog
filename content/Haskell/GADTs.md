---
tags:
  - haskell
---

## 为什么需要 GADTs ？

GADTs，意为 Generalised Algebraic Data Types，中文称为广义代数数据类型，是对代数数据类型（ADT）的一种扩展。

那么，传统的代数数据类型有啥问题是无法解决的呢？

假设我们需要有以下数据类型：

```haskell
data Term t
 = Zero
 | Succ (Term Int)
 | Pred (Term Int)
 | IsZero (Term Int)
 | If (Term Bool) (Term t) (Term t)
```

目前为止，似乎是正常的，但是，实际上是有一些问题的，我们的 `Zero`，应当是一个 `Term Int`，而不是一个 `Term t`，这里无法体现出这一点，`Succ x` 与 `Pred x` 同理，而 `IsZero x` 应当是一个 `Term Bool`，同样不应当是 `Term t`。唯一确实是 `Term t` 的，是 `If x y z`.

现在，我们只是这样子定义是不会报错的，但是，如果定义以下内容：

```haskell
eval :: Term t -> t
eval Zero = 0
eval (Succ x) = eval x + 1
eval (Pred x) = eval x - 1
eval (IsZero x) = eval x == 0
eval (If x y z) = if eval x then eval y else eval z
```

此时，会在第二行的 `eval x` 处报错：

```
Couldn't match type ‘t’ with ‘Int’
  Expected: Term t
  Actual: Term Int
```

这是因为，`Succ x` 的类型为：`Term t`，因此应当返回 `t` 类型，按照 `data` 中的类型说明，`x` 的类型应当是 `Term Int`，因此，`eval x` 是 `Int`，再加1后，还是 `Int`，不是类型 `t`。

## GADTs 的用法

这个问题的根本，是使用 `data` 关键字创建的自定义类型中，所有的构造函数，都需要有统一的类型，但是，这个例子中，不同的构造函数返回的类型并不相同。**我们只能保证返回的是一个Term，不能保证包裹在里面的类型是相同的**。

现在我们对这个程序进行以下改造：

```haskell
{-# LANGUAGE GADTs #-}

data Term t where
 Zero :: Term Int
 Succ :: Term Int -> Term Int
 Pred :: Term Int -> Term Int
 IsZero :: Term Int -> Term Bool
 If :: Term Bool -> Term t -> Term t -> Term t

eval :: Term t -> t
eval Zero = 0
eval (Succ x) = eval x + 1
eval (Pred x) = eval x - 1
eval (IsZero x) = eval x == 0
eval (If x y z) = if eval x then eval y else eval z
```

现在就不会有错误了，`eval` 函数也可以正常运行。

我们再仔细思考一下与刚刚的不同之处。`eval (Succ x)` 中，`Succ x` 是一个 `Term Int`，而不是 `Term t`。因此，`eval (Succ x)` 的类型，就从 `Term t -> t` 变为了 `Term Int -> Int` 了，完全没有问题。

所以，GADTs，就是允许不同的构造函数，返回不同的类型。我们直接用函数的类型系统，来声明构造函数就可以了。
