## 与原始缓冲区接口：Map 类

本页说明如何使用“原始”C/C++数组。这在各种上下文中都很有用，尤其是在将向量和矩阵从其他库“导入”到 Eigen 中时。

### 介绍

有时，您可能有一个预定义的数字数组，您希望在 Eigen 中将其用作向量或矩阵。虽然一种选择是创建数据的副本，但最常见的是，您可能希望将此内存重新用作Eigen类型。幸运的是，使用 [Map](14_参考/1_核心模块/30_Map.md) 类，这非常容易。

### Map类型和声明Map变量

Map 对象具有由其Eigen等效项定义的类型：

```cpp
Map<Matrix<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime> >
```

请注意，在此默认情况下，Map 只需要一个模板参数。
要构造 Map 变量，您需要另外两条信息：指向定义元素数组的内存区域的指针，以及矩阵或向量的所需形状。例如，若要定义在编译时确定大小的浮点数矩阵，可以执行以下操作：

```cpp
Map<MatrixXf> mf(pf,rows,columns);
```

其中 pf 是指向内存数组的 `float *` 。整数的固定大小只读向量可能声明为

```cpp
Map<const Vector4i> mi(pi);
```

其中 pi 是 `int *` 。在这种情况下，不必将大小传递给构造函数，因为它已由矩阵/数组类型指定。

请注意，Map 没有默认构造函数。您必须传递一个指针来初始化对象。但是，您可以解决此要求（请参阅[更改映射的数组](#更改映射的数组)）。

Map 足够灵活，可以适应各种不同的数据表示形式。还有另外两个（可选）模板参数：

```cpp
Map<typename MatrixType,
    int MapOptions,
    typename StrideType>
```

* `MapOptions` 指定指针是[Aligned](14_参考/1_核心模块/4_枚举/readme.md#alignmenttype)还是[Unaligned](14_参考/1_核心模块/4_枚举/readme.md#alignmenttype)。默认值为Unaligned。
* `StrideType` 允许您使用 [Stride](14_参考/1_核心模块/53_Stride.md) 类为内存数组指定自定义布局。一个示例是指定数据数组以行为主格式进行组织：

**例子：**

```cpp
int array[8];
for(int i = 0; i < 8; ++i) array[i] = i;
cout << "Column-major:\n" << Map<Matrix<int,2,4> >(array) << endl;
cout << "Row-major:\n" << Map<Matrix<int,2,4,RowMajor> >(array) << endl;
cout << "Row-major using stride:\n" <<
  Map<Matrix<int,2,4>, Unaligned, Stride<1,4> >(array) << endl;
```

**输出：**

```powershell
Column-major:
0 2 4 6
1 3 5 7
Row-major:
0 1 2 3
4 5 6 7
Row-major using stride:
0 1 2 3
4 5 6 7
```

但是，Stride甚至比这更灵活。有关详细信息，请参阅 [Map](14_参考/1_核心模块/30_Map.md) 和 [Stride](14_参考/1_核心模块/53_Stride.md) 类的文档。

### 使用Map变量

您可以像使用任何其他Eigen类型一样使用 Map 对象：

**例子：**

```cpp
typedef Matrix<float,1,Dynamic> MatrixType;
typedef Map<MatrixType> MapType;
typedef Map<const MatrixType> MapTypeConst;   // 只读映射
const int n_dims = 5;
MatrixType m1(n_dims), m2(n_dims);
m1.setRandom();
m2.setRandom();
float *p = &m2(0);  // 获取存储 m2 数据的地址
MapType m2map(p,m2.size());   // m2map 与 m2 共享数据
MapTypeConst m2mapconst(p,m2.size());  // m2 的只读访问器
cout << "m1: " << m1 << endl;
cout << "m2: " << m2 << endl;
cout << "Squared euclidean distance: " << (m1-m2).squaredNorm() << endl;
cout << "Squared euclidean distance, using map: " <<
  (m1-m2map).squaredNorm() << endl;
m2map(3) = 7;   // 这将改变m2，因为它们共享相同的数组
cout << "Updated m2: " << m2 << endl;
cout << "m2 coefficient 2, constant accessor: " << m2mapconst(2) << endl;
/* m2mapconst(2) = 5; */   // 这会产生编译时错误
```

**输出：**

```powershell
m1:   0.68 -0.211  0.566  0.597  0.823
m2: -0.605  -0.33  0.536 -0.444  0.108
Squared euclidean distance: 3.26
Squared euclidean distance, using map: 3.26
Updated m2: -0.605  -0.33  0.536      7  0.108
m2 coefficient 2, constant accessor: 0.536
```

所有Eigen函数都编写为接受 Map 对象，就像其他Eigen类型一样。但是，在编写采用Eigen类型的自己的函数时，这不会自动发生：Map 类型与其[Dense](../../6_类列表/1_Eigen/33_Dense.md)等效类型不同。有关详细信息，请参阅[编写将Eigen类型作为参数的函数](../../5_一般主题/1_编写将Eigen类型作为参数的函数.md)。

### 更改映射的数组

可以在声明后使用C++“placement new”语法更改 Map 对象的数组：

**例子：**

```cpp
int data[] = {1,2,3,4,5,6,7,8,9};
Map<RowVectorXi> v(data,4);
cout << "The mapped vector v is: " << v << "\n";
new (&v) Map<RowVectorXi>(data+4,5);
cout << "Now v is: " << v << "\n";
```

**输出：**

```powershell
The mapped vector v is: 1 2 3 4
Now v is: 5 6 7 8 9
```

尽管外观如此，但这不会调用内存分配器，因为语法指定了存储结果的位置。

通过此语法，可以在不首先知道映射数组在内存中的位置的情况下声明 Map 对象：

```cpp
Map<Matrix3f> A(NULL);  // 不要尝试使用此矩阵！
VectorXf b(n_matrices);
for (int i = 0; i < n_matrices; i++)
{
  new (&A) Map<Matrix3f>(get_matrix_pointer(i));
  b(i) = A.trace();
}
```
