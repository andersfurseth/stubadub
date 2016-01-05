# stubadub

A small stubbing library for Clojure.

To quote [Kris Jenkins](https://twitter.com/krisajenkins) in his [Which Programming Languages Are Functional?](http://blog.jenkster.com/2015/12/which-programming-languages-are-functional.html) blog post:

> Seen through the lens of side-effects, mocks are a flag that your code is
> impure, and in the functional programmer's eye, proof that something is wrong.

So yeah, you should treat stubbing your code much like stubbing your toe: It's
painful and you should strive to avoid it.

Sometimes tho, the code you're testing needs to do some side-effecting. Or maybe
all the code you're maintaining isn't a dreamy pure function landscape.

In that case, feel free to use stubadub.

## Install

Add `[stubadub "1.0.0"]` to `[:profiles :dev :dependencies]` in your `project.clj`.

## Usage

`with-stub` takes a function symbol and replaces it in the scope of its body.
Use `calls-to` to return the list of calls to the stub.

Like this:

```clj
(with-stub spit
  (spit "test1.txt" "not written to disk")
  (spit "test2.txt" "not written to disk either")
  (calls-to spit))

;; => (("test1.txt" "not written to disk")
;;     ("test2.txt" "not written to disk either"))
```

You can specify a return value for the stub with `:returns`:

```clj
(with-stub slurp :returns "not read from disk"
  (slurp "test3.txt"))

;; => "not read from disk"
```

And you can nest several stubs, at which point you're probably not a happy
person:

```clj
(with-stub spit
  (with-stub slurp :returns "not read from disk either"
    (spit "test4.txt" (slurp "test5.txt"))
    (calls-to spit)))

;; => (("test4.txt" "not read from disk either"))
```

## Contribute

I mainly consider this library complete, but if you have a wonderful addition to
the library, please don't let that stop you. :)

Remember to add tests for your feature or fix tho, or I'll
certainly break it later.

### Running the tests

Run tests with

    lein expectations

Run tests automatically on changes with

    lein autoexpect

If you want notifications, on OSX you can `brew install terminal-notifier` then

    lein autoexpect :notify

## License

Copyright © 2016 Magnar Sveen

Distributed under the Eclipse Public License, the same as Clojure.