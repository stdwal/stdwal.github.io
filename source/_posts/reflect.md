---
title: Java反射机制
Date: 2019.3.26
---

```Java
public class Car {

    private String brand;

    private String color;

    private int maxSpeed;

    // 默认构造函数
    public Car() {}

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public void setColor(String color) {
        this.color = color;
    }

    public void setMaxSpeed(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", color='" + color + '\'' +
                ", maxSpeed=" + maxSpeed +
                '}';
    }
}
```

```Java
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

public class ReflectTest {

    public static Car initByDefaultConst() throws Throwable {

        // 通过类装载器获得Car类对象
        ClassLoader loader = Thread.currentThread().getContextClassLoader();
        Class clazz = loader.loadClass("Car");

        // 获取类的构造方法并通过它实例化Car
        Constructor cons = clazz.getDeclaredConstructor((Class[])null);
        Car car = (Car)cons.newInstance();

        // 通过反射方法设置属性
        Method setBrand = clazz.getMethod("setBrand", String.class);
        setBrand.invoke(car, "benz");
        Method setColor = clazz.getMethod("setColor", String.class);
        setColor.invoke(car, "黑色");
        Method setMaxSpeed = clazz.getMethod("setMaxSpeed", int.class);
        setMaxSpeed.invoke(car, 200);
        return car;
    }

    public static void main(String[] args) throws Throwable {
        Car car = initByDefaultConst();
        System.out.println(car);
    }
}
```

## 1.类加载器ClassLoader
类加载器就是寻找类的字节码文件并构造出类在JVM内部表示对象的组件。

类加载器把一个类装入JVM中，需要经过以下步骤：
（1）装载：查找和导入Class文件
（2）链接：执行校验、准备和解析（可选）步骤
    1.校验：检查载入Class文件数据的正确性
    2.准备：给类的静态变量分配存储空间
    3.解析：将符号引用转换成直接引用
（3）初始化：对类的静态变量、静态代码块执行初始化工作

类装载工作由ClassLoader及其子类负责。JVM在运行时会产生3个ClassLoader：根装载器、ExtClassLoader（拓展类装载器）和AppClassLoader（应用类装载器）。

根装载器不是ClassLoader的子类，它使用C++编写，因而在Java中看不到它，它负责装载JRE的核心类库
ExtClassLoader和AppClassLoader都是ClassLoader的子类，ExtClassLoader负责装载JRE拓展目录ext中的jar包，AppClassLoader负责装载classpath路径下的类包。

```Java
public class ClassLoaderTest {

    public static void main(String[] args) {
        ClassLoader loader = Thread.currentThread().getContextClassLoader();
        System.out.println("current loader = " + loader);
        System.out.println("parent loader = " + loader.getParent());
        System.out.println("grandparent loader = " + loader.getParent().getParent());
    }
}
```

```
current loader = sun.misc.Launcher$AppClassLoader@18b4aac2
parent loader = sun.misc.Launcher$ExtClassLoader@60e53b93
grandparent loader = null
```

每个类在JVM中都拥有一个对应的java.lang.Class对象，它提供了类结构信息的描述。数组、枚举、注解及基本Java类型，甚至void都拥有对应的Class对象。Class没有public的构造方法。Class对象是在装载类时由JVM通过调用类装载器中的defineClass()方法自动构造的。

Class反射对象描述类语义结构，可以从Class对象中获取构造函数、成员变量、方法类等元素的反射对象，并通过反射对象对目标类对象进行操作：
* Constuctor：类的构造函数反射类
* Method：类方法的反射类
* Field：类的成员变量的反射类

参考：《精通Spring 4.x  企业应用开发实战》
