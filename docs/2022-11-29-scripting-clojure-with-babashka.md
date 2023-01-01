## Intro to Babashka 
---
## A little story...
---
```
❯ time clojure -e '(+ 1 2)'
3

________________________________________________________
Executed in  402.52 millis    fish           external
   usr time  837.30 millis  482.00 micros  836.82 millis
   sys time   79.77 millis  108.00 micros   79.66 millis
```
---
## 😢
---
```
❯ time bb -e '(+ 1 2)'
3

________________________________________________________
Executed in   88.47 millis    fish           external
   usr time    5.46 millis  441.00 micros    5.01 millis
   sys time   24.17 millis  100.00 micros   24.07 millis
```
---
## 😊
---
```
❯ clojure -e '(json/parse-string "{\"hello\":\"world\"}")'
Syntax error compiling at (REPL:1:1).
No such namespace: json

Full report at:
/tmp/clojure-7592095476679460364.edn
```
---
## 😢
---
```
❯ bb -e '(json/parse-string "{\"hello\":\"world\"}")'
{"hello" "world"}
```
---
## 😊
---
Friend: I want to learn Clojure!
You: Great!
Friend: How do I get started?
You: Uh...

---
## 😢😢😢
---
![[../assets/aws-lambda.png]]

---
## 😢😢😢
---
#### We'll come back to that one...
---
### What is Babashka?
- A Clojure dialect for scripting
- Natively compiled using GraalVM
- Interpreted via sci (with Clojure runtime)
- Developed by Michiel Borkent (aka borkdude)
---
### What is GraalVM?
- Alternate JVM implementation
- Supports AOT native compilation
- Lots more we can't cover...
---
### What is sci?
- Small Clojure Interpreter (in Clojure)
- Needed for runtime eval in GraalVM
- Runs everywhere Clojure dialects run
- Has many other uses!
---
## Installing Babashka
https://github.com/babashka/babashka/releases
```
wget https://github.com/babashka/babashka/releases/download/v1.0.166/babashka-1.0.166-linux-amd64-static.tar.gz
tar -xzf babashka-1.0.166-linux-amd64-static.tar.gz
./bb -e '(+ 1 2)'
```
---
## A few examples
---
```
❯ ls | bb -i '(filter #(-> % io/file .isDirectory) *input*)'
("Documents" "Downloads" "Work")
```
---
```
❯ ls | bb -i -o '(filter #(-> % io/file .isDirectory) *input*)'
Documents
Downloads
Work
```
---
```
❯ bb -e '(-> "." (fs/list-dir "D*") (->> (map str)))'
("./Downloads" "./Documents")
```
---
## 🔋s Included

- https://book.babashka.org/#libraries

---
## Wait, was that a 📖?
---
## Yes, indeed!
- https://book.babashka.org/
--- 
## Tasks
```
{:tasks
 {:requires ([babashka.fs :as fs])
  clean (fs/delete-tree "target")}}
```
---
```
{:tasks
 {clean {:doc "Removes target folder"
         :requires ([babashka.fs :as fs])
         :task (fs/delete-tree "target")}}}
```
---
## Using tasks
```
bb clean # runs clean task
bb tasks # lists tasks
```

---
## A Babashka script

_show the script_

------
## Wait, what about AWS Lambda?

Time for nbb!

---
### What is nbb?
- Babashka, but on node.js
- sci compiled to ClojureScript
- Fast to start + access to npm

---
## Installing nbb
```
npm install -g nbb
```
or
```
npm install nbb
```
---
## Running nbb
```
❯ time nbb -e '(+ 1 2)'
3
```
---
## nbb startup time
```
❯ time nbb -e '(+ 1 2)'
3

________________________________________________________
Executed in  138.56 millis    fish           external
   usr time  143.33 millis    0.00 micros  143.33 millis
   sys time   14.35 millis  315.00 micros   14.03 millis
```
---
## AWS Lambda time!
---
## Entry point: index.mjs
```
import { loadFile } from 'nbb';

const { handler } = await loadFile('handler.cljs');

export { handler };
```

---
## Handler: handler.cljs
```
(ns handler)

(defn handler [event _ctx]
  (js/console.log event)
  (js/Promise.resolve #js{:hello "world"}))

#js {:handler handler}
```

---
## Things I didn't cover
- BABAHSKA_PRELOADS
- pods (Babashka deps packaging)
- multi-threading
- Tests
- moar tasks
  - cli completion
  - dependencies 
  - parallel execution
--- 
## That's All

Thanks!

---
## References
- https://www.youtube.com/watch?v=Nw8aN-nrdEk 
- https://book.babashka.org/
- https://blog.michielborkent.nl/aws-lambda-nbb.html
