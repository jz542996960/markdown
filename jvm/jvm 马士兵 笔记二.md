![image-20200911202530674](assets/image-20200911202530674.png)

栈空间方法结束后会自动释放

内存泄漏 -> 内存溢出





什么是垃圾：

   没有引用指向的内存，就定位为垃圾



引用计数：

​    无法解决循环引用

   ![image-20200911210316947](assets/image-20200911210316947.png)

Root Searching

![image-20200911211026714](assets/image-20200911211026714.png)

Gc 算法

![image-20200911211038989](assets/image-20200911211038989.png)

![image-20200911211101862](assets/image-20200911211101862.png)

内存碎片化严重，时间长了，无法分配大量连续内存



![image-20200911211321167](assets/image-20200911211321167.png)

内存浪费



![image-20200911211421010](assets/image-20200911211421010.png)

效率低



垃圾回收器：

![image-20200911211718004](assets/image-20200911211718004.png)

内存分带模型

![image-20200911212238744](assets/image-20200911212238744.png)

![image-20200911214124068](assets/image-20200911214124068.png)

![image-20200911214209570](assets/image-20200911214209570.png)

CMS   concurrentgc  没有stw

![image-20200911215040014](assets/image-20200911215040014.png)

![image-20200911215245690](assets/image-20200911215245690.png)

cms

![image-20200911215527177](assets/image-20200911215527177.png)

三色标记算法

并发标记

![image-20200911224712012](assets/image-20200911224712012.png)



![image-20200911225047445](assets/image-20200911225047445.png)



![image-20200911221947817](assets/image-20200911221947817.png)

![image-20200911221959341](assets/image-20200911221959341.png)

