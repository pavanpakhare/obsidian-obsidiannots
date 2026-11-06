[[java]]

## Java Internationalization (I18N) — Summary

**Internationalization (I18N)** means designing an application so it can support **different countries and languages without changing the application code**. For example, an Indian request can receive India-specific formatting, while a US request receives US-specific formatting.

The document mainly covers **three Java classes**:

1. `Locale`
    
2. `NumberFormat`
    
3. `DateFormat`
    

---

### 1. `Locale`

`Locale` represents a **geographical country or language** and belongs to `java.util`.

#### Creating Locale

```java
Locale l1 = new Locale("en");
Locale l2 = new Locale("en", "US");
```

Java also provides predefined locales:

```java
Locale.US
Locale.UK
Locale.ITALY
Locale.CHINA
```

#### Important methods

|Method|Purpose|
|---|---|
|`getDefault()`|Gets default Locale|
|`setDefault(Locale l)`|Changes default Locale|
|`getLanguage()`|Gets language code|
|`getDisplayLanguage(Locale l)`|Gets language name|
|`getCountry()`|Gets country code|
|`getDisplayCountry(Locale l)`|Gets country name|
|`getISOLanguages()`|Gets ISO language codes|
|`getISOCountries()`|Gets ISO country codes|
|`getAvailableLocales()`|Gets available locales|

---

# 2. `NumberFormat`

Different countries format numbers differently.

For example:

```text
India  → 1,23,456.789
US     → 123,456.789
Italy  → 123.456,789
```

`NumberFormat` belongs to `java.text` and is **abstract**, so you obtain objects through factory methods rather than `new NumberFormat()`.

### Format a number

```java
double d = 123456.789;

NumberFormat nf =
    NumberFormat.getInstance(Locale.ITALY);

System.out.println(nf.format(d));
```

Output:

```text
123.456,789
```

### Currency formatting

Use:

```java
NumberFormat.getCurrencyInstance(locale);
```

Example:

```java
NumberFormat nf =
    NumberFormat.getCurrencyInstance(Locale.US);

System.out.println(nf.format(123456.789));
```

The document demonstrates currency formatting for **India, UK, US and Italy**.

### Controlling digits

```java
nf.setMaximumFractionDigits(3);
nf.setMinimumFractionDigits(3);

nf.setMaximumIntegerDigits(3);
nf.setMinimumIntegerDigits(3);
```

These control the number of **fractional and integer digits** displayed.

Example:

```text
123.4567 → 123.457
123.4    → 123.400
1.234    → 001.234   // minimum integer digits = 3
```

---

# 3. `DateFormat`

`DateFormat` formats dates according to a particular **Locale**. It belongs to `java.text` and is abstract.

### Date styles

Java provides four styles:

```java
DateFormat.FULL
DateFormat.LONG
DateFormat.MEDIUM
DateFormat.SHORT
```

The document notes that **MEDIUM is the default style**.

Example:

```java
DateFormat.getDateInstance(DateFormat.FULL)
```

Possible US-style output:

```text
Wednesday, July 20, 2011
```

Other styles:

```text
FULL   → Wednesday, July 20, 2011
LONG   → July 20, 2011
MEDIUM → Jul 20, 2011
SHORT  → 7/20/11
```

---

## Locale-specific DateFormat

```java
DateFormat UK =
    DateFormat.getDateInstance(DateFormat.FULL, Locale.UK);

DateFormat US =
    DateFormat.getDateInstance(DateFormat.FULL, Locale.US);

DateFormat ITALY =
    DateFormat.getDateInstance(DateFormat.FULL, Locale.ITALY);
```

This allows the **same date** to be displayed according to different countries.

---

# 4. Date + Time

To format both date and time:

```java
DateFormat df =
    DateFormat.getDateTimeInstance(
        DateFormat.FULL,
        DateFormat.FULL,
        Locale.ITALY
    );

System.out.println(df.format(new Date()));
```

The document demonstrates `getDateTimeInstance()` for displaying both date and time.

---

## ⭐ Important methods to remember

### Locale

```java
Locale.getDefault()
Locale.setDefault(locale)
locale.getLanguage()
locale.getCountry()
```

### NumberFormat

```java
NumberFormat.getInstance(locale)
NumberFormat.getCurrencyInstance(locale)

nf.format(number)
nf.parse(string)

nf.setMaximumFractionDigits(n)
nf.setMinimumFractionDigits(n)
nf.setMaximumIntegerDigits(n)
nf.setMinimumIntegerDigits(n)
```

### DateFormat

```java
DateFormat.getDateInstance(style)
DateFormat.getDateInstance(style, locale)

DateFormat.getDateTimeInstance(
    dateStyle,
    timeStyle,
    locale
)

df.format(date)
df.parse(string)
```

## 🧠 Core idea

Think of I18N as:

```text
Locale
  ↓
Country / Language preference
  ↓
┌─────────────────┐
│ NumberFormat    │ → 123,456.78 / 1,23,456.78
│ DateFormat      │ → different date styles
│ CurrencyFormat  │ → $ / INR / £ / etc.
└─────────────────┘
```

**For Java interviews, the main thing to understand is:** `Locale` tells Java **which regional rules to use**, while `NumberFormat` and `DateFormat` apply those rules to numbers, currencies, dates, and times.