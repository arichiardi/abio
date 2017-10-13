# Abio

A set of host abstractions mimicking similar facilities available in Clojure for I/O, shell access, _etc_., along with mechanisms to bind the abstractions to host-specific implementations.

# Architectural Thoughts

cljs-node-io has a pretty good thought on using channels for async/io as an abstraction over the evented process.

cljs-node-io is built with the jvm hosted cljs, and uses a promise-chan from core.async, which takes only a single value and returns that value over and over again.

I suspect it's because the file reading utilities assume you can read the whole file into memory, and if not you should be using streams.

1. Use a dynamic variable that is referenced throughout the library. This holds the host implementations and makes using them straightforward
2. Define Protocols for Reading/Writing/Streaming
3. Define a Bindings protocol for creating and interacting with the host implementation to construct the records that implement the R/W/S protocols from #2
4. Define helper functions to tie into the host implementation

## Usage

Here is an example using Abio with [Node bindings](https://github.com/abiocljs/abio-node):

```
$ lumo -qc abio.jar:abio-node.jar
cljs.user=> (require 'abio.core '[abio.io :as io] 'abio.node)
nil
cljs.user=> (abio.core/set-bindings! (abio.node/bindings))
nil
cljs.user=> (def users-dir (io/as-file "/Users"))
#'cljs.user/users-dir
cljs.user=> users-dir
#abio.io.File{:path "/Users"}
cljs.user=> (io/directory? users-dir)
true
cljs.user=> (io/directory? "/etc/hosts")
false
cljs.user=> (def r (io/reader "/usr/share/dict/words" :encoding "UTF8"))
#'cljs.user/r
cljs.user=> (take 8 (io/line-seq r))
("A" "a" "aa" "aal" "aalii" "aam" "Aani" "aardvark")
```

## License

Copyright © 2017 abiocljs and Contributors

Distributed under the Eclipse Public License either version 1.0 or (at your option) any later version.
