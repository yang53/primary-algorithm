# 顺序容器
>所有容器类都共享公共的接口，不同容器按不同方式对其进行扩展。基于某种容器所学习的内容也都适用于其他容器。每种容器都提供了不同的性能和功能的权衡。
### 顺序容器包含以下几种：
* `vector` 可变大小数组。支持快速随机访问。在尾部之外的位置插入或删除元素可能很慢。
* `deque` 双端队列。支持快速随机访问。在头尾位置插入/删除速度很快。
* `list` 双向链表。只支持双向顺序访问。在list中任何位置进行插入/删除操作速度都很快。
* `forward_list` 单向链表。只支持单向顺序访问。在链表任何位置进行插入/删除操作速度都很快。
* `array` 固定大小数组。支持快速随机访问。不能添加或删除元素。
* `string` 与vector相似的容器，但专门用于保存字符。随机访问速度快。在尾部插入/删除都很快。
### 除了固定大小的array之外，其他容器都提供高效、灵活的内存管理。存储策略会影响特定容器是否支持特定操作。
* string、vector将元素保存在连续的内存空间，所以元素下标来索引其地址非常快速，但是进行中间元素的插入/删除操作就会特别麻烦。
* list、forward_list两个容器的设计目的是令容器任何位置的添加/删除操作都很快速。作为代价，这两个容器不支持随机访问：如果要访问一个元素，必须从头遍历整个容器。并且，与vector/deque/array相比，这两个容器的额外内存开销也很大。 
* deque是一个更为复杂的数据结构。与string、vector类似，支持快速随机访问并且在中间位置插入或删除元素的代价（可能）很高。但是，在两边进行添加删除操作却很快，与list、forward_list相当。
* forward_list新C++标准增加的类型，设计目标是达到与最好的手写的单向链表数据结构相当的性能。因此，没有size操作，因为保存或计算器大小就会比手写链表多出额外的开销。
* array也是新标准增加的类型，一种更安全更容易的数组类型，array对象大小固定，不支持添加和删除元素以及改变容器大小的操作。
### 选择容器的基本原则：
* 除非有很好理由选择其他容器，否则应使用vector。
* 如果程序中有很多小的元素，且空间的额外开销很重要，则不要使用list或forward_list。
* 如果程序要求随机访问元素，则应使用vector或deque。
* 如果程序需要再头尾位置插入或删除元素，但不会在中间位置进行插入或删除操作，则使用deque。
* 如果程序只有在读输入是才需要再容器中间位置插入元素，随后需要随机访问元素，则：
  * 首先，确定是否真的需要再容器中间位置添加元素。当处理输入数据时，可以通过向vector追加数据并进行sort排序从而避免在中间位置进行添加元素。
  * 如果必须在中间位置添加元素，考虑在输入阶段使用list，当输入完成后，将list中的内容拷贝到一个vector中。
### 容器赋值运算：
* c1 = c2：将c1中的元素替换成c2中元素的拷贝。c1和c2必须具有相同类型。
* c = {a,b,c,...} 将c1中元素替换为初始化列表中元素的拷贝（array不适用）
* swap(c1,c2)/c1.swap(c2) 交换c1和c2的元素。c1和c2必须具有相同的类型。swap通常比从c2向c1拷贝元素快得多。
#### assign操作不适用于关联容器和array
* seq.assign(b,e) 将seq中的元素替换为迭代器b和e所表示的范围中的元素，迭代器b和e不能指向seq中的元素。
* seq.assign(il) 将seq中的元素替换为初始化列表il中的元素。
* seq.assign（n, t）将seq中的元素替换为n个值为t的元素。
#### 赋值相关运算会导致指向左边容器内部的迭代器，引用和指针失效。swap操作将容器内容交换则不会导致失效（array和string的情况下除外）。
#### 除array外，swap不对任何元素进行拷贝、删除或插入操作，因此可以保证在常数时间内完成。
### 关系运算符：每个容器类型都支持相等运算符（==和!=）;除了无序关联容器外的所有容器都支持关系运算符（>、>=、<、<=）。关系运算符左边两边的运算对象必须是相同类型的容器，且必须保存相同类型的元素。比较的工作原理如下：
* 如果两个容器具有相同大小且所有元素都两两对应相等，则这两个容器相等；否则两个容器不等。
* 如果两个容器大小不同，但较小容器中每个元素都等于较大容器中的对应元素，则较小容器小于较大容器。
* 如果两个容器都不是另一个容器的前缀子序列，则他们的比较结果取决于第一个不相等的元素的比较结果。
```C++
vector<int> v1 = {1, 3, 5, 7, 9, 12};
vector<int> v2 = {1, 3, 9};
vector<int> v3 = {1, 3, 5, 7};
vector<int> v4 = {1, 3, 5, 7, 9, 12};
v1 < v2;//true;v1和v2在元素[2]处不同：v1[2]<v2[2]
v1 < v3;//false;所有元素都相等，但v3元素个数更少。
v1 == v4;//true;所有元素都相等且元素个数相同
v1 == v2;//false;元素个数不相等。
```
