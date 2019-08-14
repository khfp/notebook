<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-08-13 15:36:31
 * @LastEditTime: 2019-08-14 15:13:54
 * @LastEditors: Please set LastEditors
 -->
## length() 方法，length 属性和 size() 方法的区别:

* length() 方法是针对字符串来说的，要求一个字符串的长度就要用到它的length()方法；
* length 属性是针对 Java 中的数组来说的，要求数组的长度可以用其 length 属性；
* Java 中的 size() 方法是针对泛型集合说的, 如果想看这个泛型有多少个元素, 就调用此方法来查看!

## equals()与==的区别

* 在基本数据类型中(byte,short,char,int,long,float,double,boolean)(**String不是基本类型**)的比较应使用**==**，比较的是他们的值
* 在复合类型(**类**)中：当他们用 **==** 进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。
* JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地址，对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的。
但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date，在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。因为Object的equals方法也是用双等号 **==** 进行比较的，所以比较后的结果跟双等号 **==** 的结果相同。
