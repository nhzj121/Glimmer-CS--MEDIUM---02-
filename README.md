# CS-MEDIUM-02 定点数精确运算
## 课前准备--文件操作
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

int j = 0; // 用于记录当前处理的数组索引

// 整数部分的二进制转十进制函数
double ZS_two_to_ten(char (*zsp)[7], int size) {
    int i = 0;
    int ZS = 0; // 用于存储转换后的十进制整数部分
    for (; size > 0; size--) {
        zsp[j][size - 1] = zsp[j][size - 1] - '0'; // 将字符转换为对应的整数
        ZS += zsp[j][size - 1] * pow(2, i); // 计算二进制数对应的十进制值
        i++;
    }
    return ZS; // 返回计算结果
}

// 小数部分的二进制转十进制函数
double XS_two_to_ten(char (*xsp)[5], int size) {
    int i = 0;
    float XS = 0; // 用于存储转换后的十进制小数部分
    for (; size > 0; size--) {
        xsp[j][i] = xsp[j][i] - '0'; // 将字符转换为对应的整数
        XS += xsp[j][i] * pow(0.5, i + 1); // 计算二进制数对应的十进制值
        i++;
    }
    return XS; // 返回计算结果
}

int main() {
    char a[15][20]; // 用于存储从文件中读取的每一行数据

    double sum; // 用于存储转换后的十进制数
    char zs[5][7], xs[5][5]; // 用于存储二进制的整数和小数部分
    FILE *fp; // 文件指针
    fp = fopen("C:\\Users\\19522\\Desktop\\CS_M_02.txt", "r"); // 打开文件
    while (fscanf(fp, "%20[^\n]\n", a[j]) == 1) { // 读取每一行数据，直到文件末尾
        int count = 0;
        count = strlen(a[j]); // 获取当前行的长度
        if (a[j][count - 1] == 'B') { // 如果行尾是'B'，表示这是一个二进制数
            int p = 0, q = 0, duandian = 0; // 初始化索引和点的标志
            for (int y = 2; y < count; y++) { // 从第三个字符开始处理
                if (a[j][y] != '.' && duandian == 0) { // 如果不是点，且之前没有遇到过点
                    zs[j][p] = a[j][y]; // 存储整数部分的字符
                    p++;
                    continue;
                } else if (a[j][y] == '.') { // 如果遇到点
                    duandian = 1; // 设置点的标志
                    continue;
                } else if (a[j][y] != 'B' && duandian == 1) { // 如果不是'B'，且之前遇到过点
                    xs[j][q] = a[j][y]; // 存储小数部分的字符
                    q++;
                    continue;
                } else if (a[j][y] == 'B') { // 如果遇到'B'，表示二进制数结束
                    zs[j][p] = '\0'; // 整数部分结束
                    xs[j][q] = '\0'; // 小数部分结束
                    printf("整数部分：%s 小数部分：%s\n", zs[j], xs[j]); // 打印二进制的整数和小数部分
                    sum = ZS_two_to_ten(zs, p) + XS_two_to_ten(xs, q); // 转换为十进制并相加
                    printf("转化为十进制：%.4f\n", sum); // 打印十进制结果
                    continue;
                }
            }
        }
        printf("%s", a[j]); // 打印当前行的内容
        j++; // 移动到下一行
        printf("\n"); // 换行
        continue;
    }
    fclose(fp); // 关闭文件
    return 0; 
}

```
## 任务1
1. 计算机中小数部分的存储受限于二进制，采用小数部分=(0.5)+(0.25)+(0.125).....+(1/2)^n,()表示可能存在,来存储小数。在数位较小，精度较低时可以正常使用。但是由于计算机不可能存储到小数点后无限位，所以对于像0.1，0.2这样的数只能近似存储。所以才会出现0.1+0.2>0.3的情况。
2. Java的Biglnteger和BigDecimal分别可以适用于任意大小的整数计算和精确的小数或浮点数计算，原理方面和大数运算类似，使用字符串来表示数字。BigDecimal会将小数分为整数部分和小数部分，分别进行运算。<br>Python的Decimal采用三元组来存储数字，原理和Java类似。<br>C语言的gmp.h头文件使用mpz_t类型（一个结构体数组）来存储高精度数字。它使用动态内存，将存储空间随数字大小变动而变动；将数字分部分来存储计算。<br>在我个人看来，高精度小数和大数运算的实现似乎都是利用字符串或数字分块，使得在计算机现有的体系下可以正确表示输入数。然后再分块（字符串就是以一位为一块）进行计算。当然可能还有更复杂的表达方式，但我觉得应该在本质上是一样的。</br>
3.
```c
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>

// 将二进制字符串转换为十进制数
double ZS_two_to_ten(char zsp[10], int size) {
    int i = 0;
    int ZS = 0;
    for (; size > 0; size--) {
        zsp[size - 1] = zsp[size - 1] - '0'; // 将字符转换为对应的整数
        ZS += zsp[size - 1] * pow(2, i); // 计算二进制数对应的十进制值
        i++;
    }
    return ZS; // 返回计算结果
}

// 将二进制小数部分转换为十进制小数
double XS_two_to_ten(char xsp[10], int size) {
    int i = 0;
    double XS = 0;
    for (; size > 0; size--) {
        xsp[i] = xsp[i] - '0'; // 将字符转换为对应的整数
        XS += xsp[i] * pow(0.5, i + 1); // 计算二进制小数部分对应的十进制值
        i++;
    }
    return XS; // 返回计算结果
}

// 将十进制字符串转换为二进制字符串
char *ZS_ten_to_two(char zsp[10], int size) {
    int sum = 0, j = 0, y = 0;
    char temp[33];
    char *op = (char *)malloc(33 * sizeof(char)); // 分配内存存储结果
    int k = size - 1;
    for (int i = 0; i < size; i++) {
        zsp[i] -= '0'; // 将字符转换为对应的整数
        sum += zsp[i] * pow(10, k); // 计算十进制数的值
        k--;
    }
    while (sum != 0) {
        if (sum % 2) {
            temp[j] = '1'; // 进行除以2的取余操作，生成二进制数
            sum /= 2;
            j++;
        } else {
            temp[j] = '0';
            sum /= 2;
            j++;
        }
    }
    memset(op, 0, 33); // 初始化结果字符串
    for (; j > 0; j--) {
        *(op + y) = temp[j - 1]; // 将临时数组中的二进制数反转并复制到结果字符串
        y++;
    }
    *(op + y) = '\0'; // 添加字符串结束符
    return op; // 返回结果字符串
}

// 将十进制小数部分转换为二进制小数
char *XS_ten_to_two(char xsp[10], int size) {
    int count = 0;
    double sum = 0;
    char *opp = (char *)malloc(33 * sizeof(char)); // 分配内存存储结果
    for (int i = 0; i < size; i++) {
        xsp[i] -= '0'; // 将字符转换为对应的整数
        sum += xsp[i] * pow(0.1, i + 1); // 计算十进制小数部分的值
    }
    memset(opp, 0, 33); // 初始化结果字符串
    for (;;) {
        while (sum * 2 < 1 && sum < 1) {
            sum *= 2;
            *(opp + count) = '0'; // 进行乘以2的操作，生成二进制小数
            count++;
            if (count >= 32) {
                *(opp + count) = '\0'; // 如果超过最大长度，添加字符串结束符并返回
                return opp;
            }
        }
        sum *= 2;
        sum--; // 进行乘以2并减1的操作，生成二进制小数
        *(opp + count) = '1';
        count++;
        if (sum == 0 || count >= 32) {
            *(opp + count) = '\0'; // 如果小数部分为0或超过最大长度，添加字符串结束符并返回
            return opp;
        }
    }
}

int main() {
    char str[30];
    char zs[10] = {0}, xs[10] = {0};
    char *token, *save_ptr;
    int i = 0, p = 0, q = 0;
    int count, sz;
    double sum;
    gets(str); // 读取输入字符串
    count = strlen(str); // 计算字符串长度
    printf("%d\n", count); // 打印字符串长度

    if (str[count - 1] == 'B') sz = 2; // 检查字符串是否以'B'结尾，如果是，则为二进制
    if (str[count - 1] == 'D') sz = 10; // 检查字符串是否以'D'结尾，如果是，则为十进制

    token = strtok_r(str, ".", &save_ptr); // 使用strtok_r分割字符串
    if (sz == 2) {
        // 如果是二进制字符串
        while (*token != '\0') {
            zs[p] = *token;
            token++;
            p++;
        }
        zs[p] = '\0'; // 添加字符串结束符

        token = strtok_r(NULL, "B", &save_ptr); // 继续分割字符串
        while (*token != '\0') {
            xs[q] = *token;
            token++;
            q++;
        }
        xs[q] = '\0'; // 添加字符串结束符

        sum = ZS_two_to_ten(zs, p) + XS_two_to_ten(xs, q); // 计算二进制字符串对应的十进制值
        printf("%f", sum); // 打印结果
    }

    if (sz == 10) {
        // 如果是十进制字符串
        while (*token != '\0') {
            zs[p] = *token;
            token++;
            p++;
        }
        zs[p] = '\0'; // 添加字符串结束符

        token = strtok_r(NULL, "D", &save_ptr); // 继续分割字符串
        while (*token != '\0') {
            xs[q] = *token;
            token++;
            q++;
        }
        xs[q] = '\0'; // 添加字符串结束符

        char *zs_two, *xs_two;
        zs_two = ZS_ten_to_two(zs, p); // 将十进制整数部分转换为二进制
        printf("%s.", zs_two); // 打印二进制整数部分
        free(zs_two); // 释放内存

        xs_two = XS_ten_to_two(xs, q); // 将十进制小数部分转换为二进制
        printf("%s", xs_two); // 打印二进制小数部分
        free(xs_two); // 释放内存
    }
    return 0; 
}
```
## 任务2
### step1
```c
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#define ZHONGCHANG 68
#define MAX 33

typedef struct{

    unsigned char count;
    unsigned char sign;
    char sz;
    char zs[MAX];
    char xs[MAX];

}PointFixedNum;

char *ZS_two_to_ten(char *a,int size)
{
    int i=0,j=0,sum=0;
    int ZS[MAX]={0};
    char *result = (char *)malloc(MAX * sizeof(char));
    for(;size > 0;size--)
    {
        *(a+size-1) = *(a+size-1) - '0';
        sum += *(a+size-1) * pow(2,i);
        i++;
    }
    ZS[0] = 0;
    i=0;
    while(sum !=0)
    {
        ZS[i] = sum%10;
        sum /= 10;
        i++;
    }
    for(int j=0;j<i;j++)
    {
        *(result+j) = ZS[i-j-1] + '0';
    }
    *(result+i) = '\0';
    return result;
}
//小数部分二进制变十进制
char *XS_two_to_ten(char *b,int size)
{
    int i=0;
    char XS[MAX]={0};
    char *Result = (char *)malloc(MAX * sizeof(char));
    double sum=0;
    for(;i<size;i++)
    {
        *(b+i) = *(b+i) - '0';
        sum += *(b+i) * pow(0.5,i+1);
    }
    i=0;
    while(sum != 0)
    {
        sum *= 10;
        int SUM = sum;
        sum -= SUM;
        *(Result+i) = SUM + '0';
        i++;
    }
    *(Result+i) = '\0';
    return Result;
}

PointFixedNum init(char str[ZHONGCHANG])
{
    PointFixedNum t;
    char *token, *save_ptr;
    int p=0,q=0,sum=0;
    t.count = strlen(str);
    if(str[0] == '-') t.sign=0;
    if(str[0] != '-') t.sign=1;
    t.sz = str[t.count-1];
    token = strtok_r(str, ".",&save_ptr);
    if(!t.sign) token++;
    if(t.sz == 'B')
    {
        while(*token != '\0')
        {
            t.zs[p] = *token;
            token++;
            p++;
        }
        t.zs[p] = '\0';

        token = strtok_r(NULL, "B",&save_ptr);
        while(*token != '\0')
        {
            t.xs[q] = *token;
            token++;
            q++;
        }
        t.xs[q] = '\0';


        char* tp = ZS_two_to_ten(t.zs,p);
        for(int y=0;y< p+1;y++)
        {
            t.zs[y] = tp[y];
        }
        free(tp);

        char* tpp = XS_two_to_ten(t.xs,q);
        for(int u=0;u< q+1;u++)
        {
            t.xs[u] = tpp[u];
        }
        free(tpp);
        t.sz = 'D';
        return t;
    }
}

int main()
{
    char Sign;
    char str[ZHONGCHANG];
    PointFixedNum number;

    for(int i=0;i<3;i++)
    {
        gets(str);
        number = init(str);
        Sign = number.sign ? '\0': '-';
        printf("%c%s.%s%c\n",Sign,number.zs,number.xs,number.sz);
    }
    return 0;
}
```
![屏幕截图 2024-10-22 230600](https://github.com/user-attachments/assets/f96826cb-61dd-44b8-b60e-9f904ef30491)

### step2
