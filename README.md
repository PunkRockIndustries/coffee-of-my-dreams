Once this alternative coffeescript compiler is completed, it will hopefuly
become the next official compiler, though Jeremy may not agree with the entire
list of changes below. Either way, I would like the language it compiles to
remain as close to a superset of coffeescript as possible, unlike the
ever-diverging [Coco](https://github.com/satyr/coco). This means keeping the
*change* and *remove* lists very small, which I believe I have done. I count
only two major changes: classes (an overhaul) and list comprehension syntax.
One noteworthy enhancement is that code generation will be kept entirely
separate from the parse tree so that we can have separate targets (ES3, ES5,
harmony, ...) and modules that implement user-defined targets.

I will push my progress once I am finished with the design and architecture
phases. At that point, I'll accept pull requests from anyone interested in
helping to implement the AST classes. For those familiar with coffeescript
internals: I am recycling {cake,coffee-script,command,optparse,repl}.coffee.
This is only a rewrite of the lexing, parsing, and code generation, not the
interfaces to the compiler.

# Full Change List

## change
* compile to strings as late as possible (still need to work out proper rules) ([#908](https://github.com/jashkenas/coffee-script/issues/908), [#1011](https://github.com/jashkenas/coffee-script/issues/1011), [#1072](https://github.com/jashkenas/coffee-script/issues/1072))
* newer, awesome class syntax/semantics ([#1207](https://github.com/jashkenas/coffee-script/issues/1207), [#640](https://github.com/jashkenas/coffee-script/issues/640#issuecomment-376129), [harmony:classes](http://wiki.ecmascript.org/doku.php?id=harmony:classes))
* change comprehension syntax to {python,harmony}-style `[... for ... in ...]` ([harmony:array_comprehensions](http://wiki.ecmascript.org/doku.php?id=harmony:array_comprehensions), [#77](https://github.com/jashkenas/coffee-script/issues/77), [#2030](https://github.com/jashkenas/coffee-script/issues/2030), [#1191](https://github.com/jashkenas/coffee-script/issues/1191))
* output indentation style == input indentation style
* fix parameter lists issue from [#1007](https://github.com/jashkenas/coffee-script/issues/1007) (make temp vars)
* nice parameter lists compilation ([#1338](https://github.com/jashkenas/coffee-script/issues/1338))
* only use semicolons before `[`, `(`, `+`, `-`, and `/` that begin new lines; omit everywhere else

## remove
- inline JS (really, it's completely unnecessary)
- block comments are *comments*, don't pass them through to the compilation target
- YAML-style object literals (only during initial development phase; these complicate the shit out of the language)
- destructuring in list comprehensions (to allow for [#866](https://github.com/jashkenas/coffee-script/issues/866), mentioned below)
- disallow `arguments`, `eval` in parameter lists ([#1007](https://github.com/jashkenas/coffee-script/issues/1007))
- disallow arguments with identical names ([#1002](https://github.com/jashkenas/coffee-script/issues/1002))
- disallow top-level literals and other obvious errors ([#1066](https://github.com/jashkenas/coffee-script/issues/1066), [#1069](https://github.com/jashkenas/coffee-script/issues/1069), [#1240](https://github.com/jashkenas/coffee-script/issues/1240))

## add
+ `@0`, `@1`, etc. for `this[0]`, `this[1]`, etc.
+ `@@` as sugar for `@constructor`
+ `a.0.1` for `a[0][1]` ([#918](https://github.com/jashkenas/coffee-script/issues/918), [#1334](https://github.com/jashkenas/coffee-script/issues/1334))
+ unnamed splats in array destructuring and function parameters ([#870](https://github.com/jashkenas/coffee-script/issues/870))
+ single-value-skipping `null` syntax ([#870](https://github.com/jashkenas/coffee-script/issues/870))
+ stepped ranges: `[0..8 by 2]` ([#835](https://github.com/jashkenas/coffee-script/issues/835))
+ Haskell's function-infixing via backticks (kinda [#915](https://github.com/jashkenas/coffee-script/issues/915), [#1429](https://github.com/jashkenas/coffee-script/issues/1429), <code>f0 a, b \`f1\` c</code>, <code>f0 longArgumentName \`fn1\` b</code> requires arbitrary look-ahead...)
+ allow identifiers that are reserved words in JS but not coffee through use of unicode escape sequences ([#1452](https://github.com/jashkenas/coffee-script/issues/1452))
+ shorthand proposed by [#1089](https://github.com/jashkenas/coffee-script/issues/1089)
+ implement syntax from [#866](https://github.com/jashkenas/coffee-script/issues/866)
+ require indents to match outdents ([#689](https://github.com/jashkenas/coffee-script/issues/689), [#1275](https://github.com/jashkenas/coffee-script/issues/1275), others)
+ unary `::` operator ([#1220](https://github.com/jashkenas/coffee-script/issues/1220))
+ `**` exponentiation operator ([#2026](https://github.com/jashkenas/coffee-script/issues/2026), [#1990](https://github.com/jashkenas/coffee-script/issues/1990), [#79](https://github.com/jashkenas/coffee-script/issues/79))
+ Ruby's [`=~` regexp matching operator](http://ruby-doc.org/core/classes/String.html#M001135) ([#1651](https://github.com/jashkenas/coffee-script/issues/1651), [#1653](https://github.com/jashkenas/coffee-script/issues/1653), [#115](https://github.com/jashkenas/coffee-script/issues/115))
+ call superclass's `extended` method on extension ([#710](https://github.com/jashkenas/coffee-script/issues/710), [#841](https://github.com/jashkenas/coffee-script/issues/841#issuecomment-1300193), [gist:612786](https://gist.github.com/612786))
+ make `@constructor.name` portable ([#1272](https://github.com/jashkenas/coffee-script/pull/1272))
+ underscore in number literals ([#632](https://github.com/jashkenas/coffee-script/issues/632), [#857](https://github.com/jashkenas/coffee-script/issues/857), [#913](https://github.com/jashkenas/coffee-script/issues/913))
+ quote-word (`<[ word1 word2 ]>`) ([#582](https://github.com/jashkenas/coffee-script/issues/582), [#1211](https://github.com/jashkenas/coffee-script/issues/1211), others)
+ yada-yada ([#1142](https://github.com/jashkenas/coffee-script/issues/1142))
+ ES5 shims like Function::bind when they may not exist ([kriskowal/es5-shim](https://github.com/kriskowal/es5-shim))
+ `{+flag}`, `{-flag}` ([#885](https://github.com/jashkenas/coffee-script/issues/885))
+ (possibly) add Function::new ([gist:612786](https://gist.github.com/612786))
+ (possibly) scheme-style argument accessor (if I can resolve ambiguity with `a <b> c` somehow) ([#739](https://github.com/jashkenas/coffee-script/issues/739))
