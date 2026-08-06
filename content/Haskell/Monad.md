---
tags:
  - haskell
---

## 到底什么是 `Monad`

一直以来，`Haskell` 社区都说：一旦学会 `Monad`，
就不要再写一个 `Monad` 的教程了，教程已经太多啦。
但是我这儿又忍不住写了一个教程，看看能否让这个内容变得简单一点。

很多人喜欢从数学的角度上去理解 `Monad`，但是我觉得，只需要掌握一句话：

> [!info] Monad 是什么
> Monad 是可以组合（串连执行）的动作。

掌握 `Monad` 的最好方法，是只考虑其最基本的定义，**不要用类比**，
比如将 `Monad` 想像成容器之类的。
这和数学概念的学习一样，用已有的知识框架去解释，
往往会出现以偏概全的情况。真正的学习方式，就是看到什么都直接套定义，
然后大量地练习和阅读，一段时间后，自然就掌握了。

所以，我们需要看 `Monad` 的定义，以下是官方源代码中的内容：

```haskell
class Applicative m => Monad m where
    -- | Sequentially compose two actions, passing any value produced
    -- by the first as an argument to the second.
    --
    -- \'@as '>>=' bs@\' can be understood as the @do@ expression
    --
    -- @
    -- do a <- as
    --    bs a
    -- @
    --
    -- An alternative name for this function is \'bind\', but some people
    -- may refer to it as \'flatMap\', which results from it being equivalent
    -- to
    --
    -- @\\x f -> 'join' ('fmap' f x) :: Monad m => m a -> (a -> m b) -> m b@
    --
    -- which can be seen as mapping a value with
    -- @Monad m => m a -> m (m b)@ and then \'flattening\' @m (m b)@ to @m b@ using 'join'.
    (>>=)       :: forall a b. m a -> (a -> m b) -> m b

    -- | Sequentially compose two actions, discarding any value produced
    -- by the first, like sequencing operators (such as the semicolon)
    -- in imperative languages.
    --
    -- \'@as '>>' bs@\' can be understood as the @do@ expression
    --
    -- @
    -- do as
    --    bs
    -- @
    --
    -- or in terms of @'(>>=)'@ as
    --
    -- > as >>= const bs
    (>>)        :: forall a b. m a -> m b -> m b
    m >> k = m >>= \_ -> k -- See Note [Recursive bindings for Applicative/Monad]
    {-# INLINE (>>) #-}

    -- | Inject a value into the monadic type.
    -- This function should /not/ be different from its default implementation
    -- as 'pure'. The justification for the existence of this function is
    -- merely historic.
    return      :: a -> m a
    return      = pure
```

我们唯一需要关注的，只有三点：

1. 官方的注释中对于 `Monad` 的解释
2. `Monad` 必然是一个 `Applicative`
3. `Monad` 的实现仅需要 `(>>=)` 函数

根据官方的解释，`Monad` 主要作用就是把两个动作连起来，把第一个动作产生的值，传递给第二个动作，
从而将两个动作组合成为一个动作。

`Applicative` 是可以组合的函数，那么函数和动作有何分别呢？
最主要的区别是，对于动作，可以根据**之前动作**的结果，决定当前动作的执行方式。
这里不用过多纠结，用多了自然就会碰到。如果非要举一个例子，可以参看[[#Monad vs Applicative]]

## 示例

我们需要用大量的例子来说明。

> [!example] Maybe
>
> 比如想要将多个计算结果进行组合
> `readInt :: Maybe Int`
>
> `safeDiv :: Int -> Int -> Maybe Int`
>
> `readInt >>= safeDiv 3 >>= \x -> "Result is " ++ show x`
>
> 中间如果出错，最终结果就会是 `Nothing`，
>
> 一切正常，就是 `Just ...`，最终组合成了一个 `Maybe String` 类型。

<!-- TODO: Add More Examples -->

## Monad vs Applicative

`Monad` 的强大之处，在于引用了时间的概念，即当前的行为取决于之前的结果。考虑以下例子：

```haskell
ifA :: Applicative f => Bool -> f a -> f a -> f a
ifA p e f = g <$> pure e <*> e <*> f
  where
    g c x y = if c then x else y
```

看似没有问题，但是如果执行：

```haskell
ifA True (Just ()) Nothing
```

会返回`Nothing`。这是由于，在 `Maybe` 的 `Applicative` 实现中，是这样的：

```haskell
instance Applicative Maybe where
  pure = Just
  Just f <*> m = fmap f m
  Nothing f <*> _m = Nothing
```

所以，执行到最后会是一个 `fmap m Nothing`，然后返回 `Nothing`。

而 `ifM` 则没有这个问题。考虑 `Maybe` 的 `Monad` 实现：

```haskell
instance Monad Maybe where
  (Just x) >>= k = k x
  Nothing >>= _ = Nothing
```

这里最终会直接组合成一个真正的函数调用，而不是依赖于 `fmap`了。
