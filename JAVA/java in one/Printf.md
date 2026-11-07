# Java `printf()` Complete Tutorial

`printf()` is used to print formatted output.

## Syntax

```java
System.out.printf("format string", values);
```

Example:

```java
String name = "Pavan";
int age = 22;

System.out.printf("Name: %s Age: %d%n", name, age);
```

Output:

```text
Name: Pavan Age: 22
```

---

# Common Format Specifiers

| Specifier | Type |
|------------|--------|
| `%d` | Integer |
| `%f` | Floating Point |
| `%c` | Character |
| `%s` | String |
| `%b` | Boolean |
| `%x` | Hexadecimal |
| `%o` | Octal |
| `%e` | Scientific Notation |
| `%n` | New Line |
| `%%` | Print `%` |

---

# `%d` Integer

```java
int x = 10;

System.out.printf("%d%n", x);
```

Output:

```text
10
```

---

# `%f` Floating Point

```java
double pi = 3.1415926535;

System.out.printf("%f%n", pi);
```

Output:

```text
3.141593
```

Default precision = 6 decimal places.

---

# Control Decimal Places

## 2 Decimal Places

```java
System.out.printf("%.2f%n", pi);
```

Output:

```text
3.14
```

## 4 Decimal Places

```java
System.out.printf("%.4f%n", pi);
```

Output:

```text
3.1416
```

## 6 Decimal Places

```java
System.out.printf("%.6f%n", pi);
```

Output:

```text
3.141593
```

---

# `%s` String

```java
String name = "Pavan";

System.out.printf("%s%n", name);
```

Output:

```text
Pavan
```

---

# `%c` Character

```java
char ch = 'A';

System.out.printf("%c%n", ch);
```

Output:

```text
A
```

---

# `%b` Boolean

```java
boolean flag = true;

System.out.printf("%b%n", flag);
```

Output:

```text
true
```

---

# `%x` Hexadecimal

```java
System.out.printf("%x%n", 255);
```

Output:

```text
ff
```

Uppercase:

```java
System.out.printf("%X%n", 255);
```

Output:

```text
FF
```

---

# `%o` Octal

```java
System.out.printf("%o%n", 10);
```

Output:

```text
12
```

---

# `%e` Scientific Notation

```java
System.out.printf("%e%n", 12345.678);
```

Output:

```text
1.234568e+04
```

---

# Width Specifier

## Right Aligned

```java
System.out.printf("%5d%n", 10);
```

Output:

```text
   10
```

Width = 5 characters.

---

## Width for String

```java
System.out.printf("%10s%n", "Java");
```

Output:

```text
      Java
```

---

# Left Alignment

Use `-`

```java
System.out.printf("%-10sEND%n", "Java");
```

Output:

```text
Java      END
```

---

# Leading Zeros

```java
System.out.printf("%05d%n", 25);
```

Output:

```text
00025
```

---

# Width + Precision

```java
double num = 12.34567;

System.out.printf("%10.2f%n", num);
```

Output:

```text
     12.35
```

Meaning:

```text
10 = total width
2 = decimal places
```

---

# Print Percent Sign

```java
System.out.printf("Success = 90%%%n");
```

Output:

```text
Success = 90%
```

Use `%%`.

---

# New Line

```java
System.out.printf("Hello%nWorld");
```

Output:

```text
Hello
World
```

Use `%n` instead of `\n` inside `printf`.

---

# Multiple Variables

```java
String name = "Pavan";
int age = 22;
double cgpa = 8.45;

System.out.printf(
    "Name: %s Age: %d CGPA: %.2f%n",
    name,
    age,
    cgpa
);
```

Output:

```text
Name: Pavan Age: 22 CGPA: 8.45
```

---

# Competitive Programming Examples

## Plus Minus Problem

```java
System.out.printf("%.6f%n", positiveRatio);
```

Output:

```text
0.500000
```

---

## Print Table

```java
System.out.printf("%-10s %5d%n", "Java", 100);
System.out.printf("%-10s %5d%n", "Python", 90);
```

Output:

```text
Java         100
Python        90
```

---

# Cheat Sheet

```java
%d      Integer

%f      Double/Float

%.2f    2 Decimal Places

%.6f    6 Decimal Places

%s      String

%c      Character

%b      Boolean

%x      Hexadecimal

%o      Octal

%e      Scientific Notation

%n      New Line

%%      Percent Sign

%5d     Width 5

%-5d    Left Align

%05d    Leading Zeros

%10.2f  Width 10 + 2 Decimals
```

---

# Most Asked Interview Formats

```java
System.out.printf("%.2f%n", num);

System.out.printf("%.6f%n", num);

System.out.printf("%05d%n", num);

System.out.printf("%-10s%n", text);

System.out.printf("%d %s %.2f%n",
                  age,
                  name,
                  cgpa);
```

Remember:

```text
%d -> int
%f -> double
%s -> String
%c -> char
%b -> boolean
%n -> newline
```