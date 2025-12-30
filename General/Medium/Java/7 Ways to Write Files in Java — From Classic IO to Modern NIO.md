
Writing files is one of those tasks every Java developer does — yet many of us keep using the **same old approach**without knowing better, cleaner, or more modern options exist.

Java has evolved significantly over the years.

Today, we have everything from classic IO streams to elegant one-line NIO methods.

In this article, we’ll walk through **7 different ways to write files in Java**, explain **what’s actually happening**, and most importantly — **when you should use each approach**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*PQEWrC1tQs1blKTr1O6jGQ.jpeg)

## Two Important Distinctions

### 1. Text vs Binary

- **Text files** → characters (`String`, `char`)
- **Binary files** → bytes (`byte[]`)

Choosing the wrong API can lead to encoding issues or poor performance.

### 2. Old IO vs NIO

- **Classic IO (**`**java.io**`**)** → older, stream-based
- **NIO (**`**java.nio.file**`**)** → modern, cleaner, more flexible

You’ll see both below — because real projects often still use both.

## `1. Files.writeString()`

If you’re on **Java 11 or newer**, this should be your default choice for simple text writing.

`java.nio.file.Files` class has a `writeString()` method which can be used to write a file.

`Files` has a [**static**](https://codippa.com/static-keyword-in-java/) `writeString()` method which takes following arguments:

1. path of file. This should be an object of `java.nio.file.Path`.
2. string to be written to the file.
3. mode in which the file is opened, that is, write. This is provided as [**an enum**](https://codippa.com/how-to-create-enum-in-java/) `StandardOpenOption`.

Path path = Path.of("output.txt");  
Files.writeString(  
    path,  
    "Hello from modern Java!",  
    StandardOpenOption.CREATE,  
    StandardOpenOption.TRUNCATE_EXISTING  
);

### What’s happening here?

- `Path.of(...)` represents the file location (modern replacement for `File`)
- `writeString()` converts the string into bytes using UTF-8 by default
- `CREATE` → creates the file if it doesn’t exist
- `TRUNCATE_EXISTING` → clears existing content before writing

### Why this is great

1. Very readable
2. Handles encoding properly
3. Less boilerplate
4. Encourages modern Java style

### **Use this when:**

You just want to write or overwrite text — fast and clean.

## `2. FileWriter`

This is one of the oldest ways to write text files in Java.

try (FileWriter writer = new FileWriter("output.txt")) {  
  writer.write("Hello World!");  
}

### What’s happening?

- `FileWriter` writes **characters**, not raw bytes
- It internally converts characters to bytes using the platform’s default charset
- The file is **overwritten by default**

Appending instead:

new FileWriter("output.txt", true);

### Pros & Cons

1. Simple
2. Charset is platform-dependent
3. No buffering by default

### **Use this when:**

You’re working with legacy code or very simple writing logic.

## `3. BufferedWriter`

`BufferedWriter` improves performance by **reducing disk writes**.

try (BufferedWriter bw =  
         new BufferedWriter(new FileWriter("output.txt"))) {  
    bw.write("First line");  
    bw.newLine();  
    bw.write("Second line");  
}

### What’s happening?

- Data is stored temporarily in memory (buffer)
- Disk writes happen **in batches**, not per write call
- `newLine()` adds a platform-independent line separator

### Why buffering matters

Writing to disk is expensive. Buffering minimizes how often it happens.

### **Use this when:**

You’re writing **multiple lines or large text content**.

## `4. PrintWriter`

`PrintWriter` is all about **convenience**.

```java
try (PrintWriter pw = new PrintWriter("output.txt")) {  
    pw.println("Line 1");  
    pw.printf("User score: %d%n", 42);  
}
```

### What’s happening?

- `println()` auto-adds line breaks
- `printf()` lets you format output (like `String.format`)
- Errors are swallowed unless explicitly checked

## Trade-off

1. Very readable
2. Less control
3. Not ideal for critical error handling

### **Use this when:**

You’re writing **human-readable or formatted text**, logs, or reports.

## `5. FileOutputStream`

This API writes **bytes directly**.

```java
try (FileOutputStream fos =  
         new FileOutputStream("output.dat")) {  
    fos.write("Binary Data".getBytes());  
}
```

### What’s happening?

- `getBytes()` converts the string to bytes
- Java writes bytes **exactly as provided**
- No character encoding handling

### Why this exists

Not all files are text:

- Images
- PDFs
- Serialized objects

### **Use this when:**

You’re dealing with **binary data**, not text.

## `6. Files.newBufferedWriter()`

This combines **modern NIO** with **buffered writing**.

```java
try (BufferedWriter bw =  
         Files.newBufferedWriter(Path.of("output.txt"))) {  
  bw.write("Modern buffered writing");  
}
```

### What’s happening?

- Uses `Path` instead of `File`
- Uses UTF-8 by default
- Buffered for performance

### Why this is underrated

It’s cleaner than classic IO and just as efficient.

### **Use this when:**

You want **buffering + modern API** without verbosity.

## `7. FileChannel`

For serious performance needs:

```java
try (FileChannel channel = FileChannel.open(  
  Path.of("big.txt"),  
  StandardOpenOption.CREATE,  
  StandardOpenOption.WRITE)) {  
    ByteBuffer buffer = ByteBuffer.wrap("Channel data".getBytes());  
    channel.write(buffer);  
}
```

### What’s happening?

- Data is written via a `ByteBuffer`
- Channels support non-blocking IO
- Used internally by high-performance systems

### Why it’s advanced

More control = more responsibility.

### **Use this when:**

You’re writing **very large files or performance-critical systems**.

## Which One Should You Use?

Ask yourself these questions:

**Are you writing simple text in one go?**  
→ Use `**Files.writeString()**`

**Are you writing multiple lines or large text content?**  
→ Use `**BufferedWriter**` or `**Files.newBufferedWriter()**`

**Do you need formatted output (numbers, reports, logs)?**  
→ Use `**PrintWriter**`

**Are you writing binary data (images, PDFs, bytes)?**  
→ Use `**FileOutputStream**`

**Do you care about modern APIs and clean code?**  
→ Use **NIO (**`**Files**`**,** `**Path**`**)**

**Are you dealing with very large files or performance-critical systems?**  
→ Use `**FileChannel**`

## Best Practices

- Always use **try-with-resources**
- Prefer **NIO (**`**Files**`**,** `**Path**`**)** in new code
- Use buffering for repeated writes
- Be explicit about **charset** when needed

## Final Thoughts

Java doesn’t have _one_ right way to write files — it has **many**, each designed for a specific use case.

Knowing _why_ a method exists is far more valuable than memorizing syntax.

> _Senior developers don’t just write files — they choose the right tool._

