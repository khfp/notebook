<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-05-09 16:24:27
 * @LastEditTime: 2019-08-15 10:21:20
 * @LastEditors: Please set LastEditors
 -->
# python学习笔记

## 1. numpy.ndarray.dtype

&emsp;改变dtype时，需要用astype函数，不能直接改。

## 2. 浅复制与深度复制

&emsp;浅复制复制的是引用对象，仍指向同一内存地址；深度复制会开辟新的内存地址存放相同的内容。

## 3. y//x与 int(y/x) 的区别

&emsp; // 朝 **小方向** 取整；int朝**0方向**取整。

## 4. if not x is None 和 if x is not None 的区别

关键在于 **is**: is 比较的是两个实例对象是不是完全相同，它们是不是同一个对象，占用的内存地址是否相同; **==** 比较的是两个对象的内容是否相等，即内存地址可以不一样，内容一样就可以了
**None**在python中是一个单例对象
`
x = None
y = []
x_a = x is None # True

x_b = not (x is None) # False  

x_c = x is not None # False  

y_a = y is None # False  

y_b = not (y is None) # True  

y_c = y is not None # True
`
