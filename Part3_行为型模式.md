# 模板方法模式

```cpp
#include<iostream>
#include<string>
using namespace std;

/*
	案例：写简历
	内容：
		最近有个招聘会，可以带上简历去应聘了。
		但是，其中有一家公司不接受简历，
		而是给应聘者发了一张简历表，上面有基本信息、教育背景、工作经历等栏，
		让应聘者按照要求填写完整。
		每个人拿到这份表格后，就开始填写。
		如果用程序实现这个过程，该如何做呢？
		一种方案就是用模板方法模式：定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。
		模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
*/

// 简历（抽象）
class Resume
{
protected:
	virtual void writeBasicInfo() = 0;
	virtual void writeEducation() = 0;
	virtual void writeWorkExperience() = 0;

public:
	// 模板方法核心
	void FillResume()
	{
		writeBasicInfo();
		writeEducation();
		writeWorkExperience();
	}

	virtual ~Resume() {};	// 析构函数
};

// 简历A（具体）
class ResumeA : public Resume
{
protected:
	void writeBasicInfo()
	{
		cout << "简历A: 基本信息, ";
	}

	void writeEducation()
	{
		cout << "教育背景, ";
	}

	void writeWorkExperience()
	{
		cout << "工作经验 (越详细越好)." << endl;
	}
};


// 简历B（具体）
class ResumeB : public Resume
{
protected:
	void writeBasicInfo()
	{
		cout << "简历B: 基本信息, ";
	}

	void writeEducation()
	{
		cout << "教育背景, ";
	}

	void writeWorkExperience()
	{
		cout << "工作经验 (请简要概况)." << endl;
	}
};


int main()
{
	// 写简历A
	Resume* r1 = new ResumeA;
	r1->FillResume();

	// 写简历B
	Resume* r2 = new ResumeB;
	r2->FillResume();

	delete r1;
	delete r2;

	system("pause");
	return 0;
}
```



# 命令模式

```cpp
#include<iostream>
#include<string>
#include<vector>
using namespace std;

/*
	案例：客人点餐
		1. 客人发出命令，让厨师做饭
		2. 客人发出命令，让厨师取消做饭
		3. 客人发出命令，让厨师煮面
		4. 客人发出命令，让厨师取消煮面
*/

// 厨师（具体）
class Chef
{
public:
	// 做饭
	void makeMeals()
	{
		cout << "正在做饭中..." << endl;
	}

	// 取消做饭
	void cancelMeals()
	{
		cout << "已取消做饭!" << endl;
	}

	// 煮面
	void makeNoodles()
	{
		cout << "正在煮面中..." << endl;
	}

	// 取消煮面
	void cancelNoodles()
	{
		cout << "已取消煮面！" << endl;
	}
};

// 命令（抽象）
class Command
{
protected:
	Chef* chef;		// 用来保存一个厨师

public:
	virtual void executeCommand() = 0;		// 执行命令
	virtual void cancelCommand() = 0;		// 取消命令
	virtual ~Command() {};
};

// 关于做饭的命令（具体）
class AboutMealsCommand : Command
{
public:
	// 构造函数，列表初始化：每构造一条命令，都要对应着一个厨师，让他执行这个命令
	AboutMealsCommand(Chef* c)
	{
		this->chef = c;
	}

	// 执行命令
	void executeCommand()
	{
		chef->makeMeals();			// 让厨师做饭
	}

	// 取消命令
	void cancelCommand()
	{
		chef->cancelMeals();		// 让厨师取消做饭
	}
};

// 关于煮面的命令（具体）
class AboutNoodlesCommand : Command
{
public:
	// 构造函数，列表初始化：每构造一条命令，都要对应着一个厨师，让他执行这个命令
	AboutNoodlesCommand(Chef* c)
	{
		this->chef = c;
	}

	// 执行命令
	void executeCommand()
	{
		chef->makeNoodles();			// 让厨师煮面
	}

	// 取消命令
	void cancelCommand()
	{
		chef->cancelNoodles();		// 让厨师取消煮面
	}
};

// 客人（具体）
class Customer
{
private:
	vector<Command*> commandQueue;		// 命令队列，用来存放这个顾客的一系列命令

public:
	// 添加命令
	void add(Command* command)
	{
		commandQueue.push_back(command);		// 将当前这一条的下单命令添加到命令队列中
		cout << "客人发出了一条命令" << endl;
	}

	// 移除命令
	void remove(Command* command)
	{
		for (auto iter = commandQueue.begin(); iter != commandQueue.end(); iter++)
		{
			// 将当前命令从命令队列中删除
			if ((*iter) == command)
			{
				commandQueue.erase(iter);
				break;
			}
		}
		cout << "客人取消了一条命令" << endl;
	}

	// 确认订单
	void confirm()
	{
		for (auto command : commandQueue)
		{
			command->executeCommand();
		}
		commandQueue.clear();
	}

	// 取消订单
	void cancel()
	{
		for (auto command : commandQueue)
		{
			command->cancelCommand();
		}
		commandQueue.clear();
	}
};


int main()
{
	// 招牌一个厨师
	Chef* chef = new Chef();

	// 制定好跟"做饭"相关的"命令/规则"，具体包括，做饭与取消做饭两个功能
	Command* mealsCommand = (Command*) new AboutMealsCommand(chef);

	// 制定好跟"煮面"相关的"命令/规则"，具体包括，做饭与取消做饭两个功能
	Command* noodlesCommand = (Command*) new AboutNoodlesCommand(chef);

	// 来了一位顾客
	Customer* customer = new Customer();

	// 顾客下达一系列的命令，想要点单
	customer->add(mealsCommand);
	customer->add(noodlesCommand);
	customer->remove(mealsCommand);

	// 顾客确认订单
	customer->confirm();

	// 顾客下达了一系列的命令，想要取消点单
	customer->add(noodlesCommand);

	// 顾客取消了订单
	customer->cancel();
	

	delete customer;
	delete chef;
	delete mealsCommand;
	delete noodlesCommand;

	system("pause");
	return 0;
}
```



# 责任链模式

```cpp
#include<iostream>
#include<string>
using namespace std;

/*
	案例：员工请假
	内容：
		当员工申请请假1天以内，由组长批准即可（处理者为组长）
		当员工申请请假超过3天，需要由经理批准（处理者为经理）
		当员工申请请假超过7天，需要由老板批准（处理者为老板）
*/

// 处理者（抽象）
class Handler
{
protected:
	Handler* nextHanler;	// 维护下一个处理者的指针。万一当前处理者没有权力处理时，就交给下一个处理者进行处理。

public:
	// 构造函数，列表初始化
	Handler() : nextHanler(nullptr) { cout << "初始化成功！" << endl; }

	// 设置下一个处理者是谁（如果不设置，就默认没有关联）
	void setNextHandler(Handler* next)
	{
		this->nextHanler = next;
	}

	// 具体的处理请求
	virtual void handleRequest(int days) = 0;
	virtual ~Handler() {};
};

// 组长（具体）
class GroupLeader : public Handler
{
public:
	void handleRequest(int days)
	{
		cout << "组长回复：";
		if (days <= 1)
		{
			cout << "同意请假！" << endl;
		}
		else
		{
			cout << "请假太久了，你去找经理请假。" << endl;
			if (this->nextHanler != nullptr) nextHanler->handleRequest(days);
		}
	}
};

// 经理（具体）
class Manager : public Handler
{
public:
	void handleRequest(int days)
	{
		cout << "经理回复：";
		if (days <= 3)
		{
			cout << "同意请假！" << endl;
		}
		else
		{
			cout << "请假太久了，你去找老板请假。" << endl;
			if (this->nextHanler != nullptr) nextHanler->handleRequest(days);
		}
	}
};

// 老板（具体）
class Boss : public Handler
{
public:
	void handleRequest(int days)
	{
		cout << "老板回复：";
		if (days <= 7)
		{
			cout << "同意请假！" << endl;
		}
		else
		{
			cout << "请假太久了，不行！" << endl;
			if (this->nextHanler != nullptr) nextHanler->handleRequest(days);
		}
	}
};

int main()
{
	// 实例化一个组长、一个经理、一个老板
	Handler* groupLeader = new GroupLeader;
	Handler* manager = new Manager;
	Handler* boss = new Boss;

	// 组装链
	groupLeader->setNextHandler(manager);
	manager->setNextHandler(boss);


	// 请假
	int days;
	// ------------------
	days = 1;
	cout << "想要请假" << days << "天" << endl;
	groupLeader->handleRequest(days);
	// ------------------
	days = 3;
	cout << "想要请假" << days << "天" << endl;
	groupLeader->handleRequest(days);
	// ------------------
	days = 7;
	cout << "想要请假" << days << "天" << endl;
	groupLeader->handleRequest(days);
	// ------------------
	days = 30;
	cout << "想要请假" << days << "天" << endl;
	groupLeader->handleRequest(days);


	delete groupLeader;
	delete manager;
	delete boss;

	system("pause");
	return 0;
}
```



# 策略模式

```cpp
#include<iostream>
#include<string>
using namespace std;

// 策略（抽象）
class Strategy
{
public:
	virtual int execute(int left, int right) = 0;
};

// 加法策略（具体）
class Add : public Strategy
{
public:
	int execute(int left, int right)
	{
		return left + right;
	}
};

// 减法策略（具体）
class Sub : public Strategy
{
public:
	int execute(int left, int right)
	{
		return left - right;
	}
};

// 乘法策略（具体）
class Mul : public Strategy
{
public:
	int execute(int left, int right)
	{
		return left * right;
	}
};

// 除法策略（具体）
class Div : public Strategy
{
public:
	int execute(int left, int right)
	{
		if (right == 0)
		{
			cout << "除数不能为零！" << endl;
			return 0;
		}
		return left / right;
	}
};

// 策略容器（具体）
class Container
{
private:
	Strategy* strategy;		// 维护一个策略

public:
	// 设置策略
	void setStrategy(Strategy* s)
	{
		this->strategy = s;
	}

	// 执行某策略的功能
	int executeStrategy(int left, int right)
	{
		return strategy->execute(left, right);
	}
};


int main()
{
	// 实例化一个策略容器
	Container* container = new Container;

	int left, right;
	char symbol;
	Strategy* strategy = nullptr;
	while (true)
	{
		cout << "(Count) >>> ";

		// 获取用户输入
		cin >> left >> symbol >> right;

		// 根据用户选择，向策略容器里面添加合适的策略
		switch (symbol)
		{
		case '+':
			strategy = new Add;
			container->setStrategy(strategy);
			break;
		case '-':
			strategy = new Sub;
			container->setStrategy(strategy);
			break;
		case '*':
			strategy = new Mul;
			container->setStrategy(strategy);
			break;
		case '/':
			strategy = new Div;
			container->setStrategy(strategy);
			break;
		}
		
		// 执行策略容器里面的策略
		cout << container->executeStrategy(left, right) << endl;
		delete strategy;
	}


	system("pause");
	return 0;
}
```

# 观察者模式

```cpp
#include<iostream>
#include<string>
#include<vector>
using namespace std;

/*
	案例：员工摸鱼————通过观察老板是否出现，员工做出不同的反应（摸鱼或努力工作）
*/

// 声明一个老板类
class Boss;

// 实现一个员工类（具体）
class Employee
{
private:
	string m_name;		// 维护员工自己的姓名

public:
	// 构造函数，初始化列表
	Employee(string name) : m_name(name) {};

	// 更新"老板是否来了"的动作消息，做出相应的反应
	void updateInfomation(string info)
	{
		cout << m_name << "收到情报：" << info << endl;
		if (info == "老板来了")
		{
			cout << "--> 开启工作模式" << endl;
		}
		else if (info == "老板走了")
		{
			cout << "--> 开启摸鱼模式" << endl;
		}
	}
};

// 实现一个老板类（具体）
class Boss
{
private:
	string information;							// 保存一个老板的动作消息
	vector<Employee*> employeeContainer;		// 员工容器，用来存放老板手下的所有员工

public:
	// 添加员工
	void addEmployee(Employee* employee)
	{
		this->employeeContainer.push_back(employee);
	}

	// 老板设置自己的动作消息
	void setActionInfo(string info)
	{
		this->information = info;		// 老板设置自己的动作消息
		notify();						// 发出通知（老板更新了自己的动作）
	}

	// 发出通知
	void notify()
	{
		for (auto v : employeeContainer)
		{
			v->updateInfomation(this->information);		// 更新员工的消息，从而实现监控(观测)老板
		}
	}
};

int main()
{
	// 实例化一个老板（被观察者）
	Boss* boss = new Boss;

	// 实例化一些员工（观察者）
	Employee* emp1 = new Employee("小明");
	Employee* emp2 = new Employee("老张");
	Employee* emp3 = new Employee("老李");

	// 让老板去招聘上面这些员工（建立关联）
	boss->addEmployee(emp1);
	boss->addEmployee(emp2);
	boss->addEmployee(emp3);

	// 老板设置动作消息
	// 当老板设置动作消息成功后，将会自动发出通知，更新员工的消息，员工根据消息内容选择是否摸鱼
	boss->setActionInfo("老板来了");
	boss->setActionInfo("老板走了");


	delete boss;
	delete emp1;
	delete emp2;
	delete emp3;

	system("pause");
	return 0;
}
```



# 访问者模式

```cpp
#include<iostream>
#include<string>
using namespace std;

/*
	案例：
		这里实现一个不同职业的人去医院和餐厅的例子来说明访问者模式
		在小镇上有一个医院和一个餐厅，
		每天都会有不同的人访问这两个地方，
		由于访问者不同到这两个地方要做的事也有区别。
		医生去医院是为了工作给病人看病，
		厨师去医院是为了检查身体，
		医生去餐厅是为了吃饭，
		厨师去餐厅是为了工作给客人烹饪菜肴。
*/

// 声明"地方类"
class Place;

// 访问者（抽象访问者）
class Visitor
{
public:
	virtual void visitHospital(Place* place) = 0;
	virtual void visitResteraunt(Place* place) = 0;
	virtual ~Visitor() {};
};

// 地方（抽象地方）
class Place
{
protected:
	string placeName;		// 保存地方的名字

public:
	string getName()		// 获得地方的名字
	{
		return this->placeName;
	}

	// 让这个地方接收访问者
	virtual void accept(Visitor* visitor) = 0;
	virtual ~Place() {}
};

// 医院（具体地方）
class Hospital : public Place
{
public:
	Hospital(string name)
	{
		this->placeName = name;
	}

	// 接受访问者的访问，访问者将访问医院内部
	void accept(Visitor* visitor)
	{
		visitor->visitHospital(this);		// 访问者访问医院内部
	}
};

// 餐馆（具体地方）
class Resteraunt : public Place
{
public:
	Resteraunt(string name)
	{
		this->placeName = name;
	}

	// 接受访问者的访问，访问者将访问餐馆内部
	void accept(Visitor* visitor)
	{
		visitor->visitResteraunt(this);		// 访问者访问餐馆内部
	}
};

// 医生（具体访问者）
class Doctor : public Visitor
{
public:
	void visitHospital(Place* place)
	{
		cout << "医生访问" << place->getName() << "是为了治疗病人" << endl;
	}

	void visitResteraunt(Place* place)
	{
		cout << "医生访问" << place->getName() << "是为了吃一顿饭" << endl;
	}
};

// 厨师（具体访问者）
class Chef : public Visitor
{
public:
	void visitHospital(Place* place)
	{
		cout << "厨师访问" << place->getName() << "是为了治病" << endl;
	}

	void visitResteraunt(Place* place)
	{
		cout << "厨师访问" << place->getName() << "是为了做菜" << endl;
	}
};

int main()
{
	// 实例化一个医院
	Place* hospital = new Hospital("丽江市第一人名医院");

	// 实例化一个餐馆
	Place* resteraunt = new Resteraunt("幸福餐馆");

	// 实例化一个医生（访问者）
	Visitor* doctor = new Doctor();

	// 实例化一个厨师（访问者）
	Visitor* chef = new Chef();

	// 医生访问医院（医院接收医生的拜访）
	hospital->accept(doctor);

	// 医生访问餐馆（餐馆接收医生的拜访）
	resteraunt->accept(doctor);

	// 厨师访问医院（医院接收厨师的拜访）
	hospital->accept(chef);

	// 厨师访问餐馆（餐馆接收厨师的拜访）
	resteraunt->accept(chef);


	delete hospital;
	delete resteraunt;
	delete doctor;
	delete chef;

	system("pause");
	return 0;
}
```



# 中介者模式

```cpp
#include<iostream>
#include<string>
using namespace std;

class Role;		// 声明角色

// 中介者（抽象中介者）
class Mediator
{
protected:
	Role* hr;
	Role* student;

public:
	void set_HR(Role* hr)
	{
		this->hr = hr;
	}

	void set_Student(Role* student)
	{
		this->student = student;
	}

	virtual void match() = 0;
};

// 角色（抽象角色）
class Role
{
protected:
	string name;			// 角色名字
	string offer;			// offer的岗位或内容
	Mediator* mediator;		// 保存一个中介，该角色需要求助"中介"

public:
	string getName()
	{
		return this->name;
	}

	string getOffer()
	{
		return this->offer;
	}

	// 进行配对
	virtual void match(Role* role) = 0;
};

// 学生（具体角色）
class Student : public Role
{
public:
	Student(string name, string offer, Mediator* mediator)
	{
		this->name = name;
		this->offer = offer;
		this->mediator = mediator;
	}

	void match(Role* role)
	{
		mediator->set_HR(role);
		mediator->set_Student(this);
		mediator->match();
	}
};

// 人事HR（具体角色）
class HR : public Role
{
public:
	HR(string name, string offer, Mediator* mediator)
	{
		this->name = name;
		this->offer = offer;
		this->mediator = mediator;
	}

	void match(Role* role)
	{
		mediator->set_HR(this);
		mediator->set_Student(role);
		mediator->match();
	}
};

// 猎聘App（具体中介者）
class LiepinApp : public Mediator
{
public:
	void match()
	{
		cout << "------------------" << endl;
		cout << hr->getName() << "提供岗位：" << hr->getOffer() << endl;
		cout << student->getName() << "需求职位：" << student->getOffer() << endl;
		if (hr->getOffer() == student->getOffer())
		{
			cout << "配对成功！" << endl;
		}
		else
		{
			cout << "配对失败！" << endl;
		}
		cout << "------------------" << endl;
	}
};

int main()
{
	// 实例化 中介 ———— 猎聘App
	Mediator* app1 = new LiepinApp;

	// 实例化 人事HR
	Role* hr = new HR("花儿姐", "软件工程师", app1);		// 使用 app1
	app1->set_HR(hr);		// 把自己的招聘信息挂到网上

	// 实例化 学生
	Role* student = new Student("小明", "机械工程师", app1);	// 使用 app1
	app1->set_Student(student);		// 把自己的求职信息挂到网上

	// 让 app1 进行匹配 
	app1->match();


	delete app1;
	delete hr;
	delete student;

	system("pause");
	return 0;
}
```



# 备忘录模式

```cpp
#include<iostream>
#include<string>
using namespace std;

/*
	备忘录模式：
		在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。
		这样以后就可将该对象恢复到原先保存的状态。

	案例：
		比如我们打游戏时分，如果在打大BOSS之前存档，此时就需要将对应的游戏场景，任务信息，人物信息等等状态存储起来；
		当赢得大BOSS之后，覆盖之前的存档时，就将之前的存档丢弃，新建立一个存档，保存当前的状态信息；
		如果打输了，恢复存档，就将之前的存档信息读取出来，还原到打大BOSS之前的游戏场景，重新开始打大BOSS。
		这里面就是使用的备忘录模式。
		一个备忘录是一个对象，它存储另一个对象在某个瞬间的内部状态，而后者称为备忘录的原发器。
		当需要设置原发器的检查点时，取消操作机制会向原发器请求一个备忘录。
		原发器用描述当前状态的信息初始化该备忘录。
		只有原发器可以向备忘录中存取信息，备忘录中的信息对其他的对象是“不可见”的。
*/


// 备忘录
class Memorandum
{
private:
	int blood;		// 生命力
	int attack;		// 攻击力
	int defense;	// 防御力

public:
	Memorandum(int blood, int attack, int defense)
	{
		this->blood;
		this->attack;
		this->defense;
	}

	int getBlood() { return this->blood; }
	int getAttack() { return this->attack; }
	int getDefense() { return this->defense; }
};

// 原发器：记录当前时刻的内部状态
class GameRole
{
private:
	int blood;		// 生命力
	int attack;		// 攻击力
	int defense;	// 防御力

public:
	// 创建游戏角色时，默认的血量
	GameRole()
	{
		this->blood = 100;
		this->attack = 100;
		this->defense = 100;
	}

	// 展示游戏角色的状态
	void showState()
	{
		cout << "当前角色："
			<< "[生命力：" << this->blood
			<< "] [攻击力：" << this->attack
			<< "] [防御力：" << this->defense << "]" << endl;
	}

	// 让游戏角色进行战斗
	void fight()
	{
		this->blood -= 40;
		this->attack -= 16;
		this->defense -= 32;
		cout << "遭受敌人攻击！生命力-40，攻击力-16，防御力-32..." << endl;

		if (blood <= 0)
		{
			cout << "您已阵亡" << endl;
		}
	}

	// 存档：保存当前的数据到备忘录中
	Memorandum* saveState()
	{
		cout << "存档成功！" << endl;
		return new Memorandum(this->blood, this->attack, this->defense);
	}

	// 恢复存档：从备忘录里面获取所需的数据
	void recoveryState(Memorandum* m)
	{
		this->blood = m->getBlood();
		this->attack = m->getAttack();
		this->defense = m->getDefense();
		cout << "恢复存档成功！" << endl;
	}
};


int main()
{
	// 创建一个游戏角色
	GameRole* role = new GameRole();

	// 查看当前"游戏角色"的状态
	role->showState();

	// 玩游戏（去战斗）
	role->fight();

	// 存档
	Memorandum* record = role->saveState();

	// 继续玩游戏（去战斗）
	role->fight();
	role->fight();

	// 挑战Boss失败，就读档，恢复原来的状态
	role->recoveryState(record);

	// 继续玩游戏（去战斗）
	role->fight();


	delete role;
	delete record;

	system("pause");
	return 0;
}
```



# 状态模式

```cpp
#include<iostream>
#include<string>
#include<memory>
using namespace std;

// 上下文类
class Context;		// 声明

// 状态类（抽象状态类）
class AbstractState
{
public:
	virtual void handle(Context* p) = 0;
};

// "None"状态类（具体状态类）
class NoneState : public AbstractState
{
	void handle(Context* p)
	{
		cout << "没有上下文...";
	}
};

// "Exist"状态类（具体状态类）
class ExistState : public AbstractState
{
	void handle(Context* p)
	{
		cout << "存在上下文...";
	}
};

// 上下文类
class Context	// 实现
{
private:
	AbstractState* state;

public:
	Context(AbstractState* state) : state(state) {}

	void request()
	{
		cout << "内容：{";
		if (state)
		{
			state->handle(this);
		}
		cout << "}" << endl;
	}

	void changeState(AbstractState* newState)
	{
		state = newState;
	}
};

int main()
{
	// 实例化两种状态
	unique_ptr<AbstractState> noneState = make_unique<NoneState>();
	unique_ptr<AbstractState> existState = make_unique<ExistState>();

	// 实例化一个上下文
	unique_ptr<Context> context = make_unique<Context>(nullptr);

	// 请求内容
	context->request();

	// 切换为 noneState 状态后，再次请求内容
	context->changeState(noneState.get());
	context->request();

	// 切换为 existState 状态后，再次请求内容
	context->changeState(existState.get());
	context->request();

	system("pause");
	return 0;
}
```



# 迭代器模式

```cpp
#include<iostream>
#include<string>
#include<memory>
#include<vector>

using namespace std;


// 声明
template<class U>
class Iterator;


// 自定义 vector 容器
template<class T>
class SimpleVector
{
	friend class Iterator<T>;

protected:
	vector<T> vector;		// 创建一个容器

public:
	// 添加数据
	void add(T data)
	{
		this->vector.push_back(data);
	}

	// 删除数据
	void remove()
	{
		this->vector.pop_back();
	}

	// 创建该容器的迭代器
	unique_ptr<Iterator<T>> createIterator()
	{
		return make_unique<Iterator<T>>(this);		// 通过智能指针创建一个迭代器对象
	}
};


// 迭代器类
template<class T>
class Iterator
{
protected:
	SimpleVector<T>* container;		// 保存一个需要迭代的对象————"容器对象"
	typedef typename vector<T>::iterator iter_type;		// typename vector<T>::iterator -> 声明是一种类型。而 typedef ... iter_type 则是取别名
	iter_type iter;

public:
	// 创建迭代器
	Iterator(SimpleVector<T>* simpleVector)
	{
		this->container = simpleVector;
		this->iter = simpleVector->vector.begin();
	}

	// 获取当前所迭代位置的内容
	T get()
	{
		return *(this->iter);
	}


	// 迭代到下一位
	void next()
	{
		this->iter++;
	}

	// 判断是否已经迭代到尾部
	bool reachTheEnd()
	{
		return (this->iter == this->container->vector.end());		// 因为 vector 是私有属性，使用它需要借助友元
	}
};


int main()
{
	// 实例化一个容器
	SimpleVector<int> v;

	// 向容器里面添加数据
	for (int i = 1; i < 10; i++)
	{
		v.add(i * 100);
	}

	// 创造一个迭代器
	unique_ptr<Iterator<int>> iterator = v.createIterator();		// 可简写为: auto iterator = v.createIterator();


	// 使用迭代器读取容器里面的数据
	while (!iterator->reachTheEnd())
	{
		cout << iterator->get() << endl;	// 获取当前迭代的内容
		iterator->next();					// 迭代下一个
	}

	system("pause");
	return 0;
}
```



# 解释器模式

```cpp
#include<iostream>
#include<string>
#include<map>
#include<stack>

using namespace std;

/*
	实现原理：
		原材料：
			1.表达式(计算规则)：	a@b#c	（需要解释成a+b-c的运算过程和结果）
			2.数据对应关系(变量)： map<string, int> mp = {
				{"a", 11},
				{"b", 22},
				{"c", 33}
			}

		产品：
			a@b#c ==》 变量解释器1 @ 变量解释器2 # 变量解释器3
				  ==》 栈 【 => 变量解释器1 => 变量解释器2 => 变量解释器3 => 】 与 【=> @符号对象 => #符号对象】
*/

// 解释器（抽象解释器）
class Interpreter
{
public:
	virtual int parse(map<string, int>& var) = 0;	// 分析
};


// 变量解释器（具体解释器）
// 一个变量解释器对象只能解释一个变量
class VarInterpreter : public Interpreter
{
private:
	string key;		// 记录要被解释的字符串

public:
	VarInterpreter(string key) : key(key) {};		// 要解释哪个字符串

	// 解释变量，获得变量的值
	int parse(map<string, int>& var)				// 对这个字符串进行解释，返回解释后的结果
	{
		return var[key];
	}
};


// 运算符解释器
// 抽象父类
class SymbolInterpreter : public Interpreter
{
protected:
	Interpreter* left;		// 用来记录"变量解释器对象"
	Interpreter* right;		// 用来记录"变量解释器对象"

public:
	// 构造函数，初始化列表
	SymbolInterpreter(Interpreter* left, Interpreter* right) : left(left), right(right) {};

	// 提供接口，让外界可以访问
	Interpreter* getLeft() { return left; }
	Interpreter* getRight() { return right; }
};


// 加法解释器（具体解释器）
class AddInterpreter : SymbolInterpreter
{
public:
	// 子类构造函数，调用父类构造函数
	AddInterpreter(Interpreter* left, Interpreter* right) : SymbolInterpreter(left, right) {};

	// 解释加法（解释减法 = 解释左变量获得 + 解释右变量获得）
	int parse(map<string, int>& var)
	{
		int res = left->parse(var) + right->parse(var);
		return res;
	}
};

// 减法解释器（具体解释器）
class SubInterpreter : public SymbolInterpreter
{
public:
	// 子类构造函数，调用父类构造函数
	SubInterpreter(Interpreter* left, Interpreter* right) : SymbolInterpreter(left, right) {};

	// 解释减法（解释减法 = 解释左变量获得 - 解释右变量获得）
	int parse(map<string, int>& var)
	{
		int res = left->parse(var) - right->parse(var);
		return res;
	}
};


// 封装一个解析者类，统一调用上面的接口
class Parser
{
private:
	Interpreter* finalInterpreter;		// 最终解释器对象（最终要解释的）
	stack<Interpreter*> stk;			// 用来装"解释器对象"指针的栈

public:
	// 构造函数，需要传入"规则字符串"，通过分析，形成最终解释器
	Parser(string str)
	{
		// 准备
		Interpreter* left = nullptr;		// 准备临时解释器对象
		Interpreter* right = nullptr;		// 准备临时解释器对象
		this->finalInterpreter = nullptr;	// 最终要用的解释器对象

		// 解析字符串 a@b#c
		for (int i = 0; i < str.length(); i++)
		{
			switch (str[i])
			{
			case '@':		// 匹配到+这个符号，执行加法解释

				// 从栈顶中读取解释器对象
				left = stk.top();

				// 从文法字符串中，构建解释器对象，并将其推入栈中
				right = new VarInterpreter(str.substr(++i, 1));		// 注意！这里的 ++i 起到了两个作用。作用一：字符串提取；作用二：跳过下一字符的匹配，即：跳过下一次循环
				stk.push(right);

				// 传入解释器对象，构建新的解释器对象，将其存入栈中
				stk.push((Interpreter*) new AddInterpreter(left, right));

				break;

			case '#':		// 匹配到-这个符号，执行减法解释

				// 从栈中取出解释器对象
				left = stk.top();

				// 从文法字符串中，构建解释器对象，并将其推入栈中
				right = new VarInterpreter(str.substr(++i, 1));		// 作用：同上
				stk.push(right);

				// 传入解释器对象，构建新的解释器对象，将其存入栈中
				stk.push((Interpreter*) new SubInterpreter(left, right));

				break;

			default:		// 匹配到其他符号，执行变量解释

				stk.push(new VarInterpreter(str.substr(i, 1)));

				break;
			}
		}

		// 如果栈不为空，则执行里面的代码
		if (!stk.empty())
		{
			this->finalInterpreter = stk.top();		// 栈顶中保存的就是最终语法树的根
		}
	}

	// 运行计算器
	int run(map<string, int>& var)
	{
		// 下面 finalInterpreter->parse(var) 的调用过程类似于递归，可以自己画图分析
		int res = (this->finalInterpreter == nullptr) ? -1: finalInterpreter->parse(var);

		// 释放栈里面指针所指向的对象（即：释放上面代码中new出来的对象）
		while (!stk.empty())
		{
			delete stk.top();	// 删除指针指向的对象
			stk.pop();			// 从栈顶中移除该指针
		}

		return res;
	}
};


int main()
{
	// 要解析的内容
	string str = "a@b#c";

	// 符号与数据之间的映射关系————变量————法则
	map<string, int> var = {
		{"a", 30},
		{"b", 20},
		{"c", 25}
	};

	// 实例化一个解析者
	Parser p(str);

	// 进行计算
	cout << p.run(var) << endl;		// 30@20#25 == 30+20-25 == 25


	system("pause");
	return 0;
}
```

