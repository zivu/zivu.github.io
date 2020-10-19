---
layout: post
title: "java 14 pattern matching"
date: 2020-06-20
excerpt: "Before java 14, we had to have instanceof check, class casting, and variable assignment..."
image: "/images/pattern-matching.png"
---

<!-- <span class="image fit"><img src="{{ "/images/pattern-matching.png" | absolute_url }}" alt="" /></span> -->
Check out the [Official documentation] [java14-pattern-matching].

{% highlight java %}
private static void printIfInstanceOfStringPriorToJava14(Object x) {
    if(x instanceof String) {
        String person = (String) x;
        System.out.println(person);
    }
}
{% endhighlight %}

Now we can create the `person` variable through pattern matching.
If x is instanceof String, then the person variable will be created.
{% highlight java %}
private static void printIfInstanceOfStringPatternMatching(Object x) {
    if(x instanceof String person) {
        System.out.println(person);
    }
}
{% endhighlight %}

Pattern matching can be also used to write one line checks. If x is an instance of User, the user variable is created and the address returned.
{% highlight java %}
/**
 * Auxiliary class to be send to getUsersAddress(Object x) method.
 * Scroll down.
 */
@Builder
@Getter
static class User {
    private final String address;
}

private static String getUsersAddress(Object x) {	
//  if (x instanceof User) {
//     return ((User) x).getAddress();
//  }
//  return "";
    return x instanceof User user ? user.getAddress() : "";
}
{% endhighlight %}

If on the left of `&&` condition we create a pattern variable, then this variable can be also used on the right.
{% highlight java %}
private static boolean validateAddress(Object x) {
    return x instanceof String address && address.length() < 20;
}
{% endhighlight %}

Pattern variable cannot be used on the right of `||` because it means that x is not an instance of String if the second condition is checked.
{% highlight java %}
private static boolean validateName(Object x, boolean ignoreCase) {
    return x instanceof String name || /*name.length() < 10 && */ ignoreCase;
}
{% endhighlight %}


## Interesting case
What variable will be printed: parameter or the class static member variable if `true`?
{% highlight java %}
class ExampleWithStaticVariable {
    private static final String test = "test";

    public static void printIfInstanceOfStringOrDefault(Object x) {
        if (!(x instanceof String test)) {
            System.out.println(test);
        } else {
            System.out.println(test);
        }
    }
}
{% endhighlight %}

[java14-pattern-matching]: https://openjdk.java.net/jeps/305