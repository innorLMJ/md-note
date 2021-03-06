### Bridge

**定义**：将抽象部分（业务功能）与它的实现部分（平台实现）分离，使它们都可以独立地变化。
**动机**：一个类中的方法可能可以划分为几个部分（维度），每个维度应该可以独立的变化，因此将它们按维度<u>拆分</u>；再将拆分后的接口<u>组合</u>，使得一个维度可以调用另一个维度的方法。

**适用性**：

1. 不希望抽象和它的实现之间有固定的绑定关系，希望程序运行时可以选择/切换具体实现。如多平台。
2. 希望类的抽象和实现可以分别独立地进行扩充（多维度地扩充）。

**结构**：

- implementor：抽象接口。
  - 定义一些基本操作供abstraction调用。（一个维度定义一个implementor）
- abstraction：定义抽象功能的接口。
  - 含有指向implementor的指针；
  - 子类在实现接口时，会用到imp接口定义的基本操作。这使得抽象部分的实现与其他维度的实现（如平台）无关。

**总结**：

1. 使用对象间的组合关系，来解耦抽象和实现，使得抽象和实现可以各自独立地变化。
2. 可以替代多继承，后者违背单一职责原则，复用性差。
3. 一般应用在“两个非常强的变化维度”。

**Bridge感觉结构上和strategy一样？**

Bridge关注的是类结构上的设计，职责的拆分与组合，集合上，strategy也属于一种变化的维度；而Strategy要体现的是运行时绑定思想。  

