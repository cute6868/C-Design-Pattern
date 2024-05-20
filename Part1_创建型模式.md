# 单例模式

## 懒汉式

```cpp
#include<iostream>
using namespace std;

// 懒汉式
class SingleTon
{
public:
	static SingleTon* m_singleTon;		// 静态指针
	static SingleTon* GetInstance()		// 提供静态方法，用来获得对象
	{
		if (m_singleTon == NULL)
		{
			m_singleTon = new SingleTon;
		}
		return m_singleTon;
	}

	void test()
	{
		cout << "成功调用了对象{" << this << "}的方法" << endl;
	}

private:
	SingleTon()		// 构造函数私有化
	{
		cout << "构造了一个对象" << endl;
	}
};


SingleTon* SingleTon::m_singleTon = NULL;

int main()
{
	// SingleTon obj;		// 报错，无法直接创建对象

	SingleTon* obj1 = SingleTon::GetInstance();	// 创建对象
	obj1->test();

	SingleTon* obj2 = SingleTon::GetInstance();	// 创建对象
	obj2->test();


	delete obj1;	// 或者 delete obj2;		obj1 和 obj2 两者指向同一个对象

	system("pause");
	return 0;
}
```



## 饿汉式

```cpp
#include<iostream>
using namespace std;

// 饿汉式
class SingleTon
{
public:
	static SingleTon* m_singleTon;		// 静态指针
	static SingleTon* GetInstance()		// 提供静态方法，用来获得对象
	{
		return m_singleTon;
	}

	void test()
	{
		cout << "成功调用了对象{" << this << "}的方法" << endl;
	}

private:
	SingleTon()		// 构造函数私有化
	{
		cout << "构造了一个对象" << endl;
	}
};


SingleTon* SingleTon::m_singleTon = new SingleTon();

int main()
{
	SingleTon* obj1 = SingleTon::GetInstance();	// 创建对象
	obj1->test();

	SingleTon* obj2 = SingleTon::GetInstance();	// 创建对象
	obj2->test();


	delete obj1;	// 或者 delete obj2;		obj1 和 obj2 两者指向同一个对象

	system("pause");
	return 0;
}
```

# 工厂模式

## 简单工厂模式

```cpp
#include<iostream>
using namespace std;

/*
	1. 提供一个工厂类：产生不同的产品
	2. 提供一个抽象产品类：运算符类 + 负责运算，得到结果
	3. 提供一个具体产品类：具体运算符
	// 简单计算器 + - * /
*/

// 抽象产品类
class Operation
{
public:
	double m_leftValue;
	double m_rightValue;
	virtual double GetResult() = 0;
	virtual ~Operation() {};
};

// 具体产品类
// 加法运算
class AddOperation :public Operation
{
	double GetResult()
	{
		return m_leftValue + m_rightValue;
	}
};
// 减法运算
class SubOperation :public Operation
{
	double GetResult()
	{
		return m_leftValue - m_rightValue;
	}
};
// 乘法运算
class MulOperation :public Operation
{
	double GetResult()
	{
		return m_leftValue * m_rightValue;
	}
};
// 除法运算
class DivOperation :public Operation
{
	double GetResult()
	{
		return m_leftValue / m_rightValue;
	}
};

// 工厂类
class OperationFactory
{
public:
	static Operation* createOperation(char c)
	{
		switch (c)
		{
		case '+':
			return new AddOperation;
		case '-':
			return new SubOperation;
		case '*':
			return new MulOperation;
		case '/':
			return new DivOperation;
		default:
			break;
		}
	}
};


int main()
{
	Operation* add = OperationFactory::createOperation('+');
	Operation* sub = OperationFactory::createOperation('-');
	Operation* mul = OperationFactory::createOperation('*');
	Operation* div = OperationFactory::createOperation('/');

	// 乘法
	mul->m_leftValue = 10;		// 设置左值
	mul->m_rightValue = 30;		// 设置右值
	cout << mul->GetResult() << endl;	// 输出相乘结果: 300


	delete add;
	delete sub;
	delete mul;
	delete div;

	system("pause");
	return 0;
}
```

## 工厂方法模式

```cpp
#include<iostream>
using namespace std;


// ------------------- 产品 -------------------

// 抽象产品
class AbstractProduct
{
public:
	virtual void fuel() = 0;		// 此产品能补充燃料
	virtual void fly() = 0;			// 此产品能飞
	virtual ~AbstractProduct() {};
};

// 飞机产品
class PlaneProduct : public AbstractProduct
{
public:
	void fuel()
	{
		cout << "飞机-CS1024，补充燃料中..." << endl;
	}

	void fly()
	{
		cout << "飞机-CS1024，起飞！" << endl;
	}
};

// 火箭产品
class RocketProduct : public AbstractProduct
{
public:
	void fuel()
	{
		cout << "火箭-CS2048，补充燃料中..." << endl;
	}

	void fly()
	{
		cout << "火箭-CS2048，点火！" << endl;
	}
};

// ------------------- 工厂 -------------------

// 抽象工厂
class AbstractFactory
{
public:
	virtual AbstractProduct* CreateProduct() = 0;
	virtual ~AbstractFactory() {};
};

// 飞机工厂
class PlaneFactory : public AbstractFactory
{
public:
	AbstractProduct* CreateProduct()
	{
		return new PlaneProduct();
	}
};

// 火箭工厂
class RocketFactory : public AbstractFactory
{
public:
	AbstractProduct* CreateProduct()
	{
		return new RocketProduct();
	}
};

void work(AbstractFactory* factory)
{
	// 工厂干的活是：

	// 1. 生产出产品
	AbstractProduct* product = factory->CreateProduct();

	// 2. 使用该产品的"补充燃料"的功能
	product->fuel();

	// 2. 使用该产品的"飞行"的功能
	product->fly();

	delete factory;
}

int main()
{
	work(new PlaneFactory);		// 让飞机工厂干活
	work(new RocketFactory);	// 让火箭工厂干活

	system("pause");
	return 0;
}
```

## 抽象工厂模式

```cpp
#include<iostream>
using namespace std;

// 抽象产品
class Product
{
public:
	virtual void show() = 0;
	virtual ~Product() {};
};

// 抽象键盘产品
class KeyBoard : public Product
{
public:
	virtual ~KeyBoard() {};
};

// 具体键盘产品
class MikaKeyBoard : public KeyBoard
{
public:
	void show()
	{
		cout << "Mika键盘" << endl;
	}
};
class PikiKeyBoard : public KeyBoard
{
public:
	void show()
	{
		cout << "Piki键盘" << endl;
	}
};

// 抽象鼠标产品
class Mouse : public Product
{
public:
	virtual ~Mouse() {};
};

// 具体鼠标产品
class MikaMouse : public Mouse
{
public:
	void show()
	{
		cout << "Mika鼠标" << endl;
	}
};
class PikiMouse : public Mouse
{
public:
	void show()
	{
		cout << "Piki鼠标" << endl;
	}
};

// 抽象工厂
class Factory
{
public:
	virtual KeyBoard* CreateKeyBoard() = 0;
	virtual Mouse* CreateMouse() = 0;
	virtual ~Factory() {};
};

// 具体Mika工厂
class MikaFactory : public Factory
{
	KeyBoard* CreateKeyBoard()
	{
		return new MikaKeyBoard;
	}

	Mouse* CreateMouse()
	{
		return new MikaMouse;
	}
};

// 具体Piki工厂
class PikiFactory : public Factory
{
	KeyBoard* CreateKeyBoard()
	{
		return new PikiKeyBoard;
	}

	Mouse* CreateMouse()
	{
		return new PikiMouse;
	}
};

void work(Factory* factory)
{
	KeyBoard* keyboard = factory->CreateKeyBoard();			// 让工厂制造键盘
	Mouse* mouse = factory->CreateMouse();					// 让工厂制造鼠标
	keyboard->show();	// 查看键盘配置信息
	mouse->show();		// 查看鼠标配置信息

	delete factory;
}

int main()
{
	work(new MikaFactory());	// 创建一个Miki工厂,并让它开工
	work(new PikiFactory());	// 创建一个Piki工厂,并让它开工

	system("pause");
	return 0;
}
```



# 建造者模式

```cpp
#include<iostream>
#include<string>
#include<vector>
using namespace std;

/*
	组装电脑：显示器、鼠标、键盘、主机
	1. 找到店铺老板告诉需求
	2. 客服安排技术员工组装
	3. 产品组装完成
*/

// 抽象产品
class AbstractProduct
{
public:
	virtual void SetDisplayer(string displayer) = 0;
	virtual void SetMouse(string mouse) = 0;
	virtual void SetKeyBoard(string keyboard) = 0;
	virtual void SetHost(string host) = 0;
	virtual void Show() = 0;
	virtual ~AbstractProduct() {};
};

// 具体产品（电脑）
class Computer : AbstractProduct
{
	void SetDisplayer(string displayer)
	{
		cout << "正在组装" << displayer << endl;
		m_computer.push_back(displayer);	// 模拟组装显示器的过程
	}

	void SetMouse(string mouse)
	{
		cout << "正在组装" << mouse << endl;
		m_computer.push_back(mouse);		// 模拟组装鼠标的过程
	}

	void SetKeyBoard(string keyboard)
	{
		cout << "正在组装" << keyboard << endl;
		m_computer.push_back(keyboard);		// 模拟组装键盘的过程
	}

	void SetHost(string host)
	{
		cout << "正在组装" << host << endl;
		m_computer.push_back(host);			// 模拟组装主机的过程
	}

	// 查看当前电脑组装了什么
	void Show()
	{
		cout << "电脑配置结果：" << endl;
		for (auto v : m_computer)
		{
			cout << v << endl;
		}
	}

	vector<string> m_computer;		// 模拟空壳电脑
};

// 抽象建造者
class AbstractBuilder
{
public:
	// 构造函数 （默认构造一个建造者时，就随之产生一个空壳产品，并要求建造者组装好这个产品）
	AbstractBuilder()
	{
		this->m_product = (AbstractProduct*) new Computer;
	}

	// 抽象建造者组装零件的动作
	virtual void assembleDisplayer(string displayer) = 0;
	virtual void assembleMouse(string mouse) = 0;
	virtual void assembleKeyBoard(string keyboard) = 0;
	virtual void assembleHost(string host) = 0;

	AbstractProduct* GetProduct()
	{
		return m_product;	// 返回"已组装或未组装"好的产品
	}

	virtual ~AbstractBuilder() {};

protected:
	AbstractProduct* m_product;		// 空壳产品
};

// 具体建造者（技术员工）
class Technician : AbstractBuilder
{
	// 让技术员工组装显示器
	void assembleDisplayer(string displayer)
	{
		this->m_product->SetDisplayer(displayer);
	}

	// 让技术员工组装鼠标
	void assembleMouse(string mouse)
	{
		this->m_product->SetMouse(mouse);
	}

	// 让技术员工组装键盘
	void assembleKeyBoard(string keyboard)
	{
		this->m_product->SetKeyBoard(keyboard);
	}

	// 让技术员工组装主机
	void assembleHost(string host)
	{
		this->m_product->SetHost(host);
	}
};

// 老板
class Boss
{
public:
	// 构造函数（默认构造一个老板时，就要求老板管理一个员工）
	Boss(AbstractBuilder* technician)
	{
		this->m_technician = technician;
	}

	// 让员工组装 -- Let employees assemble
	void letEmployeeAssemble(string displayer, string mouse, string keyBoard, string host)
	{
		// 调用员工的组装功能
		this->m_technician->assembleDisplayer(displayer);
		this->m_technician->assembleMouse(mouse);
		this->m_technician->assembleKeyBoard(keyBoard);
		this->m_technician->assembleHost(host);
	}

private:
	AbstractBuilder* m_technician;	// 技术人员
};

int main()
{
	// 实例一个技术人员，作为员工
	AbstractBuilder* technician = (AbstractBuilder*) new Technician;

	// 实例一个老板，管理上面的技术人员
	Boss* boss = new Boss(technician);

	// 老板让员工组装 "Miki显示器", "PoPo鼠标", "Jinna键盘", "BiGong主机"
	boss->letEmployeeAssemble("Miki显示器", "PoPo鼠标", "Jinna键盘", "BiGong主机");

	// 员工把组装好的(电脑)产品，交出来
	AbstractProduct* computer = technician->GetProduct();

	// 查看组装好以后的电脑的配置
	computer->Show();


	delete computer;
	delete boss;
	delete technician;

	system("pause");
	return 0;
}
```



# 原型模式

```cpp
#include<iostream>
#include<string>
using namespace std;

// 孙悟空七十二变: 变成一颗树


// 原型
class Monkey
{
public:
	virtual Monkey* Clone() = 0;		// 克隆对象
	virtual void Show() = 0;			// 展示对象的内容
	virtual ~Monkey() {};
};

// 变化后
class Tree : Monkey
{
public:
	// 构造函数
	Tree(string name) : m_name(name)	// 列表初始化
	{
		;	// 空语句，仅为了占位
	}

	// 拷贝构造函数
	Tree(const Tree& obj)
	{
		this->m_name = obj.m_name;
	}

	// 展示内容
	void Show()
	{
		cout << m_name << endl;
	}

	// 克隆对象
	Monkey* Clone()
	{
		cout << "克隆成功！" << endl;
		return new Tree(*this);		// 调用拷贝构造函数，返回一个新对象
	}

private:
	string m_name;
};


int main()
{
	Tree* aTree = new Tree("菩提树");
	aTree->Show();

	Tree* bTree = (Tree*)aTree->Clone();
	bTree->Show();

	delete bTree;
	delete aTree;

	system("pause");
	return 0;
}
```

# 总结

**创建型模式总结**

- 单例模式：创建一个全局的对象
- 工厂方法模式：实现单个类的对象的创建
- 抽象工厂模式：实现多个类的对象的创建
- 建造者模式：实现复杂类的对象的创建
- 原型模式：实现自身类的克隆

