<!--
 * @Author: your name
 * @Date: 2020-06-08 22:33:46
 * @LastEditTime: 2020-06-28 11:50:32
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \GitHubnotebook\面试题笔记\JavaSE.md
--> 

# JavaSE

## 1. 自增变量

- 赋值操作=，最后计算
- =右边的从左到右加载值依次压入操作数栈
- 实际先算哪个看运算符优先级
- 自增，自减操作都是直接修改变量的值不经过操作数栈
- 最后的赋值之前，临时结果也是存储在操作数栈中
  
## 2. Singleton

Singleton: 在java中指单例设计模式

- 某个类只能有一个实例
  - 构造器私有化
- 必须自行创建这个实例
  - 该类的静态变量保存这个唯一的实例
- 必须自行向整个系统提供这个实例
  - 对外提供获取该实例对象的方式
    - 直接暴露
    - 用静态变量的get方法获取

- 饿汉式：直接创建对象，不存在线程安全问题
  - 直接实例化饿汉式(简洁直观)
  - 枚举式(最简洁)
  - 静态代码段饿汉式(适合复杂实例化)
- 懒汉式：延迟创建对象
  - 线程不安全(适合单线程)
  - 线程安全(适用于多线程)
  - 静态内部类形式(适用于多线程)

```java
/*
 *饿汉式：直接创建实例对象，不管是否需要
    (1) 构造器私有化
    (2) 自行创建，并用静态变量保存
    (3) 向外提供这个实例
    (4) 强调这是个单例，用final修改
 */
public class Singleton1{
    public static final Singleton1 INSTANCE = new Sington1()
    private Singteton1(){
        }
}
```

```java
/*
 * 枚举类型：表示该类型的对象是有限的几个
 * 限制成为一个，就成了单例
 */
public enum Singleton2 {
    INSTANCE
}
```

```java
/*
 *与配置文件交互
*/
public class Singleton3 {
    public static final Singleton3 INSTANCE;
    private String info;
    static{
        Properties pro = new Properties();
        pro.load(Singleton3.class.getClassLoader().getResourceAsStream("single.properties"))
        INSTANCE = new Singleton3(pro.getProperty("info"));
    }
    private Singleton3(String info){
        this.info = info;
    }
}
```

```java
/*
 * 懒汉式：延迟创建这个对象
 * 
 * (1)构造器私有化
 * (2)用一个静态变量保存这个唯一的实例
 * (3)提供一个静态方法，获取这个实例对象
 */
//线程不安全的
public class Singleton4 {
    private static Singleton4 instance;
    private Singleton4(){
    }
    public static Singleton4 getInstance(){
        if(instance == null){
            instance = new Sintleton4();
        }
        return instance
    }
}
//线程安全的
public class Singleton5 {
    private static Singleton5 instance;
    private Singleton5(){
    }
    public static Singleton5 getInstance(){
        if(instance==null){
            synchronized(Singleton.class){
                if(instance==null){
                    try{
                        Thread.sleep(100);
                    }catch(InterruptedException e){
                        e.printStackTrace();
                    }
                    instance = new Singleton5();
                }
            }
        }
        return instance
    }
}
```

```java
/*
 *在内部类被加载和初始化时，才创建INSTANCE实例对象
 *静态内部类不会自动随着外部类的加载和初始化而加载初始化,它是要单独加载和初始化的
 */
public class Singleton6 {
    private Singleton6(){
    }
    private static class Inner{
        private static final Singleton6 INSTACE = new Sinleton6();
    }
    public static Singleton6 getInstance(){
        return Inner.INSTANCE;
    }
}
```

##　类初始化过程

1. 一个类要创建实例需要先加载并初始化该类
    - main方法所在的类需要先加载和初始化
2. 一个子类要初始化需要先初始化父类
3. 一个类的初始化就是执行<clinit>()方法
   - <clinit>()方法由静态类变量显示赋值代码和静态代码块组成
   - 类变量显示赋值代码和静态代码块从上到下顺序执行
   - <clinit>()方法只执行一次

## 实例初始化过程

1. 实例初始化就是执行<init>()方法
   - <init>()方法可能重载多个，有几个构造器就有几个<init>方法
   - <init>()方法由非静态实例变量显示赋值代码和非静态代码块，对应构造器代码组成
   - 非静态实例变量显示赋值代码和非静态代块代码从上到下顺序执行，而对应的构造器的代码最后执行
   - 每次创建实例对象，调用对应构造器，执行的就是对应的<init>方法
   - <init>方法的首行是super()或super(实参列表)，即对应父类的<init>方法

## 方法的重写

1. 哪些方法不能被重写
   - final方法
   - 静态方法
   - private等子类中不可见方法
2. 对象的多态性
   - 子类的如果重写了父类方法，通过子类对象调用的一定是子类重写过的代码
   - 非静态方法默认的调用对象是this
   - this对象在构造器或者说<init>方法中就是正在创建的对象

## 方法参数传递

1. 形参是基本数据类型
   - 传递数据值
2. 实参是引用数据类型
   - 传递地址值
   - **特殊的类型：String,包装类等对象不可变性**

## 变量

1. 就近原则
2. 变量的分类
  1. 局部变量和成员变量的区别：
    - 局部变量：方法体{}中,形参，代码块{}中
    - 成员变量：类中方法外
      - 类变量：有static修饰
      - 实例变量：没有static修饰
  2. 修饰符
    - 局部变量：final
    - 成员变量：public protected private final static volatile transient
  3. 值存储的位置
    - 局部变量：栈
    - 实例变量：堆
    - 类变量：方法区
  4. 作用域：
    - 局部变量：从声明处开始，到所属的}结束
    - 实例变量：在当前类中"this."(this有时可以缺省)，在其他类中对象名的访问。
    - 类变量：在当前类中"类名."(有时类名可以缺省)，在其他类中"类名."或"对象名."访问
  5. 生命周期
    - 局部变量：每一个线程，每一次调用执行都是新的生命周期
    - 实例变量：随着对象的创建而初始化，随着对象的销毁而消亡，每一个对象的实例变量都是独立的
    - 类变量：随着类的初始化而初始化，随着类的卸载而消亡，该类的所有对象的类变量都是共享的
3. 非静态代码块的执行
4. 方法的调用规则

## 集合类不安全问题

1. ArrayList(/Map/Set)线程不安全
   1. 故障现象：java.util.ConcurrentModification java多线程下常见的异常
   2. 导致原因：
   3. 解决方案：
      1. new Vector();
      2. Collections.synchronizedList(new ArrayList<>());
      3. new CopyOnWriteArrayList(/Map/Set)<>(); 写时复制 读写分离的思想
   4. 优化建议：写时复制 读写分离的思想
      1. CopyOnWrite容器即写时复制的容器。往一个容器添加元素的时候，不直接往当前容器object[] 容器添加，而是先将当前容器object[] 进行拷贝，复制出一个新的容器object[] newElements,然后新的容器object[] newElements里添加元素，添加完元素之后再将原容器的引用指向新的容器setArray(newElements);这样做的好处是可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素，所以CopyOnWrite也是一种读写分离的思想，读和写不同的容器
2. 
