## Array类和元素运算

本页旨在提供有关如何使用[Eigen](../xxx.md)[Array](../xxx.md)类的概述和解释。

### 什么是Array类？

Array类提供通用数组，而[Matrix](../xxx.md)(类用于线性代数。 此外，Array类提供了一种简单的方法来执行按元素的运算，这可能没有线性代数意义，例如向数组中的每个元素添加一个常数或将两个数组按元素相乘。

### 数组类型

数组是一个类模板，采用与矩阵相同的模板参数。与矩阵一样，前三个模板参数是必需的：

```cpp
Array<typename Scalar, int RowsAtCompileTime, int ColsAtCompileTime>
```

最后三个模板参数是可选的。由于这与Matrix完全相同，我们在此不再解释，只参考[Matrix类](1_Matrix类.md)。

Eigen还为一些常见情况提供typedefs，其方式类似于矩阵typedefs，但略有不同，因为单词“array”用于一维和二维数组。我们采用ArrayNt形式的typedefs表示一维数组的约定，其中N和t是大小和标量类型，如[这页](1_Matrix类.md)中解释的矩阵typedefs。对于二维数组，我们使用ArrayNNt形式的typedefs。下表显示了一些示例：

| Type                            | Typedef  |
| ------------------------------- | -------- |
| Array<float, Dynamic, 1>        | ArrayXf  |
| Array<float, 3, 1>              | Array3f  |
| Array<double, Dynamic, Dynamic> | ArrayXXd |
| Array<double, 3, 3>             | Array33d |

### 访问数组内的值

与矩阵一样，括号运算符重载以提供对数组元素的读写访问。此外， *<<* 运算符可用于初始化数组（通过逗号初始化器）或打印它们。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

int main()
{
    Eigen::ArrayXXf  m(2,2);
    //按元素分配一些值
    m(0,0) = 1.0; m(0,1) = 2.0;
    m(1,0) = 3.0; m(1,1) = m(0,1) + m(1,0);
    //将值打印到标准输出
    std::cout << m << std::endl << std::endl;
    //也允许使用逗号初始化器
    m << 1.0,2.0,
        3.0,4.0;
    //将值打印到标准输出
    std::cout << m << std::endl;
}
```

**输出：**

```powershell
1 2
3 5

1 2
3 4
```

有关逗号初始化器的更多信息，请参阅[高级初始化](../xxx.md)。

### 加减法

两个数组的加法和减法与矩阵的加法和减法相同。如果两个数组的大小相同，并且加法或减法是按元素进行的，则该操作有效。

数组还支持 *数组+标量* 形式的表达式，将标量添加到数组中的每个元素。 这提供了矩阵对象无法直接使用的功能。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

int main()
{
    Eigen::ArrayXXf a(3,3);
    Eigen::ArrayXXf b(3,3);
    a << 1,2,3,
        4,5,6,
        7,8,9;
    b << 1,2,3,
        1,2,3,
        1,2,3;
    // Adding two arrays
    std::cout << "a + b = " << std::endl << a + b << std::endl << std::endl;

    // Subtracting a scalar from an array
    std::cout << "a - 2 = " << std::endl << a - 2 << std::endl;
}
```

**输出：**

```powershell
a + b =
 2  4  6
 5  7  9
 8 10 12

a - 2 =
-1  0  1
 2  3  4
 5  6  7
```

### 数组乘法

首先，当然可以将数组乘以标量，这与矩阵的工作方式相同。数组与矩阵有本质区别的地方是当你们将两个矩阵相乘时。矩阵将乘法解释为矩阵乘积，数组将乘法解释为元素乘积。 因此，当且仅当两个数组的维数相同时，才可以将其相乘。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

int main()
{
    Eigen::ArrayXXf a(2,2);
    Eigen::ArrayXXf b(2,2);
    a << 1,2,
        3,4;
    b << 5,6,
        7,8;
    std::cout << "a * b = " << std::endl << a * b << std::endl;
}
```

**输出：**

```powershell
a * b =
 5 12
21 32
```

### 其他元素操作

数组类定义了除上述加法、减法和乘法运算符之外的其他元素运算。例如[.abs()](../xxx.md)方法取每个元素的绝对值，而[.sqrt()](../xxx.md)计算元素的平方根。如果有两个大小相同的数组，可以调用[.min(.)](../xxx.md)构造其元素为两个给定数组对应元素的最小值的数组。以下示例说明了这些操作。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

int main()
{
    Eigen::ArrayXf a = Eigen::ArrayXf::Random(5);
    a *= 2;
    std::cout << "a =" << std::endl
            << a << std::endl;
    std::cout << "a.abs() =" << std::endl
            << a.abs() << std::endl;
    std::cout << "a.abs().sqrt() =" << std::endl
            << a.abs().sqrt() << std::endl;
    std::cout << "a.min(a.abs().sqrt()) =" << std::endl
            << a.min(a.abs().sqrt()) << std::endl;
}
```

**输出：**

```powershell
a =
  1.36
-0.422
  1.13
  1.19
  1.65
a.abs() =
 1.36
0.422
 1.13
 1.19
 1.65
a.abs().sqrt() =
1.17
0.65
1.06
1.09
1.28
a.min(a.abs().sqrt()) =
  1.17
-0.422
  1.06
  1.09
  1.28
```

在[快速参考指南](../xxx.md)中可以找到更多元素操作。

### 数组和矩阵表达式之间的转换

什么时候应该使用Matrix类的对象，什么时候应该使用Array类的对象？不能对数组应用矩阵运算，也不能对矩阵应用数组运算。因此，如果需要进行线性代数运算，如矩阵乘法，则应使用矩阵；如果需要进行元素运算，那么应该使用数组。然而，有时并不那么简单，但您需要同时使用矩阵和数组操作。在这种情况下，需要将矩阵转换为数组或相反。这使您可以访问所有操作，而不必选择将对象声明为数组或矩阵。

[矩阵表达式](../xxx.md)具有[.array()](../xxx.md)方法，该方法将它们“转换”为[数组表达式](../xxx.md)，因此可以轻松应用元素操作。相反，数组表达式具有[.matrix()](../xxx.md)方法。与所有Eigen表达式抽象一样，这没有任何运行时成本（前提是您让编译器进行优化）。.array()和.matrix()二者都可以用作右值和左值。

在Eigen里禁止在表达式中混合矩阵和数组。例如，不能直接将矩阵和数组相加； *+* 运算符的操作数应该都是矩阵或都是数组。 然而，使用.array()和.matrix()很容易从一个转换到另一个。此规则的例外是赋值运算符：允许将矩阵表达式赋给数组变量，或将数组表达式赋给矩阵变量。

下面的示例显示了如何通过使用.array()方法对矩阵对象使用数组操作。例如，语句 *result=m.array()\*n.array()* 取两个矩阵 *m* 和 *n* ，将它们都转换为一个数组，使用它们按元素相乘，并将结果分配给矩阵变量 *result* （这是合法的，因为Eigen允许将数组表达式分配给矩阵变量）。

事实上，这种用例非常常见，因此Eigen为矩阵提供了一个[const .cwiseProduct(.)](../xxx.md)计算元素乘积。示例程序中也显示了这一点。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

using Eigen::MatrixXf;

int main()
{
    MatrixXf m(2,2);
    MatrixXf n(2,2);
    MatrixXf result(2,2);
    m << 1,2,
        3,4;
    n << 5,6,
        7,8;
    result = m * n;
    std::cout << "-- Matrix m*n: --\n" << result << "\n\n";
    result = m.array() * n.array();
    std::cout << "-- Array m*n: --\n" << result << "\n\n";
    result = m.cwiseProduct(n);
    std::cout << "-- With cwiseProduct: --\n" << result << "\n\n";
    result = m.array() + 4;
    std::cout << "-- Array m + 4: --\n" << result << "\n\n";
}
```

**输出：**

```powershell
-- Matrix m*n: --
19 22
43 50

-- Array m*n: --
 5 12
21 32

-- With cwiseProduct: --
 5 12
21 32

-- Array m + 4: --
5 6
7 8
```

类似地，如果 *array1* 和 *array2* 是数组，则表达式 *array1.matrix() \* array2.matrix()* 计算其矩阵乘积。

这是一个更高级的示例。 表达式 *(m.array() + 4).matrix() \* m* 将矩阵 *m* 中的每个元素加4，然后用 *m* 计算结果的矩阵积。类似地，表达式 *(m.array() \* n.array()).matrix() \* m* 计算矩阵 *m* 和 *n* 的元素乘积，然后计算与 *m* 的矩阵乘积结果。

**例子：**

```cpp
#include <Eigen/Dense>
#include <iostream>

using Eigen::MatrixXf;

int main()
{
  MatrixXf m(2,2);
  MatrixXf n(2,2);
  MatrixXf result(2,2);

  m << 1,2,
       3,4;
  n << 5,6,
       7,8;

  result = (m.array() + 4).matrix() * m;
  std::cout << "-- Combination 1: --\n" << result << "\n\n";
  result = (m.array() * n.array()).matrix() * m;
  std::cout << "-- Combination 2: --\n" << result << "\n\n";
}
```

**输出：**

```powershell

-- Combination 1: --
23 34
31 46

-- Combination 2: --
 41  58
117 170
```
