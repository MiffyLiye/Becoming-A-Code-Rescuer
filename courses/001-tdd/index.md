# 测试驱动开发

Produced by 王韬 Wang, Tao

https://miffyliye.org/

---

## 课程安排

* 理论
* 实践
* 讨论

---

## 测试驱动开发

*  测试驱动开发（Test-Driven Development, TDD）是极限编程（Extreme Programming, XP）中倡导的程序开发方法，倡导先写测试程序，然后编码实现其功能得名
* TDD过程  
<small>
<span style="color:red">添加一个测试且测试失败（红）</span>  
<span style="color:green">使测试通过（绿）</span>  
<span style="color:blue">重构</span>  
下一个任务
</small>
* 三条原则  
<small>
没有测试之前不要写任何功能代码  
一次只写一个刚好失败的测试，作为新功能的描述  
不写任何多余的产品代码，除非它刚好能让失败的测试通过
</small>

---

## 为什么使用TDD

* 细化需求，拆分任务（Test-Driven Business）
* 辅助系统设计，松耦合（Test-Driven Design）
* 快速反馈
* 最新的说明文档（Live Document）
* 安全重构
* ......

---

## 系统需求：猜数字游戏

* 游戏开始后，系统会随机生成4个不重复的数字
* 你有6次猜测的机会，如果猜对则获胜，否则失败
* 每次猜测时，需按顺序输入4个数字，程序会根据猜测的情况给出{x}A{y}B的反馈
* A前面的数字代表位置和数字都对的个数，B前面的数字代表数字对但是位置不对的个数

---

## 实例化需求  

* 通过实例去分析和沟通需求
* 实例化需求的过程  
<small>
从目标中获取范围  
用实例进行描述  
精炼需求说明  
自动化验证，无需改变需求说明  
频繁验证  
积累出组织良好、易于查找、前后一致的活文档
</small>
<small>
> 如果没有活文档，任何重大的重构都是自寻死路  
> Without live documentation, any major refactoring would be a suicide  
> 
> From [*Specification by Example:How Successful Teams Deliver the Right Software*](https://book.douban.com/subject/6515486/)
</small>

---

## 问题分解
（Problem Decomposition）

* 练习分解问题
* 选择问题解决顺序

---

## 模块划分

* 信息的拥有者同时就是信息的操作者，  
保持模块的相对独立
* 各模块的职责单一明确，易于理解

---

## 测试驱动

* 测试应根据业务编写测试用例
* 一个测试方法只做一件事，  
代表一个测试样本的一个业务规则
* 测试如何表达设计者的意图，  
提高测试的可维护性
* 测试对象如何具有良好的可测试性
* 关注接口，而非实现

---

## Given-When-Then 风格

* Given：创建被测试对象，以及其他协作对象
* When：确定被测试接口的命名、传入参数，  
考虑行为方式是命令式（Command）  
还是查询式（Query）
* Then：验证结果

---

## 实现

* 伪实现
* 直接实现
* ......

---

## 测试编写的FIRST原则

* Fast
* Independent
* Repeatable
* Self-Validation
* Timely

---

## 测试替身
(Test Doubles)

* Stub
* Mock
* Dummy, Fake, Spy, etc.

---

## 问题讨论