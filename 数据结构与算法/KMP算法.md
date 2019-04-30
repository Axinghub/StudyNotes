---
typora-copy-images-to: image
typora-root-url: ..
---

# KMP算法

KMP算法是一个字符串匹配算法。

------

首先我们来看一个例子：现在有两个字符串T和P，问在字符串T中是否包含P

T：abcaabababaa

P：abab

通常情况下，我们的匹配算法是这样的：

1、取T字符串和P字符串首部对齐比较，比较字符串的对应位，如果相同，则比较下一位，如此下去，

2、当T中有一位和P中的一位不一致时，取P字符串和T字符串的下一位对齐进行比较，

3、如此下去，当P字符串和T字符串比较都相同，则存在

​      否则，不存在

![o_KMP_1](/数据结构与算法/image/o_KMP_1.gif)

有一种情况：

T：abbabbabbbbaa

P：abbabbb

这种情况下，我们在正常匹配的情况下，当到最后一位时，才发现不相等，如果要将数据右移一位再进行比计较，然后对前面已经比较过的字符，再次进行比较，这种情况下，是对前面已知数据的一种浪费，算法的效率会非常低

T：aaaaaaaaaaaaaaaaaaaab

P：aaaaaab

这种情况下，当比较到第七位发现不相等时，按照往常做法是将P串右移，从头开始比较，这也是一种对已知数据的一种浪费。

------

KMP算法就是为了解决上面的问题：

首先我们来了解一下前后缀：

前缀：指的是字符串的子串中从原串最前面开始的子串，如abcdef的前缀有：a,ab,abc,abcd,abcde

后缀：指的是字符串的子串中在原串最后面结尾的子串，如abcdef的后缀有：f,ef,def,cdef,bcdef

KMP算法需要计算出匹配串的所有前缀子串的<u>最长相等前后缀长度</u>，并将所有最长相等前后缀地长度往下移一个位置，前面置为-1（习惯这种做法），得前缀表：

![1556376530351](/数据结构与算法/image/1556376530351.png)



如下所示：

1、当匹配到第4个字符时，发现T[3]不等于P[3]，则在前缀表中找到p[3]对应的标识（1），将P数组下标为1的位置移到和T[3]相对的位置（即T[3]和P[1]相对的位置），继续比较（可以保证P[1]前面的元素和T中对应位置是相等的，这是前缀表的好处）

![1556377035038](/数据结构与算法/image/1556377035038.png)

2、当T[3]和P[1]不相等时，P[1]对应位0，将P[0]移到和T[3]对应的位置，再比较T[3]和P[0]，当T[3]和P[0]相等时比较T[4]和p[1]，重复这些步骤

------

代码实现

```java
   public class KMP {
    public static void main(String[] args) {
        String texts = "ABABCABAACABABCABAA";
        String patterns = "ABABCABAA";
        char[] text = texts.toCharArray();
        char[] pattern = patterns.toCharArray();

        int[] prefix = new int[9];
        int n = 9;
        //构造前缀表
        prefix_table(pattern,prefix,n);
//        for (int j = 0; j < prefix.length ; j++) {
//            System.out.println(prefix[j]);
//        }
        kmp_search(text,pattern,prefix);
    }

    //KMP搜索
    private static void kmp_search(char[] text, char[] pattern,int[] prefix) {
        //text[i]   length=m
        //pattern[j]   length=n
        int m = text.length;
        int n = pattern.length;
        int i = 0,j=0;
        while (i<m){
            if(j==n-1 && text[i]==pattern[j]){
                System.out.println(i-n+1);
                j = prefix[j];
            }
            if(text[i]==pattern[j]){
                i++;
                j++;
            }else {
                j = prefix[j];
                if(j==-1){
                    i++;
                    j=0;
                }
            }
        }
    }

    //计算前缀表
    public static void prefix_table(char pattern[],int prefix[],int n){
        prefix[0]=0;
        int len = 0;
        int i=1;
        while(i < n){
            if(pattern[i]==pattern[len]){
                len++;
                prefix[i]=len;
                i++;
            }else {
                if(len > 0){
                    len = prefix[len-1];
                }else {
                    prefix[i]=0;
                    i++;
                }
            }
        }
        //往后移动一位，第一位置-1
        for (int j = n-1; j > 0 ; j--) {
            prefix[j] = prefix [j-1];
        }
        prefix[0] = -1;
    }
}

```
```java
//取最长前后缀的长度
if(len > 0){
    len = prefix[len-1];
}
```

![1556603422239](/数据结构与算法/image/1556603422239.png)

