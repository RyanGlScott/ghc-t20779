# `ghc-t20779`

A minimal reproduction of [GHC#20779](https://gitlab.haskell.org/ghc/ghc/-/issues/20779).

# Steps to reproduce

You will need a recent GHC HEAD. I am using `cabal-install-3.6.2.0` in the steps below, although I'm not sure if that is important.

1. Make sure you do not have any prior installations of `base-orphans` in your `cabal` store for GHC HEAD.
2. Run `cabal build -w <path-to-ghc-head> --constraint="base-orphans==0.8.6"`. This should succeed.
3. Run `cabal build -w <path-to-ghc-head> --constraint="base-orphans==0.8.5"`. This will mysteriously fail with:

   ```
   Lib.hs:5:1: error:
       Could not load module ‘Data.Orphans’
       It is a member of the hidden package ‘base-orphans-0.8.6’.
       Perhaps you need to add ‘base-orphans’ to the build-depends in your .cabal file.
     |
   5 | import "base-orphans" Data.Orphans ()
     | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   ```

Here are all the steps together:

```
$ cabal build -w ~/Software/ghc-9.3.20211203/bin/ghc --constraint="base-orphans==0.8.6"
Resolving dependencies...
Build profile: -w ghc-9.3.20211203 -O1
In order, the following will be built (use -v for more details):
 - ghc-bug-0.1 (lib) (first run)
Configuring library for ghc-bug-0.1..
Preprocessing library for ghc-bug-0.1..
Building library for ghc-bug-0.1..
[1 of 1] Compiling Lib              ( Lib.hs, /home/ryanglscott/Documents/Hacking/Haskell/ghc-bug/dist-newstyle/build/x86_64-linux/ghc-9.3.20211203/ghc-bug-0.1/build/Lib.o, /home/ryanglscott/Documents/Hacking/Haskell/ghc-bug/dist-newstyle/build/x86_64-linux/ghc-9.3.20211203/ghc-bug-0.1/build/Lib.dyn_o )

$ cabal build -w ~/Software/ghc-9.3.20211203/bin/ghc --constraint="base-orphans==0.8.5"
Resolving dependencies...
Build profile: -w ghc-9.3.20211203 -O1
In order, the following will be built (use -v for more details):
 - ghc-bug-0.1 (lib) (configuration changed)
Configuring library for ghc-bug-0.1..
Preprocessing library for ghc-bug-0.1..
Building library for ghc-bug-0.1..
[1 of 1] Compiling Lib              ( Lib.hs, /home/ryanglscott/Documents/Hacking/Haskell/ghc-bug/dist-newstyle/build/x86_64-linux/ghc-9.3.20211203/ghc-bug-0.1/build/Lib.o, /home/ryanglscott/Documents/Hacking/Haskell/ghc-bug/dist-newstyle/build/x86_64-linux/ghc-9.3.20211203/ghc-bug-0.1/build/Lib.dyn_o ) [Data.Orphans changed]

Lib.hs:5:1: error:
    Could not load module ‘Data.Orphans’
    It is a member of the hidden package ‘base-orphans-0.8.6’.
    Perhaps you need to add ‘base-orphans’ to the build-depends in your .cabal file.
  |
5 | import "base-orphans" Data.Orphans ()
  | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```
