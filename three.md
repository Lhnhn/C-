### 1.结构体可以像联合体一样使用
```C++
struct 结构体名称
{
   公有成员
  protected：
   保护型成员
  private：
   私有成员
};
```
### 2.在全部数据成员是公有成员，没有定义构造函数，没有基类和虚函数时可以这样赋值
类型名 变量名={成员数据1 初值，成员数据2 初值，···};<br>
实用举例：
```C++
struct Student {	//学生信息结构体
	int num;		//学号
	string name;	//姓名
	char sex;		//性别
	int age;		//年龄
};

int main() {
	Student stu = { 97001, "Lin Lin", 'F', 19 };
	cout << "Num:  " << stu.num << endl;
	cout << "Name: " << stu.name << endl;
	cout << "Sex:  " << stu.sex << endl;
	cout << "Age:  " << stu.age << endl;
	return 0;
}
```
### 3.个人银行管理程序
```C++
//4_9.cpp
#include <iostream>
#include <cmath>
using namespace std;

class SavingsAccount { //储蓄账户类
private:
	int id;				//账号
	double balance;		//余额
	double rate;		//存款的年利率
	int lastDate;		//上次变更余额的时期
	double accumulation;	//余额按日累加之和

	//记录一笔帐，date为日期，amount为金额，desc为说明
	void record(int date, double amount);
	//获得到指定日期为止的存款金额按日累积值
	double accumulate(int date) const {
		return accumulation + balance * (date - lastDate);
	}
public:
	//构造函数
	SavingsAccount(int date, int id, double rate);
	int getId() { return id; }
	double getBalance() { return balance; }
	double getRate() { return rate; }

	//存入现金
	void deposit(int date, double amount);
	//取出现金
	void withdraw(int date, double amount);
	//结算利息，每年1月1日调用一次该函数
	void settle(int date);
	//显示账户信息
	void show();
};

//SavingsAccount类相关成员函数的实现
SavingsAccount::SavingsAccount(int date, int id, double rate)
	: id(id), balance(0), rate(rate), lastDate(date), accumulation(0) {
	cout << date << "\t#" << id << " is created" << endl;
}

void SavingsAccount::record(int date, double amount) {
	accumulation = accumulate(date);
	lastDate = date;
	amount = floor(amount * 100 + 0.5) / 100;	//保留小数点后两位
	balance += amount;
	cout << date << "\t#" << id << "\t" << amount << "\t" << balance << endl;
}

void SavingsAccount::deposit(int date, double amount) {
	record(date, amount);
}

void SavingsAccount::withdraw(int date, double amount) {
	if (amount > getBalance())
		cout << "Error: not enough money" << endl;
	else
		record(date, -amount);
}

void SavingsAccount::settle(int date) {
	double interest = accumulate(date) * rate / 365;	//计算年息
	if (interest != 0)
		record(date, interest);
	accumulation = 0;
}

void SavingsAccount::show() {
	cout << "#" << id << "\tBalance: " << balance;
}

int main() {
	//建立几个账户
	SavingsAccount sa0(1, 21325302, 0.015);
	SavingsAccount sa1(1, 58320212, 0.015);

	//几笔账目
	sa0.deposit(5, 5000);
	sa1.deposit(25, 10000);
	sa0.deposit(45, 5500);
	sa1.withdraw(60, 4000);

	//开户后第90天到了银行的计息日，结算所有账户的年息
	sa0.settle(90);
	sa1.settle(90);

	//输出各个账户信息
	sa0.show();	cout << endl;
	sa1.show();	cout << endl;
	return 0;
}
```
