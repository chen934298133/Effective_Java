# 使用静态工厂方法代替构造器

## 一、 客户端获取一个类的实例，两种解决方案



## 二、 静态工厂方法的优点

### 1. 不同的静态工厂方法，有自己的**专属名称**。具有名称的静态工厂方法会**更容易使用**，产生的客户端代码**更易于阅读**。
- 构造器


<details>
<summary>查看代码</summary>


```java
public class GenerateOrder_Constructor {

    private int count;
    private double money;
    private String address;

    // 一个参数
    public GenerateOrder_Constructor(double money){
        this(0, money, null);
    }

    //两个参数
    public GenerateOrder_Constructor(int count, double money){
        this(count, money, null);
    }

    //三个参数
    public GenerateOrder_Constructor(int count, double money, String address){
        this.count = count;
        this.money = money;
        this.address =address;
    }

    public String getAddress() {
        return address;
    }

    public int getCount() {
        return count;
    }

    public double getMoney() {
        return money;
    }
}

```

```java
public class Item_1 {
    public static void main(String[] args){

        // 使用构造器调用
        GenerateOrder_Constructor gc1 = new GenerateOrder_Constructor(200D);
        GenerateOrder_Constructor gc2 = new GenerateOrder_Constructor(1,200D);
        GenerateOrder_Constructor gc3 = new GenerateOrder_Constructor(1,200, "深圳");
        System.out.println(gc3.getMoney());
        System.out.println(gc3.getCount());
        System.out.println(gc3.getAddress());
  
    }
}

```
</details>

- 静态工厂方法

<details>
<summary>查看代码</summary>

```java

public class GenerateOrder_StaticFactoryMethods {

    private int count;
    private double money;
    private String address;

    private GenerateOrder_StaticFactoryMethods(int count, double money, String address){
        this.count = count;
        this.money = money;
        this.address = address;
    }

    // A机构 仅仅是需要money
    public static GenerateOrder_StaticFactoryMethods generateOrderByA(double money){
        return new GenerateOrder_StaticFactoryMethods(0, money, null);
    }

    // B机构 需参数为count、money。
    public static GenerateOrder_StaticFactoryMethods generateOrderByB(int count, double money){
        return new GenerateOrder_StaticFactoryMethods(count, money, null);
    }

    // C机构 需参数为count、money、address。
    public  static GenerateOrder_StaticFactoryMethods generateOrderByC(int count, double money, String address){
        return new GenerateOrder_StaticFactoryMethods(count, money, address);
    }

    public int getCount() {return count; }

    public double getMoney() {return money;}

    public String getAddress() {return address;}
}

```

```java
public class Item_1 {
    public static void main(String[] args){

        // 使用静态工厂调用
        GenerateOrder_StaticFactoryMethods gs1 = GenerateOrder_StaticFactoryMethods.generateOrderByA(200D);
        GenerateOrder_StaticFactoryMethods gs2 = GenerateOrder_StaticFactoryMethods.generateOrderByB(1, 200D);
        GenerateOrder_StaticFactoryMethods gs3 = GenerateOrder_StaticFactoryMethods.generateOrderByC(1, 200D, "深圳");
        System.out.println(gs1.getMoney());
        System.out.println(gs2.getCount());
        System.out.println(gs3.getAddress());
    }
}
	

```
</details>

### 2. 不必在每次调用它们的时候都创建一个新对象。

静态工厂方法可以使不可变类**直接使用预先构建好的实例**，或者**将构建好的实例缓存起来**，进行重复利用，如果程序**经常请求**创建相同的对象，并且创建对象的**代价很高**，则这项技术可以**极大地提升性能**。

静态工厂方法能够为重复的调用返回相同对象，这样有助于类总能**严格控制在某个时刻哪些实例应该存在**，可以直接和单例搭配起来使用。

<details>
<summary>懒汉单例模式</summary>

```java

public class SingletonDemo_ {
    //懒汉单例模式

    //构造函数私有化
    private SingletonDemo_(){}

    //维护一个单例对象
    private static SingletonDemo_ singletonDemo_;

    public static SingletonDemo_ getSingletonDemo_(){
        if (singletonDemo_ == null){
            singletonDemo_ = new SingletonDemo_();
        }
        return singletonDemo_;
    }
}

```

</details>
<details>
<summary>静态工厂模式</summary>

```java
public class SingletonDemo_SFM {

    private SingletonDemo_SFM(){}

    private volatile static SingletonDemo_SFM singletonDemo_sfm;

    public static SingletonDemo_SFM getSingletonDemo_sfm(){
        if (singletonDemo_sfm == null){
            // 由于内存模型，静态工厂方法多线程情况下也会有问题，即使用了双重锁定也一样
            synchronized (SingletonDemo_.class){
                if(singletonDemo_sfm == null){
                    singletonDemo_sfm = new SingletonDemo_SFM();
                }
            }
        }
        return singletonDemo_sfm;
    }
}

```
</details>

### 3. 它们可以返回原返回类型的任何子类型的对象

**构造器**只能返回当前类的实例，无法返回子类的实例。
虽说推荐的是复合，而不推荐继承。但是有时还是会因为业务需求，可能**需要返回对应的子类型对象**。这个时候依靠**构造器就无法完成**了，只能单独创建对应的返回子对象的方法来实现对应需求。
但是使用**静态方法**我们就可以很好的解决这个问题。还减少了重复的方法创建。

<details>
<summary>静态工厂返回子类型</summary>

```java
public class SingletonDemo_SFM {

    private SingletonDemo_SFM(){}

    private volatile static SingletonDemo_SFM singletonDemo_sfm;

    public static SingletonDemo_SFM getSingletonDemo_sfm(){
        if (singletonDemo_sfm == null){
            // 由于内存模型，静态工厂方法多线程情况下也会有问题，即使用了双重锁定也一样
            synchronized (SingletonDemo_.class){
                if(singletonDemo_sfm == null){
                    singletonDemo_sfm = new SingletonDemo_SFM();
                }
            }
        }
        return singletonDemo_sfm;
    }
}

```
</details>

4. 返回的类可以随着每次调用而动态变化，这取决于静态工厂的方法的参数值
所返回的对象的类取决于静态工厂方法的**参数值**。
只要是已声明的返回类型的**子类型**，都是允许的。返回对象的类也可能随着发行版本的不同而不同。

5. 代码更加简洁，减少我们的代码量。
6. 静态工厂返回的类可以不存在



[参考博客](https://www.cnblogs.com/zhh19981104/p/12145724.html)