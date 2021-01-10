## Chapter 4. Classes and Interfaces（类和接口）

### Item 25: Limit source files to a single top-level class（源文件仅限有单个顶层类）

While the Java compiler lets you define multiple top-level classes in a single source file, there are no benefits associated with doing so, and there are significant risks. The risks stem from the fact that defining multiple top-level classes in a source file makes it possible to provide multiple definitions for a class. Which definition gets used is affected by the order in which the source files are passed to the compiler. To make this concrete, consider this source file, which contains only a Main class that refers to members of two other top-level classes (Utensil and Dessert):

虽然 Java 编译器允许你在单个源文件中定义多个顶层类，但这样做没有任何好处，而且存在重大风险。这种风险源于这样一个事实：在源文件中定义多个顶层类使得为一个类提供多个定义成为可能。所使用的定义受源文件传给编译器的顺序的影响。说得更具体些，请考虑这个源文件，它只包含一个主类，该主类引用另外两个顶层类的成员（Utensil 和 Dessert）：

```
public class Main {
    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
```

Now suppose you define both Utensil and Dessert in a single source file named Utensil.java:

现在假设你在一个名为 `Utensil.java` 的源文件中定义了 Utensil 类和 Dessert 类：

```
// Two classes defined in one file. Don't ever do this!
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}
```

Of course the main program prints pancake. Now suppose you accidentally make another source file named Dessert.java that defines the same two classes:

当然，main 方法应该输出 pancake。现在假设你意外地制作了另一个名为 Dessert 的源文件。java 定义了相同的两个类：

```
// Two classes defined in one file. Don't ever do this!
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
```

If you’re lucky enough to compile the program with the command javac Main.java Dessert.java, the compilation will fail, and the compiler will tell you that you’ve multiply defined the classes Utensil and Dessert. This is so because the compiler will first compile Main.java, and when it sees the reference to Utensil (which precedes the reference to Dessert), it will look in Utensil.java for this class and find both Utensil and Dessert. When the compiler encounters Dessert.java on the command line, it will pull in that file too, causing it to encounter both definitions of Utensil and Dessert.

如果你足够幸运，使用 `javac Main.java Dessert.java` 命令编译程序时，编译将失败，编译器将告诉你多重定义了 Utensil 和 Dessert。这是因为编译器将首先编译 `Main.java`，当它看到对 Utensil 的引用（在对 Dessert 的引用之前）时，它将在 `Utensil.java` 中查找这个类，并找到餐具和甜点。当编译器在命令行上遇到 `Dessert.java` 时，（编译器）也会载入该文件，导致（编译器）同时遇到 Utensil 和 Dessert 的定义。

If you compile the program with the command javac Main.java or javac Main.java Utensil.java, it will behave as it did before you wrote the Dessert.java file, printing pancake. But if you compile the program with the command javac Dessert.java Main.java, it will print potpie. The behavior of the program is thus affected by the order in which the source files are passed to the compiler, which is clearly unacceptable.

如果你使用命令 `javac Main.java` 或 `javac Main.java Utensil.java` 编译程序，它的行为将与编写 `Dessert.java` 文件（打印 pancake）之前一样。但是如果你使用命令 `javac Dessert.java Main.java` 编译程序，它将打印 potpie。因此，程序的行为受到源文件传递给编译器的顺序的影响，这显然是不可接受的。

Fixing the problem is as simple as splitting the top-level classes (Utensil and Dessert, in the case of our example) into separate source files. If you are tempted to put multiple top-level classes into a single source file, consider using static member classes (Item 24) as an alternative to splitting the classes into separate source files. If the classes are subservient to another class, making them into static member classes is generally the better alternative because it enhances readability and makes it possible to reduce the accessibility of the classes by declaring them private (Item 15). Here is how our example looks with static member classes:

修复这个问题非常简单，只需将顶层类（在我们的示例中是 Utensil 和 Dessert）分割为单独的源文件即可。如果你想将多个顶层类放到一个源文件中，请考虑使用静态成员类（[Item-24](https://github.com/clxering/Effective-Java-3rd-edition-Chinese-English-bilingual/blob/master/Chapter-4/Chapter-4-Item-24-Favor-static-member-classes-over-nonstatic.md)）作为将类分割为单独的源文件的替代方法。如果（多个顶层类）隶属于另一个类，那么将它们转换成静态成员类通常是更好的选择，因为它增强了可读性，并通过声明它们为私有（[Item-15](https://github.com/clxering/Effective-Java-3rd-edition-Chinese-English-bilingual/blob/master/Chapter-4/Chapter-4-Item-15-Minimize-the-accessibility-of-classes-and-members.md)），降低了类的可访问性。下面是我们的静态成员类示例的样子：

```
// Static member classes instead of multiple top-level classes
public class Test {

    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }

    private static class Utensil {
        static final String NAME = "pan";
    }

    private static class Dessert {
        static final String NAME = "cake";
    }
}
```

The lesson is clear: Never put multiple top-level classes or interfaces in a single source file. Following this rule guarantees that you can’t have multiple definitions for a single class at compile time. This in turn guarantees that the class files generated by compilation, and the behavior of the resulting program, are independent of the order in which the source files are passed to the compiler.

教训很清楚：永远不要将多个顶层类或接口放在一个源文件中。遵循此规则可以确保在编译时单个类不会拥有多个定义。这反过来保证了编译所生成的类文件，以及程序的行为，与源代码文件传递给编译器的顺序无关。

---
**[Back to contents of the chapter（返回章节目录）](https://github.com/clxering/Effective-Java-3rd-edition-Chinese-English-bilingual/blob/master/Chapter-4/Chapter-4-Introduction.md)**
- **Previous Item（上一条目）：[Item 24: Favor static member classes over nonstatic（静态成员类优于非静态成员类）](https://github.com/clxering/Effective-Java-3rd-edition-Chinese-English-bilingual/blob/master/Chapter-4/Chapter-4-Item-24-Favor-static-member-classes-over-nonstatic.md)**
- **Next Item（下一条目）：[Chapter 5 Introduction（章节介绍）](https://github.com/clxering/Effective-Java-3rd-edition-Chinese-English-bilingual/blob/master/Chapter-5/Chapter-5-Introduction.md)**
