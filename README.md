<a id="top"></a>
# Intermediate Java

This workshop is targeted towards people who have taken an introductory computer science course (e.g. 1004, 1006) or want a refresher in Java and software engineering fundamentals. I hope to elucidate concepts that may not have been crystal clear when you first learned them such as primitives and objects, static and final, and abstraction. Then we will dive into a few common object-oriented design patterns and explore why abstraction is so cool.

## Table of Contents
1. [Variables](#variables)
	1. [Primitives](#primitives)
	2. [Objects](#objects)
	3. [Equality](#equality)
	4. [Wrappers](#wrappers)
	5. [Access Modifiers](#accessmodifiers)
	6. [Static vs Final](#staticvsfinal)
	7. [Enums](#enums)
2. [Abstraction](#abstraction)
	1. [Abstract Classes](#abstractclasses)
	2. [Interfaces](#interfaces)
3. [Patterns](#patterns)
	1. [Strategy Pattern](#strategy)
	2. [Singleton Pattern](#singleton)
	3. [Composite Pattern](#composite)
 
-------------------------
 
<a id="variables"></a>
## 1.0 Variables

<a id="primitives"></a>
### 1.1 Primitives

Primitives are data types that are defined by the language.

In Java there are 8 primitives:

|  Primitive  |  Bytes  |
|-------------|---------|
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

Primitives have independent states. Changing the value of one does not influence another.

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

When we instantiate an object, we create a reference to that object in memory. A value of a reference is the location of the object.

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

This is <b>wrong</b>. The `==` operator in Java tells you if two references are equal. It does not tell you if the objects have the same instance variable values or any other comparison metric. So for most purposes, when comparing object equality, we use the `equals()` method. Let's looks at the documentation from the Java API:

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

Remember that `year` is an `int` which is a primitive. So this means we can't even invoke a method on a `year`, so using `equals()` is out of the question. For primitives, `==` compares values.

```java
int a = 5;
int b = 5;
System.out.println(a == b);
```

Gives us:

```
true
```

A general rule of thumb is `equals()` for objects, and `==` for primitives.

<b>Two objects that are equal must return the same hash code.</b> This is a contract between `equals()` and `hashCode()`. `equals()` and `hashCode()` are both methods in `Object`, which every class implicitly inherits from. By default, `hashCode()` returns the memory location of an object, and `equals()` compares if the `hashCode()` of two objects is the same. When we overwrite either of these two methods, we have to make sure that the contract is still upheld. 

<a id="wrappers"></a>
### 1.4 Wrappers

Sometimes we need objects. For example, when we want to create an ArrayList, we have to specify what type of object will be stored in the ArrayList.

```java
List<Integer> myList = new ArrayList<Integer>();
```

But why can't we use `int`? Why do we have to use `Integer`?

Because `int` is not an object, and the generic `E` in `ArrayList<E>` must be a class.

`Integer` is an example of a wrapper class: a class that wraps a primitive into an object.

Java provides built-in <b>primitive wrappers</b> for all 8 primitive types.

| Primitive  |  Wrapper class  |
|------------|-----------------|
| byte | Byte |
| boolean | Boolean |
| char | Character |
| short | Short |
| int | Integer |
| float | Float |
| double | Double |
| long | Long |

This seems a little complicated at first, but note that Java <b>autoboxes</b> for us. This means that we don't have to explicitly convert between wrapper objects and primitives. We can construct an ArrayList of Integer objects and then add the Integer objects just like we add primitives.

```java
ArrayList<Integer> myList = new ArrayList<Integer>();
int sum = 0;

for (int i = 0; i < myList.size(); i++)
{
	sum += myList.get(i);
}
```


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

<i>Note that that the absence of a modifier in interfaces means `public` by virtue of the role of interfaces. Later in this talk we will discuss interfaces and abstraction at a deeper level.</i>

This chart elegantly displays the distinctions between the four access modifiers. A checked box indicates that the variable can be accessed from that scope. For example, a `private` variable can only be accessed from within that class--other classes in the package, its subclasses, and other classes outside its package cannot access it.

| Modifier | Class | Package | Subclass | World |
|----------|:-----:|:-------:|:--------:|:-----:|
| public | ✓ | ✓ | ✓ | ✓ |
| protected | ✓ | ✓ | ✓ |  |
| packaged | ✓ | ✓ |  |  |
| private | ✓ |  |  |  |

<a id="staticvsfinal"></a>
### 1.6 Static vs Final

`static` is used to describe fields and methods that are associated with a class, not instances of a class.

For example, in our Car example above, each Car instance has its own instance variables that describe its color or year. These fields are not `static`.

An example of a static method is `Math.max()` or `Math.abs()` and an example of a static variable is `Math.PI`. Notice how we never create an instance of the `Math` class?

`Math.PI` is also a `final` variable. `final` is a keyword used to describe things that are constant. It can describe fields, methods, and classes.

A `final` field is a constant that does not change. A `final` variable can only be initialized once. If the variable holds a reference to an object it cannot ever refer to another object. The contents of the object can change, but the variable will always "point" to that exact instance. For example, if we had a `final ArrayList<Integer> list`, we could `add()` and `remove()` elements from the ArrayList, but we can't do `list = new ArrayList<Integer>()`.

A `final` method cannot be overridden by a subclass.

A `final` class cannot be extended (no subclasses allowed).

<a id="enums"></a>
### 1.7 Enums

Say we want to represent different coins like pennies, nickels, dimes, and quarters. There are a lot of approaches to this design dilemma. One way is to simply define a `Coin` class with a field to store the value.

```java
public class Coin
{
	int value;
	
	public Coin(int value)
	{
		this.value = value;
	}
}
```

This is pretty bad. Why?

How can we do better?

We can define the denominations using static final ints:

```java
public class CoinDenomination
{
	public static final int PENNY = 1;
	public static final int NICKEL = 5;
	public static final int DIME = 10;
	public static final int QUARTER = 25;
}
```

Now we can create coins like this:

```java
Coin c = new Coin(CoinDenomination.QUARTER);
```

Cool! But how can we do better?

Enums!

```java
public enum Coin { PENNY, NICKEL, DIME, QUARTER };
```

In Java, this syntax automatically creates the static final constants PENNY, NICKEL, DIME, and QUARTER. Let's associate values with them!

```java
public enum Coin
{
	PENNY(1), NICKEL(5), DIME(10), QUARTER(25)
	
	private int value;
	
	private Coin(int value)
	{
		this.value = value;
	}
};
```

Now we can create coins like this:

```java
Coin c = Coin.QUARTER;
```

Enums are essentially mini-classes that represent a set of constants. They can optionally have fields and methods. Some other basic examples of enum use cases are to represent the days of the week, planets in our solar system, and chess pieces.

Notes:
The enum constructor must be `private` to preserve type-safety.
Since enum constants are `final`, we compare them using `==`.

<a id="abstraction"></a>
## 2.0 Abstraction

Abstraction is a programming ideology that involves extracting physical implementation from method signatures. Abstract classes and interfaces are ways to achieve abstraction. Abstract classes and interfaces are similar in many ways so they can be tough to understand at first. Let's try and clear that up.

<a id="abstractclasses"></a>
### 2.1 Abstract Classes

Abstract classes can contain instance variables, abstract methods, and concrete methods. If a class extends an abstract class, it must implement all abstract methods defined in its parent--unless it itself is an abstract class. Abstract classes cannot be instantiated.

Semantically, abstract classes define a close relation between the abstract class and its subclasses. For example, you could define an abstract class called `GeometricShape`.

```java
abstract class GeometricShape
{
	Color col;

	Color getColor()
	{
		return col;
	}
	
	abstract double calculateArea();
}
```

Just like you can't really draw just a generic geometric shape, you can't instantiate a `GeometricShape` object because the class is abstract. Let's define some subclasses:

```java
class Square extends GeometricShape
{
	double edgeLength;

	double calculateArea()
	{
		return edgeLength * edgeLength;
	}
}

class Circle extends GeometricShape
{
	double radius;
	
	double calculateArea()
	{
		return Math.PI * Math.pow(radius, 2);
	}
}
```

<a id="interfaces"></a>
### 2.2 Interfaces

Interfaces cannot have instance variables and cannot have implementations of methods. You can think of interfaces as templates for classes that share a common trait. When a class implements an interface, it must implement all of the methods declared in that interface. Like abstract classes, interfaces cannot be instantiated.

For example, the `Comparable` interface only defines the method `compareTo(T o)`. The Oracle documentation describes its purpose: "Compares this object with the specified object for order. Returns a negative integer, zero, or a positive integer as this object is less than, equal to, or greater than the specified object." So if we have a class that implements the `Comparable` interface, it <b>must</b> provide an implementation for the `compareTo(T o)` method. In this sense, interfaces are also <b>contracts</b> between the high-level and the low-level details of the program.

Let's say we have a `Speakable` interface with the method `speak()`.

```java
public interface Speakable()
{
	void speak();
}
```

Now we can let `Human`, `Dog`, and `Car` all implement `Speakable`.

```java
public class Human implements Speakable
{
	public void speak()
	{
		System.out.println("Hello!");
	}
}

public class Dog implements Speakable
{
	public void speak()
	{
		System.out.println("Bark!");
	}
}

public class Train implements Speakable
{
	public void speak()
	{
		System.out.println("Choo choo!");
	}
}
```

We can now aggregate them!

```java
List<Speakable> thingsThatCanSpeak = new ArrayList<Speakable>();
thingsThatCanSpeak.add(new Human());
thingsThatCanSpeak.add(new Dog());
thingsThatCanSpeak.add(new Train());
```

Watch this:

```java
for (Speakable speaker : thingsThatCanSpeak)
{
	thingsThatCanSpeak.get(i).speak();
}
```

Outputs:

```
Hello!
Bark!
Choo choo!
```

<i>We can use the Java "foreach loop" or "enhanced for loop" here because `ArrayList` is an implementation of the `List` interface which implements `Iterable`.</i>

A class can implement multiple interfaces but only extend one class. Additionally, all methods in an interface are public, and all fields in an interface are public, static, and final. And unlike the fact that abstract classes should be closely related to their subclasses, interfaces are used when the implementing classes are unrelated to each other.

<a id="patterns"></a>
## 3.0 Patterns

<a id="strategy"></a>
### 3.1 Strategy Pattern

The strategy pattern enables easy interchangability of parts. We define a grouping of parts (interface) and then encapsulate each specific part (classes that implement the interface). This way, when a part is needed, any one of the parts can be used and the program will run smoothly.

```java
public interface RandomNumberGenerator
{
	int roll();
}

public class Die implements RandomNumberGenerator
{
	public int roll()
	{
		return (int)(Math.random() * 6) + 1;
	}
}

public class RiggedDie implements RandomNumberGenerator
{
	public int roll()
	{
		return 4;
	}
}

public class Casino
{
	private RandomNumberGenerator x;
	
	public Casino(RandomNumberGenerator x)
	{
		this.x = x;
	}
	
	public void play()
	{
		// ...
		int outcome = x.roll();
		// ...
	}
}
```

<a id="singleton"></a>
### 3.2 Singleton Pattern

The singleton pattern only allows there to be one instance of a class. There are a few ways to achieve this but here is a simple way:

```java
public class Dictator
{
	private static final Dictator INSTANCE = new Dictator();
	private Dictator() {}
	
	public static Dictator getInstance()
	{
		return INSTANCE;
	}
}
```

As you can see, the concepts `static`, `final`, and `private` that we learned earlier all play a role in this pattern.

Another interesting implemention of the singleton pattern is based off of the initialization-on-demand holder idiom. This implementation takes advantage of the way the JVM initializes classes. Let's look at a simple example:

```java
public class Dictator
{
	private Dictator() {}

	private static class LazyHolder
	{
		private static final Dictator INSTANCE = new Dictator();
	}

	public static Dictator getInstance()
	{
		return LazyHolder.INSTANCE;
	}
}
```

Because `LazyHolder` is a static inner class of `Dictator`, it is not initialized in the initialization phase of the JVM. It is only when the static `getInstance()` method is first invoked that Java realizes that it needs to initialize `LazyHolder` and does so.

This pattern results in a thread-safe singleton instance because Java initialization is guaranteed to be serial. That is, no synchronization overhead needs to be added to this class to preserve the singleton property. Additionally, lazy loading adds a bit of efficiency to the overall program since we only initialize `LazyHolder` when it is immediately needed.


<a id="composite"></a>
### 3.3 Composite Pattern

The composite pattern allows for flexible aggregation of different objects that share a common trait. This common trait is embodied by an interface.

```java
public interface Packageable
{
	int getWeight();
}

public class Item implements Packageable
{
	private int weight;

	public Item(int weight)
	{
		this.weight = weight;
	}

	public int getWeight()
	{
		return weight;
	}
}

public class Box implements Packageable
{
	private List<Packageable> contents = new ArrayList<Packageable>();
	
	public int getWeight()
	{
		int sum = 0;
		for (Packageable p : contents)
		{
			sum += p.getWeight();
		}
		return sum;
	}
}
```

Why is this cool?

Not only can we can put items into boxes, but we can also put boxes into boxes! And we can query the weight of any of the Packageable items because they all have a `getWeight()` method!


