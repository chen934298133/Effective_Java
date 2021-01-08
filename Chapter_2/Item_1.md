# 使用静态工厂方法代替构造器

## 一、 客户端获取一个类的实例，两种解决方案

- 构造器
<details>
<summary>展开</summary>


```
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

```
public class Item_1 {
    public static void main(String[] args){

        // 使用构造器
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
