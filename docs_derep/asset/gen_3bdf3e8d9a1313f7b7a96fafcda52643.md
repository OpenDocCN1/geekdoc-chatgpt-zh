# 三分钟看懂三款高频设计模式的核心实现

## 备忘录模式：给对象状态拍快照，随时回滚
Originator 负责生成快照并在需要时依据快照恢复内部字段，Caretaker 只做保管人不窥探内容。实战流程是先给源对象设定初始值并打印，紧接着生成备忘录保存，随后把状态改动再打印一次，最后调用恢复方法把之前保存的值写回去并输出对比结果。整个过程把状态捕获和状态管理解耦，实现了撤销能力。

    public Memento createMemento() {

        return new Memento(state);

    }

    public void restoreMemento(Memento memento) {

        state = memento.getState();

    }

}

// 管理者类

class Caretaker {

    private Memento memento;

    public void setMemento(Memento memento) {

        this.memento = memento;

    }

    public Memento getMemento() {

        return memento;

    }

}

// 客户端类

public class Client {

    public static void main(String[] args) {

        Originator originator = new Originator();

        Caretaker caretaker = new Caretaker();

        // 设置初始状态

        originator.setState("State 1");

        System.out.println("当前状态: " + originator.getState());

        // 创建备忘录并保存

        Memento memento = originator.createMemento();

        // 修改状态

        originator.setState("State 2");

        System.out.println("当前状态：" + originator.getState());

        // 恢复状态

        originator.restoreMemento(caretaker.getMemento());

        System.out.println("恢复后的状态：" + originator.getState());

    }

}

## 访问者模式：把操作从元素结构中抽离出来
Element 定义统一的 accept 入口，ConcreteElementA 与 ConcreteElementB 各自把自己交给访问者处理。Visitor 接口声明针对不同元素的访问方法，ConcreteVisitor 在方法内部调用元素的具体业务 operationA / operationB。客户端只需要创建元素和访问者实例，然后让元素 accept 访问者即可完成解耦扩展。

[PRE0]java

// 元素接口

interface Element {

    void accept(Visitor visitor);

}

// 具体元素A

class ConcreteElementA implements Element {

    public void accept(Visitor visitor) {

        visitor.visitConcreteElementA(this);

    }

    public void operationA() {

        // 具体元素A的操作

    }

}

// 具体元素B

class ConcreteElementB implements Element {

    public void accept(Visitor visitor) {

        visitor.visitConcreteElementB(this);

    }

    public void operationB() {

        // 具体元素B的操作

    }

}

// 访问者接口

interface Visitor {

    void visitConcreteElementA(ConcreteElementA elementA);

    void visitConcreteElementB(ConcreteElementB elementB);

}

// 具体访问者

class ConcreteVisitor implements Visitor {

    public void visitConcreteElementA(ConcreteElementA elementA) {

        // 对具体元素A的操作

        elementA.operationA();

    }

    public void visitConcreteElementB(ConcreteElementB elementB) {

        // 对具体元素B的操作

        elementB.operationB();

    }

}

// 客户端

public class Client {

    public static void main(String[] args) {

        Element elementA = new ConcreteElementA();

        Element elementB = new ConcreteElementB();

        Visitor visitor = new ConcreteVisitor();

        elementA.accept(visitor);

        elementB.accept(visitor);

    }

}

## 装饰器模式：动态给对象叠加功能
Component 规定核心操作，ConcreteComponent 提供基础实现。Decorator 抽象类持有被装饰对象并在 operation 中转发调用，具体装饰器 ConcreteDecoratorA / ConcreteDecoratorB 在转发前后追加额外行为。客户端先实例化基础组件，再用多层装饰器包裹，最后统一调用 operation 即可看到基础行为与增强行为依次执行。

[PRE1]java

// 组件接口

interface Component {

    void operation();

}

// 具体组件类

class ConcreteComponent implements Component {

    public void operation() {

        System.out.println("执行具体组件操作");

    }

}

// 抽象装饰器类

abstract class Decorator implements Component {

    protected Component component;

    public Decorator(Component component) {

        this.component = component;

    }

    public void operation() {

        component.operation();

    }

}

// 具体装饰器类A

class ConcreteDecoratorA extends Decorator {

    public ConcreteDecoratorA(Component component) {

        super(component);

    }

    public void operation() {

        super.operation();

        System.out.println("执行具体装饰器A的操作");

    }

}

// 具体装饰器类B

class ConcreteDecoratorB extends Decorator {

    public ConcreteDecoratorB(Component component) {

        super(component);

    }

    public void operation() {

[PRE2]

super.operation();

System.out.println("执行具体装饰器B的操作"); }

)

public class Client {

public static void main(String[] args) {

// 创建具体组件对象

Component component = new ConcreteComponent();

// 使用装饰器包装具体组件

Component decoratedComponent = new ConcreteDecoratorA(new Concrete

DecoratorB(component));

// 调用装饰后的操作

decoratedComponent.operation();

}

[PRE3]

// 具体组件类

class ConcreteComponent implements Component {

    public void operation() {

        System.out.println("执行具体组件操作");

    }

}

// 抽象装饰器类

abstract class Decorator implements Component {

    protected Component component;

    public Decorator(Component component) {

        this.component = component;

    }

    public void operation() {

        component.operation();

    }

}

// 具体装饰器类A

class ConcreteDecoratorA extends Decorator {

    public ConcreteDecoratorA(Component component) {

        super(component);

    }

    public void operation() {

        super.operation();

        System.out.println("执行具体装饰器A的操作");

    }

}

// 具体装饰器类B

class ConcreteDecoratorB extends Decorator {

    public ConcreteDecoratorB(Component component) {

        super(component);

    }

    public void operation() {

        super.operation();

        System.out.println("执行具体装饰器B的操作");

    }

}

[PRE4]

public class Client {

    public static void main(String[] args) {

        // 创建具体组件对象

        Component component = new ConcreteComponent();

        // 使用装饰器包装具体组件

        Component decoratedComponent = new ConcreteDecoratorA(new ConcreteDecoratorB(component));

        // 调用装饰后的操作

        decoratedComponent.operation();

    }

}