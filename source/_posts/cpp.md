---
title: C++满分密集
date: 2025-01-05 19:09:31
tags:
toc: true
---
# 语法基础
引入头文件
```c++
#include <iostream>     //无分号“；”
```
使用命名空间
```c++
using namespace std;    //有分号“；”
```
- 打印Hello World
	- 使用命名空间：`cout<<"Hello World";`
	- 未使用命名空间：`std::cout<<"Hello World";`

从键盘输入
```c++
//定义要输入的变量,使用cin
int a;
cin>>a;
```
数组
像C一样定义数组
```c++
int i[] = {1,2,3};
```
使用new关键字
```c++
int *i = new int[10];
```
**函数**  
主函数

```c++
//返回值必须是int, 函数名别写成“面”
int main(){}
```
函数重载  
即使函数名相同，但参数等不同，就不是同一个函数
```c++
//函数1
int test(){}
//函数2
void test(){}
//函数3
void test(int i){}
```
传参时，若将数组作为参数，形参写成指针
一般还会增加一个形参n，表示数组的长度，方便函数处理
```c++
void test(int *p, int n){}
int main(){
	int i[] = {1,2,3};
	test(i, 3);
}
```


# 类

`class`
英 `/ klɑːs /`
n. （货物、服务或人的）等级，类别；
v. 把……归类，把……看作；属于……类（或等级），被列为某类（或某级）

```c++
class Person{};	//注意有分号“；”，函数可写在分号后面
```

成员可见性
- public:   可在类外面直接访问
- private:   只能在类里面访问
- protected:   private+可在子类中访问

```c++
class Person{
private:
	int a;
public:
	int b;
};
class Test{
	//如果不写，则默认private
	int c;
};
```
定义成员函数
1. 类内部
   ```c++
   class Person{
	   void test(){
		   cout<<"Hello World"<<endl;
	   }
   };
   ```
2. 类外部
   ```c++
   class Person{
	   void test();    //分号结尾
   };
   void Person::test(){
	   cout<<"Hello World"<<endl;
   }
   ```

成员变量和函数的调用
……

# 对象
`object`  
英 `/ ˈɒbdʒɪkt; əbˈdʒekt /`  
n. （引发某种情感或行为的）对象；物体，实物；目的，目标

# 构造函数

构造(创建)对象时，自动调用的函数

```c++
class Person{
int age,height;
public:
    //构造函数要写在public里面，不然外部访问不到，没法创建对象
    //构造函数必须与类名相同，不要写返回值，void也别写
    Person(){
        cout<<"Hello World"<<endl;
    }
};

int main(){
    //这句话会创建一个名为p的对象，程序会自动调用构造函数
    //控制台打印 Hello World
    Person p;
}
```

带参数的构造函数

```c++
Person(int newAge, int newHeight){
    age=newAge;
    height=newHeight;
}
```

等价

```c++
//冒号分割，参数用逗号隔开，最后的花括号不要丢
Person(int na, int nh):age(na),height(nh){}
```

通过带参数的构造函数创建对象

```c++
int main(){
    Person p(18,178);
}
```

# 复制构造函数

与构造函数类似

```c++
class Person{
int age,height;
public:
    //构造函数
    Person(int na, int nh):age(na),height(nh){}

    //复制构造函数
    Person(const Person &other){
        age=other.age;
        height=other.height;
    }
};

int main(){
    Person p1(18,178);

    //通过复制构造函数来构造一个新的对象p2;
    Person p2(p1);
}
```

# 析构函数

对象销毁时，程序自动调用的函数

```c++
class Person{
int age,height;
public:
    //析构函数
    ~Person(){
        cout<<"Bye"<<endl;
    }
};
```

# 静态成员

## 静态变量

使用`static`关键字，将变量或函数标记为静态，静态成员属于`类`，所有`对象`共享

```c++
class Person{
public:
int age=18;
int height=178;
//静态变量赋初值必须在类外，否则报错
static int count;
};

int Person::count=0;

int main(){
    //因为静态变量不属于对象，而是属于类
    //因此可以直接通过Person::count访问
    cout<<Person::count<<endl;
    //也可以通过 对象.count 访问
    Person::count++;
    Person p;
    cout<<p.count<<endl;
}
```

==不管如何访问count这个变量，count自始至终都是同一个==

## 静态函数

同样使用`static`关键字

```c++
class Person{
public:
static void say(){
    cout<<"How are you?"<<endl;
}
};

int main(){
    Person::say();
}

```

## 友元函数，友元类
- 友元函数
友元函数不属于类，直接通过函数名调用
  ```c++
	class MyClass {
	private:
	    int privateData;
	
	public:
	    friend void friendFunction(MyClass &obj);
	};
	
	void friendFunction(MyClass &obj) {
	    // 可以访问 obj 的私有成员
	    obj.privateData = 10;
	}
  ```

- 友元类
	```c++
	class Student{
		//定义Teacher类为Student的友元类
	    friend class Teacher;
	private:
	    int score;
	public:
	    Student(int s):score(s){}
	    friend void setVal(Student &stu, int sco);
	};
	
	void setVal(Student &stu, int sco){
	    stu.score = sco;
	}
	
	class Teacher{
	public:
	    static void showScore(const Student &s){
			//此时，可以直接通过对象.属性访问private属性
	        cout<<s.score<<endl;
	    }
	};
	
	int main(){
	    Student s(90);
	    setVal(s,99);
	    Teacher::showScore(s);
	}
	```

# 继承
c++中，一个类可以继承自多个类

## 单继承
```c++
class Person{
public:
	Person(){
		cout<<"Person构造函数"<<endl;
	}
	~Person(){
		cout<<"Person析构函数"<<endl;
	}
};

//冒号 public 被继承的类
//公有继承自Person
class Girl:public Person{
public:
	Girl(){
		cout<<"Girl构造函数"<<endl;
	}
	~Girl(){
		cout<<"Girl析构函数"<<endl;
	}
};

int main(){
	Girl g;
	return 0;
}
```
当通过`Girl g`创建Girl对象时，首先会调用Girl的父类的构造函数，再调用Girl的构造函数
程序结束后，系统会自动调用析构函数销毁对象，子类会率先被销毁，最后销毁父类，因此，先调用Girl的析构函数，再调用Person的析构函数

当父类构造函数有参数时
```c++
class Person{
int age;
public:
	Person(int age):age(age){
		cout<<"Person构造函数"<<endl;
	}
	~Person(){
		cout<<"Person析构函数"<<endl;
	}
};

//冒号 public 被继承的类
//公有继承自Person
class Girl:public Person{
public:
	Girl(int age):Person(age){
		cout<<"Girl构造函数"<<endl;
	}
	~Girl(){
		cout<<"Girl析构函数"<<endl;
	}
};

int main(){
	Girl g(18);
	return 0;
}
```
子类的构造函数需要在初始化列表中调用父类的构造函数，并给一个参数，这样父类的构造函数才能正常执行

## 多继承
```c++
class Person{
public:
	int age;
	void say(){
		cout<<"Hello"<<endl;
	}
};

class Girl:public Person{
public:
	string name;
};
class Friend:public Person{
public:
	string hobby;
};

class Girlfriend:public Girl,public Friend{
public:
	void say(){
		cout<<"I love you!"<<endl;
	}
};

int main(){
	Girlfriend gf;
    gf.Girl::age=10;
    cout<<gf.Girl::age<<endl;
    cout<<gf.Friend::age<<endl;
    gf.say();
}
```
Person中的age和say()分别被继承了，Girlfriend有两个父类，此时Girlfriend类中有两个age和say()，直接通过`gf.age`访问会报错，有两个age，鬼晓得你要访问哪个age，则通过`gf.Girl::age`和`gf.Friend::age`访问某一个age，注意：这两个age是两个不同的变量，值不同。
gf中的say()由于与Person中的say()相同，则直接覆盖了，使用gf.say()可以直接调用gf中的say()，并打印I love you!

为了解决二义性，可以只用virtual关键字，以虚基类的形式继承Person
`class Girl:virtual public Person`
`class Friend:virtual public Person`
此时`gf.Girl::age`，`gf.Friend::age`以及`gf.age`是同一个变量

# 运算符重载
运算符重载，给运算符定义为我的功能，做到1+1=3
```c++
class Person{
	int age;
public:
	Person(int a):age(a){}
	Person operator+(Person &p){
		return Person(age+p.age);
	}
	void show(){
        cout<<age<<endl;
    }
};
int main(){
    Person p1(18);
    Person p2(18);
    Person p3 = p1 + p2;
    p3.show();
}
```

自增`++`运算符
```c++
class Person{
    int age;
public:
    Person(int a):age(a){}
    Person operator++(int){
        cout<<"后置++"<<endl;
        Person tmp = *this;
        age++;
        return tmp;
    }

    Person & operator++(){
        cout<<"前置++"<<endl;
        age++;
        return *this;
    }
    void show(){
        cout<<age<<endl;
    }
};

int main(){
    Person p1(18);
    Person p2(18);
    p1++;
    ++p2;
    p1.show();
    p2.show();
}
```
注意区分：
- 后置：`Person operator++(int)`
  后置在后面括号里写个int
- 前置：`Person & operator++()`
  前置在前面加个&

# 虚函数
这玩意比较绕
多态：相同的代码，不同的功能
虚函数：
```c++
class Person{
public:
    virtual void say(){
        cout<<"Hello"<<endl;
    }
};

class Girl: public Person{
public:
    void say() override{
        cout<<"hi"<<endl;
    }
};

class Friend: public Person{
public:
    void say() override{
        cout<<"How are you?"<<endl;
    }
};

int main(){
    Person *g = new Girl;
    Person *f = new Friend;
    g->say();
    f->say();
}
```

纯虚函数：
没有函数体，必须在子类中重写，否则报错
`virtual void virFunction()=0;`
一般用在无需事例化的抽象类中

# 模版
对于不确定的参数类型，不用写一堆函数
对于函数，在函数上方加上`template <typename T>`，接下来的函数中，使用T代指任意一种类型，可以是int，float等
要代指多种类型，则`template <typename T1, typename T2>`
```c++
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}
```

类模版，则在类定义前加上`template <typename T>`
```c++
template <typename T>
class Person{
private:
	T data;
public:
	Person(T d):data(d){}
	void show();
};

//若在类外定义函数体，需要加上类模版
template <typename T>
void Person<T>::show(){
	cout<<data<<endl;
}

int main(){
	//实例化一个模版类
	//类后面紧跟尖括号，这里用int表示所有的T
	Person<int> p(123);
	p.show();

	//写什么，模版中的T就代表什么
	Person<float> p2(3.14);
    p2.show();
}
```