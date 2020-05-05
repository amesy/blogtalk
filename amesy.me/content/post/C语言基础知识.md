---
# title: "学习笔记 -《C程序设计语言》第1章"
title: "C语言基础知识"
date: 2014-09-04T09:42:40+08:00
lastmod: 2014-09-04T09:42:40+08:00
hidden: false
draft: false
tags: ["C Programming"]
categories: ["C语言"]
description: ""
slug: "c-programming-language-first"
---

<!--more--> 

# 一、入门

在 UNIX 操作系统中，打印 `Hello, World` 

```c
#include <stdio.h>              // 包含标准库的信息
int main() {                    // 定义名为main的函数，它不接收参数值
    printf("Hello World\n");    // main函数调用库函数printf以显示字符序列，\n代表换行符
    return 0;
}
```

编译程序：

```c
cc hello.c
```

编译完会生成一个可执行文件 a.out。运行 a.out 即可打印出相应的信息。

# 二、算术运算符

## 练习

题目：使用公式 ℃ = (5 / 9) (℉ - 32)打印下列华氏温度与摄氏温度对照表

|   ℉   |  1    |   20   |   40   | 60 | 80 | 100 | 120 | 140 | 160 | 180 | 200 | ... | 300 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|   ℃   | -17 | -6 | 4 | 15 | 26 | 37 | 48 | 60 | 71 | 82 | 93 | ... | 148 |

代码实现：

```c
#include <stdio.h>

int main() {
    int celsius, fahr;          /* celsius: 摄氏度, fahr: 华氏度 */
    int lower, upper, step;

    lower = 0;      /* 摄氏度下限 */
    upper = 300;    /* 摄氏度上限 */
    step = 20;      /* 步长      */

    fahr = lower;
    while (fahr <= upper) {
        celsius = 5 * (fahr - 32) / 9;
        // printf("%3d %6d\n", fahr, celsius);  /* 数值右对齐，分别占3个数字宽和6个数字宽*/
        printf("%d\t%d\n", fahr, celsius);
        fahr += step;
    }

    return 0;
}
```

## 格式说明

格式说明可以省略精度和宽度，例如 %6f 表示待打印的浮点数至少有6个字符宽；%.2f 指定待打印的浮点数的小数点后有两位小数，但宽度没有限制；%f则仅要求按照浮点数打印该数。

```markdown
%d      按照十进制整型数打印
%6d     按照十进制整型数打印，至少6个字符宽
%f      按照浮点数打印
%6f     按照浮点数打印，至少6个字符宽
%.2f    按照浮点数打印，小数点后有2位
%6.2f   按照浮点数打印，至少6个字符宽，小数点后有2位
```

此外，printf 函数支持下列格式说明：

```markdown
%o      八进制数
%x      十六进制数
%c      字符
%s      字符串
%%      百分号(%)本身
```

## 练习

题目：编写一个程序打印摄氏温度转换为相应的华氏温度的转换表，转换公式：℉ =  (9.0 * ℃) / 5.0 + 32.0

```c
#include <stdio.h>

int main() {
    float celsius, fahr;     /* celsius: 摄氏度, fahr: 华氏度 */
    int lower, upper, step;

    lower = 0;      /* 摄氏度下限 */
    upper = 300;    /* 摄氏度上限 */
    step = 20;      /* 步长      */

    celsius = lower;
    printf("fahr  celsius\n");

    while (celsius <= upper) {
        fahr = (9.0 * celsius) / 5.0 + 32.0;
        printf("%3.0f%6.0f\n", fahr, celsius);
        celsius += step;
    }

    return 0;
}
```

# 三、for语句

使用 for 语句实现打印华氏温度 - 摄氏温度对照表：

```c
#include <stdio.h>

int main() {
    /* ℃=(5/9)(℉-32)*/

    int fahr;
    for ( fahr = 0; fahr <= 300; fahr += 20) {
        printf("%3d\%7.1f\n", fahr, 5.0 * (fahr - 32) / 9.0);
    }

    return 0;
}
```

同样的功能，对比 for 循环语句和 while 循环语句，可以发现 for 语句适合初始化和增加步长都是单条语句并且逻辑相关的情形，因为它将循环控制语句集中在一起，且比while语句更紧凑。

## 练习

题目：修改温度转换程序，要求以逆序（即按照300度到0度的顺序）打印温度转换表.

```c
#include <stdio.h>

int main() {
    /* ℃=(5/9)(℉-32) */

    int fahr;
    for ( fahr = 300; fahr >=0; fahr -= 20) {   /* 唯一的修改之处 */                
        printf("%3d\%7.1f\n", fahr, 5.0 * (fahr - 32) / 9.0);
    }

    return 0;
}
```

# 四、符号常量

类似前面程序示例中使用的 300、20 等类似的“幻数”无法向后面阅读该程序的人提供相应信息，而且使程序的修改变得更加困难。

```c
int lower, upper, step;

lower = 0;      /* 摄氏度下限 */
upper = 300;    /* 摄氏度上限 */
step = 20;      /* 步长 */
```

处理这种“幻数”的一种方式就是赋予它们有意义的名字，由此引出了 `#define`指令。`#define`指令可以把符号名（符号常量)定义为一个特定的字符串。

```c
#define 名字 替换文本
```

示例代码如下：

```c
#include <stdio.h>

#define UPPER  300    // UPPER、LOWER、STEP都是符号常量，非变量，因此不需要出现在声明中。
#define LOWER  0      // 符号常量通常用大写字母表示，方便和小写字母拼写的变量名相区别。
#define STEP   20     // #define 指令行的末尾没有分号

int main() {
    /* ℃=(5/9)(℉-32) */    

    int fahr;
    for (fahr = LOWER; fahr <= UPPER; fahr += STEP) {
        printf("%3d, %7.1f\n", fahr, 5.0 * (fahr - 32) / 9.0);
    }

    return 0;
}
```

五、字符输入/输出

文本的输入/输出都是按照字符流的方式处理的。文本流是由多行字符构成的字符序列，每行字符由0个或多个字符组成，行末是一个换行符。标准库负责使每个输入/输出流都能遵守这一模型。

标准库中提供了一次读/写一个字符的函数 getchar 和 putchar 。
