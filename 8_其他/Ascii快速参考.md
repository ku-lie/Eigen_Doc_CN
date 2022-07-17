// 一个简单的Eigen快速参考。添加缺少的任何内容。
// 主要作者：Keir Mierle
```cpp
#include <Eigen/Dense>

Matrix<double, 3, 3> A;               // 固定行和列。与Matrix3d相同。
Matrix<double, 3, Dynamic> B;         // 固定行，动态列。
Matrix<double, Dynamic, Dynamic> C;   // 全动态。与MatrixD相同。
Matrix<double, 3, 3, RowMajor> E;     // Row major; 默认值为 column-major.
Matrix3f P, Q, R;                     // 3x3浮点矩阵。
Vector3f x, y, z;                     // 3x1浮点矩阵。
RowVector3f a, b, c;                  // 1x3浮点矩阵。
VectorXd v;                           // 双精度动态列向量
double s;

// 基本用法
// Eigen          // Matlab           // 注释
x.size()          // length(x)        // 矢量大小
C.rows()          // size(C,1)        // 行数
C.cols()          // size(C,2)        // 列数
x(i)              // x(i+1)           //Matlab是基于1的
C(i,j)            // C(i+1,j+1)       //

A.resize(4, 4);   // 如果断言处于启用状态，则出现运行时错误。
B.resize(4, 9);   // 如果断言处于启用状态，则出现运行时错误。
A.resize(3, 3);   // 可以，啊尺寸没有变化。
B.resize(3, 9);   // 可以，只有动态COL发生了变化。

A << 1, 2, 3,     // 初始化A。 元素也可以是矩阵，
     4, 5, 6,     // 沿列堆叠，
     7, 8, 9;     // 然后堆叠行。
B << A, A, A;     // B是三个水平堆叠的A。
A.fill(10);       // 用所有10个填充A。

// Eigen                                    // Matlab
MatrixXd::Identity(rows,cols)               // eye(rows,cols)
C.setIdentity(rows,cols)                    // C = eye(rows,cols)
MatrixXd::Zero(rows,cols)                   // zeros(rows,cols)
C.setZero(rows,cols)                        // C = zeros(rows,cols)
MatrixXd::Ones(rows,cols)                   // ones(rows,cols)
C.setOnes(rows,cols)                        // C = ones(rows,cols)
MatrixXd::Random(rows,cols)                 // rand(rows,cols)*2-1            // MatrixXd::Random返回（-1，1）中的均匀随机数。
C.setRandom(rows,cols)                      // C = rand(rows,cols)*2-1
VectorXd::LinSpaced(size,low,high)          // linspace(low,high,size)'
v.setLinSpaced(size,low,high)               // v = linspace(low,high,size)'
VectorXi::LinSpaced(((hi-low)/step)+1,      // low:step:hi
                    low,low+step*(size-1))  //


// 矩阵切片和块。这里列出的所有表达式都是读/写的。
// 模板大小版本更快。注意，Matlab是基于1的（大小N向量是x（1）。。。x（N））。
/******************************************************************************/
/*                             请帮助我们改进此部分                              */
/*            Eigen 3.4支持针对子矩阵的改进API，包括从阵列切片和索引：               */
/* http://eigen.tuxfamily.org/dox-devel/group__TutorialSlicingIndexing.html   */
/******************************************************************************/
// Eigen                           // Matlab
x.head(n)                          // x(1:n)
x.head<n>()                        // x(1:n)
x.tail(n)                          // x(end - n + 1: end)
x.tail<n>()                        // x(end - n + 1: end)
x.segment(i, n)                    // x(i+1 : i+n)
x.segment<n>(i)                    // x(i+1 : i+n)
P.block(i, j, rows, cols)          // P(i+1 : i+rows, j+1 : j+cols)
P.block<rows, cols>(i, j)          // P(i+1 : i+rows, j+1 : j+cols)
P.row(i)                           // P(i+1, :)
P.col(j)                           // P(:, j+1)
P.leftCols<cols>()                 // P(:, 1:cols)
P.leftCols(cols)                   // P(:, 1:cols)
P.middleCols<cols>(j)              // P(:, j+1:j+cols)
P.middleCols(j, cols)              // P(:, j+1:j+cols)
P.rightCols<cols>()                // P(:, end-cols+1:end)
P.rightCols(cols)                  // P(:, end-cols+1:end)
P.topRows<rows>()                  // P(1:rows, :)
P.topRows(rows)                    // P(1:rows, :)
P.middleRows<rows>(i)              // P(i+1:i+rows, :)
P.middleRows(i, rows)              // P(i+1:i+rows, :)
P.bottomRows<rows>()               // P(end-rows+1:end, :)
P.bottomRows(rows)                 // P(end-rows+1:end, :)
P.topLeftCorner(rows, cols)        // P(1:rows, 1:cols)
P.topRightCorner(rows, cols)       // P(1:rows, end-cols+1:end)
P.bottomLeftCorner(rows, cols)     // P(end-rows+1:end, 1:cols)
P.bottomRightCorner(rows, cols)    // P(end-rows+1:end, end-cols+1:end)
P.topLeftCorner<rows,cols>()       // P(1:rows, 1:cols)
P.topRightCorner<rows,cols>()      // P(1:rows, end-cols+1:end)
P.bottomLeftCorner<rows,cols>()    // P(end-rows+1:end, 1:cols)
P.bottomRightCorner<rows,cols>()   // P(end-rows+1:end, end-cols+1:end)

// 特别注意的是高度优化的Eigen交换函数。
// Eigen                           // Matlab
R.row(i) = P.col(j);               // R(i, :) = P(:, j)
R.col(j1).swap(mat1.col(j2));      // R(:, [j1 j2]) = R(:, [j2, j1])

// 视图、转置等；
/******************************************************************************/
/*                           请帮助我们改进此部分                                */
/* Eigen 3.4支持一种新的重塑API：                                                */
/* 3_章节/1_密集矩阵和数组操作/8_重塑.md                                          */
/******************************************************************************/
// Eigen                           // Matlab
R.adjoint()                        // R'
R.transpose()                      // R.' or conj(R')       // 读写
R.diagonal()                       // diag(R)               // 读写
x.asDiagonal()                     // diag(x)
R.transpose().colwise().reverse()  // rot90(R)              // 读写
R.rowwise().reverse()              // fliplr(R)
R.colwise().reverse()              // flipud(R)
R.replicate(i,j)                   // repmat(P,i,j)


// 与Matlab相同，但matlab没有*=样式运算符。
// Matrix-vector.  Matrix-matrix.   Matrix-scalar.
y  = M*x;          R  = P*Q;        R  = P*s;
a  = b*M;          R  = P - Q;      R  = s*P;
a *= M;            R  = P + Q;      R  = P/s;
                   R *= Q;          R  = s*P;
                   R += Q;          R *= s;
                   R -= Q;          R /= s;

// 对每个元素独立进行矢量化操作
// Eigen                       // Matlab
R = P.cwiseProduct(Q);         // R = P .* Q
R = P.array() * s.array();     // R = P .* s
R = P.cwiseQuotient(Q);        // R = P ./ Q
R = P.array() / Q.array();     // R = P ./ Q
R = P.array() + s.array();     // R = P + s
R = P.array() - s.array();     // R = P - s
R.array() += s;                // R = R + s
R.array() -= s;                // R = R - s
R.array() < Q.array();         // R < Q
R.array() <= Q.array();        // R <= Q
R.cwiseInverse();              // 1 ./ P
R.array().inverse();           // 1 ./ P
R.array().sin()                // sin(P)
R.array().cos()                // cos(P)
R.array().pow(s)               // P .^ s
R.array().square()             // P .^ 2
R.array().cube()               // P .^ 3
R.cwiseSqrt()                  // sqrt(P)
R.array().sqrt()               // sqrt(P)
R.array().exp()                // exp(P)
R.array().log()                // log(P)
R.cwiseMax(P)                  // max(R, P)
R.array().max(P.array())       // max(R, P)
R.cwiseMin(P)                  // min(R, P)
R.array().min(P.array())       // min(R, P)
R.cwiseAbs()                   // abs(P)
R.array().abs()                // abs(P)
R.cwiseAbs2()                  // abs(P.^2)
R.array().abs2()               // abs(P.^2)
(R.array() < s).select(P,Q );  // (R < s ? P : Q)
R = (Q.array()==0).select(P,R) // R(Q==0) = P(Q==0)
R = P.unaryExpr(ptr_fun(func)) // R = arrayfun(func, P)   // with: scalar func(const scalar &x);


// 减少。
int r, c;
// Eigen                  // Matlab
R.minCoeff()              // min(R(:))
R.maxCoeff()              // max(R(:))
s = R.minCoeff(&r, &c)    // [s, i] = min(R(:)); [r, c] = ind2sub(size(R), i);
s = R.maxCoeff(&r, &c)    // [s, i] = max(R(:)); [r, c] = ind2sub(size(R), i);
R.sum()                   // sum(R(:))
R.colwise().sum()         // sum(R)
R.rowwise().sum()         // sum(R, 2) or sum(R')'
R.prod()                  // prod(R(:))
R.colwise().prod()        // prod(R)
R.rowwise().prod()        // prod(R, 2) or prod(R')'
R.trace()                 // trace(R)
R.all()                   // all(R(:))
R.colwise().all()         // all(R)
R.rowwise().all()         // all(R, 2)
R.any()                   // any(R(:))
R.colwise().any()         // any(R)
R.rowwise().any()         // any(R, 2)

// 点乘，归一化等
// Eigen                  // Matlab
x.norm()                  // norm(x).    请注意，norm(R)在 Eigen 中不起作用。
x.squaredNorm()           // dot(x, x)   请注意，对于复数，等价性不成立
x.dot(y)                  // dot(x, y)
x.cross(y)                // cross(x, y) 需要#include <Eigen/Geometry>

//// 类型转换
// Eigen                  // Matlab
A.cast<double>();         // double(A)
A.cast<float>();          // single(A)
A.cast<int>();            // int32(A)
A.real();                 // real(A)
A.imag();                 // imag(A)
// 如果原始类型等于目标类型，则不执行任何工作

// 请注意，对于大多数操作，Eigen要求所有操作数具有相同的类型：
MatrixXf F = MatrixXf::Zero(3,3);
A += F;                // 在Eigen中是非法的。在 Matlab 中，A = A+F 是允许的
A += F.cast<double>(); // F转换为双精度，然后相加（通常，转换即时发生）

// Eigen可以将现有内存映射到Eigen矩阵中。
float array[3];
Vector3f::Map(array).fill(10);            // 创建基于数组的临时映射并将条目设置为 10
int data[4] = {1, 2, 3, 4};
Matrix2i mat2x2(data);                    // 将数据复制到 mat2x2 中
Matrix2i::Map(data) = 2*mat2x2;           // 用 2*mat2x2 覆盖数据元素
MatrixXi::Map(data, 2, 2) += mat2x2;      // 将 mat2x2 添加到数据元素(如果在编译时不知道大小，则为替代语法）

// 求解 Ax = b。结果存储在 x 中。Matlab： x = A \ b.
x = A.ldlt().solve(b));  // A sym. p.s.d.    #include <Eigen/Cholesky>
x = A.llt() .solve(b));  // A sym. p.d.      #include <Eigen/Cholesky>
x = A.lu()  .solve(b));  // Stable and fast. #include <Eigen/LU>
x = A.qr()  .solve(b));  // No pivoting.     #include <Eigen/QR>
x = A.svd() .solve(b));  // Stable, slowest. #include <Eigen/SVD>
// .ldlt() -> .matrixL() and .matrixD()
// .llt()  -> .matrixL()
// .lu()   -> .matrixL() and .matrixU()
// .qr()   -> .matrixQ() and .matrixR()
// .svd()  -> .matrixU(), .singularValues(), and .matrixV()

// 特征值问题
// Eigen                          // Matlab
A.eigenvalues();                  // eig(A);
EigenSolver<Matrix3d> eig(A);     // [vec val] = eig(A)
eig.eigenvalues();                // diag(val)
eig.eigenvectors();               // vec
// 对于自伴随矩阵使用 SelfAdjointEigenSolver<>
```