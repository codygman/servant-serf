# 🚜 servant-serf

## Example

### Input:
```haskell
{-# OPTIONS_GHC -F -pgmF servant-serf
  -optF --package-name=servant-serf-example
  -optF --is-handler-module=Foo.Handler.*
  -optF --ghc-options=-freduction-depth=0
#-}

module Foo.Api where

import Foo.Handler.HelloWorld
import Foo.Handler.GoodbyeWorld
```

### Output:
```haskell
{-# OPTIONS_GHC -fno-warn-partial-type-signatures -freduction-depth=0 #-}
{-# LANGUAGE PartialTypeSignatures #-}
{-# LANGUAGE ExplicitNamespaces #-}
{-# LANGUAGE TypeOperators #-}
module Foo.Api (type Route, handler) where

import Servant ((:<|>)((:<|>)))
import qualified GHC.Stack as Stack
import qualified Servant

import qualified Foo.Handler.HelloWorld
import qualified Foo.Handler.GoodbyeWorld

type Route
  = Foo.Handler.HelloWorld.Route
  :<|> Foo.Handler.GoodbyeWorld.Route

handler :: Stack.HasCallStack => Servant.ServerT Route _
handler
  = Foo.Handler.HelloWorld.handler
  :<|> Foo.Handler.GoodbyeWorld.handler
```

### Module discovery:
When the preprocessor runs it looks at your package.yaml and the `is-handler-module` option
to determine which modules should be imported to the generated API module.
Because the ordering of the routes matters, you must explicitly import each route in the order
they will appear in the Route type. If you haven't yet imported a module which `servant-serf` has
disovered, you'll receive a helpful error message like the following:
```
/home/zach/Dev/servant-serf/servant-serf-example//tmp/ghc32747_0/ghc_1.hspp:9:11: error:
    • Missing handler imports. Consider adding the following imports to the file src/Foo/Api.hs
       or updating the is_handler_module regular expression in your .servant-serf.toml
      import Foo.Handler.GoodbyeWorld
```
