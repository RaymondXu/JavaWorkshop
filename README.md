<a id="top"></a>
# Intermediate Java

This workshop is targeted towards people who have taken an introductory computer science course (e.g. 1004, 1006) and want to dive deeper into Java and become more comfortable with the language. I hope to elucidate concepts that may not have been crystal clear when you first learned them such as primitives and objects, static and final, and abstraction. Then we will dive into a couple object-oriented design patterns that solve common software engineering problems using abstraction.

## Table of Contents
1. [Variables](#variables)
  1. [Primitives](#primitives)
  2. [Objects](#objects)
  3. [Equality](#equality)
  4. [Wrappers](#wrappers)
  5. [Access Modifiers](#accessmodifiers)
  6. [Static vs Final](#staticvsfinal)
2. [Abstraction](#abstraction)
  1. [Abstract Classes](#abstractclasses)
  2. [Interfaces](#interfaces)
3. [Patterns](#patterns)
  1. [Iterator Pattern](#iterator)
  2. [Factory Pattern](#factory)
  3. [Singleton Pattern](#singleton)
  4. [Composite Pattern](#composite)
 
-------------------------
 
<a id="variables"></a>
## 1.0 Variables

<a id="primitives"></a>
### 1.1 Primitives

Primitives are data types that are defined by the language.

In Java there are 8 primitives:

| Primitive Type  |  Bytes  |
|-----------------|---------|
| byte | 1 |
| boolean | 1 |
| char | 2 |
| short | 2 |
| int | 4 |
| float | 4 |
| double | 8 |
| long | 8 |

Let's look at some quick examples of primitive declarations:

```java
byte a = -128;
boolean b = true;
char c = 'q';
short d = 128;
int e = 32768;
float f = 9.81;
double g = 3.14159265358979;
long h = 12345678901234567890;
```

Primitives values have independent states. Changing the value of one does not influence another.

```java
int a = 5;
int b = a;
b = 7;
System.out.println("a is " + a);
System.out.println("b is " + b);
```

Gives us:

```
a is 5
b is 7
```

Now let's look at what happens when we do the same thing to objects.

<a id="objects"></a>
### 1.2 Objects

Here's a simple class declaration:

```java
public class Car
{
  public int year;
  
  public Car(int y)
  {
    year = y;
  }
}
```

What happens when we execute the following piece of code?

```java
public static void main(String[] args)
{
  Car a = new Car(1996);
  Car b = a;
  b.year = 2015;
  
  System.out.println("a's year is " + a.year);
  System.out.println("b's year is " + b.year);
}
```

We get:

```
a's year is 2015
b's year is 2015
```

Why?

The answer lies in <b>references</b>.

When we instantiate an object, we create a reference to that object in memory. A reference can be thought of as an arrow pointing to location of the object.

```java
Car a = new Car();
```

This line of code calls the default constructor of the Car class to instantiate a new reference of a Car object. `a` is <b>not</b> actually the Car object, it is a <b>reference</b> to the Car object.

So when we tell `a` to execute a method, we are actually telling whatever `a` points at to execute the method. Since multiple variables can reference the same object, it sometimes gets confusing. Let's look back at our code above:

```java
public static void main(String[] args)
{
  Car a = new Car(1996);
  Car b = a;
  b.year = 2015;
  
  System.out.println("a's year is " + a.year);
  System.out.println("b's year is " + b.year);
}
```

`a` is a reference to a Car object that has a year of 2015.
`b` is a reference to that same Car object. You don't see the keyword `new`, so you know that there are no other Car objects in play.
When we change the year of `b`, we change the year of the Car object that `a` and `b` both refer to. This is why we get:

```
a's year is 2015
b's year is 2015
```

<a id="equality"></a>
### 1.3 Equality

On a related note, how do you compare if two objects are equal?

```java
Car a = new Car(1996);
Car b = new Car(1996);
System.out.println(a == b);
```

This is <b>wrong</b>. The `==` operator in java tells you if two references are equal. It does not tell you if the objects have the same instance variable values or any other comparison metric. So for most purpses, when comparing object equality, we use the `equals()` method. Let's looks at the documentation from the Java API:

> public boolean equals(Object obj)
> 
> Indicates whether some other object is "equal to" this one.
> The equals method implements an equivalence relation on non-null object references:
> 
> The equals method implements an equivalence relation on non-null object references:
> 
> It is reflexive: for any non-null reference value x, x.equals(x) should return true.
> It is symmetric: for any non-null reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
> It is transitive: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
> It is consistent: for any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.
> For any non-null reference value x, x.equals(null) should return false.
> 
> The equals method for class Object implements the most discriminating possible equivalence relation on objects; that is, for any non-null reference values x and y, this method returns true if and only if x and y refer to the same object (x == y has the value true).
> 
> Note that it is generally necessary to override the hashCode method whenever this method is overridden, so as to maintain the general contract for the hashCode method, which states that equal objects must have equal hash codes.
> 
> Parameters:
> obj - the reference object with which to compare.
> Returns:
> true if this object is the same as the obj argument; false otherwise.

This means that when we write our own classes and want to include equality comparison capabilities, we need to override the `equals()` method and write our own equality metric. For example, our Car class could use:

```java
public boolean equals(Object obj)
{
    return this.year == ((Car)(obj)).year;
}
```

This is a simple example, but note that if our Car class had other instance variables like color and model, we could define two Cars as equal if all of their qualities were equal, or just a few.

But wait! Why do we use `==` when we compare years?

Remember that `year` is an `int` which is a primitive. So this means we can't even invoke a method on a year, so equals() is out of the question. For primitives, `==` compares values.

```java
int a = 5;
int b = 5;
System.out.println(a == b);
```

Gives us:

```
True
```

A general rule of thumb is `equals()` for objects, and `==` for primitives.

<a id="wrappers"></a>
### 1.4 Wrappers

Java provides built-in <b>primitive wrappers</b> for all 8 primitive types.

| Primitive Type  |  Wrapper class  |
|-----------------|---------|
| byte | Byte |
| boolean | Boolean |
| char | Character |
| short | Short |
| int | Integer |
| float | Float |
| double | Double |
| long | Long |


Here we can see three different ways of integer creation:

```java
int a = 3;
Integer b = new Integer(3);
Integer c = new Integer("3");
```

And due to <b>autoboxing</b> in Java, we do not have to worry about the differences between Wrappers and primitives--they are implicitly wrapped and unboxed for us. For example:

```java
ArrayList<Integer> list = new ArrayList<Integer>();
int i = 5;
list.add(i);
```

This is perfectly valid code even though the ArrayList only holds `Integer` objects and `i` is a primitive int.

<a id="accessmodifiers"></a>
### 1.5 Access Modifiers

All instance variables have an access modifier. You may be familiar with `public` and `private`:

```java
public Car a;
private Car b;
```

There are two more you may not have heard of: `protected` and `packaged`.

```java
protected Car c;
Car d;
```

Note that the `packaged` modifier is represented by the absence of a modifier.

<i>Also remember that the absense of a modifier in interfaces means `public` by virtue of the role of interfaces. Later in this talk we will discuss interfaces and abstraction at a deeper level.</i>

This chart elegantly displays the distinctions between the four access modifiers. A checked box indicates that the variable can be accessed from that scope. For example, a `private` variable can only be accessed from within that class--other classes in the package, its subclasses, and other classes outside its package cannot access it.

| Modifier | Class | Package | Subclass | World |
|----------|:-----:|:-------:|:--------:|:-----:|
| public | ✓ | ✓ | ✓ | ✓ |
| protected | ✓ | ✓ | ✓ |  |
| packaged | ✓ | ✓ |  |  |
| private | ✓ |  |  |  |

<a id="staticvsfinal"></a>
### 1.6 Static vs Final

<a id="abstraction"></a>
## 2.0 Abstraction

<a id="abstractclasses"></a>
### 2.1 Abstract Classes

<a id="interfaces"></a>
### 2.2 Interfaces

<a id="patterns"></a>
## 3.0 Patterns

<a id="iterator"></a>
### 3.1 Iterator Pattern

<a id="factory"></a>
### 3.2 Factory Pattern

<a id="singleton"></a>
### 3.3 Singleton Pattern

<a id="composite"></a>
### 3.4 Composite Pattern
