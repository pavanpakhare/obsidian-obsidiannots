[[java]]
## Java File I/O Package — Summary

The uploaded notes cover the **`java.io` File I/O package**, mainly these classes: `File`, `FileWriter`, `FileReader`, `BufferedWriter`, `BufferedReader`, and `PrintWriter`.

### 1. `File`

`File` represents a **file or directory**. Creating a `File` object does **not** automatically create the physical file.

```java
File f = new File("abc.txt");
```

Important methods:

|Method|Purpose|
|---|---|
|`exists()`|Checks whether file/directory exists|
|`createNewFile()`|Creates a file|
|`mkdir()`|Creates a directory|
|`isFile()`|Checks whether it is a file|
|`isDirectory()`|Checks whether it is a directory|
|`list()`|Gets names inside a directory|
|`length()`|Gets file size/character count as described in notes|
|`delete()`|Deletes file/directory|

Example:

```java
File f = new File("test.txt");

System.out.println(f.exists());
f.createNewFile();
System.out.println(f.exists());
```

---

### 2. `FileWriter`

Used to **write character data** to a file.

```java
FileWriter fw = new FileWriter("test.txt");
fw.write("Hello Java");
fw.close();
```

Important methods:

- `write(int)`
    
- `write(char[])`
    
- `write(String)`
    
- `flush()`
    
- `close()`
    

#### Append mode

```java
FileWriter fw = new FileWriter("test.txt", true);
```

`true` means **append instead of overwrite**.

**Problem:** `FileWriter` requires manually handling line separators such as `\n`, and `FileReader` reads character-by-character.

---

### 3. `FileReader`

Used to **read character data** from a file.

```java
FileReader fr = new FileReader("test.txt");

int ch;
while ((ch = fr.read()) != -1) {
    System.out.print((char) ch);
}

fr.close();
```

`read()` returns the character's integer/Unicode value and returns **`-1` when the end is reached**.

It can also read into a `char[]`.

---

### 4. `BufferedWriter`

A wrapper around another `Writer`, commonly `FileWriter`.

```java
FileWriter fw = new FileWriter("test.txt");
BufferedWriter bw = new BufferedWriter(fw);

bw.write("Hello");
bw.newLine();
bw.write("Java");

bw.close();
```

The major advantage is:

```java
bw.newLine();
```

instead of manually writing `\n`.

**Important:** `BufferedWriter` communicates with the file **through a Writer**, such as `FileWriter`.

---

### 5. `BufferedReader`

An enhanced reader used to read data, especially **line by line**.

```java
BufferedReader br =
    new BufferedReader(new FileReader("test.txt"));

String line;

while ((line = br.readLine()) != null) {
    System.out.println(line);
}

br.close();
```

The key method is:

```java
readLine()
```

It returns the next line, or **`null` at the end of the file**.

---

### 6. `PrintWriter`

The notes describe `PrintWriter` as the most enhanced Writer for writing text data. It can write different types of data, not just characters.

```java
PrintWriter pw = new PrintWriter("test.txt");

pw.println("Java");
pw.println(100);
pw.println(true);
pw.println('A');

pw.close();
```

Useful methods:

```java
print()
println()
write()
flush()
close()
```

### `write(100)` vs `print(100)`

This is an important point from the notes:

```java
pw.write(100);
```

writes the character represented by Unicode value `100` → **`d`**

Whereas:

```java
pw.print(100);
```

writes the actual text:

```text
100
```

---

## Character vs Binary Data

This is an important concept:

|Type|Classes|Examples|
|---|---|---|
|**Character/text data**|Reader / Writer|`.txt`, text|
|**Binary data**|InputStream / OutputStream|images, audio, video|

The notes specifically distinguish Readers/Writers from InputStreams/OutputStreams.

---

## Most Important Comparison

|Class|Purpose|Key feature|
|---|---|---|
|`File`|File/directory management|Create, delete, check|
|`FileWriter`|Write text|Character-by-character writing|
|`FileReader`|Read text|Character-by-character reading|
|`BufferedWriter`|Write text efficiently|`newLine()` + buffering|
|`BufferedReader`|Read text efficiently|`readLine()`|
|`PrintWriter`|Write formatted text/data|`print()` / `println()`|

### Easy way to remember

```text
File
 ↓
FileWriter → writes characters
FileReader → reads characters

BufferedWriter → better writing + newLine()
BufferedReader → better reading + readLine()

PrintWriter → convenient text/data writing
```

### File I/O operations covered

The notes also demonstrate:

1. Creating files/directories
    
2. Listing files/directories
    
3. Writing and reading files
    
4. Appending data
    
5. Merging two files
    
6. Alternately merging two files line-by-line
    
7. Merging all files in a folder
    
8. Removing duplicate entries
    
9. Extracting/removing selected lines from a file
    

**For Java interviews, focus especially on:** `File`, `FileReader` vs `BufferedReader`, `FileWriter` vs `BufferedWriter`, `PrintWriter`, `read()` vs `readLine()`, `write()` vs `print()`, and **character streams vs byte streams**.