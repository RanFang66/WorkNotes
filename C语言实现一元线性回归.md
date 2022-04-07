# C语言实现一元线性回归

在实际项目开发过程中，经常会出现一些需要进行数据拟合的情况，例如，传感器数据采样的拟合，执行机构如阀，涡轮等特性的拟合。而在实际运用中比较简单的一元线性回归是用的最多的，例如对于某个传感器，直接采样得到的AD值与实际的模拟量之间大多呈线性或者分段线性的关系。如果能够在程序中实现线性回归的算法，就能实现一些方便的功能。

## 一元线性回归介绍

如果有一组输入$X=\{x_1,x_2,\dots x_n\} \in \mathbb R^n$ , 一组输出$Y={y_1, y_2, \dots y_n} \in \mathbb R^n$，有如下函数f：
$$
y=f(x)=a x+b
$$
使得下面的误差函数最小：
$$
\begin{align}
L_f(X,Y) &= \frac{1}{n}(f(X)-Y)(f(X)-Y)^T	\\
		 &= \frac{1}{n}(aX+b-Y)(aX+b-Y)^T		\\
		 &= \frac{1}{n}(a^2 \sum_{i=1}^{n}x_i^2+\sum_{i=1}^n y_i^2+2ab\sum_{i=1}^{n}x_i-2a\sum_{i=1}^{n}x_iy_i-2b\sum_{i=1}^{n}y_i+nb^2) \\
		 &=a^2\overline{x^2} +2ab\overline{x} - 2a\overline{xy}+\overline{y}-2b\overline{y}+b^2
\end{align}
$$
其中$\overline{x_2},\overline{x}, \overline{xy}, \overline{y}$ 分别表示输入平方，输入，输入输出乘积，输出的平均值，则称$y=f(x)$ 是对输入$X$ 和输出$Y$ 的一元线性回归。

## 一元线性回归求解

为了求误差函数$L_f(X,Y)$ 的极小值，需要对$L_f$ 对$a$和

$b$ 求导。如下：
$$
\frac{\partial L_f}{\partial a}=2(a\overline{x^2}+b\overline{x}-\overline{xy}) \\
\frac{\partial L_f}{\partial b} =  2(a\overline{x}-\overline{y}+b)
$$
为求得极小值，令上述两个偏导均为0，联立可求得回归参数$a, b$    如下：
$$
a=\frac{\overline{x} \ \overline{y}-\overline{xy}}{\overline{x}^2-\overline{x^2}} \\
b = \overline{y}-a\overline{x}
$$
另一种形式为：
$$
a=\frac{\sum_{i=1}^n(x_i-\overline{x})(y_i-\overline{y})}{\sum_{i=1}^{n}(x_i-\overline{x})^2}\\
b=\overline{y}-a\overline{x}
$$
