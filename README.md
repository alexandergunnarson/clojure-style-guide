# Clojure Style Guide

This Clojure style guide recommends best practices so that real-world Clojure programmers can write code that can be maintained by other real-world Clojure programmers. A style guide that reflects real-world usage gets used.

I didn't come up with all the rules out of nowhere; they are mostly based on my experience as a professional software engineer, feedback and suggestions from members of the Clojure community, various highly regarded Clojure programming resources such as ["Clojure Programming"](http://www.clojurebook.com/) and ["The Joy of Clojure"](http://joyofclojure.com/), and of course the [Clojure Style Guide](https://github.com/bbatsov/clojure-style-guide).

Note that the Clojure developing community maintains a list of [coding standards for libraries](http://dev.clojure.org/display/community/Library+Coding+Standards), too.

## Table of Contents

* [Formatting](#formatting)
* [Namespace Organization](#namespace-organization)
* [Usage and Syntax](#usage-and-syntax)
* [Naming](#naming)
* [Collections](#collections)
* [Types & Records](#types--records)
* [Macros](#macros)
* [Comments](#comments)
* [Documentation](#documentation)
* [Mutability](#mutability)

## Formatting

* <a name="spaces"></a>
Use **spaces** for indentation. No hard tabs.
<sup>[[link](#spaces)]</sup>

* <a name="body-indentation"></a>
Use 2 spaces to indent the bodies of forms that have body parameters that span multiple lines. This covers all `def` forms, special forms and macros that introduce local bindings (e.g. `loop`, `let`, `when-let`) and many macros like `when`, `cond`, `as->`, `cond->`, `case`, `with-*`, etc.
For macros that take pairs of arguments (e.g. `cond`, `cond->`, `case`), it is acceptable to group the pairs by either putting each pair on the same line or (preferably only when pairs are long) by putting each member of each pair on its own line and indenting the second of the pairs two additional spaces.
<sup>[[link](#body-indentation)]</sup>

    ```Clojure
    ;; good
    (when something (something-else))

    ;; good
    (when something
      (something-else))

    ;; good
    (cond this that :else other)

    ;; good
    (cond
      this  that
      :else other)

    ;; good
    (cond
      this-very-long-predicate-that-goes-on-for-a-while
        this-very-long-then-clause-that-goes-on-for-a-while
      :else
        other)

    ;; good
    (with-out-str
      (println "Hello, ")
      (println "world!"))

    ;; bad - additional space and mismatch of horizontal vs. vertical alignment
    (cond
      this that

      :else
      other)

    ;; bad - pairs are not long enough to necessitate a newline approach, and then-clauses are not
    ;; indented two spaces
    (cond
      this
      that
      :else
      other)

    ;; bad - four spaces
    (when something
        (something-else))

    ;; bad - one space
    (with-out-str
     (println "Hello, ")
     (println "world!"))
    ```

* <a name="vertically-align-fn-args"></a>
Given arguments that must span multiple lines, if the function/macro name is 3 chars or less, align the arguments. Otherwise use 2 spaces to indent the arguments. Arguments that span multiple lines should either take up as much of the line as possible, or they should take up one line each after the function/macro name (if one or more arguments are beyond a sufficient length).
<sup>[[link](#vertically-align-fn-args)]</sup>

    ```Clojure
    ;; good
    (do-this this-arg this-other-arg)

    ;; good
    (if condition then else)

    ;; good
    (or this that other)

    ;; good if the line limit is about to be reached
    (do-this-thing-a-few-times this-arg
      this-other-arg this-yet-another-arg)

    ;; good if the line limit is about to be reached
    (do-this
      this-arg
      this-other-arg)

    ;; good if the line limit is about to be reached
    (if condition
        then
        else)

    ;; good if the line limit is about to be reached
    (or this
        that
        other)

    ;; good if the line limit is about to be reached
    (myf this
         that
         other)

    ;; good only if line length doesn't permit `this-other-arg` on the same line
    (do-this this-arg
      this-other-arg)

    ;; bad (function name is too long to line up args)
    (my-long-function this-arg
                      this-other-arg)

    ;; bad (function name is too short to not line up args)
    (if condition
      then
      else)

    ;; bad (function name is too short to not line up args)
    (or
      this
      that
      other)

    ;; bad - second arg is nto indented 2 spaces
    (my-function this-arg
        this-other-arg)
    ```

* <a name="vertically-align-bindings-and-map"></a>
Vertically align bindings and map keywords.
<sup>[[link](#vertically-align-bindings-and-map)]</sup>

    ```Clojure
    ;; good
    (let [thing1  "some stuff"
          thing30 "other stuff"]
      {:thing1  thing1
       :thing30 thing30})

    (binding [thing1  "some stuff"
              thing30 "other stuff"]
      ...)

    (t/fn [thing1  t/long?
           thing30 t/string?]
      ...)

    (for [thing1  [0 1 2]
          thing30 [3 1 4]]
      ...)

    ;; bad
    (let [thing1 "some stuff"
      thing30 "other stuff"]
      {:thing1 thing1
      :thing30 thing30})

    ;; bad
    (let [thing1 "some stuff"
          thing30 "other stuff"]
      {:thing1 thing1
       :thing30 thing30})
    ```

* <a name="no-new-line-after-fn-name"></a>
Omit the new line between the function name and arglist for `defn` only when there is no docstring or metadata, provided doing so does not exceed the line limit.
<sup>[[link](#no-new-line-after-fn-name)]</sup>

    ```Clojure
    ;; good
    (defn foo [x]
      (bar x))

    ;; good
    (defn foo
      {:meta-key "meta-val"}
      [x]
      (bar x))

    ;; good provided the line limit has been reached
    (defn foo
      [a b c d e f g h i]
      (bar x))

    ;; bad
    (defn foo
      [x]
      (bar x))

    ;; bad
    (defn foo
      [x] (bar x))
    ```

* <a name="multimethod-dispatch-val-placement"></a>
Place the `dispatch-val` and arglist of a multimethod on the same line as the function name, provided doing so does not exceed the line limit.
<sup>[[link](#multimethod-dispatch-val-placement)]</sup>

    ```Clojure
    ;; good
    (defmethod foo :bar [x] (baz x))

    ;; good
    (defmethod foo :bar [x]
      (baz x))

    ;; good only if line limit has been reached
    (defmethod foo :bar
      [x]
      (baz x))

    ;; bad
    (defmethod foo
      :bar
      [x]
      (baz x))

    ;; bad
    (defmethod foo
      :bar [x]
      (baz x))
    ```

* <a name="oneline-short-fn"></a>
Omit the new line between the argument vector and a function body if the line limit permits and there are not multiple forms in the body.
<sup>[[link](#oneline-short-fn)]</sup>

    ```Clojure
    ;; good
    (defn foo [x] (bar x))

    ;; good if line limit has been reached
    (defn foo [x]
      (bar x))

    ;; good
    (defn foo
      ([x] (bar x))
      ([x y] (if (predicate? x) (bar x) (baz x))))

    ;; good if arities are easy to understand
    (defn foo ([x] (bar x)) ([x y] (if (predicate? x) (bar x) (baz x))))

    ;; bad
    (defn foo
      [x] (if (predicate? x) (bar x) (baz x)))
    ```

* <a name="multiple-arity-indentation"></a>
Indent each arity form of a function definition two spaces.
<sup>[[link](#multiple-arity-indentation)]</sup>

    ```Clojure
    ;; good
    (defn foo
      "I have two arities."
      ([x]
        (foo x 1))
      ([x y]
        (+ x y)))

    ;; bad - only one space
    (defn foo
      "I have two arities."
      ([x]
       (foo x 1))
      ([x y]
       (+ x y)))
    ```

* <a name="multiple-arity-order"></a>
Sort the arities of a function from fewest to most arguments.
<sup>[[link](#multiple-arity-order)]</sup>

    ```Clojure
    ;; good - it's easy to scan for the nth arity
    (defn foo
      "I have two arities."
      ([x] (foo x 1))
      ([x y] (+ x y)))

    ;; bad - unordered
    (defn foo
      ([x] 1)
      ([x y z] (foo x (foo y z)))
      ([x y] (+ x y))
      ([w x y z & more] (reduce foo (foo w (foo x (foo y z))) more)))
    ```

* <a name="trim-whitespace"></a>
Automatically trim whitespace.
<sup>[[link](#trim-whitespace)]</sup>

    * Most editors have the capability to auto-trim whitespace.

* <a name="bracket-spacing"></a>
If any text precedes an opening bracket(`(`, `{` and `[`) or follows a closing bracket(`)`, `}` and `]`), separate that text from that bracket with a space. Conversely, leave no space after an opening bracket and before following text, or after preceding text and before a closing bracket.
<sup>[[link](#bracket-spacing)]</sup>

    ```Clojure
    ;; good
    (foo (bar baz) quux)

    ;; bad
    (foo(bar baz)quux)
    (foo ( bar baz ) quux)
    ```

* <a name="no-commas-for-seq-literals"></a>
Don't use commas between the elements of sequential collection literals.
<sup>[[link](#no-commas-for-seq-literals)]</sup>

    ```Clojure
    ;; good
    [1 2 3]
    (1 2 3)

    ;; bad
    [1, 2, 3]
    (1, 2, 3)
    ```

* <a name="opt-commas-in-map-literals"></a>
Optionally enhance the readability of map literals via judicious use of commas and line breaks.
<sup>[[link](#opt-commas-in-map-literals)]</sup>

    ```Clojure
    ;; good
    {:name "Bruce Wayne" :alter-ego "Batman"}

    ;; good and arguably a bit more readable
    {:name      "Bruce Wayne"
     :alter-ego "Batman"}

    ;; good and more compact
    {:name "Bruce Wayne", :alter-ego "Batman"}
    ```

* <a name="gather-trailing-parens"></a>
Place all trailing parentheses on a single line instead of distinct lines, unless a comment prevents.
<sup>[[link](#gather-trailing-parens)]</sup>

    ```Clojure
    ;; good; single line
    (when something
      (something-else))

    ;; okay; distinct lines but comment prevents gathering
    (when something
      (something-else)
      ;; some comment that belongs after `something-else` but inside `when`
    )

    ;; bad; distinct lines
    (when something
      (something-else)
    )
    ```

* <a name="blank-lines-between-top-level-forms"></a>
Use a single blank line between top-level forms unless they are strongly related.
<sup>[[link](#blank-lines-between-top-level-forms)]</sup>

    ```Clojure
    ;; good
    (def x ...)

    (defn foo ...)

    ;; good
    (def min-rows 10)
    (def max-rows 20)
    (def min-cols 15)
    (def max-cols 30)

    ;; good
    (defn   abcde  [] ...)
    (t/defn fghijk [] ...)
    (defn   klmno  [] ...)

    ;; bad
    (def x ...)
    (defn foo ...)

    ;; bad
    (def x ...)


    (defn foo ...)
    ```

* <a name="no-blank-lines-within-def-forms"></a>
Do not place blank lines in the middle of a form (e.g. `do` block, function/macro definition, etc.).
<sup>[[link](#no-blank-lines-within-def-forms)]</sup>

* <a name="100-character-limits"></a>
Where feasible, avoid making lines longer than 100 characters.
<sup>[[link](#100-character-limits)]</sup>

* <a name="trailing-whitespace"></a>
Use trailing whitespace of exactly one line.
<sup>[[link](#trailing-whitespace)]</sup>

## Namespace Organization

* <a name="one-file-per-namespace"></a>
Use one file per namespace.
<sup>[[link](#one-file-per-namespace)]</sup>

* <a name="comprehensive-ns-declaration"></a>
Start every namespace with a comprehensive `ns` form, comprised of `refer-clojure`, `require`s and `import`s, in that order.
<sup>[[link](#comprehensive-ns-declaration)]</sup>

    ```Clojure
    (ns examples.ns
      (:refer-clojure
        :exclude [next replace remove])
      (:require
        [clojure.java.shell :as sh]
        [clojure.set        :as set]
        [clojure.string     :as s
          :refer [blank?]])
      (:import
        java.text.SimpleDateFormat
        java.util.Date
        [java.util.concurrent Executors LinkedBlockingQueue]))
    ```

* <a name="prefer-require-over-use"></a>
In the `ns` form prefer `:require :as` over `:require :refer` over `:require :refer :all`.  Prefer `:require` over `:use`; the latter form is deprecated.
<sup>[[link](#prefer-require-over-use)]</sup>

    ```Clojure
    ;; good
    (ns examples.ns
      (:require
        [clojure.zip :as zip]))

    ;; good
    (ns examples.ns
      (:require
        [clojure.zip
          :refer [lefts rights]]))

    ;; bad
    (ns examples.ns
      (:require
        [clojure.zip  
          :refer :all]))

    ;; bad
    (ns examples.ns
      (:use clojure.zip))
    ```

* <a name="no-single-segment-namespaces"></a>
  Avoid single-segment namespaces.
<sup>[[link](#no-single-segment-namespaces)]</sup>

    ```Clojure
    ;; good
    (ns example.ns)

    ;; bad
    (ns example)
    ```

* <a name="loc-per-fn-limit"></a>
Avoid excessively long functions. Most functions should have no more than 5-10 lines per overload. Long functions should be the exception, not the rule.
<sup>[[link](#loc-per-fn-limit)]</sup>

* <a name="5-positional-fn-args-limit"></a>
Avoid arglists with five or more positional arguments.
<sup>[[link](#5-positional-fn-args-limit)]</sup>

* <a name="forward-references"></a>
Generally avoid forward references. They should only be necessary to avoid circularity. Use `declare` to enable forward references when forward references are necessary.
<sup>[[link](#forward-references)]</sup>

## Usage and Syntax

* <a name="ns-fns-only-in-repl"></a>
Avoid the use of namespace-manipulating functions like `require` and `refer`. They are entirely unnecessary outside of a REPL environment.
<sup>[[link](#ns-fns-only-in-repl)]</sup>

* <a name="higher-order-fns"></a>
Prefer higher-order functions like `map` to `loop/recur`.
<sup>[[link](#higher-order-fns)]</sup>

* <a name="type-checks"></a>
Prefer type checks in `t/fn` and `t/defn` to checks inside a function's body.
<sup>[[link](#type-checks)]</sup>

    ```Clojure
    ;; good
    (t/defn foo [x pos?] (bar x))

    ;; bad
    (t/defn foo [x _]
      (if (pos? x)
          (bar x)
          (throw (IllegalArgumentException. "x must be a positive number!")))
    ```

* <a name="dont-def-vars-inside-fns"></a>
Don't define vars inside functions.
<sup>[[link](#dont-def-vars-inside-fns)]</sup>

    ```Clojure
    ;; very bad
    (defn foo []
      (def x 5)
      ...)
    ```

* <a name="dont-shadow-clojure-core"></a>
Don't shadow `clojure.core` names with local bindings.
<sup>[[link](#dont-shadow-clojure-core)]</sup>

    ```Clojure
    ;; bad - you're forced to use clojure.core/(map|name) fully qualified inside
    (defn foo [map name]
      ...)
    ```

* <a name="alter-var"></a>
Use `alter-var-root` instead of `def` to change the value of a var.
<sup>[[link]](#alter-var)</sup>

    ```Clojure
    ;; good
    (def thing 1) ; value of thing is now 1
    ; do some stuff with thing
    (alter-var-root #'thing (constantly nil)) ; value of thing is now nil

    ;; bad
    (def thing 1)
    ; do some stuff with thing
    (def thing nil)
    ; value of thing is now nil
    ```

* <a name="when-instead-of-single-branch-if"></a>
Use `when` instead of `(if <condition> <then-clause>)`.
<sup>[[link](#when-instead-of-single-branch-if)]</sup>

    ```Clojure
    ;; good
    (when pred (foo))

    ;; bad
    (if pred (foo))
    ```

* <a name="if-when-let"></a>
Use `if-let` instead of `let` + `if`, and `when-let` instead of `let` + `when`.
<sup>[[link](#if-when-let)]</sup>

* <a name="if-when-not"></a>
Use `if-not` instead of `(if (not ...) ...)`, and `when-not` instead of `(when (not ...) ...)`.
<sup>[[link](#if-when-not)]</sup>

* <a name="not-equal"></a>
Use `not=` instead of `(not (= ...))`.
<sup>[[link](#not-equal)]</sup>

    ```Clojure
    ;; good
    (not= foo bar)

    ;; bad
    (not (= foo bar))
    ```

* <a name="multiple-arity-of-gt-and-ls-fns"></a>
When doing comparisons, prefer using the varargs arities of `<`, `>` rather than multiple blocks.
<sup>[[link](#multiple-arity-of-gt-and-ls-fns)]</sup>

    ```Clojure
    ;; good
    (< 5 x 10)

    ;; bad
    (and (> x 5) (< x 10))
    ```

* <a name="single-param-fn-literal"></a>
Prefer `%` over `%1` in function literals with only one parameter.
<sup>[[link](#single-param-fn-literal)]</sup>

    ```Clojure
    ;; good
    #(Math/round %)

    ;; bad
    #(Math/round %1)
    ```

* <a name="multiple-params-fn-literal"></a>
Prefer `%1` over `%` in function literals with more than one parameter.
<sup>[[link](#multiple-params-fn-literal)]</sup>

    ```Clojure
    ;; good
    #(Math/pow %1 %2)

    ;; bad
    #(Math/pow % %2)
    ```

* <a name="no-useless-anonymous-fns"></a>
Don't wrap functions in anonymous functions when you don't need to.
<sup>[[link](#no-useless-anonymous-fns)]</sup>

    ```Clojure
    ;; good
    (filter even? (range 1 10))

    ;; bad
    (filter #(even? %) (range 1 10))
    ```

* <a name="no-multiple-forms-fn-literals"></a>
Don't use function literals if the function body will consist of more than one form.
<sup>[[link](#no-multiple-forms-fn-literals)]</sup>

    ```Clojure
    ;; good
    (fn [x]
      (println x)
      (* x 2))

    ;; bad (you need an explicit do form)
    #(do (println %)
         (* % 2))
    ```

* <a name="complement"></a>
Favor the use of `complement` versus the use of an anonymous function.
<sup>[[link](#complement)]</sup>

    ```Clojure
    ;; good
    (filter (complement some-pred?) coll)

    ;; bad
    (filter #(not (some-pred? %)) coll)
    ```

    This rule should obviously be ignored if the complementing predicate
    exists in the form of a separate function (e.g. `even?` and `odd?`).

* <a name="comp"></a>
Leverage `comp` when doing so yields simpler code.
<sup>[[link](#comp)]</sup>

    ```Clojure
    ;; Assuming `(:require [clojure.string :as str])`...

    ;; good
    (map #(str/capitalize (str/trim %)) ["top " " test "])

    ;; better
    (map (comp str/capitalize str/trim) ["top " " test "])
    ```

* <a name="partial"></a>
Leverage `partial` when doing so yields simpler code.
<sup>[[link](#partial)]</sup>

    ```Clojure
    ;; good
    (map #(+ 5 %) (range 1 10))

    ;; (arguably) better
    (map (partial + 5) (range 1 10))
    ```

* <a name="threading-macros"></a>
Prefer the use of the threading macros `->` (thread-first) and `->>` (thread-last) to form nesting.
<sup>[[link](#threading-macros)]</sup>

    ```Clojure
    ;; good
    (-> [1 2 3]
        reverse
        (conj 4)
        prn)

    ;; not as good
    (prn (conj (reverse [1 2 3])
               4))

    ;; good
    (->> (range 1 10)
         (filter even?)
         (map (partial * 2)))

    ;; not as good
    (map (partial * 2)
         (filter even? (range 1 10)))
    ```

* <a name="else-keyword-in-cond"></a>
Use `:else` as the catch-all test expression in `cond`.
<sup>[[link](#else-keyword-in-cond)]</sup>

    ```Clojure
    ;; good
    (cond
      (neg? n) "negative"
      (pos? n) "positive"
      :else "zero")

    ;; bad
    (cond
      (neg? n) "negative"
      (pos? n) "positive"
      true "zero")
    ```

* <a name="conditionals"></a>
Prefer `condp` instead of `cond` when the predicate and expression don't change. Prefer `case` instead of `cond` or `condp` when test expressions are compile-time constants.
<sup>[[link](#conditionals)]</sup>

    ```Clojure
    ;; good
    (cond
      (= x 10) :ten
      (= x 20) :twenty
      (= x 30) :forty
      :else :dunno)

    ;; better
    (condp = x
      10 :ten
      20 :twenty
      30 :forty
      :dunno)

    ;; best
    (case x
      10 :ten
      20 :twenty
      30 :forty
      :dunno)
    ```

* <a name="set-as-predicate"></a>
  Use a `set` as a predicate when appropriate.
<sup>[[link](#set-as-predicate)]</sup>

    ```Clojure
    ;; acceptable
    (remove #(= % 1) [0 1 2 3 4 5])

    ;; acceptable but perhaps unnecessary object allocation
    (remove #{1} [0 1 2 3 4 5])

    ;; good
    (count (filter #{\a \e \i \o \u} "mary had a little lamb"))

    ;; bad
    (count (filter #(or (= % \a)
                        (= % \e)
                        (= % \i)
                        (= % \o)
                        (= % \u))
                   "mary had a little lamb"))
    ```

* <a name="inc-and-dec"></a>
Use `(inc x)` & `(dec x)` instead of `(+ x 1)` and `(- x 1)`.
<sup>[[link](#inc-and-dec)]</sup>

* <a name="pos-and-neg"></a>
Use `(pos? x)`, `(neg? x)` & `(zero? x)` instead of `(> x 0)`, `(< x 0)` & `(= x 0)`.
<sup>[[link](#pos-and-neg)]</sup>

* <a name="sugared-java-interop"></a>
Use the sugared Java interop forms.
<sup>[[link](#sugared-java-interop)]</sup>

    ```Clojure
    ;;; object creation
    ;; good
    (java.util.ArrayList. 100)

    ;; bad
    (new java.util.ArrayList 100)

    ;;; static method invocation
    ;; good
    (Math/pow 2 10)

    ;; bad
    (. Math pow 2 10)

    ;;; instance method invocation
    ;; good
    (.substring "hello" 1 3)

    ;; bad
    (. "hello" substring 1 3)

    ;;; static field access
    ;; good
    Integer/MAX_VALUE

    ;; bad
    (. Integer MAX_VALUE)

    ;;; instance field access
    ;; good
    (.someField some-object)

    ;; bad
    (. some-object someField)
    ```

* <a name="compact-metadata-notation-for-true-flags"></a>
Use the compact metadata notation for metadata that contains only slots whose keys are keywords and whose value is boolean `true`.
<sup>[[link](#compact-metadata-notation-for-true-flags)]</sup>

    ```Clojure
    ;; good
    (def ^:private a 5)

    ;; bad
    (def ^{:private true} a 5)
    ```

* <a name="private"></a>
Denote private parts of your code.
<sup>[[link](#private)]</sup>

    ```Clojure
    ;; good
    (defn- private-fun [] ...)
    ;; good
    (def- private-var ...)

    ;; bad - not private at all
    (defn private-fun [] ...)
    ;; bad - overly verbose
    (defn ^:private private-fun [] ...)
    ;; bad - overly verbose
    (def ^:private private-var ...)
    ;; bad - not private at all
    (def private-var ...)
    ```

## Naming

* <a name="ns-naming-schemas"></a>
When naming namespaces favor the following two schemas:
<sup>[[link](#ns-naming-schemas)]</sup>

    * `project.module`
    * `organization.project.module`

* <a name="lisp-case-ns"></a>
Use `lisp-case` in composite namespace segments(e.g. `bruce.project-euler`)
<sup>[[link](#lisp-case-ns)]</sup>

* <a name="lisp-case"></a>
Use `lisp-case` for function and variable names.
<sup>[[link](#lisp-case)]</sup>

    ```Clojure
    ;; good
    (def some-var ...)
    (defn some-fun ...)

    ;; bad
    (def someVar ...)
    (defn somefun ...)
    (def some_fun ...)
    ```

* <a name="CamelCase-for-protocols-records-structs-and-types"></a>
Use `CamelCase` for protocols, records, structs, and types. (Keep acronyms like HTTP, RFC, XML uppercase.)
<sup>[[link](#CamelCase-for-protocols-records-structs-and-types)]</sup>

* <a name="pred-with-question-mark"></a>
The names of predicate methods (methods that return a boolean value) should end in a question mark (e.g., `even?`).
<sup>[[link](#pred-with-question-mark)]</sup>

    ```Clojure
    ;; good
    (defn palindrome? ...)

    ;; bad
    (defn palindrome-p ...) ; Common Lisp style
    (defn is-palindrome ...) ; Java style
    ```

* <a name="effectful-fns-with-exclamation-mark"></a>
The names of functions/macros that cause side effects should end with an exclamation mark (e.g. `reset!`).
<sup>[[link](#effectful-fns-with-exclamation-mark)]</sup>

* <a name="arrow-instead-of-to"></a>
Use `>` instead of `to` in the names of conversion functions.
<sup>[[link](#arrow-instead-of-to)]</sup>

    ```Clojure
    ;; good
    (defn f>c ...)

    ;; not preferred since `f->c` means "a map from f to c"
    (defn f->c ...)

    ;; not so good
    (defn f-to-c ...)
    ```

* <a name="earmuffs-for-dynamic-vars"></a>
Use `*earmuffs*` for things intended for rebinding (ie. are dynamic).
<sup>[[link](#earmuffs-for-dynamic-vars)]</sup>

    ```Clojure
    ;; good
    (def ^:dynamic *a* 10)

    ;; bad
    (def ^:dynamic a 10)
    ```

* <a name="dont-flag-constants"></a>
Don't use a special notation for constants; everything is assumed a constant unless specified otherwise.
<sup>[[link](#dont-flag-constants)]</sup>

* <a name="underscore-for-unused-bindings"></a>
Use `_` for destructuring targets and formal argument names whose value will be ignored by the code at hand.
<sup>[[link](#underscore-for-unused-bindings)]</sup>

    ```Clojure
    ;; good
    (let [[a b _ c] [1 2 3 4]]
      (println a b c))

    (dotimes [_ 3]
      (println "Hello!"))

    ;; bad
    (let [[a b c d] [1 2 3 4]]
      (println a b d))

    (dotimes [i 3]
      (println "Hello!"))
    ```

* <a name="idiomatic-names"></a>
Use idiomatic names like `pred` and `xs` (these are established in part in clojure.core).
<sup>[[link](#idiomatic-names)]</sup>

    * in functions:
        * `x` - arbitrary input
        * `f`, `g`, `h` - function input
        * `n` - a size
        * `i` - integer index
        * `xs` - collection (consumed/treated sequentially)
        * `m` - map
        * `s` - string input
        * `re` - regular expression
        * `pred` - a predicate function
        * `& xs` - variadic input
        * `xf` - a transducer
    * in macros:
        * `expr` - an expression
        * `body` - a macro body

## Collections

* <a name="avoid-lists"></a>
Avoid the use of lists for generic data storage (unless a list is exactly what you need).
<sup>[[link](#avoid-lists)]</sup>

* <a name="keywords-for-map-keys"></a>
Prefer the use of keywords for map keys.
<sup>[[link](#keywords-for-map-keys)]</sup>

    ```Clojure
    ;; good
    {:name "Bruce" :age 30}

    ;; bad
    {"name" "Bruce" "age" 30}
    ```

* <a name="literal-coll-syntax"></a>
Prefer the use of the literal collection syntax where applicable. However, when defining sets, only use literal syntax when the values are compile-time constants.
<sup>[[link](#literal-coll-syntax)]</sup>

    ```Clojure
    ;; good
    [1 2 3]
    #{1 2 3}
    (hash-set (func1) (func2)) ; values determined at runtime

    ;; bad
    (vector 1 2 3)
    (hash-set 1 2 3)
    #{(func1) (func2)} ; will throw runtime exception if (func1) = (func2)
    ```

* <a name="keywords-as-fn-to-get-map-values"></a>
Prefer the use of keywords as functions for retrieving values from maps, where applicable.
<sup>[[link](#keywords-as-fn-to-get-map-values)]</sup>

    ```Clojure
    (def m {:name "Bruce" :age 30})

    ;; good
    (:name m)

    ;; more verbose than necessary
    (get m :name)

    ;; bad - susceptible to NullPointerException
    (m :name)
    ```

## Types & Records

* <a name="record-constructors"></a>
Don’t use the interop syntax to construct type and record instances unless performance clearly dictates it. `deftype` and `defrecord` automatically create constructor functions. Use those instead of the interop syntax, as they make it clear that you're dealing with a `deftype` or a `defrecord`. See [this article](https://stuartsierra.com/2015/05/17/clojure-record-constructors) for more details.
  <sup>[[link](#record-constructors)</sup>

    ``` Clojure
    (defrecord Foo [a b])
    (deftype Bar [a b])

    ;; good
    (->Foo 1 2)
    (map->Foo {:b 4 :a 3})
    (->Bar 1 2)

    ;; bad
    (Foo. 1 2)
    (Bar. 1 2)
    ```

* <a name="custom-record-constructors-naming"></a>
Don't override the auto-generated type/record constructor functions. People expect them to have a certain behavior and you changing this behavior violates the principle of the least surprise . See [this article](https://stuartsierra.com/2015/05/17/clojure-record-constructors) for more details.
  <sup>[[link](#custom-record-constructors-naming)</sup>

    ``` Clojure
    (defrecord Foo [num])

    ;; good
    (t/defn >foo [num pos?] (->Foo num))

    ;; bad
    (t/defn ->Foo [num pos?] (Foo. num))
    ```

## Macros

* <a name="dont-write-macro-if-fn-will-do"></a>
Don't write a macro if a function will do.
<sup>[[link](#dont-write-macro-if-fn-will-do)]</sup>

* <a name="macros-as-syntactic-sugar"></a>
A macro should usually just provide syntactic sugar and the core of the macro should be a plain function. Doing so will improve composability.
<sup>[[link](#macros-as-syntactic-sugar)]</sup>

* <a name="syntax-quoted-forms"></a>
Prefer syntax-quoted forms over building lists manually.
<sup>[[link](#syntax-quoted-forms)]</sup>

## Comments

* <a name="self-documenting-code"></a>
Endeavor to make your code as self-documenting as possible. Never write a comment if code is clearer. Avoid writing comments to explain bad code — refactor the code to make it self-explanatory.
<sup>[[link](#self-documenting-code)]</sup>

* <a name="two-semicolons-for-vertical-application"></a>
Use two semicolons to denote a comment that applies vertically to the form(s) beneath it.
<sup>[[link](#two-semicolons-for-vertical-application)]</sup>

* <a name="one-semicolon-for-horizontal-application"></a>
Use one semicolon to denote a comment that applies horizontally to the form(s) to the left of it.
<sup>[[link](#one-semicolon-for-horizontal-application)]</sup>

* <a name="semicolon-space"></a>
Maintain exactly one space between the semicolon and the text that follows it.
<sup>[[link](#semicolon-space)]</sup>

    ```Clojure
    ;; Frob Grovel

    ;; This section of code has some important implications:
    ;; 1. Foo.
    ;; 2. Bar.
    ;; 3. Baz.

    (defn fnord [zarquon]
      ;; If zob, then veeblefitz.
      (quux zot
            mumble ; Zibblefrotz
            frotz))
    ```

* <a name="english-syntax"></a>
Comments longer than a word begin with a capital letter and use punctuation. Separate sentences with [one space](http://en.wikipedia.org/wiki/Sentence_spacing).
<sup>[[link](#english-syntax)]</sup>

* <a name="comment-upkeep"></a>
Keep existing comments up-to-date. An outdated comment is worse than no comment at all.
<sup>[[link](#comment-upkeep)]</sup>

* <a name="dash-underscore-reader-macro"></a>
Prefer the use of the `#_` reader macro over a regular comment when you need to comment out a particular form.
<sup>[[link](#dash-underscore-reader-macro)]</sup>

    ```Clojure
    ;; good
    (+ foo #_(bar x) delta)

    ;; bad
    (+ foo
       ;; (bar x)
       delta)
    ```

* <a name="todo"></a>
Use `TODO` to note missing features or functionality that should be added at a later date. Follow it with the username/nametag of the person to whom the comment applies (and keep it consistent across the codebase).
<sup>[[link](#todo)]</sup>

    ```Clojure
    ;; TODO @alexandergunnarson do this
    ```

* <a name="fixme"></a>
Use `FIXME` to note broken code that needs to be fixed before released. Follow it with the username/nametag of the person to whom the comment applies (and keep it consistent across the codebase).
<sup>[[link](#fixme)]</sup>

## Documentation

Docstrings are the primary way to document Clojure code. Many definition forms (e.g. `def`, `defn`, `defmacro`, `ns`) support docstrings and usually it's a good idea to make good use of them, regardless of whether the var in question is something public or private.

* <a name="prefer-docstrings"></a>
If a form supports docstrings directly prefer them over using `:doc` metadata:
  <sup>[[link](#prefer-docstrings)]</sup>

    ```clojure
    ;; good
    (defn foo
      "This function doesn't do much."
      []
      ...)

    (ns foo.bar.core
      "That's an awesome library.")

    (def abc "This does blah." 1)

    ;; bad
    (defn foo
      ^{:doc "This function doesn't do much."}
      []
      ...)

    (ns ^{:doc "That's an awesome library.")
      foo.bar.core)

    (def ^{:doc "This does blah."} abc 1)
    ```

* <a name="docstring-grammar"></a>
Docstrings should be composed of proper English sentences.
<sup>[[link](#docstring-grammar)]</sup>

* <a name="docstring-indentation"></a>
Line up the left edge of multi-line docstrings with the end of the quote.
<sup>[[link](#docstring-indentation)]</sup>

    ```clojure
    ;; good
    (ns my.ns
      "It is actually possible to document a ns.
       It's a nice place to describe the purpose of the namespace and maybe even
       the overall conventions used.")

    ;; bad
    (ns my.ns
      "It is actually possible to document a ns.
      It's a nice place to describe the purpose of the namespace and maybe even
      the overall conventions used.")
    ```

* <a name="docstring-leading-trailing-whitespace"></a>
Neither start nor end your doc strings with any whitespace.
<sup>[[link](#docstring-leading-trailing-whitespace)]</sup>

    ```clojure
    ;; good
    (def foo "I'm so awesome." 42)

    ;; bad
    (def silly
      "    It's just silly to start a doc string with spaces.
      Just as silly as it is to end it with a bunch of them.      "
      42)
    ```

## Mutability

* <a name="avoid-mutability"></a>
Avoid the use of mutability except for performance-critical portions of the code.
<sup>[[link](#avoid-mutability)]</sup>

# License

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a
[Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/deed.en_US)
