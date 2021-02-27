#### KMP

实现*KMP*字符串模式匹配算法,输入两行只包含小写字母的字符串,第一行输出一个整数，如果匹配成功，输出 A 在  B中首次出现的起始位置 ， 如果匹配不成功，输出

s是模式串(要被匹配的)，s1是匹配串，要先对匹配串进行faillink，其中flink[i] 表示s[0, i - 1]前后缀最长相等的，即长度为i的字符串前后缀最长匹配。

```c++
#include<bits/stdc++.h>
#define N 100002

using namespace std;

void faillink(char *p, int *flink)              //返回一个数组的失败链接值
{
    flink[0] = -1;
    int i = 1, j;
    while(p[i-1])
    {
        j = flink[i-1];
        while(j != -1 && p[j] != p[i-1])
            j = flink[j];
        flink[i] = j + 1;
        i++;
    }
}
int kmp_match(char *t, char *p, int *flink)            //返回以t开始，往后能匹配到p的位数，如果不行，返回-1
{
    int i = 0, j = 0, m =strlen(p);
    while(t[i])
    {
        while(j != -1 && p[j] != t[i])
            j = flink[j];
        if(j == m - 1) return (i - m + 1);
        i++;
        j++;
    }
    return -1;
}

int main()
{
    char s[N], s1[N];
    int flink[N];
    scanf("%s %s", s, s1);
    faillink(s1, flink);
    printf("%d\n", kmp_match(s, s1, flink));
    for(int i = 1; i < strlen(s1); i++)
        printf("%d ", flink[i]);
    printf("%d\n", flink[strlen(s1)]);
}

/*
ababa
ababa
0
0 0 1 2 3
*/
```

