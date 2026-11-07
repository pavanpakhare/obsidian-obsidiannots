# Java Pattern Tricks Cheat Sheet

Most pattern questions can be solved using a few simple formulas.

---

# 1. Left Triangle

Output:

```text
#
##
###
####
#####
```

Code:

```java
for (int i = 1; i <= n; i++) {
    System.out.println("#".repeat(i));
}
```

---

# 2. Right Triangle

Output:

```text
    #
   ##
  ###
 ####
#####
```

Code:

```java
for (int i = 1; i <= n; i++) {
    System.out.println(" ".repeat(n - i) + "#".repeat(i));
}
```

---

# 3. Inverted Left Triangle

Output:

```text
#####
####
###
##
#
```

Code:

```java
for (int i = n; i >= 1; i--) {
    System.out.println("#".repeat(i));
}
```

---

# 4. Inverted Right Triangle

Output:

```text
#####
 ####
  ###
   ##
    #
```

Code:

```java
for (int i = n; i >= 1; i--) {
    System.out.println(" ".repeat(n - i) + "#".repeat(i));
}
```

---

# 5. Pyramid

Output:

```text
    *
   ***
  *****
 *******
*********
```

Code:

```java
for (int i = 1; i <= n; i++) {
    System.out.println(" ".repeat(n - i) + "*".repeat(2 * i - 1));
}
```

---

# 6. Inverted Pyramid

Output:

```text
*********
 *******
  *****
   ***
    *
```

Code:

```java
for (int i = n; i >= 1; i--) {
    System.out.println(" ".repeat(n - i) + "*".repeat(2 * i - 1));
}
```

---

# 7. Diamond

Output:

```text
    *
   ***
  *****
 *******
*********
 *******
  *****
   ***
    *
```

Code:

```java
for (int i = 1; i <= n; i++) {
    System.out.println(" ".repeat(n - i) + "*".repeat(2 * i - 1));
}

for (int i = n - 1; i >= 1; i--) {
    System.out.println(" ".repeat(n - i) + "*".repeat(2 * i - 1));
}
```

---

# 8. Number Triangle

Output:

```text
1
12
123
1234
12345
```

Code:

```java
for (int i = 1; i <= n; i++) {
    StringBuilder sb = new StringBuilder();

    for (int j = 1; j <= i; j++) {
        sb.append(j);
    }

    System.out.println(sb);
}
```

---

# 9. Floyd's Triangle

Output:

```text
1
2 3
4 5 6
7 8 9 10
```

Code:

```java
int num = 1;

for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= i; j++) {
        System.out.print(num++ + " ");
    }
    System.out.println();
}
```

---

# Pattern Formula Cheat Sheet

| Pattern | Spaces | Symbols |
|----------|---------|---------|
| Left Triangle | 0 | i |
| Right Triangle | n - i | i |
| Inverted Left | 0 | n - i + 1 |
| Inverted Right | i - 1 | n - i + 1 |
| Pyramid | n - i | 2 * i - 1 |
| Inverted Pyramid | n - i | 2 * i - 1 |
| Diamond | Pyramid + Inverted Pyramid | |

---

# Three Formulas to Remember

```java
" ".repeat(n - i)      // right alignment

"*".repeat(i)          // triangle

"*".repeat(2 * i - 1)  // pyramid
```

If you remember these three formulas, you can solve most pattern questions in coding interviews and assessments.