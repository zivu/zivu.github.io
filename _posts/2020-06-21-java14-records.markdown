---
layout: post
title: "java 14 records"
date: 2020-06-21
excerpt: "We can create a class with instance fields, constructor, toString(), hashCode(), equals() and getters with a single
line of code ..."
image: "/images/records.png"
---
Check out the [Official documentation] [records]

We can create a class with instance fields, constructor, toString(), hashCode(), equals() and getters.
That requires a lot of boilerplate code:

{% highlight java %}
final class RecordsExample {
    private final String name;
    private final String shape;

    public RecordsExample(String name, String shape) {
        this.name = name;
        this.shape = shape;
    }

    @Override
    public String toString() {
        return "RecordsExample{" +
                "name='" + name + '\'' +
                ", shape='" + shape + '\'' +
                '}';
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, shape);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        RecordsExample that = (RecordsExample) o;
        return name.equals(that.name) &&
                shape.equals(that.shape);
    }

    public String getName() {
        return name;
    }

    public String getShape() {
        return shape;
    }
}
{% endhighlight %}

In jdk 14 we can have the same result with records.

{% highlight java %}
record RecordExample(String name, String shape) {}
{% endhighlight %}

The record can be without components (arguments).

{% highlight java %}
record Base() {}
{% endhighlight %}

Instance fields are not allowed in records.
Even if we try, no setter will be generated as records supposed to be shallowly immutable.

{% highlight java %}
@Setter
record Bill(String address) {
//    private final String x;
}
{% endhighlight %}

A record can have static fields, static and instance methods.

{% highlight java %}
record Restaurant () {

    private static int instanceCounter = 0;

    public Restaurant {
        instanceCounter++;
    }

    public static int getInstanceCounter() {
        return instanceCounter;
    }

    public void testInstanceVariableAccessibility() {
        System.out.println("instance method are allowed in records");
    }
}
{% endhighlight %}

Records can have a compact constructor with no parameters.
When we use a compact constructor we do not need to assign input parameters to private members.
Record's generated methods can be overridden (ctrl + o in Intellij).

{% highlight java %}
record Person(String firstName, String secondName) {

    public Person {
        if (firstName.length() < 4 || secondName.length() < 4) {
            throw new IllegalArgumentException("first or second name length is less then 4");
        }
//        this.firstName = firstName;
//        this.secondName = secondName;
    }

    @Override
    public int hashCode() {
        return 18;
    }
}
{% endhighlight %}

We can also generate a canonical constructor. Canonical constructor has all parameters.
Initialization of fields required. alt + Insert for generation in Intellij.

{% highlight java %}
record Animal(String x, String y) {

    public Animal(String x, String y) {
        this.x = x;
        this.y = y;
    }
}
{% endhighlight %}

Records have a custom constructor, that is not required to have all parameters, but has to call a canonical constructor.

{% highlight java %}
record Administrator(String firstName, String secondName) {

    public Administrator(String firstName) {
        this(firstName, null);
    }
}
{% endhighlight %}

Records can be generic. We can create record the same way as a class:<br>
`Shape<Triangle> triangleShape = new Shape<>(new Triangle());`

{% highlight java %}
/**
 *  Auxiliary class to be used with generic record example below.
 */
class Triangle {
    String shapeName = "triangle";
}

record Shape<T>(T shape) {
}
{% endhighlight %}

Nested records are declared static.

{% highlight java %}
class ExampleWithNestedRecord {
    record NestedRecord(){}
}
{% endhighlight %}

New methods has been added to the Class to support new Record type:
- isRecord() _and_
- getRecordComponents().

{% highlight java %}
class NewReflectionApi {

    /**
     * RecordComponent class has information about accessor method, annotations, field name and so on.
     */
    public static RecordComponent[] getRecordComponents(Class clazz) {
        if (clazz.isRecord()) {
            return clazz.getRecordComponents();
        }
        return new RecordComponent[]{};
    }
}
{% endhighlight %}

A tuple is a collection of elements of different (not necessarily the same) type. Tuples are an alternative to java records. Java records have a few advantages over tuples:
- records have meaningful class and members names;
- a class can support state validation in their constructor.

{% highlight java %}
class RecordsAlternative {
    public static void main(String[] args) {
        Pair<String, String> personTuple = new Pair<>("John", "Doe");
        System.out.println(personTuple);
        Person personRecord = new Person("John", "Doe");
        System.out.println(personRecord);
    }
}
{% endhighlight %}

[records]: https://openjdk.java.net/jeps/359