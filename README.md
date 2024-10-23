# CS-MEDIUM-02 定点数精确运算
## 课前准备--文件操作
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

int j=0;
//整数部分的二进制变十进制
double ZS_two_to_ten(char (*zsp)[7],int size)
{
    int i=0;
    int ZS=0;
    for(;size > 0;size--)
    {
        zsp[j][size-1] = zsp[j][size-1] - '0';
        ZS += zsp[j][size-1] * pow(2,i);
        i++;
    }
    return ZS;
}
//小数部分二进制变十进制
double XS_two_to_ten(char (*xsp)[5],int size)
{
    int i=0;
    float XS=0;
    for(;size > 0;size--)
    {
        xsp[j][i] = xsp[j][i] - '0';
        XS += xsp[j][i] * pow(0.5 ,i+1);
        i++;
    }
    return XS;
}

int main()
{
    char a[15][20];

    double sum;
    char zs[5][7],xs[5][5];
    FILE *fp;
    fp=fopen("C:\\Users\\19522\\Desktop\\CS_M_02.txt","r");
    while(fscanf(fp,"%20[^\n]\n",a[j]) == 1)//[^\n]表除了\n的所有字符
    {
        int count=0;
        count = strlen(a[j]);
        if(a[j][count-1] == 'B')
        {
            int p=0,q=0,duandian=0;
            for(int y=2;y<count ;y++)
            {
                if(a[j][y] != '.' && duandian == 0)
                {
                    zs[j][p] = a[j][y];
                    p++;
                    continue;
                }
                else if(a[j][y] == '.')
                {
                    duandian =1;
                    continue;
                }
                else if(a[j][y] != 'B' && duandian == 1)
                {
                    xs[j][q] = a[j][y];
                    q++;
                    continue;
                }
                else if(a[j][y] == 'B')
                {
                    zs[j][p] = '\0';
                    xs[j][q] = '\0';
                    printf("整数部分：%s 小数部分：%s\n",zs[j],xs[j]);
                    sum = ZS_two_to_ten(zs,p) + XS_two_to_ten(xs,q);
                    printf("转化为十进制：%.4f\n",sum);
                    continue;
                }
            }
        }
        printf("%s",a[j]);
        j++;
        printf("\n");
        continue;
    }
    fclose(fp);
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


double ZS_two_to_ten(char zsp[10],int size)
{
    int i=0;
    int ZS=0;
    for(;size > 0;size--)
    {
        zsp[size-1] = zsp[size-1] - '0';
        ZS += zsp[size-1] * pow(2,i);
        i++;
    }
    return ZS;
}
//小数部分十进制变二进制
double XS_two_to_ten(char xsp[10],int size)
{
    int i=0;
    double XS=0;
    for(;size > 0;size--)
    {
        xsp[i] = xsp[i] - '0';
        XS += xsp[i] * pow(0.5 ,i+1);
        i++;
    }
    return XS;
}

char *ZS_ten_to_two(char zsp[10],int size)
{
    int sum=0,j=0,y=0;
    char temp[33];
    char *op = (char *)malloc(33 * sizeof(char));
    int k = size-1;
    for(int i=0;i < size ;i++)
    {
        zsp[i] -= '0';
        sum += zsp[i] * pow(10 ,k);
        k--;
    }
    while(sum != 0)
    {

        if(sum%2)
        {
            temp[j] = '1';
            sum /= 2;
            j++;
        }
        else
        {
            temp[j] = '0';
            sum /= 2;
            j++;
        }
    }
    memset(op,0,33);
    for(;j>0;j--)
    {
        *(op+y) = temp[j-1];
        y++;
    }
    *(op+y) = '\0';
    return op;
}

char *XS_ten_to_two(char xsp[10],int size)
{
    int count=0;
    double sum=0;
    char *opp = (char *)malloc(33 * sizeof(char));
    for(int i=0;i < size ;i++)
    {
        xsp[i] -= '0';
        sum += xsp[i] * pow(0.1 ,i+1);
    }
    memset(opp,0,33);
    for(;;)
    {
        while(sum*2 < 1 && sum < 1)
        {
            sum *= 2;
            *(opp+count) = '0';
            count++;
            if(count >= 32)
            {
                *(opp+count) = '\0';
                return opp;
            }
        }
        sum *=2;
        sum--;
        *(opp+count) = '1';
        count++;
        if(sum == 0 || count >= 32)
        {
            *(opp+count) = '\0';
            return opp;
        }
    }
}

int main()
{
    char str[30];
    char zs[10]={0},xs[10]={0};
    char *token, *save_ptr;
    int i=0,p=0,q=0;
    int count,sz;
    double sum;
    gets(str);
    count = strlen(str);
    printf("%d\n",count);

    if(str[count-1] == 'B') sz=2;
    if(str[count-1] == 'D') sz=10;

    token = strtok_r(str, ".",&save_ptr);
    if(sz == 2)
    {
        while(*token != '\0')
        {
            zs[p] = *token;
            token++;
            p++;
        }
        zs[p] = '\0';

        token = strtok_r(NULL, "B",&save_ptr);
        while(*token != '\0')
        {
            xs[q] = *token;
            token++;
            q++;
        }
        xs[q] = '\0';

        sum = ZS_two_to_ten(zs,p) + XS_two_to_ten(xs,q);
        printf("%f",sum);
    }

    if(sz == 10)
    {
        while(*token != '\0')
        {
            zs[p] = *token;
            token++;
            p++;
        }

        zs[p] = '\0';

        token = strtok_r(NULL, "D",&save_ptr);
        while(*token != '\0')
        {
            xs[q] = *token;
            token++;
            q++;
        }
        xs[q] = '\0';

        char *zs_two,*xs_two;
        zs_two = ZS_ten_to_two(zs,p);
        printf("%s.",zs_two);
        free(zs_two);

        xs_two = XS_ten_to_two(xs,q);
        printf("%s",xs_two);
        free(xs_two);
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
