---
layout: post
title: "jdk 10/11 local variable type inference"
date: 2020-06-24
excerpt: "Reserved type name 'var' was introduced to be used instead of explicit type declaration, where type can be deduced..."
image: "/images/var.png"
---

[Official documentation] [doc] <br>
[Style guidelines] [guide]

This lesson includes local variable type inference from JDK 10
and local variable syntax for lambda parameters (JDK 11).
Reserved type name 'var' was introduced to be used instead of the explicit type declaration, where type can be deduced.

## How to use 'var'?

1) local variable declarations with initializer;

{% highlight java %}
var users = new ArrayList<String>();
users.add("Tom");
users.add("Jim");
{% endhighlight %}

2) enhanced for-loop

{% highlight java %}
for (var user : users) {
    System.out.println(user);
}
{% endhighlight %}

3) traditional for-loop

{% highlight java %}
for (var i = 0; i < users.size(); i++) {
    System.out.println(users.get(i));
}
{% endhighlight %}

4) try with resources

{% highlight java %}
try (var scanner = new Scanner(new File("src/main/resources/example.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
}
{% endhighlight %}

5) with anonymous classes

{% highlight java %}
var x = new Runnable() {
    @Override
    public void run() {
        System.out.println("var is allowed with anonymous classes");
    }
};
x.run();
{% endhighlight %}

6) casting lambda expressions to intersection types
{% highlight java %}
public static void varIsAllowedInCastingLambdaExpressionsToIntersectionTypes() {
    var x = (LogSystem & Serializable) () -> System.out.println("implementation");
    x.print();
}
{% endhighlight %}

## How to not use 'var'?

1) `var` cannot be used in catch clauses

{% highlight java %}
catch (var e) {
	e.printStackTrace();
}
{% endhighlight %}

2) `var` not allowed for method type declaration
{% highlight java %}
public static void varNotAllowedForMethodArgumentTypeDeclaration(var illegalVarDeclaration) {
    System.out.println(illegalVarDeclaration);
}
{% endhighlight %}

3) `var` cannot be used to define constructor arguments type

{% highlight java %}
public VarExamples(var x) {
    this.x = x;
}
{% endhighlight %}

4) `var` cannot be used as a method return type

{% highlight java %}
public var varCannotBeUsedAsMethodReturnType() {
    return "example";
}
{% endhighlight %}

5) `var` not allowed to be used as a field type

{% highlight java %}
private var varNotAllowedToBeUsedAsFieldType;
{% endhighlight %}

6) `var` not allowed as a target type of lambda expression

{% highlight java %}
var serialNumber = (x, y) -> x + "&" + y;
{% endhighlight %}

7) null cannot be assigned to `var`

{% highlight java %}
var x = null;
{% endhighlight %}

8) `var` cannot be used with array initializer

{% highlight java %}
var x = {"one", "two"};
{% endhighlight %}

## 'var' is not a keyword, it is reserved type name, hence:

1) you can use var as a variable name. you cannot do it with keywords.

{% highlight java %}
private final String var ="t";
//  private final String class = "";
{% endhighlight %}

2) you can use it as a package name;

3) you can use it as a method name

{% highlight java %}
public void var() {}
{% endhighlight %}

4) we cannot create a class name with a name var

{% highlight java %}
//  public static class var {}
{% endhighlight %}

5) we cannot name interface as 'var'

{% highlight java %}
public interface var {}
{% endhighlight %}

## Java 11

Java 11 allows using var in lambda expressions.
1. (var x, var y) -> x + "-" + y ... is the same as ... (x, y) -> x + "-" + y
2. This change is a further enhancement of var usage in java, that was introduced in jdk 10.
3. var allows us usage of annotations (@NotNull for example).
4. var cannot be mixed with no var.
5. var cannot be mixed with explicitly typed parameters (e.i. int)
6. var should be used for all lambda formal parameters not be used at all.

{% highlight java %}
public static void combineSerialNumberAndPrint(String classification, String no) {
    BiFunction<String, String, String> serialNumber = (var x, var y) -> x + "-" + y;
    System.out.println(serialNumber.apply(classification, no));
}
{% endhighlight %}

## lombok alternative: 'var' and 'val'

Explicit import required:

{% highlight java %}
import lombok.var;
{% endhighlight %}

## Pitfalls

Be careful using var with diamonds <>.
Type inference for generic methods relies on the target type (what on the left of '=').
For 'var inferredQueue' inferred type is PriorityQueue<Object> might be not what we might expect.
Can be mitigated by providing constructors with parameters.

{% highlight java %}
public void varPitfallsWithDiamonds() {
    PriorityQueue<String> queue = new PriorityQueue<>();
    System.out.println(queue);
    var inferredQueue = new PriorityQueue<>();
    System.out.println(inferredQueue);
    Comparator<String> comparator = (o1, o2) -> 0;
    var correctInferredQueue = new PriorityQueue<>(comparator);
    System.out.println(correctInferredQueue);
}
{% endhighlight %}

It is a common practice in java to construct a concrete instance and assign it to an interface type.
If 'var' is used, the inferred type is a concrete instance, instead of an interface.

{% highlight java %}
public void varPitfallsWithList() {
    List<String> users = new ArrayList<>();
    System.out.println(users);
    var workers = new ArrayList<>();
    System.out.println(workers);
}
{% endhighlight %}

With a numeric type on the left, a type might be widened or narrowed to types other than int.
With 'var' type is always int.

{% highlight java %}
public void pitfallWithNumericLiterals() {
    byte point = 0;
    short hash = 0x77f;
    long id = 2;
    var x = 34;
}
{% endhighlight %}

[doc]: https://openjdk.java.net/jeps/286
[guide]: https://openjdk.java.net/projects/amber/LVTIstyle.html