
![](https://miro.medium.com/v2/resize:fit:1400/1*AsY0KWU2FXOGQzCassJNDw.png)

If you’ve been heads-down since Java 17, Java 21 (LTS) is the release that quietly changes how day-to-day code is written. Some features ship **final**, others are **preview/incubator** — but together they make Java faster to write, easier to read, and far better at concurrent work.

> **_How to run preview/incubator examples_**_  
> Compile & run with:  
> _`_javac --release 21 --enable-preview *.java_``_java --enable-preview Main_`_(For the Vector API, also add:_ `_--add-modules jdk.incubator.vector_`_.)_

Below are ten features you’ll actually use, each with a tiny, practical snippet and a “when to reach for it.”

## 1) Virtual Threads (GA) — Millions of cheap threads, simple code

Project Loom’s virtual threads let you write _blocking-style_ code that scales like async. No callbacks, no reactive gymnastics — just threads that cost ~nothing.

```java
try (var exec = java.util.concurrent.Executors.newVirtualThreadPerTaskExecutor()) {  
    var urls = java.util.List.of("https://api1", "https://api2", "https://api3");  
    var futures = urls.stream()  
        .map(u -> exec.submit(() -> java.net.http.HttpClient.newHttpClient()  
                .send(java.net.http.HttpRequest.newBuilder(java.net.URI.create(u)).build(),  
                      java.net.http.HttpResponse.BodyHandlers.ofString())  
                .body()))  
        .toList();  
  
    for (var f : futures) System.out.println(f.get()); // still blocking, now cheap  
}
```

**Use when:** you have lots of I/O-bound tasks (HTTP calls, DB queries, RPC) and want straightforward, readable code that scales.

## 2) Structured Concurrency (Preview) — Concurrency that fails and succeeds together

Stop hand-wiring futures and timeouts. A `StructuredTaskScope` treats a set of subtasks as a unit with clear lifecycles and cancellation.

```java
import java.util.concurrent.StructuredTaskScope;  
  
record Profile(String name) {}  
record Orders(int count) {}  
record View(Profile profile, Orders orders) {}  
  
View loadView(String userId) throws Exception {  
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {  
        var p = scope.fork(() -> fetchProfile(userId));  
        var o = scope.fork(() -> fetchOrders(userId));  
        scope.join();  
        scope.throwIfFailed();  
        return new View(p.get(), o.get());  
    }  
}
```

**Use when:** you fan out to multiple services and want “all-or-nothing” results with sane cancellation.

## 3) Scoped Values (Preview) — A safer, faster alternative to `ThreadLocal`

Pass contextual data (request id, auth, locale) down a call tree — even across virtual threads — without mutating globals.

```java
import jdk.incubator.concurrent.ScopedValue; // in 21 it's jdk.incubator? (Preview API)  
  
static final ScopedValue<String> REQ_ID = ScopedValue.newInstance();  
  
void handle(String id) {  
    ScopedValue.where(REQ_ID, id).run(() -> {  
        log("start " + REQ_ID.get());  
        doWork();  
    });  
}
```

**Use when:** you previously reached for `ThreadLocal` but want something immutable, structured, and virtual-thread-friendly.

## 4) Pattern Matching for `switch` (Final) — Fewer casts, safer branches

Switch directly on types and use **guards** to keep logic close to data.

```java
static String describe(Object obj) {  
    return switch (obj) {  
        case String s when s.isBlank() -> "empty string";  
        case String s                 -> "string(" + s.length() + ")";  
        case Integer i                -> "int(" + i + ")";  
        case null                     -> "null";  
        default                       -> "something else";  
    };  
}
```

**Use when:** you have polymorphic payloads (JSON nodes, messages, events) and want exhaustive, readable handling.

## 5) Record Patterns (Final) — Deconstruct data right where you need it

Records already cut boilerplate; **record patterns** let you pull fields out inline — even nested.

record Point(int x, int y) {}  
record Box(Point topLeft, Point bottomRight) {}  
  
static int width(Box b) {  
    return switch (b) {  
        case Box(Point(var x1, _), Point(var x2, _)) -> x2 - x1;  
    };  
}

**Use when:** you’re matching on immutable data structures and want less ceremony.

## 6) String Templates (Preview) — Interpolation without `String.format`

Type-checked, IDE-friendly templates with processors like `STR` and `FMT`.

// import static java.lang.template.StringTemplate.STR;  // preview  
String user = "alice";  
int count = 3;  
var msg = STR."User \{user} has \{count} new messages."; // compile-time checks

**Use when:** you build log messages, SQL, or JSON. Templates are composable and safer than ad-hoc concatenation.  
_(Still preview: requires_ `_--enable-preview_`_.)_

## 7) Sequenced Collections (Final) — First/last and reversible views, everywhere

Collections finally expose a consistent _encounter order_. Lists, sets, and maps gain first/last operations and reversible views.

```java
var names = new java.util.ArrayList<String>();  
names.addLast("Bob");  
names.addFirst("Alice");  
System.out.println(names.getFirst() + " & " + names.getLast()); // Alice & Bob  
  
var reversed = names.reversed(); // live view; updates reflect both ways
```

**Use when:** you care about order but don’t want to juggle `Deque`, `LinkedHashMap`, etc., for basic operations.

## 8) Unnamed Patterns & Variables (Preview) — Make intent obvious, skip the noise

Use `_` to ignore things you don’t care about—both in **patterns** and in **variables**.

record Pair(int left, int right) {}  
  
static int rightOnly(Object o) {  
    return switch (o) {  
        case Pair(_, int r) -> r;   // ignore the left value  
        default             -> -1;  
    };  
}  
  
try (var _ = tracer.span("expensive-op")) {  // we need the span, not the name  
    work();  
}

**Use when:** you’re deconstructing but only need a subset, or when a variable is required by the API but unused.

## 9) Foreign Function & Memory API (Preview) — Safe, fast native interop

Call C libraries and manage off-heap memory **without JNI**. Allocate memory safely, map structs, and talk to native code directly.

import java.lang.foreign.Arena;  
import java.lang.foreign.MemorySegment;  
import java.lang.foreign.ValueLayout;  
  
// allocate off-heap and write/read like a ByteBuffer, but safer  
try (Arena arena = Arena.ofConfined()) {  
    MemorySegment seg = arena.allocate(16);  
    seg.set(ValueLayout.JAVA_INT, 0, 42);  
    int x = seg.get(ValueLayout.JAVA_INT, 0);  
    System.out.println(x);  
}

**Use when:** you integrate with native libs (compression, crypto, ML runtimes) or need zero-copy I/O.  
_(Preview: APIs may evolve.)_

## 10) Unnamed Classes & Instance `main` (Preview) — One-file scripts feel natural

Prototype quickly without ceremony — no public class, no static `main`.

// File: Hello.java  (preview)  
void main() {  
    System.out.println("Hello, Java 21!");  
}

**Use when:** teaching, CLIs, or quick experiments where setup time kills momentum.

## Honorable Mentions

- **Generational ZGC (GA)**: lower pause times in heaps large and small. You don’t write code differently — but your latency graphs will.
- **Vector API (Incubator)**: express SIMD operations in Java for big numerical wins.
- **KEM API (Preview)**: modern cryptographic key encapsulation primitives.

## Migration Playbook (90-minute upgrade that pays off all year)

1. **Target 21 in CI** (`--release 21`) and run tests with `--enable-preview` to try new syntax in isolated modules.
2. **Flip executors** to virtual-thread per task in I/O services; profile before & after.
3. **Refactor feature branches** to use pattern matching & record patterns where you already switch on `Object`.
4. **Wrap fan-out calls** in `StructuredTaskScope.ShutdownOnFailure` and add timeouts.
5. **Replace incidental** `**ThreadLocal**`**s** that carry context with **Scoped Values** in new code.
6. **Adopt Sequenced Collections** to simplify first/last/reversed operations and reduce custom utilities.

## FAQs you’ll hear from the team (and quick answers)

- **“Are virtual threads safe for JDBC?”** Yes — most drivers block. That’s exactly where virtual threads shine. Keep connections pooled; don’t hold them longer than needed.
- **“Do preview features risk lock-in?”** They can change; keep them in leaf modules. You can compile only those modules with `--enable-preview`.
- **“Will GC changes hurt throughput?”** Generational ZGC typically improves real-world throughput due to shorter, more predictable pauses. Measure — not guess.

## Closing Thought

Java 21 isn’t a cosmetic update; it’s a productivity jump. If you embrace **virtual threads + structured concurrency** for I/O, sprinkle **pattern matching/record patterns** for clarity, and lean on **sequenced collections** and **string templates** for ergonomics, your codebase gets simpler while your services scale better.

If this saved you refactor time, share it with one teammate today. The fastest path to better Java is a team reading the same playbook.