## 第二章

### 13 vector 和 string 优先于动态分配的数组

​	动态数组优先使用vector和string

### 14 使用reserve来变不必要的重新分配

​	一、成员函数max_size 可以查询该容器最大限制

​	二、容器增长过程：

​		1. 分配大小为当前容器每个背书新内存（vector和sting为2倍增长）

​		2. 旧容器所有元素从旧内存拷贝至新内存

​		3. 析构旧内存

​		4. 释放旧内存

size() 容器有多少元素 ; 

capacity() 容器已经分配的内存可容纳多少元素 ;

resize() ; 强迫容易改变到包含n个元素的状态；n< size()  n后会被析构 ,n>size 重新分配内存

reserve()；强迫容器把它的容量变成至少是n，n需要比capacity()  大

### 15 注意string实现的多样性

### 16 了解如何把vector 和string 数据传给旧的API

```c++
vector<int> v

void doSomething(const int* pInts, size_t numInts);

if(!v.empty()){
	doSomething(&v[0], v.size());
}
v.begin()是迭代器，不能代替 &v[0]
```

```c++
void doSomething(const char* pString);

doSomething(s.c_str());
```

### 17 使用“swap技巧” 出去多余的容量

### 18 避免使用vector < bool >

Vector 不能存储bool ，储存在vector中的每个bool仅占一个二进制位，一个8位的字节可容纳8个“bool”

## 第三章

### 19 理解相等（equality）和等价（equivalence）的区别

相等：operator==

等价：key_comp()        

```c++
!c.key_comp()( x , y ) && !c.key_comp()( y , x ) //在c的排序顺序中，x在y之前不为真，y在x之前也不为真
```

### 20 为包含指针的关联容器指定比较类型

注意：容器内存的是指针， xx.begin()得到的是指针

### 21 总让比较函数在等值的情况下返回false

判断a>b的函数，  > 返回true ，= ；< 返回 false

### 22 切勿直接修改set或multiset中的键

改变key会影响容器的排序性。如果改变了这部分内容，那么可能会破坏容器，导致不确定的结果

如果为了确保可移植性，就要确保set和mutiset中的元素（key和value）不能被修改。反之可以修改value



修改set，multiset，map和mutimap中元素的步骤

1. 找到想要修改容器的元素。（参照45条）
2. 为将要被修改的元素做一份拷贝；map/mutimap下，不要把该拷贝的第一部分声明为const
3. 修改该拷贝，使它具有你期望它在容器中的值
4. 把该元素从容器中删除，用erase（参照第八条）
5. 把新的值插入到容器中，使用insert

```c++
xxxset se;            //se 是按照id号排序的雇员集合
xxxyee selectedID     //selectedID是存储有特定id号的xxxyee变量
···
xxxset::iterator i = se.find(selectedID);  //第1步： 找到带修改的元素

if（i != se.end()){ 
	xxxyee e(*i);										//第2步：拷贝该元素
	e.setTitle("Corporate Deity");  //第3步：修改拷贝
	se.erase(i++);									//第4步：删除该元素
																	//递增迭代器以保持它的有效性（参考第9条）
	se.insert(i,e);									//第5步：插入新元素；提示她的位置和原来相同
}
```



### 23 考虑用排序的vector替代关联容器

binary_search

equal_range

lower_bound

```c++
vector<Widget> vw; // 代替set<Widget>
... // 建立阶段：很多插入，
// 几乎没有查找
sort(vw.begin(), vw.end()); // 结束建立阶段。（当
// 模拟一个multiset时，你
// 可能更喜欢用stable_sort
// 来代替；参见条款31。）
Widget w; // 用于查找的值的对象
... // 开始查找阶段
if (binary_search(vw.begin(), vw.end(), w))... // 通过binary_search查找
vector<Widget>::iterator i =
lower_bound(vw.begin(), vw.end(), w); // 通过lower_bound查找
if (i != vw.end() && !(w < *i))... // 条款19解释了
// “!(w < *i)”测试
pair<vector<Widget>::iterator,
vector<Widget>::iterator> range =
equal_range(vw.begin(), vw.end(), w); // 通过equal_range查找
if (range.first != range.second)...
... // 结束查找阶段，开始
// 重组阶段
sort(vw.begin(), vw.end()); // 开始新的查找阶段...
```



```c++
typedef pair<string, int> Data; // 在这个例子里
// "map"容纳的类型
class DataCompare { // 用于比较的类
public:
bool operator()(const Data& lhs, // 用于排序的比较函数
const Data& rhs) const
{
return keyLess(lhs.first, rhs.first); // keyLess在下面
}

bool operator()(const Data& Ihs, // 用于查找的比较函数
const Data::first_type& k) const // （形式1）
{
return keyLess(lhs.first, k);
}

bool operator()(const Data::first_type& k, // 用于查找的比较函数
const Data& rhs) const // （形式2）
{
return keyLessfk, rhs.first);
}

private:
bool keyLess(const Data::first_type& k1, // “真的”
const Data::first_type& k2) const // 比较函数
{
return k1 < k2;
}
};

vector<Data> vd; // 代替map<string, int>
... // 建立阶段：很多插入，
// 几乎没有查找
sort(vd.begin(), vd.end(), DataCompare()); // 结束建立阶段。（当
// 模拟multimap时，你
// 可能更喜欢用stable_sort
// 来代替；参见条款31。）
string s; // 用于查找的值的对象
... // 开始查找阶段
if (binary_search(vd.begin(), vd.end(), s,
DataCompare()))... // 通过binary_search查找
vector<Data>::iterator i =
lower_bound(vd.begin(), vd.end(), s,
DataCompare()); // 在次通过lower_bound查找，
if (i != vd.end() && !DataCompare()(s, *i))... // 条款45解释了
// “!DataCompare()(s, *i)”测试
pair<vector<Data>::iterator,
vector<Data>::iterator> range =
equal_range(vd.begin(), vd.end(), s,
DataCompare()); // 通过equal_range查找
if (range.first != range.second)...
... // 结束查找阶段，开始
// 重组阶段
sort(vd.begin(), vd.end(), DataCompare()); // 开始新的查找阶段...

```

### 24 当效率至关重要时，请在map::operator[] 与 map::insert 之间谨慎做出选择。

在map内添加时候用  map::insert

更新时候用map::operator[]

### 25 熟悉非标准的哈希容器



## 第四章 迭代器

### 26 iterator 优于 const_iterator、reverse_iterator以及 const_reverse_iterator

iterator 从头至尾遍历

reverse_iterator  从尾到头遍历

#### 27 使用distance 和 advance 将容器的 const_iterator 转换成 iterator

使用时仔细考虑是否真的需要，最好不这样做

### 28 正确理解有reverse_iterator 的base() 成员函数所产生的iterator的用法

```c++
vector<int> v;
v.reserve(5);											//见第14条
for(int i = 1 ; i <= 5; ++i){			//插入1到5
	v.push_back(i);
}

//使ri指向3
vector<int>::reverse_iterator ri =  find(v.rbegin(), v.rend() 3); 
//使i与ri的基相同
vector<int>::iterator i(ri.base()); 

```

在执行了上述代码之后，该vector和相应迭代器的状态如下图所示：

<img src="/Users/ashina/Documents/GitHub/books-notes/Effective STL/image-20211015135844316.png" alt="image-20211015135844316" style="zoom:50%;" />

注意:  

​	删除 3 则使用 v.erase(--ri.base()) //     i=ri.base()

​    插入  ri.insert(99)   // 插入后1 2 3 99 4 5  （insert是在前面插入 reverse_iterator 是倒序，所以3 前面是4）

###  29 对于逐个字符的输入请考虑使用istreambuf_iterator

