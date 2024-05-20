# 代理模式

```cpp
#include<iostream>
#include<string>
using namespace std;

// 抽象（某个东西买票）
class AbstractSubject
{
public:
	virtual void BuyTicket() = 0;
    virtual ~AbstractSubject() {};
};

// 具体（男人直接去买票）
class Man :public AbstractSubject
{
public:
	void BuyTicket()
	{
		cout << "Man 购票成功！" << endl;
	}
};

// 具体（女人直接去买票）
class Woman :public AbstractSubject
{
public:
	void BuyTicket()
	{
		cout << "Woman 购票成功！" << endl;
	}
};

// 具体（通过美团去代理买票）
class Meituan : AbstractSubject
{
public:
	Meituan(AbstractSubject* obj)
	{
		this->meituanUser = obj;		// 接收要被代理的对象
	}

	// 执行"代理买票"的任务
	void BuyTicket()
	{
		cout << "正在通过美团进行购票..." << endl;
		meituanUser->BuyTicket();
	}

private:
	AbstractSubject* meituanUser;		// 美团使用者
};


int main()
{
	AbstractSubject* man = new Man;		// 实例一个男人出来
	man->BuyTicket();					// 让这个男人直接去买票

	AbstractSubject* woman = new Woman;	// 实例一个女人出来
	woman->BuyTicket();					// 让这个女人直接去买票

	// 实例一个美团对象，并且上面的男人使用美团进行代理买票
	AbstractSubject* meituanOfMan = (AbstractSubject*) new Meituan(man);
	meituanOfMan->BuyTicket();

	// 实例一个美团对象，并且上面的女人使用美团进行代理买票
	AbstractSubject* meituanOfWoman = (AbstractSubject*) new Meituan(woman);
	meituanOfWoman->BuyTicket();


	delete meituanOfWoman;
	delete meituanOfMan;
	delete woman;
	delete man;

	system("pause");
	return 0;
}
```



# 装饰者模式

```cpp
#include<iostream>
#include<string>
using namespace std;

// 人物（抽象）
class People
{
public:
	string wearingState;		// 记录人物的穿着状态
	virtual void show() = 0;	// 展示人物的穿着状态
	virtual ~People() {};
};

// 女孩（具体）
class Girl : public People
{
public:
	// 实例化一个女孩时，初始化她的穿着状态
	Girl()
	{
		this->wearingState = "女孩穿着，长裤睡衣，长袖睡衣";
	}

	// 展示女孩的穿着
	void show()
	{
		cout << this->wearingState << endl;
	}
};

// 男孩（具体）
class Boy : People
{
public:
	// 实例化一个男孩时，初始化他的穿着状态
	Boy()
	{
		this->wearingState = "男孩穿着，短裤睡衣，短袖睡衣";
	}

	// 展示男孩的穿着
	void show()
	{
		cout << this->wearingState << endl;
	}
};

// 换装（抽象）
class ChangingClothes
{
public:
	string wearingState;				// 记录换装后的穿着状态
	virtual void show() = 0;			// 展示换装后的穿着状态
	virtual ~ChangingClothes() {};

protected:
	People* people;		// 每个换装对象，对应(维护)一个具体的"男孩或女孩"(人)对象
};

// 换成校服
class ChangeIntoSchoolUniform : public ChangingClothes
{
public:
	// 实例化一个换校服对象时，更新“男孩或女孩”的穿着状态
	ChangeIntoSchoolUniform(People* people)
	{
		this->people = people;
		this->wearingState = this->people->wearingState + ", 但是最后换成了‘校服’";
	}

	// 展示最新的穿着状态
	void show()
	{
		cout << this->wearingState << endl;
	}
};

// 换成运动装
class ChangeIntoSportswear : public ChangingClothes
{
public:
	// 实例化一个换成运动装对象时，更新“男孩或女孩”的穿着状态
	ChangeIntoSportswear(People* people)
	{
		this->people = people;
		this->wearingState = this->people->wearingState + ", 但是最后换成了‘运动装’";
	}

	// 展示最新的穿着状态
	void show()
	{
		cout << this->wearingState << endl;
	}
};

int main()
{
	// 实例化一个男生，并展示他的穿着状态
	People* boy = (People*) new Boy;
	boy->show();

	// 实例化一个女生，并展示她的穿着状态
	People* girl = (People*) new Girl;
	girl->show();

	// -------------------- 装饰 --------------------

	// 让男生穿上运动装，并再次展示他的穿着状态
	ChangingClothes* newBoy = new ChangeIntoSportswear(boy);
	newBoy->show();

	// 让女生穿上校服，并再次展示她的穿着状态
	ChangingClothes* newGirl = new ChangeIntoSchoolUniform(girl);
	newGirl->show();


	delete boy;
	delete girl;
	delete newBoy;
	delete newGirl;

	system("pause");
	return 0;
}
```



# 适配器模式

## 对象适配器

```cpp
#include<iostream>
#include<string>
using namespace std;

// 电源插座案例：三插头和两插头的适配
// 用户本身只有三插头
// 用户现在需要将三插头转换为两插头

// 三插头（具体）
class ThreePlug
{
public:
	void threePlugCharge()		// 执行充电
	{
		cout << "开始充电..." << endl;
	}
};

// 两插头（抽象）
class AbstractTwoPlug
{
public:
	virtual void twoPlugCharge() = 0;
	virtual ~AbstractTwoPlug() {};
};

// 两插头（具体）
class TwoPlug
{
public:
	void twoPlugCharge()		// 执行充电
	{
		cout << "开始充电..." << endl;
	}
};

// 插头转换器（继承了两插头的功能）
class PlugConverter : public AbstractTwoPlug
{
public:
	// 实例化一个插头转换器的时候，请将三插头传入进来，方便后面将它转换为两插头
	PlugConverter(ThreePlug* threePlug)
	{
		this->threePlug = threePlug;
	}

	// 在三插头上安装转换器，使它变成两插头
	void installTheConverter()
	{
		cout << "在三插头上安装转换器成功！现在已经变为两插头！" << endl;
	}

	void twoPlugCharge()		// 执行充电
	{
		// 1. 给三插头安装转换器
		this->installTheConverter();

		// 2. 然后让三插头执行充电
		this->threePlug->threePlugCharge();
	}

protected:
	ThreePlug* threePlug;		// 维护一个三插头
};

int main()
{
	// 实例化一个三插头
	ThreePlug* threePlug = new ThreePlug;

	// 先尝试一下直接充电
	threePlug->threePlugCharge();

	// 在三插头上面安装一个转换器，将其转成两插头（即：实例化一个插头转换器）
	PlugConverter* twoPlug = new PlugConverter(threePlug);

	// 对它进行充电
	twoPlug->twoPlugCharge();


	delete threePlug;
	delete twoPlug;

	system("pause");
	return 0;
}
```

## 类适配器

```cpp
#include<iostream>
#include<string>
using namespace std;

// 电源插座案例：三插头和两插头的适配
// 用户本身只有三插头
// 用户现在需要将三插头转换为两插头

// 三插头（具体）
class ThreePlug
{
public:
	void threePlugCharge()		// 执行充电
	{
		cout << "开始充电..." << endl;
	}
};

// 两插头（抽象）
class AbstractTwoPlug
{
public:
	virtual void twoPlugCharge() = 0;
	virtual ~AbstractTwoPlug() {};
};

// 两插头（具体）
class TwoPlug
{
public:
	void twoPlugCharge()		// 执行充电
	{
		cout << "开始充电..." << endl;
	}
};

// 插头转换器【适配器】（继承了两插头和三插头的功能）
class PlugConverter : public AbstractTwoPlug, public ThreePlug
{
public:
	void twoPlugCharge()		// 执行充电
	{
		// 1. 先给三插头插上转换器【适配器】
		this->installTheConverter();

		// 2. 然后让三插头执行充电
		threePlugCharge();
	}

	// 在三插头上安装转换器，使它变成两插头
	void installTheConverter()
	{
		cout << "在三插头上安装转换器成功！现在已经变为两插头！" << endl;
	}
};

int main()
{
	// 实例化一个三插头
	ThreePlug* threePlug = new ThreePlug;

	// 先尝试一下直接充电
	threePlug->threePlugCharge();

	// 实例化一个插头转换器，它内部自带三插头，但是对外显示两插头
	PlugConverter* twoPlug = new PlugConverter;

	// 对它进行充电
	twoPlug->twoPlugCharge();


	delete threePlug;
	delete twoPlug;

	system("pause");
	return 0;
}
```



# 桥接模式

```cpp
#include<iostream>
#include<string>
using namespace std;

// 案例：颜色和形状搭配
//		1. 颜色有两个：红色、蓝色
//		2. 形状有两个：圆形、矩形
//	请根据以上条件，搭配出 "红色圆形"、"蓝色圆形"、"红色矩形"、"蓝色矩形"

// 颜色（抽象）
class Color
{
public:
	virtual void fillColor() = 0;
	virtual ~Color() {};
};

// 红色（具体）
class Red : public Color
{
public:
	Red() : colorType("Red") {}		// 初始化颜色
	void fillColor()
	{
		cout << "填充颜色:" << colorType << endl;
	}

private:
	string colorType;		// 记录颜色
};

// 蓝色（具体）
class Blue : public Color
{
public:
	Blue() : colorType("Blue") {}		// 初始化颜色
	void fillColor()
	{
		cout << "填充颜色:" << colorType << endl;
	}

private:
	string colorType;		// 记录颜色
};

// 形状（抽象）
class Shape
{
public:
	virtual void setColor(Color*) = 0;			// 选择红色类？还是选择蓝色类？
	virtual void generateShape() = 0;			// 生成形状 == 画出形状 + 填充颜色
	virtual ~Shape() {};

protected:
	virtual void drawShape() = 0;
	virtual void fillColor() = 0;
	Color* color;		// 记录这个"形状"所维护的"颜色对象"
};

// 圆形（具体）
class Circle : public Shape
{
public:
	Circle()
	{
		this->shapeType = "Circle";		// 初始化形状
	}

	// 选择哪个颜色的类？
	void setColor(Color* color)
	{
		this->color = color;
	}

	// 生成形状
	void generateShape()
	{
		// 1. 画出形状
		this->drawShape();

		// 2. 填充颜色
		this->fillColor();
	}

private:
	// 画出形状
	void drawShape()
	{
		cout << "绘制：" << shapeType << endl;
	}

	// 填充颜色
	void fillColor()
	{
		this->color->fillColor();		// 调用该颜色类的对象的填充颜色功能
	}

	string shapeType;	// 记录形状
};

// 矩形（具体）
class Rectangle: public Shape
{
public:
	Rectangle()
	{
		this->shapeType = "Rectangle";		// 初始化形状
	}

	// 选择哪个颜色的类？
	void setColor(Color* color)
	{
		this->color = color;
	}

	// 生成形状
	void generateShape()
	{
		// 1. 画出形状
		this->drawShape();

		// 2. 填充颜色
		this->fillColor();
	}

private:
	// 画出形状
	void drawShape()
	{
		cout << "绘制：" << shapeType << endl;
	}

	// 填充颜色
	void fillColor()
	{
		this->color->fillColor();		// 调用该颜色类的对象的填充颜色功能
	}

	string shapeType;	// 记录形状
};

int main()
{
	// ------------------------------------

	// 实例化红色
	Color* red = new Red;
	
	// 实例化蓝色
	Color* blue = new Blue;

	// 实例化圆形
	Shape* circle = new Circle;
	
	// 实例化矩形
	Shape* rectangle = new Rectangle;

	// ------------------------------------

	// 圆形设置为红色，生成红色圆形
	circle->setColor(red);
	circle->generateShape();

	// 矩形设置为蓝色，生成蓝色矩形
	rectangle->setColor(blue);
	rectangle->generateShape();

	
	delete red;
	delete blue;
	delete circle;
	delete rectangle;

	system("pause");
	return 0;
}
```

# 外观模式

```cpp
#include<iostream>
#include<string>
using namespace std;

/*
	编译过程是比较复杂的，通常包含以下内容：
		- 编译器在后台进行语法分析
		- 生成中间代码	（预处理）
		- 生成汇编代码	（编译）
		- 生成机器码		（汇编）
		- 生成可执行程序	（链接）

	虽然如此，现实中 IDE(集成开发环境) 通常能够给我们提供一键编译的方法，方便了我们的开发
	下面，我们将模拟这个过程
*/

// 语法分析
class CppSyntaxAnalyzer
{
public:
	void syntaxParser()
	{
		cout << "分析语法中..." << endl;
	}
};

// 中间代码
class CppMidCode
{
public:
	void generateMidCode()
	{
		cout << "生成中间代码中..." << endl;
	}

};

// 汇编代码
class CppAssemblyCode
{
public:
	void generateAssemblyCode()
	{
		cout << "生成汇编代码中..." << endl;
	}
};

// 机器码
class CppBinaryCode
{
public:
	void generateBinaryCode()
	{
		cout << "生成机器码中..." << endl;
	}
};

// 生成可执行程序
class CppLink
{
public:
	void generateProgram()
	{
		cout << "生成可执行程序中..." << endl;
	}
};

// IDE（集成开发环境）
class IDE
{
public:
	void compile()
	{
		CppSyntaxAnalyzer sa;
		CppMidCode mc;
		CppAssemblyCode ac;
		CppBinaryCode bc;
		CppLink lk;

		sa.syntaxParser();
		mc.generateMidCode();
		ac.generateAssemblyCode();
		bc.generateBinaryCode();
		lk.generateProgram();
		cout << "程序运行中..." << endl;
	}
};


int main()
{
	IDE cppIDE;
	cppIDE.compile();	// 一键编译

	system("pause");
	return 0;
}
```



# 享元模式

```cpp
#include<iostream>
#include<string>
#include<map>
#include<memory>		// 使用智能指针
using namespace std;

/*
	享元模式（Flyweight Mode）
	案例：用户访问多个相似的网站，这些相似的网站之间，有共享的部分，也有自己独有的部分
*/

// 用户类
class User
{
private:
	string username;

public:
	// 构造函数，初始化列表
	User(string name) : username(name) {}

	// 向外提供访问接口
	string getName() const { return username; }
};

// 网站（抽象）
class AbstractWebsite
{
public:
	virtual void showPage(const User& user) = 0;		// 网站为用户提供了展示页面的功能
	virtual ~AbstractWebsite() {}
};

// 网站（具体）
class Website : AbstractWebsite
{
private:
	string websiteName;

public:
	// 构造函数，初始化列表
	Website(string name) : websiteName(name) {}
	
	// 网站为用户提供了展示页面的功能
	void showPage(const User& user)
	{
		cout << "网站：" << websiteName << "，正在为用户：" << user.getName() << "，展示页面" << endl;
	}
};

// 网站工厂
class WebsiteFactory
{
private:
	map<string, shared_ptr<Website>> webMap;	// 这个Map容器"管理"和"维护"着一堆的网站 map<字符串，指向Website对象的智能指针>

public:
	// 生成网站，返回一个指向网站的智能指针
	shared_ptr<Website> generateWebsite(string websiteName)
	{
		// 判断 webMap 容器中是否存在名字为 websiteName 的网站，如果没有就创建一个
		if (webMap.find(websiteName) == webMap.end())
		{
			// make_shared<Website>(websiteName) --> 实例化一个Website对象，并将对象的 name 初始化为 websiteName，最后返回一个智能指针
			webMap.insert(make_pair(websiteName, make_shared<Website>(websiteName)));
		}
		return webMap[websiteName];		// 通过key直接返回value
	}

	// 查看网站数量
	int checkWebsiteCount()
	{
		return webMap.size();
	}
};

int main()
{
	// 实例化一个网站工厂
	shared_ptr<WebsiteFactory> webFactory = make_shared<WebsiteFactory>();		// make_shared 开辟一块内存空间，返回智能指针
	
	// 让网站工厂生成出三个网站
	shared_ptr<Website> web1 = webFactory->generateWebsite("华为官网");
	shared_ptr<Website> web2 = webFactory->generateWebsite("小米官网");
	shared_ptr<Website> web3 = webFactory->generateWebsite("腾讯官网");

	// 实例化三个用户
	shared_ptr<User> user1 = make_shared<User>("小明");
	shared_ptr<User> user2 = make_shared<User>("小帅");
	shared_ptr<User> user3 = make_shared<User>("小美");

	// 让三个网站，展示页面给三个用户看
	// showPage() 函数需要接收"用户对象"作为参数，user1 只是一个智能指针，而 *user1 才是对象
	web1->showPage(*user1);		
	web2->showPage(*user2);
	web3->showPage(*user3);

	// ... 使用智能指针，不需要手动 delete ...
	system("pause");
	return 0;
}
```



# 组合模式

```cpp
#include<iostream>
#include<string>
#include<list>
using namespace std;

// 目录（抽象）
class AbstractDirectory
{
public:
	virtual void showName() = 0;							// 显示目录的名字
	virtual void add(AbstractDirectory*) = 0;				// 向目录中添加某个东西
	virtual void remove(AbstractDirectory*) = 0;			// 将某个东西从目录中移除
	virtual list<AbstractDirectory*> getChild() = 0;		// 从目录中获取某个东西（文件或目录）
	virtual ~AbstractDirectory() {}
};

// 目录（具体）
class Directory : AbstractDirectory
{
private:
	string directoryName;					// 每一个目录对象都维护着它自己的目录名字
	list<AbstractDirectory*> container;		// 每一个目录对象都维护着一个容器，里面装的实体（目录或文件）

public:
	Directory(string name) : directoryName(name) {};		// 构造函数，列表初始化

	void showName()
	{
		cout << "目录: " << directoryName << endl;
	}

	// 向目录中添加"文件对象"或者"目录对象"
	void add(AbstractDirectory* obj)
	{
		container.push_back(obj);
	}

	// 向目录中删除"文件对象"或者"目录对象"
	void remove(AbstractDirectory* obj)
	{
		container.remove(obj);
	}

	list<AbstractDirectory*> getChild()
	{
		return container;
	}
};

class File : AbstractDirectory
{
private:
	string fileName;						// 每个文件对象维护一个文件名
	list<AbstractDirectory*> container;		// 每个文件对象维护一个容器（虽然它本质上是空容器，但是为了和上面统一格式）

public:
	File(string name) : fileName(name) {}		// 构造函数，列表初始化
	void showName() { cout << "文件: " << fileName << endl; }
	void add(AbstractDirectory*) {}
	void remove(AbstractDirectory*) {}
	list<AbstractDirectory*> getChild() { return container; }
};

// 目录节点树
void showDirectoryTree(AbstractDirectory* obj, int lineWidth)
{
	list<AbstractDirectory*> container;		// 对象容器，用来装下目录或文件里面的东西
	int i = 0;
	for (int i = 0; i < lineWidth; i++)
	{
		cout << "---";
	}
	obj->showName();				// 展示当前目录或文件的名称
	container = obj->getChild();	// 获取obj里面的内容（如果obj是文件，获取的内容为空）
	if (!container.empty())			// 如果容器不为空
	{
		// 从容器里面遍历出每一个对象
		for (auto item : container)
		{
			if (item->getChild().empty())
			{
				for (int i = 0; i <= lineWidth; i++)
				{
					cout << "---";
				}
				item->showName();
			}
			else
			{
				showDirectoryTree(item, lineWidth + 1);
			}
		}
	}
}

int main()
{
	// 实例化一个目录，作为根目录
	AbstractDirectory* root = (AbstractDirectory*) new Directory("C:");

	// 实例化一个目录，作为子目录，将此目录添加到根目录里
	AbstractDirectory* directory = (AbstractDirectory*) new Directory("mydir");
	root->add(directory);

	// 实例化一个文件，并将此文件添加到根目录中
	AbstractDirectory* file1 = (AbstractDirectory*) new File("main.cpp");
	root->add(file1);

	// 实例化一个文件，并将此文件添加到子目录中
	AbstractDirectory* file2 = (AbstractDirectory*) new File("run.py");
	directory->add(file2);

	// 查看目录节点树
	showDirectoryTree(root, 5);

	
	delete root;
	delete directory;
	delete file1;
	delete file2;

	system("pause");
	return 0;
}
```

