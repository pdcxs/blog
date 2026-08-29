---
id: Basics for Monad Transformer
aliases: []
tags:
  - haskell
  - monad
  - monad_transformer
title: Monad Transformer 入门
---

本文主要参考博客：[A Gentle Introduction to Monad Transformers](https://two-wrongs.com/a-gentle-introduction-to-monad-transformers)
需要先掌握[[Monad]]

## Either Left Right

先从简单的概念入手。假设需要从一个email地址中提取其域名，判断一个字符串是否是合法的email地址是一个复杂的工作，这里采取简单的方法，就假设只能由一个@符号，该符号之后的部分都是域名。采用text库，需要引入`Data.Text`模块，并开启`OverloadedStrings`的flag。

```haskell
{-# LANGUAGE OverloadedStrings #-}

import qualified Data.Text.IO as T
import Data.Map as Map
import Control.Applicative

data LoginError = InvalidEmail deriving Show

getDomain :: Text -> Either LoginError Text
getDomain email =
  case splitOn "@" email of
    [name, domain] -> Right domain
    _ -> Left InvalidEmail
```

接下来可以对结果进行打印：

```haskell
printResult' :: Either LoginError Text -> IO ()
printResult' domain =
  case domain of
    Right text ->
      T.putStrLn (append "Domain: " text)
    Left InvalidEmail ->
      T.putStrLn "ERROR: Invalid domain"
```

当然，此代码可以通过`either :: (a -> c) -> (b -> c) -> Either a b -> c`方法进行优化：

```haskell
printResult =
  T.putStrLn . either
    (const "ERROR: Invalid domain")
    (append "Domain: ")
```

现在，假设需要用户来输入电子邮件地址：

```haskell
getToken :: IO (Either LoginError Text)
getToken = do
  T.putStrLn "Enter email address:"
  email <- T.getLine
  return (getDomain email)
```

接下来有用户在不同域名下的密码：

```haskell
users :: Map Text Text
users = Map.fromList
  [ ("example.com", "qwerty123"), ("localhost", "password")]
```

此时，错误会有其他类型：

```haskell
data LoginError =
  InvalidEmail | NoSuchUser | WrongPassword
  deriving Show
```

接下来就是变得麻烦起来的登录函数：

```haskell
userLogin :: IO (Either LoginError Text)
userLogin = do
  token <- getToken
  case token of
    Right domain ->
      case Map.lookup domain users of
        Just userpw -> do
          T.putStrLn "Enter password:"
          password <- T.getLine

      if userpw == password then
        return token
      else
        return [Left WrongPassword]
    Nothing ->
      return (Left NoSuchUser)
  left -> return left
```

这个代码明显有多过于复杂了，嵌套了太多层次。缺乏Haskell的美感。根本问题在于，IO用于处理IO操作，并不擅长处理错误，而Either擅长处理错误，并不山城处理IO操作。

## 自定义Monad

从刚刚的代码可以看到，一直出现类型`IO (Either e a)`，所以可以自己定义一个`Monad`：

```haskell
newtype EitherIO e a = EitherIO {
    runEitherIO :: IO (Either e a)
}
```

接下来就是把`EitherIO`类型制作成基础类型，融入到工具库中了。

```haskell
instance Functor (EitherIO e) where
  fmap f eio = wrapped
    where
      unwrapped = runEitherIO eio
      fmapped = fmap (fmap f) unwrapped
      wrapped = EitherIO fmapped
```

此函数可以简化为：

```haskell
instance Functor (EitherIO e) where
  fmap f = EitherIO . fmap (fmap f) . runEitherIO
```

其他两个类型的实例化：

```haskell
instance Applicative (EitherIO e) where
  pure :: a -> EitherIO e a
  pure a = EitherIO (return (Right a))
  (<*>) :: EitherIO e (a -> b) -> EitherIO e a -> EitherIO e b
  EitherIO f <*> EitherIO a = EitherIO t
    where
      t = do
      f1 <- f
        case f1 of
          Right f2 -> fmap f2 <$> a
          Left e -> return (Left e)

instance Monad (EitherIO e) where
  return :: a -> EitherIO e a
  return = pure
  (>>=) :: EitherIO e a -> (a -> EitherIO e b) -> EitherIO e b
  EitherIO a >>= f = EitherIO r
    where
      r = do
        c <- a
        case c of
          Right x -> runEitherIO $ f x
          Left e -> return (Left e)
```

另外的实现方案：

```haskell
instance Applicative (EitherIO e) where
    pure :: a -> EitherIO e a
    pure    = EitherIO . return . Right
    (<*>) :: EitherIO e (a -> b) -> EitherIO e a -> EitherIO e b
    f <*> x = EitherIO $
      liftA2 (<*>)
        (runEitherIO f)
        (runEitherIO x)

instance Monad (EitherIO e) where
  return :: a -> EitherIO e a
  return  = pure
  (>>=) :: EitherIO e a -> (a -> EitherIO e b) -> EitherIO e b
  x >>= f = EitherIO $
    runEitherIO x >>= either
      (return . Left)
      (runEitherIO . f)
```

## 使用EitherIO

如果直接按照如下方式使用：

```haskell
getToken :: EitherIO LoginError Text
getToken = do
  T.putStrLn "Enter email address: "
    input <- T.getLine
    return (getDomain input)
```

则后存在三个类型匹配错误，`T.putStrLn`返回`IO ()`，与`EitherIO`不匹配，`getLine`是`IO Text`，也与`EitherIO`不匹配，`getDomain input`则是`Either LoginError Text`，与`Text`不匹配。

第三个匹配问题很好解决，改为：

```haskell
EitherIO $ return (getDomain input)
```

注意，此处的`return`将`EitherIO LoginError Text` 变为了`IO (EitherIO LoginError Text`。

对于前两个，则可以改为：

```haskell
EitherIO (fmap Right (T.putStrLn "email"))
EitherIO (fmap Right (T.getLine))
```

所以，`getToken`函数现在变为了：

```haskell
getToken :: EitherIO LoginError Text
getToken = do
    EitherIO (fmap Right (T.putStrLn "Enter email address:"))
    input <- EitherIO (fmap Right T.getLine)
    EitherIO (return (getDomain input))
```

目前看起来，情况并没有更加改善。

## 使用Lift

可以定义如下两个函数，以把功能有限的Monad变换为功能更加强大的Monad：

```haskell
liftEither :: Either e a -> EitherIO e a
liftEither x = EitherIO (return x)

liftIO :: IO a -> EitherIO e a
liftIO x = EitherIO (fmap Right x)
```

现在，利用这两个函数，可以稍微简化`getToken`函数：

```haskell
getToken :: EitherIO LoginError Text
getToken = do
  liftIO (T.putStrLn "Enter email address:")
  input <- liftIO T.getLine
  liftEither (getDomain input)
```

对于`userLogin`，也可以改写为：

```haskell
userLogin :: EitherIO LoginError Text
userLogin = do
  token <- getToken
  userpw <-
    maybe
      (liftEither (Left NoSuchUser))
      return
      (Map.lookup token users)
  password <-
  liftIOliftIO $ T.putStrLn "Enter your password:" >> T.getLine
  if userpw == password
    then return token
    else liftEither (Left WrongPassword)
```

现在看起来已经没有多层嵌套了。`printResult`也可进行如下修改：

```haskell
printResult :: Either LoginError Text -> IO ()
printResult res =
  T.putStrLn $ case res of
    Right token ->
      append "Logged in with token: " token
    Left InvalidEmail ->
      "Invalid email address entered."
    Left NoSuchUser ->
      "No user with that email exists."
    Left WrongPassword ->
      "Wrong password."
```

## 错误传递

目前来看，如果想传递诸如`WrongPassword`的错误，必须写成：

```haskell
liftEither (Left WrongPassword)
```

挺复杂的，可以制作一个辅助函数：

```haskell
throwE :: e -> EitherIO e a
throwE x =
  liftEither (Left x)
```

现在，可以把`userLogin`写为：

```haskell
userLogin :: EitherIO LoginError Text
userLogin = do
  token <- getToken
  userpw <- maybe (throwE NoSuchUser)
    return (Map.lookup token users)
  passwd <- liftIO $
    T.puStrLn "Enter your password: " >>
    T.getLine
  if userpw == password then
    return token
  else
    throwE WrongPassword
```

## ExceptIO

现在，可以把`EitherIO`换为`ExceptIO`：

```haskell
newtype ExceptIO e a = ExceptIO {
  runExceptIO :: IO (Either e a)
}
```

和之前是一样的，就是换了个名字。

接下来，有了`throwE`，自然可以有`catchE`：

```haskell
catchE :: ExceptIO e a -> (e -> ExceptIO e a) -> ExceptIO e a
catchE throwing handler =
  ExceptIO $ do
    result <- runExceptIO throwing
    case result of
      Left failure ->
        runExceptIO (handler failure)
      success ->
        return success
```

接着可以写错误处理函数：

```haskell
wrongPasswordHandler :: LoginError -> ExceptIO LoginError Text
wrongPasswordHandler WrongPassword = do
  liftIO (T.putStrLn "Wrong password, one more chance.")
  userLogin
wrongPasswordHandler err =
  throwE err
```

`printError`函数则是打印错误信息，并重新抛出错误：

```haskell
printError :: LoginError -> ExceptIO LoginError a
printError err = do
  liftIO . T.putStrLn $ case err of
    WrongPassword ->
      "Wrong password. No more chances."
    NoSuchUser ->
      "No user with that email exists."
    InvalidEmail ->
      "Invalid email address entered."

  throwE err
```

可以创建以下函数：

```haskell
loginDialogue :: ExceptIO LoginError ()
loginDialogue = do
  let retry = userLogin `catchE` wrongPasswordHandler
  token <- retry `catchE` printError
  liftIO $ T.putStrLn $
    append "Logged in with token: " token
```

## Generalize

目前，`ExceptIO`只能和固定的`IO Monad`组合，但是如果想和其他的Monad组合，则需要将ExceptIO一般化。

现在，将`ExceptIO`更换为`ExceptT`，此处，T代表Transformer。因此，完整的程序变为了：

```haskell
{-# LANGUAGE OverloadedStrings #-}

import Control.Applicative (Applicative (liftA2))
import Data.Map as Map (Map, fromList, lookup)
import Data.Text (Text, append, splitOn)
import Data.Text.IO as T (getLine, putStrLn)

newtype ExceptT e m a = ExceptT
  { runExceptT :: m (Either e a)
  }

instance Functor m => Functor (ExceptT e m) where
  fmap :: Functor m => (a -> b) -> ExceptT e m a -> ExceptT e m b
  fmap f = ExceptT . fmap (fmap f) . runExceptT

-- XXX: Unlawful instance. Don't use for anything serious.
instance Applicative m => Applicative (ExceptT e m) where
  pure :: Applicative m => a -> ExceptT e m a
  pure = ExceptT . pure . Right
  (<*>) ::
    Applicative m =>
    ExceptT e m (a -> b) ->
    ExceptT e m a ->
    ExceptT e m b
  f <*> x = ExceptT $ liftA2 (<*>) (runExceptT f) (runExceptT x)

instance Monad m => Monad (ExceptT e m) where
  return :: Monad m => a -> ExceptT e m a
  return = pure
  (>>=) :: Monad m => ExceptT e m a -> (a -> ExceptT e m b) -> ExceptT e m b
  x >>= f = ExceptT $ runExceptT x >>= either (return . Left) (runExceptT . f)

liftEither :: Monad m => Either e a -> ExceptT e m a
liftEither x = ExceptT (return x)

lift :: Functor m => m a -> ExceptT e m a
lift x = ExceptT (fmap Right x)

throwE :: Monad m => e -> ExceptT e m a
throwE x = liftEither (Left x)

catchE :: Monad m => ExceptT e m a -> (e -> ExceptT c m a) -> ExceptT c m a
catchE throwing handler = ExceptT $ do
  x <- runExceptT throwing
  case x of
    Left failure -> runExceptT (handler failure)
    Right success -> return (Right success)

data LoginError
  = InvalidEmail
  | NoSuchUser
  | WrongPassword

users :: Map Text Text
users = Map.fromList [("example.com", "qwerty123"), ("localhost", "password")]

main :: IO ()
main = do
  runExceptT loginDialogue
  return ()

loginDialogue :: ExceptT LoginError IO ()
loginDialogue = do
  let retry = userLogin `catchE` wrongPasswordHandler
  token <- retry `catchE` printError
  lift $ T.putStrLn (append "Logged in with token: " token)

wrongPasswordHandler :: LoginError -> ExceptT LoginError IO Text
wrongPasswordHandler WrongPassword = do
  lift (T.putStrLn "Wrong password, one more chance.")
  userLogin
wrongPasswordHandler err = throwE err

printError :: LoginError -> ExceptT LoginError IO a
printError err = do
  lift . T.putStrLn $ case err of
    WrongPassword -> "Wrong password. No more chances."
    NoSuchUser -> "No user with that email exists."
    InvalidEmail -> "Invalid email address entered."
  throwE err

userLogin :: ExceptT LoginError IO Text
userLogin = do
  token <- getToken
  userpw <-
    maybe
      (throwE NoSuchUser)
      return
      (Map.lookup token users)
  password <- lift (T.putStrLn "Enter your password:" >> T.getLine)

  if userpw == password
    then return token
    else throwE WrongPassword

getToken :: ExceptT LoginError IO Text
getToken = do
  lift (T.putStrLn "Enter email address:")
  input <- lift T.getLine
  liftEither (getDomain input)

getDomain :: Text -> Either LoginError Text
getDomain email =
  case splitOn "@" email of
    [name, domain] -> Right domain
    _ -> Left InvalidEmail
```

注意：真正的`ExceptIO`的代码可以参看：[ExceptIO Applicative](https://hackage.haskell.org/package/transformers-0.6.0.6/docs/src/Control.Monad.Trans.Except.html#line-200)
代码如下：

```haskell
instance (Functor m, Monad m) => Applicative (ExceptT e m) where
  pure a = ExceptT $ return (Right a)
  {-# INLINE pure #-}
  ExceptT f <*> ExceptT v = ExceptT $ do
    mf <- f
    case mf of
      Left e -> return (Left e)
      Right k -> do
        mv <- v
        case mv of
          Left e -> return (Left e)
          Right x -> return (Right (k x))
  {-# INLINEABLE (<*>) #-}
  m *> k = m >>= \_ -> k
  {-# INLINE (*>) #-}

instance (Monad m) => Monad (ExceptT e m) where
  m >>= k = ExceptT $ do
    a <- runExceptT m
    case a of
      Left e -> return (Left e)
      Right x -> runExceptT (k x)
  {-# INLINE (>>=) #-}
```

可以看到，官方代码反而是啰嗦的版本，并不追求抽象。
