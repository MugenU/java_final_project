# 葫芦娃大战妖精

## 151220153 於李

## 简介

程序完成了要求的全部功能，包括分阵营按随机阵型站队、对象多线程并发移动&发起攻击、执行过程的存储&复现。为某些方法项目编写了单元测试，采用Maven进行构建管理(使用mvn clean test package可以打包)。

### 程序界面

![界面](/screenshots/screenshot1.png)

界面参考了老师给的样例。要求绘制一个坐标化的二维空间，由于美工水平比较低，就干脆做成了一个10*20的棋盘，棋子则代表各个人物。

### 基本实现
使用JFrame绘制了程序窗口，Space继承了JPanel，是程序的主体，即战场。在新建Space对象时调用initField对战场元素进行了初始化(每当重复执行时都会再次调用)。当开始战斗时，从Space的线程池中给各个生物分配线程，各自进行行动，为了保证线程的安全性，在进行移动时，使用了synchronized对临界区进行了保护。Creature的移动都是随机的，会在相邻的八个格子中选择一个空格进行移动；在程序中每个生物的攻击都被设定为AOE，只要相邻的敌人都会被无差别攻击，每次攻击会在[0~生物的power]中随机选择一个数值，从敌人的power中扣除，每个线程的循环结束条件即power为0.

Space分为四个状态:BEFORE,RUN,REPLAY,FINISH.初始化后为BEFORE，可以开始/回放；开始执行后即为RUN状态，会调用Recorder进行记录；复现战斗时则为Replayer分配一个线程，为REPLAY状态，不会进行记录；战斗或回放结束后进入FINISH状态，可以重新开始或回放,关闭文件读写。

### 操作方式

空格:开始战斗

L键:读取记录

R键:重置战场

-/=:加速/减速

其中空格/L键/R键只在非战斗非回放状态下有效，而更改执行速度只在战斗或回放时有效;由于回放需要对文件读入并分析，所以运行速度相对慢一点。

## 使用的面向对象特性

### 封装
不同类之间的属性并不公开，必须通过特定的方法才能访问；不同类之间方法的具体实现是不可见的。

### 继承
程序中大部分类都应用到了继承机制。如程序的入口Main继承了JFrame类，Creature类实现了Runnable接口，并派生出Huluwa,Grandpa等其他具体的生物。

### 多态
对生物的存储时并未具体区分，全部存储在Creature类型的容器中。


## 使用的设计原则

### SRP单一职责原则
每个类能实现的功能都很单一，Creature描述生物体的属性和行动，Position描述生物体的位置，Formatter负责阵型划分，Recorder记录战斗过程，Replayer复现战斗过程，Space则负责运行与管理以及战场的绘制；减少了类之间的耦合，提高了可维护性。

### LSP替换法则
对父类的操作可以用子类来代替，集中体现在Creature与Huluwa,Grandpa等的关系上。

### DIP依赖倒置原则
高层模块仅依赖于抽象：如Huluwa依赖于抽象类Creature，而Creature只依赖于接口Runnable。

### 合成/聚合复用原则
Space与Position,Formatter,Recorder等具体功能类的关系是HAS-A，实现了组件的动态配置。

### 最少知识原则
每个类的大部分属性的访问权限都不是public，外界只能通过提供的方法来获取/操作自身的属性。


## 采用到的机制

### 异常处理
程序中的异常大多非致命错误，并未做过多处理，一般都是通过printStackTrace获取运行情况。

### 集合
程序主要采用了ArrayList对对象进行存取，取代了不能使用范型的数组。棋盘中的生物体使用ArrayList<Creature>表示，棋盘上的每一个位置则用一个二维ArrayList表示，方便访问。

### 范型
Position类的Holder只允许是Creature和其派生类，Formatter类也只允许对Creature及其派生类进行操作；另外，在使用容器时也有所体现。

### 注解
主要使用的注解有@Override,@Test和@Before，后两个在测试类中有所体现。

### 输入输出
采用的输入输出机制主要体现在文件的读取上，是程序实现记录和复现功能的重要凭依；Recorder使用了PrinterWriter以字符串格式写入文件，Replayer则利用InputStreamReader和BufferedReader完成文件的读入。

### 单元测试
为文件的读取编写和阵型的分配编写了测试类，阵型的测试是通过输出Position的状况来判断正确性，类似于前一阶段的葫芦娃；文件的读取则是在测试类中测试通过才应用到程序中(不知道算不算测试ORZ)。

## 精彩的战斗

### 《喽啰的胜利》

![回放](/screenshots/screenshot2.png)

收录在record目录下，笑到最后的既不是葫芦娃也不是蛇蝎两个BOSS，而是四个炮灰一样的喽啰。(喽啰:是我DIO哒！)

PS.建议快进体验，前期双方一直在探索地图，猥琐发育(其实是随机行走的锅)
