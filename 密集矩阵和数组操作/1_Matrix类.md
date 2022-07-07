## 矩阵类

在[Eigen](../xxx.md)中，所有矩阵和向量都是[矩阵](../xxx.md)模板类的对象。向量只是矩阵的一种特殊情况，有一行或一列。

### 矩阵的前三个模板参数

Matrix类需要六个模板参数，但现在只需要了解前三个参数就足够了。剩下的三个参数都有默认值，目前将保持不变，我们将在[下面讨论](#可选模板参数)。

矩阵的三个强制性模板参数是：

```cpp
Matrix<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime>
```

* *Scalar*是标量类型，即元素的类型。 也就是说，如果需要浮点矩阵，请在此处选择*float*。有关所有支持的标量类型的列表以及如何将支持扩展到新类型，请参见[标量类型](../xxx.md)。
* *RowsAtCompileTime*和*ColsAtCompileTime*是编译时已知的矩阵行数和列数（如果编译时不知道该数字，请参阅[下面](#特殊的动态值)的操作）。
我们提供了许多方便的类型，以涵盖通常情况。例如，*Matrix4f*是浮点的4x4矩阵。以下是它是如何由Eigen定义的：

```cpp
typedef Matrix<float, 4, 4> Matrix4f;
```

我们在[下面](#方便的类型定义)讨论这些方便的类型定义。

### 向量

如上所述，在Eigen中，向量只是矩阵的特例，具有1行或1列。最常见的情况是它们有一列；这种向量称为列向量，通常缩写为向量。在另一种情况下，它们有一行，称为行向量。

例如，方便的类型定义*Vector3f*是由3个浮点组成的（列）向量。 其定义如下：

```cpp
typedef Matrix<float, 3, 1> Vector3f;
```

我们还为行向量提供了方便的类型定义，例如：

```cpp
typedef Matrix<int, 1, 2> RowVector2i;
```

### 特殊的动态值

当然，Eigen不限于编译时已知维数的矩阵。*RowsAtCompileTime*和*ColsAtCompileTime*模板参数可以采用特殊的动态值，这表明编译时大小未知，因此必须作为运行时变量处理。在Eigen术语中，这种大小被称为动态大小；而编译时已知的大小称为固定大小。 例如，方便的类型定义*MatrixXd*表示具有动态大小的双精度矩阵，定义如下：

```cpp
typedef Matrix<double, Dynamic, Dynamic> MatrixXd;
```

同样，我们定义了一个自解释的类型定义*VectorXi*，如下所示：

```cpp
typedef Matrix<int, Dynamic, 1> VectorXi;
```

例如，您可以完全拥有固定数量的行和动态数量的列，如：

```cpp
Matrix<float, 3, Dynamic>
```

### 构造函数

默认构造函数始终可用，从不执行任何动态内存分配，也从不初始化矩阵元素。您可以执行以下操作：

```cpp
Matrix3f a;
MatrixXf b;
```

在这里
* a是一个3乘3矩阵，具有未初始化元素的纯浮点[9]数组，
* b是一个动态大小矩阵，其大小当前为0×0，其元素数组尚未分配。
也可提供不同大小的构造函数。对于矩阵，始终首先传递行数。对于向量，只需传递向量大小。它们分配具有给定大小的元素数组，但不初始化元素本身：

```cpp
MatrixXf a(10,15);
VectorXf b(30);
```

在这里
* a是10x15动态大小矩阵，具有已分配但当前未初始化的元素。
* b是大小为30的动态大小向量，具有已分配但当前未初始化的元素。
为了跨固定大小和动态大小矩阵提供统一的API，在固定大小矩阵上使用这些构造函数是合法的，即使在这种情况下传递大小是无用的。所以这是合法的：

```cpp
Matrix3f a(3,3);
```

这是一个无效操作。

矩阵和向量也可以从元素列表中初始化。在C++11之前，此功能仅限于大小不超过4的固定大小的列或向量：

```cpp
Vector2d a(5.0, 6.0);
Vector3d b(5.0, 6.0, 7.0);
Vector4d c(5.0, 6.0, 7.0, 8.0);
```

如果启用C++11，则可以通过传递任意数量的元素来初始化任意大小的固定大小列或行向量：

```cpp
Vector2i a(1, 2);                      //包含元素{1，2}的列向量
Matrix<int, 5, 1> b {1, 2, 3, 4, 5};   //包含元素{1，2，3，4，5}的行向量
Matrix<int, 1, 5> c = {1, 2, 3, 4, 5}; //包含元素{1，2，3，4，5}的列向量
```

在矩阵和向量具有固定大小或运行时大小的一般情况下，元素必须按行分组，并作为初始值列表的初始值列表传递（[详细信息](../xxx.md)）：

```cpp
MatrixXi a {      //构造2x2矩阵
      {1, 2},     //第一行
      {3, 4}      //第二行
};
Matrix<double, 2, 3> b {
      {2, 3, 4},
      {5, 6, 7},
};
```

对于列或行向量，允许隐式转置。这意味着可以从单行初始化列向量：

```cpp
VectorXd a {{1.5, 2.5, 3.5}};//具有3个元素的列向量
RowVectorXd b {{1.0, 2.0, 3.0, 4.0}};//具有4个元素的行向量
```

### 元素访问器

Eigen中的主要元素访问器accessors和mutators是重载的括号运算符。对于矩阵，始终首先传递行索引。对于向量，只需传递一个索引。编号从0开始。这个例子不言而喻：
**例子：**

```cpp
#include <iostream>
#include <Eigen/Dense>

int main()
{
    Eigen::MatrixXd m(2,2);
    m(0,0) = 3;
    m(1,0) = 2.5;
    m(0,1) = -1;
    m(1,1) = m(1,0) + m(0,1);
    std::cout << "Here is the matrix m:\n" << m << std::endl;
    Eigen::VectorXd v(2);
    v(0) = 4;
    v(1) = v(0) - 1;
    std::cout << "Here is the vector v:\n" << v << std::endl;
}
```

**输出：**

```powershell
Here is the matrix m:
  3  -1
2.5 1.5
Here is the vector v:
4
3
```

注意，语法*m(index)*不限于向量，也可用于一般矩阵，这意味着元素数组中基于索引的访问。然而，这取决于矩阵的存储顺序。所有Eigen矩阵默认为列主存储顺序，但这可以更改为行主存储顺序，请参阅[存储顺序](../xxx.md)。

对于向量中基于索引的访问，运算符 *[]* 也被重载，但请记住，C++不允许运算符 *[]* 接受多个参数。我们将运算符 *[]* 限制为向量，因为C++语言中的一个笨拙之处会使*matrix[i，j]*编译为与*matrix[j]*相同的东西！

### 逗号初始化

可以使用所谓的逗号初始化器语法方便地设置矩阵和向量元素。现在，知道这个例子就足够了：
**例子：**

```cpp
Matrix3f m;
m << 1, 2, 3,
     4, 5, 6,
     7, 8, 9;
std::cout << m;
```

**输出：**

```powershell
1 2 3
4 5 6
7 8 9
```

如[这页](../xxx.md)所述，右侧还可以包含矩阵表达式。

### 调整大小

矩阵的当前大小可以通过[rows()](../xxx.md)、[cols()](../xxx.md)和[size()](../xxx.md)检索。这些方法分别返回行数、列数和元素个数。通过[resize()](../xxx.md)方法调整动态大小矩阵的大小。
**例子：**

```cpp
#include <iostream>
#include <Eigen/Dense>

int main()
{
  Eigen::MatrixXd m(2,5);
  m.resize(4,3);
  std::cout << "The matrix m is of size "
            << m.rows() << "x" << m.cols() << std::endl;
  std::cout << "It has " << m.size() << " coefficients" << std::endl;
  Eigen::VectorXd v(2);
  v.resize(5);
  std::cout << "The vector v is of size " << v.size() << std::endl;
  std::cout << "As a matrix, v is of size "
            << v.rows() << "x" << v.cols() << std::endl;
}
```

**输出**

```powershell
The matrix m is of size 4x3
It has 12 coefficients
The vector v is of size 5
As a matrix, v is of size 5x1
```

如果实际矩阵大小不变，则*resize()*方法为不能操作；否则它是破坏性的：元素的值可能会改变。如果您想要*resize()*的保守变体，它不会更改系数，请使用[conservativeResize()](../xxx.md)，有关详细信息，请参阅[这页](../xxx.md))。

为了API的一致性，所有这些方法在固定大小的矩阵上仍然可用。当然，实际上不能调整固定大小矩阵的大小。试图将固定大小更改为实际不同的值将触发断言失败；但以下代码是合法的：
**例子：**

```cpp
#include <iostream>
#include <Eigen/Dense>

int main()
{
  Eigen::Matrix4d m;
  m.resize(4,4); //无效操作
  std::cout << "The matrix m is of size "
            << m.rows() << "x" << m.cols() << std::endl;
}
```

**输出：**

```powershell
The matrix m is of size 4x4
```

### 赋值和调整大小

赋值是使用运算符 *=*，将一个矩阵复制到另一个矩阵中的操作。Eigen自动调整左侧矩阵的大小，使其与右侧矩阵的大小相匹配。例如：
**例子：**

```cpp
MatrixXf a(2,2);
std::cout << "a is of size " << a.rows() << "x" << a.cols() << std::endl;
MatrixXf b(3,3);
a = b;
std::cout << "a is now of size " << a.rows() << "x" << a.cols() << std::endl;
```

**输出：**

```powershell
a is of size 2x2
a is now of size 3x3
```

当然，如果左侧大小固定，则不允许调整其大小。

如果您不希望自动调整大小（例如出于调试目的），可以禁用它，请参阅[这页](../xxx.md)。

### 固定与动态大小

什么时候应该使用固定大小（例如*Matrix4f*），什么时候应该选择动态大小（例如*MatrixXf*）？简单的答案是：在可能的情况下，对非常小的大小使用固定大小，对较大的大小或必须的地方使用动态大小。对于小大小，尤其是小于（大约）16的大小，使用固定大小对性能非常有利，因为它允许Eigen避免动态内存分配并展开循环。在内部，固定大小的Eigen矩阵只是一个普通阵列，即

```cpp
Matrix4f mymatrix;
```

真的等于只是做

```cpp
float mymatrix[16];
```

所以这实际上没有运行时成本。相比之下，动态大小矩阵的数组总是在堆上分配，这样做

```cpp
MatrixXf mymatrix(rows,columns);
```

相当于做

```cpp
float *mymatrix = new float[rows*columns];
```

除此之外，*MatrixXf*对象将其行数和列数存储为成员变量。

当然，使用固定大小的限制是，只有在编译时知道大小时，才可能这样做。 此外，对于足够大的尺寸，例如大于（大约）32的尺寸，使用固定尺寸的性能优势变得微不足道。更糟糕的是，试图在函数中使用固定大小创建一个非常大的矩阵可能会导致堆栈溢出，因为Eigen会尝试将数组作为局部变量自动分配，这通常在堆栈上完成。最后，根据具体情况，当使用动态大小时，Eigen也可能更积极地尝试矢量化（使用SIMD指令），请参阅[矢量化](../xxx.md)。

### 可选模板参数

我们在本页开头提到Matrix类需要六个模板参数，但到目前为止，我们只讨论了前三个。其余三个参数是可选的。以下是模板参数的完整列表：

```cpp
Matrix<typename Scalar,
       int RowsAtCompileTime,
       int ColsAtCompileTime,
       int Options = 0,
       int MaxRowsAtCompileTime = RowsAtCompileTime,
       int MaxColsAtCompileTime = ColsAtCompileTime>
```

选项是位字段。在这里，我们只讨论一点：*RowMajor*。它指定这种类型的矩阵使用行主存储顺序；默认情况下，存储顺序为列主顺序。请参阅[存储顺序](../xxx.md)页面。例如，这种类型表示行主3x3矩阵：

```cpp
Matrix<float, 3, 3, RowMajor>
```

*MaxRowsAtCompileTime*和*MaxColsAtCompileTime*在指定以下内容时非常有用：即使矩阵的确切大小在编译时未知，但在编译时已知固定的上界。您可能希望这样做的最大原因是避免动态内存分配。例如，以下矩阵类型使用12个浮点的普通数组，没有动态内存分配：

```cpp
Matrix<float, Dynamic, Dynamic, 0, 3, 4>
```

### 方便的类型定义

Eigen定义了以下矩阵类型：
* *MatrixNt*表示*Matrix<type, N, N>*。例如，*MatrixXi*表示 *Matrix<int，Dynamic，Dynamic>*。
* *MatrixXNt*表示*Matrix<type, Dynamic, N>*。例如, *MatrixX3i* 表示*Matrix<int, Dynamic, 3>*。
* *MatrixNXt*表示*Matrix<type, N, Dynamic>*。例如, *Matrix4Xd*表示*Matrix<d, 4, Dynamic>*。
* *VectorNt*表示*Matrix<type, N, 1>*。例如, *Vector2f*表示 *Matrix<float, 2, 1>*。
* *RowVectorNt*表示*Matrix<type, 1, N>*。例如, *RowVector3d*表示*Matrix<double, 1, 3>*。
在上文：
* *N*可以是*2*、*3*、*4*或*X*（表示动态）中的任意一个。
* *t*可以是*i*（表示*int*）、*f*（表示*float*）、*d*（表示*double*）、*cf*（表示*complex<float>*）或*cd*（表示*complex<double>*）。*TypeDef*仅为这五种类型定义，这并不意味着它们是唯一受支持的标量类型。 例如，支持所有标准整数类型，请参阅[标量类型](../xxx.md)。
