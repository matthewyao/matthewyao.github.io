---
layout:     post
title:      装饰者模式
discription: 本文通过一个具体的例子来解释什么是装饰者模式。
date:       2018-05-01 18:42:00
catalog:    true
tags:       [Java, 设计模式,  ]
---

### 装饰者模式定义

首先我们看一下《Head First 设计模式》一书中对装饰者模式的定义

> **装饰者模式**动态地将责任附加到对象上，若要扩展功能，装饰者提供了比继承更有弹性的替代方案

这个定义是比较抽象的，换一种方式描述即是：传统而言，我们经常使用继承来实现功能的扩展，但这种方式是在编译期间就已经决定了子类拥有的功能的，装饰者模式不仅能在运行时动态地提供功能扩展，还可以避免因继承而带来的类爆炸的副作用。



#### UML图

因为我比较喜欢吃沙拉，就以沙拉为例，来看一下装饰者模式的UML图![](http://oc26wuqdw.bkt.clouddn.com/blog/2018/5/decorator/decorator_uml.png)
- 我们可以看到，所有的类都有一个共同的超类（Salad），在这里我们也可以使用接口来代替抽象类，但抽象类能为超类提供一些具体方法的实现，避免在子类中逐个去实现，这里我们使用的是抽象类。
- 在装饰者模式中，装饰者和被装饰者有共同的超类，继承的目的是继承类型（装饰者和被装饰者都从同一超类继承而来），而不是行为。

下面我们通过具体例子来看一下


#### 代码
超类Salad，拥有两个抽象方法getName和getPrice
```
public abstract class Salad {

    private int scale;//1-小份，2-中份（加3元），3-大份（加5元）

    protected int getScale() {
        return scale;
    }

    public Salad setScale(int scale) {
        this.scale = scale;
        return this;
    }

    protected abstract String getName();//获取名称

    protected abstract double getPrice();//获取价格

    //抽象类相比接口可以实现具体的方法，避免逐个修改
    protected String getDesc() {
        return "直接吃也是可以的哦";
    }
}

```


下面看一下三个具体的沙拉子类

```
public class CaesarSalad extends Salad{

    @Override
    protected String getName() {
        return "凯撒沙拉";
    }

    @Override
    protected double getPrice() {
        return 18;
    }
}

public class CobbSalad extends Salad {

    @Override
    protected String getName() {
        return "考伯沙拉";
    }

    @Override
    protected double getPrice() {
        return 17;
    }
}

public class FruitSalad extends Salad {

    @Override
    protected String getName() {
        return "水果沙拉";
    }

    @Override
    protected double getPrice() {
        return 20;
    }
}
```
然后是装饰者抽象父类SaladDecorator，这里并没有增加其他的抽象方法，只是为了让结构更加完整，实际中可以为装饰者增加额外的方法
```
public abstract class SaladDecorator extends Salad {

}
```

然后是四种具体的装饰者类

```
public class EggDecorator extends SaladDecorator {

    private Salad salad;

    public EggDecorator(Salad salad) {
        this.salad = salad;
    }

    @Override
    protected String getName() {
        return salad.getName() + "加鸡蛋";
    }

    @Override
    protected double getPrice() {
        return salad.getPrice() + 2.5;
    }
}

public class AppleDecorator extends SaladDecorator {

    private Salad salad;

    public AppleDecorator(Salad salad) {
        this.salad = salad;
    }

    @Override
    protected String getName() {
        return salad.getName() + "加苹果";
    }

    @Override
    protected double getPrice() {
        return salad.getPrice() + 4.5;
    }
}

public class FishDecorator extends SaladDecorator {

    private Salad salad;

    public FishDecorator(Salad salad) {
        this.salad = salad;
    }

    @Override
    protected String getName() {
        return salad.getName() + "加龙利鱼";
    }

    @Override
    protected double getPrice() {
        return salad.getPrice() + 8;
    }
}

public class ScaleDecorator extends SaladDecorator {

    private Salad salad;

    public ScaleDecorator(Salad salad) {
        this.salad = salad;
    }

    @Override
    protected String getName() {
        String scale = "";
        switch (salad.getScale()) {
            case 1:
                scale = "小份";
                break;
            case 2:
                scale = "中份";
                break;
            case 3:
                scale = "大份";
                break;
        }
        return salad.getName() + scale;
    }

    @Override
    protected double getPrice() {
        double price = 0;
        switch (salad.getScale()) {
            case 1:
                price = 0;
                break;
            case 2:
                price = 3;
                break;
            case 3:
                price = 5;
                break;
        }
        return salad.getPrice() + price;
    }
}
```
这里我们注意到描述大小的装饰者ScaleDecorator，我们使用了salad中的**scale字段**，并通过switch来分别获取大小和价格，这样可以避免增加三个（乃至更多）的装饰者类来描述价格。

#### 结果
测试代码

```
public class Test {

    public static void main(String[] args) {
        Salad salad = new FishDecorator(new EggDecorator(new FruitSalad()));
        print(salad);

        Salad salad1 = new FishDecorator(new EggDecorator(new ScaleDecorator(new FruitSalad().setScale(3))));
        print(salad1);

        Salad salad2 = new AppleDecorator(new EggDecorator(new FishDecorator(new AppleDecorator(new EggDecorator(new CaesarSalad())))));
        print(salad2);

        Salad salad3 = new CobbSalad();
        print(salad3);

        Salad salad4 = new EggDecorator(new EggDecorator(new FruitSalad()));
        print(salad4);
    }

    private static void print(Salad salad) {
        System.out.println(salad.getName() +" 总计: " + salad.getPrice() + "元");
    }
}
```

我们来运行一下
![image](http://oc26wuqdw.bkt.clouddn.com/blog/2018/5/decorator/output.png)
可以看到所有的名称和价格都打印出来了
