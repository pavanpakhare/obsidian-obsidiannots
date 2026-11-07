### Bloom Filter in Java — Beginner Tutorial

A Bloom Filter is a space-efficient data structure used to check whether an element might exist in a collection.

It is commonly used in databases, caching systems, distributed systems, and large-scale applications.

### 1\. Why do we need a Bloom Filter?

Suppose your Spring Boot application has 10 million registered users. You want to check whether a username exists.

A normal `HashSet` can do this, but it needs memory to store all the usernames.

A Bloom Filter stores a compact array of bits instead.

It answers two possible results:

* Definitely not present — the username is not in the set.

* Possibly present — the username may exist, so check the actual database.

Important: A Bloom Filter can produce false positives, but a properly implemented standard Bloom Filter does not produce false negatives.

### 2\. How does it work?

Imagine a bit array containing 10 positions, all initially set to `0`.

Initial bit array

### 0

0

### 0

1

### 0

2

### 0

3

### 0

4

### 0

5

### 0

6

### 0

7

### 0

8

### 0

9

Each position is one bit: 0 means unset, 1 means set.

When adding `"java"`, the Bloom Filter runs the value through multiple hash functions. Suppose they produce positions `2`, `5`, and `8`.

After adding "java"

### 0

0

### 0

1

### 1

2

### 0

3

### 0

4

### 1

5

### 0

6

### 0

7

### 1

8

### 0

9

Only the calculated positions are changed to 1.

Now suppose you search for `"python"`.

* If any of its calculated positions contains `0`, it is definitely not present.

* If all its calculated positions contain `1`, it is possibly present.

Why only possibly? Another element may already have set those same bits.

### 3\. Java implementation

Here is a simplified Bloom Filter using a `BitSet` and three hash calculations.

```java
import java.util.BitSet;

public class BloomFilter {

    private final BitSet bits = new BitSet(1000);
    private final int size = 1000;

    public void add(String value) {
        bits.set(hash1(value));
        bits.set(hash2(value));
        bits.set(hash3(value));
    }

    public boolean mightContain(String value) {
        return bits.get(hash1(value))
            && bits.get(hash2(value))
            && bits.get(hash3(value));
    }

    private int hash1(String s) {
        return Math.floorMod(s.hashCode(), size);
    }

    private int hash2(String s) {
        return Math.floorMod(s.hashCode() * 31 + 7, size);
    }

    private int hash3(String s) {
        return Math.floorMod(s.hashCode() * 17 + 13, size);
    }

    public static void main(String[] args) {
        BloomFilter filter = new BloomFilter();

        filter.add("java");
        filter.add("spring");

        System.out.println(filter.mightContain("java"));
        // true

        System.out.println(filter.mightContain("python"));
        // Usually false, but could be true
    }
}
```

This is an educational implementation, not a production-ready one. Real implementations use carefully designed hash functions and sizing parameters to control false positives.

### 4\. Bloom Filter vs HashSet

| Feature               | Bloom Filter                     | HashSet        |
| --------------------- | -------------------------------- | -------------- |
| Memory usage          | Very low                         | Usually higher |
| False positives       | Possible                         | No             |
| False negatives       | No, under normal operation       | No             |
| Stores actual values  | No                               | Yes            |
| Can retrieve elements | No                               | Yes            |
| Can remove elements   | Not with a standard Bloom Filter | Yes            |

### 5\. Where is it used in real systems?

![Server Room](https://images.openai.com/static-rsc-4/fFZ-B7rfPVoKwuxTM5ePf4TTczr-EAciu13iyikRhGXsGUbgeZYq-VX8C1Ei4qDbpr6nH4NGWERsP-pwYy1Vh8wWvKPlXDs5OjfuRQBNlWicpFX7KOyu1qZjtSLNfGZA8C8FfhDb2Kbd95ssUbdF-wZAb_aFTyGZcggpeRGnmHJdrCLk66WtMNd7N1NKgxDn?purpose=fullsize)

Databases

Check whether a data block might contain a key before reading it from disk, potentially avoiding expensive disk I/O.

![A Guide to Top Caching Strategies - ByteByteGo Newsletter](https://images.openai.com/static-rsc-4/Fc_1cvkCdR57Ey-XfnnlZoyEqecIf_8Dpx3NxGHT0500Ct_a4jp5bQefPTPWi3NgtTS7RIMTvQCvgNFaxeFUBA6BUtbEceA23yLUiRTialyuSmgZK-5AejuO6hxcZTjW4g0LlTiTbnip0l5-HWzcKe7gofXrIKRG5CCwHdZrFZRERsk17FAiP68_2d8Auf2v?purpose=fullsize)

Caching systems

Avoid unnecessary cache or database lookups for values known to be absent.

![Computer network](https://images.openai.com/static-rsc-4/Oj8KYi68dtPn6WUt2XT20IO3QGvCTt1Y8YFhSNI1fIgjeLz3c36UoY6iq8YahmUSY_Y5EEcgkpfsp4q-gWR5dfVqf45d1mAAWGAEl4STpeVJpgesJr_hHS2WVK6C5WxDbS_k_FA6qvoCm7_plNVds2TWHWfGIMJOLOncH3OM5-QPgvbi85X3ZSW6514XbJZl?purpose=fullsize)

Distributed systems

Reduce unnecessary network requests when checking for keys across remote nodes.

### 6\. How would you use it in Spring Boot?

Imagine a product-search API that receives many requests for product IDs.

* Check the Bloom Filter for the product ID.

* If it says definitely absent, return a not-found response without querying the database.

* If it says possibly present, query PostgreSQL to confirm.

This can reduce unnecessary database queries when many requested IDs do not exist.

Important: Keep the Bloom Filter synchronized with database changes. If a newly inserted product has not been added to the filter, the filter could incorrectly report it as absent. In a real system, updates, deletions, and distributed synchronization need careful handling.

### Remember this for interviews

> A Bloom Filter is a probabilistic, space-efficient data structure that tests set membership using a bit array and multiple hash functions. It allows false positives but not false negatives under its standard operating assumptions.

The key advantage is saving memory and avoiding unnecessary expensive operations, at the cost of occasionally checking an element that is not actually present.
