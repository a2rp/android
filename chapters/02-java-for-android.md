# 2. Java essentials for Android

[Back to notes index](../README.md)

| [Previous: Build your first Android app](01-first-android-app.md) | [Notes index](../README.md) | [Next: Project structure, Gradle, and resources](03-project-structure-and-resources.md) |
|:--|:--:|--:|

## Why Java matters in these notes

Java is the language used by the examples in this notebook. Android apps also use framework classes such as `Activity`, `View`, and `Bundle`. Learning the Java basics makes those APIs easier to read because their methods, types, and callbacks follow regular Java rules.

This chapter records the language features I use most often in Android code. It is a reminder of how values, methods, objects, collections, and errors fit together, with small examples based on a study list.

## Values and types

A variable has a type, a name, and a value. Java has primitive types for simple values and reference types for objects.

```java
int completedCount = 3;
double progress = 0.75;
boolean isReady = true;
char firstLetter = 'A';
String topic = "Activities";
```

`int`, `double`, `boolean`, and `char` are primitives. `String` is a class, so `topic` holds a reference to a `String` object. A reference can also be `null`, which means it does not currently refer to an object.

Use a name that explains the value. Use `final` when a local variable should not be assigned a different value after initialization:

```java
final int maximumAttempts = 3;
```

`final` on a reference prevents replacing the reference. If the referenced object is mutable, its contents can still change. Primitive variables and local variables have different defaulting rules from fields, so initialize local variables before reading them.

## Conditions, loops, and methods

An `if` statement chooses a path based on a boolean expression. A loop repeats work. A method names a unit of behavior and can accept parameters or return a result.

```java
static String statusLabel(int completed, int total) {
    if (total <= 0) {
        return "No topics yet";
    }

    if (completed == total) {
        return "All topics reviewed";
    }

    return completed + " of " + total + " reviewed";
}
```

This method has two `int` parameters and returns a `String`. The `static` keyword means it belongs to the class rather than a particular object. A method with no result uses `void`.

A loop can process each element in a collection:

```java
for (String topic : topics) {
    System.out.println(topic);
}
```

In Android code, the same kind of loop can prepare values for a view or check a list. Keep UI work short because activity callbacks and click listeners normally run on the main thread. Longer work needs a background-work approach, covered later in these notes.

## Classes, objects, and encapsulation

A class defines the data and behavior its objects expose. A constructor sets up a new object. Encapsulation keeps fields private and exposes deliberate methods for reading or changing state.

```java
public class StudyItem {
    private final String title;
    private boolean reviewed;

    public StudyItem(String title) {
        if (title == null || title.trim().isEmpty()) {
            throw new IllegalArgumentException("Title must not be empty");
        }

        this.title = title.trim();
        this.reviewed = false;
    }

    public String getTitle() {
        return title;
    }

    public boolean isReviewed() {
        return reviewed;
    }

    public void markReviewed() {
        reviewed = true;
    }
}
```

`new StudyItem("Activities")` calls the constructor and creates an object. `this.title` refers to the field on that object. `title` without `this` is the constructor parameter. The field is `final` because the item's title should not change after construction. The `reviewed` field can change through `markReviewed()`.

The `private` access modifier limits direct access to a class. `public` methods form the part of the class that other code can use. This helps prevent unrelated code from leaving an object in an invalid state.

## Interfaces, inheritance, and callbacks

An interface describes behavior a class agrees to provide. A class can extend one class and implement one or more interfaces. Android uses inheritance in types such as activities, and interfaces for callbacks such as click handling.

```java
public class MainActivity extends Activity {
    // Activity behavior belongs here.
}
```

Here `MainActivity` is a kind of `Activity`, so it inherits activity behavior and can override lifecycle methods such as `onCreate()`. Use `@Override` so the compiler can check that a method really overrides a parent method.

An interface callback can be implemented with an anonymous class:

```java
button.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        message.setText(R.string.updated_message);
    }
});
```

The view calls `onClick()` when the user taps the button. This is a common event pattern in Java-based Android screens.

## Lists, generics, and equality

Collections hold groups of objects. A `List` preserves order and can contain repeated values. `ArrayList` is a resizable list implementation. Generics describe the element type, so a `List<String>` accepts strings and returns strings when read. The following statements belong inside a method in a class that imports `java.util.ArrayList` and `java.util.List`.

```java
List<StudyItem> items = new ArrayList<>();
items.add(new StudyItem("Java basics"));
items.add(new StudyItem("Activities"));

for (StudyItem item : items) {
    System.out.println(item.getTitle());
}
```

The variable uses the `List` interface while the object is an `ArrayList`. This keeps code flexible if the implementation changes later. `Map<K, V>` is useful when values are looked up by keys, while `Set<E>` stores unique elements. The RecyclerView chapter uses a list as the data source for rows.

For objects such as strings, compare contents with `.equals()` rather than `==`:

```java
String selectedTab = "notes";

if ("notes".equals(selectedTab)) {
    System.out.println("Show the notes tab");
}
```

`==` checks whether two references point to the same object. `.equals()` checks value equality for classes that implement it, including `String`. Putting the known non-null string first also avoids a null check for `selectedTab` in this comparison.

## Null values and exceptions

Java reference variables can be `null`. Calling a method through a null reference causes a `NullPointerException`. Check that a value exists before using it, or design a method so invalid values are rejected at the boundary.

Exceptions report problems that interrupt the normal flow. Catch an exception when the current code can recover or show a useful message. Do not catch and ignore a broad exception because that hides the cause of a failure.

```java
String countText = "12";

try {
    int count = Integer.parseInt(countText);
    System.out.println("Count: " + count);
} catch (NumberFormatException exception) {
    System.out.println("Enter a whole number");
}
```

`NumberFormatException` occurs when text is not a valid integer. Checked exceptions must be handled or declared by the method. Runtime exceptions often point to invalid assumptions, such as a null value or an incorrect index, and should usually be fixed at their source.

## Practice: track reviewed topics

Create a `StudyItem` class with a title and a reviewed flag. Add three items to an `ArrayList<StudyItem>`. Mark one item as reviewed, then loop through the list and print each title with either `Reviewed` or `To review`.

Before moving on, check that:

- the title cannot be empty
- the reviewed state changes through a method
- the list accepts `StudyItem` objects, not unrelated values
- the output uses the current state of each item

## Notes to remember

- A class defines a type, and `new` creates an object from that class.
- Keep fields private and expose methods that preserve valid state.
- Use interfaces to describe behavior and callbacks.
- Use generics to make collection element types clear.
- Use `.equals()` for comparing string contents.
- Check nullable references and handle exceptions where recovery is possible.
- Keep event callbacks short and move long-running work away from the main thread.

## References

- [Learn Java](https://dev.java/learn/)
- [Java collections framework](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collection.html)
- [Java versions in Android builds](https://developer.android.com/build/jdks)
- [Android processes and threads](https://developer.android.com/guide/components/processes-and-threads)

