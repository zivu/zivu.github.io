---
layout: post
title: "java enhanced switch statements and switch expressions"
date: 2020-06-22
excerpt: "What is the problem with the existing switch statements? Problem #1: they are error-prone. Problem #2: variable defined inside one arm has a scope of an entire block..."
image: "/images/switch-expressions.png"
---
Check out the [Official documentation] [switch-expressions]


What is the problem with the existing switch statements?
Problem #1: they are error-prone.
A developer might forget to add a break statement. This will lead to an unexpected result.
Try to call this method with "lynca mobile" input.

{% highlight java %}
private static void printInternetPackageForCellularOperator(String cellularOperator) {
    switch (cellularOperator) {
        case "virgin mobile":
        case "orange":
            System.out.println("15 GB");
            break;
        case "lynca mobile":
            System.out.println("11 GB");
        case "vodaphone":
            System.out.println("5 GB");
            break;
        case "viking":
            System.out.println("12 GB");
            break;
        default:
            System.out.println("unknown cellular operator");
    }
}
{% endhighlight %}

Enhanced switch statements were introduced.vOnly expressions on the right of -> will be executed.
This solved problem #1 mentioned above.

{% highlight java %}
private static void printPriceOfBusinessSubscriptionForCellularOperator(String cellularOperator) {
    switch (cellularOperator) {
        case "virgin mobile", "orange" -> System.out.println("15$");
        case "lynca" -> System.out.println("20$");
        case "vodaphone" -> System.out.println("10$");
        case "viking" -> System.out.println("12$");
        default -> System.out.println("unknown cellular operator");
    }
}
{% endhighlight %}

What is the problem with the existing switch statements?
Problem #2: variable defined inside one arm has a scope of an entire block.
Another arm/case cannot use the same variable name.

{% highlight java %}
private static void printClassOfAnimal(String animal) {
    switch (animal) {
        case "cat":
            int classNumber = 1;
            System.out.println(classNumber);
            break;
        case "dog":
//          int classNumber = 2;
            System.out.println("classNumber variable is already defined in the first arm");
            break;
        case "tiger":
//          int teclassNumbermp = 3;
            System.out.println("the same as in the second arm: variable defined");
    }
}
{% endhighlight %}

Enhanced switch statements solve problem #2 mentioned above.
A variable inside one arm has a scope of this arm.

{% highlight java %}
private static void checkRegistrationNumberForAnimal(String animal) {
    switch (animal) {
        case "cat" -> {
            int temp = 1234;
            System.out.println(temp);
        }
        case "dog" -> {
            int temp = 2456;
            System.out.println("animal registration number: " + temp);
        }
    }
}
{% endhighlight %}

Switch expression example. A default case is required if the enum is not used in a case. For enum, example look at printNameAssociatedWithId(Numbers) method below.

{% highlight java %}
private static void printDomesticAnimalClass(String animal) {
    int animalClass = switch (animal) {
        case "dog" -> 1;
        case "cat" -> 2;
        default -> throw new IllegalStateException("Unexpected value: " + animal);
    };
    System.out.println("animal class is " + animalClass);
}
{% endhighlight %}

Switch expression can be passed and argument to methods.

{% highlight java %}
private static void printAssociatedNumber(String x) {
    System.out.println(switch (x) {
        case "one" -> 1;
        case "two" -> 2;
        default -> 777;
    });
}
{% endhighlight %}

Jdk 14 introduced the 'yield' statement to return value.
'yield' is a restricted identifier and no class can be named that way.
Switch expression must either produce value or throw an exception.
If you try to comment yield statement compiler will complain.

{% highlight java %}
private static void printAssociatedClassWithAnimal(String x) {
    int animalClass = switch (x) {
        case "one" -> {
            System.out.println("first arm");
            yield 1;
        }
        case "two" -> {
            System.out.println("second arm");
            throw new IllegalArgumentException("second arm used");
        }
        default -> 3;
    };
    System.out.println("animal class is:" + animalClass);
}
{% endhighlight %}

Switch expression can use a traditional representation with colon label ":".

{% highlight java %}
private static void printAnimalClassificationName(String x) {
    int animalClass = switch (x) {
        case "one":
            System.out.println("first arm");
            yield 1;
        case "two":
            System.out.println("second arm");
            yield 2;
        default:
            yield 3;
    };
    System.out.println("animal class is:" + animalClass);
}
{% endhighlight %}

## Interesting case

{% highlight java %}
/**
 * Auxiliary method to be used with printAnimalWithTypeInformation(String) method
 */
private static String yield(String x) {
    return x + " - referenced";
}

/**
 * What yield("class 1 " + animal) will return?
 * - "class 1 " + animal
 *  OR
 * - "class 1 " + animal + " - referenced"
 */
private static void printAnimalWithTypeInformation(String animal) {
    String message = switch (animal) {
        case "dog" -> {
            System.out.println("customer has a dog");
            yield("class 1 " + animal);
        }
        case "cat" -> {
            System.out.println("customer has a cat");
            yield ("class 2" + animal);
        }
        default -> "unknown animal";
    };
    System.out.println(message);
}
{% endhighlight %}

The default case is not required in case of the enum (if we cover all cases). If omitted it will be added by the compiler.
It is not even recommended to add default case, to throw an exception to indicate unexpected value.

{% highlight java %}
private enum Numbers {
    ONE,
    TWO,
    THREE
}

private static void printNameAssociatedWithId(Numbers id) {
    String name = switch (id) {
        case ONE -> "Tomas";
        case TWO -> "John";
        case THREE -> "Peter";
    };
    System.out.println(name);
}

{% endhighlight %}

[switch-expressions]: https://openjdk.java.net/jeps/361
