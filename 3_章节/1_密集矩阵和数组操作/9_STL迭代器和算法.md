## STL迭代器和算法

从3.4版本开始，Eigen的密集矩阵和数组提供了STL兼容的迭代器。如下图所示，这使得它们自然地与范围和循环和STL的算法兼容。

### 迭代1D数组和矢量

任何密集的1D表达式都会公开一对 `begin()/end()` 方法以循环访问它们。

这直接启用了c++11范围for循环：

**例子：**

```cpp
VectorXi v = VectorXi::Random(4);
cout << "Here is the vector v:\n";
for(auto x : v) cout << x << " ";
cout << "\n";
```

**输出：**

```powershell
Here is the vector v:
7 -2 6 6
```

一维表达式也可以很容易地传递给 STL 算法：

**例子：**

```cpp
Array4i v = Array4i::Random().abs();
cout << "Here is the initial vector v:\n" << v.transpose() << "\n";
std::sort(v.begin(), v.end());
cout << "Here is the sorted vector v:\n" << v.transpose() << "\n";
```

**输出：**

```powershell
Here is the initial vector v:
7 2 6 6
Here is the sorted vector v:
2 6 6 7
```

与 `std::vector` 类似，1D 表达式还公开了 `cbegin()/cend()` 方法对，以方便地获取非 const 对象上的 const 迭代器。

### 迭代2D数组和矩阵的元素

STL 迭代器本质上设计用于迭代1D结构。这就是为2D表达式禁用 `begin()/end()` 方法的原因。迭代 2D 表达式的所有元素仍然很容易通过 `reshaped()` 创建一维线性视图来完成：

**例子：**

```cpp
Matrix2i A = Matrix2i::Random();
cout << "Here are the coeffs of the 2x2 matrix A:\n";
for(auto x : A.reshaped())
  cout << x << " ";
cout << "\n";
```

**输出：**

```powershell
Here are the coeffs of the 2x2 matrix A:
7 -2 6 6
```

迭代 2D 数组和矩阵的行或列
还可以获取 2D 表达式的行或列的迭代器。这些可以通过rowwise（）和colwise（）代理获得。下面是一个对矩阵的每一行进行排序的示例：

**例子：**

```cpp
ArrayXXi A = ArrayXXi::Random(4,4).abs();
cout << "Here is the initial matrix A:\n" << A << "\n";
for(auto row : A.rowwise())
  std::sort(row.begin(), row.end());
cout << "Here is the sorted matrix A:\n" << A << "\n";
```

**输出：**

```powershell
Here is the initial matrix A:
7 9 5 3
2 6 1 0
6 3 0 9
6 6 3 9
Here is the sorted matrix A:
3 5 7 9
0 1 2 6
0 3 6 9
3 6 6 9
```
