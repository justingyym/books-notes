```c++
1、std::array 代替数组
    
2、类型转换
static_cast
const_cast
dynamic_cast(不建议使用)
reinterpret_cast(最好不用)
    
3、避免使用宏

4、资源申请即初始化
    
5、使用智能指针
    std::unique_ptr
    std::shared_ptr
    std::weak_ptr
    
6、使用auto
7、constxper 使用 (在编译时候就可以运行的函数)
    		类中也可以用 
    
8、C++11 文字操作符
    
9、lambda表达式
    
10、boost 库
    
11、单一职责原则（SP）
    开闭原则（OCP）
    里氏替换原则（LSP） 派生类型必须完全替代基类型
	最少惊讶原则 
    接口隔离原则（ISP） 用角色接口代替“宽接口”
    无环依赖原则
    	C++里类的声明和定义也可以分开。我们可以先声明而暂时不定义它，这种声明称为类的前置声明
    依赖倒置原则（DIP） 解决类的循环依赖
    迪米特法则？？？？
12、final
    
    

```





