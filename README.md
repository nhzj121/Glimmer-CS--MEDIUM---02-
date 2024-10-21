# CS-MEDIUM-02 定点数精确运算
## 课前准备--文件操作
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

int j=0;
//整数部分的十进制变二进制
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
//小数部分十进制变二进制
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
                    printf("转化为二进制：%.4f\n",sum);
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
2. 
