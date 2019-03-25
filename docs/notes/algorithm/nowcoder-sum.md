# 牛客网刷题列表

## 2018校招真题

### 1. 找出最接近的对称数字

##### 题目描述：

输入一个正整数的字符串，输出与它最接近的对称数字(不包括它自己)的字符串

注1: 输入字符串的长度最多不会超过18

注2: 当大于输入数字和小于输入数字的对称数字与输入数字距离相同时，取小的数字作为答案 

##### 输入描述:

输入为一个正整数的字符串

##### 输出描述:

输出为与输入数字最接近的对称数字(不包括输入本身)的字符串

##### 思路:

为了使对称数字最接近，应该尽量保持高位不变，然后修改低位数字从而达到对称。我一开始想到的是反序，从中间位置，后一半和前一半对称填写，但考虑到这几种特殊情况就不会了

考虑比较特殊的数字如：
10,100,1000
101,1001,10001
99,999,9999

如果给定的数本来就是对称的呢

如果输入的数字不对称，直接截取前半部分反序输出
对称的话，取几个数字试验一下：999输出1001，99999输出100001，696输出686，6996输出7007，69996输出 70007，62926输出62826，6299926输出6300036，9899989输出9900099，9999999输出10000001，101输出99，202输出212，808输出818，1001输出999，1020201输出1021201。

从中得出的结论：
最中间数不是0和9的时候，将其减1。
9：最中间数有2个及其以上的9（1991，19991是，191不是），对称部分+1，否则-1。注意全为9时，特殊处理，（eg：999输出1001），这里输出位数和输入位数不同
0：最中间数仅有1个0时，对称部分+1（606输出616而不是595）（11011输出11111），这里需要注意101是个例外（输出99），因为它输入输出位数不一样。然后所有的100...001（n位）对应输出99..9（n-1位）

##### 代码:
```
//这个代码通过了，想不到如此简单，还有点不明白为什么
#include <iostream>
#include <string>
using std::string;
using std::cin;
using std::cout;
using std::endl;

int main() {
    string str;
    cin >> str;
    int size = str.size();
    int mid = size>>1;
    for(int i=0; i<mid; ++i) {
        *(str.end()-1-i) = *(str.begin()+i);
    }
    cout << str << endl;
    return 0;
}
```

### 2. 大整数乘法

#### 2.1 题目描述

有两个用字符串表示的非常大的大整数,算出他们的乘积，也是用字符串表示。不能用系统自带的大整数类型。

**输入描述：**

空格分隔的两个字符串，代表输入的两个大整数

**输出描述：** 

输入的乘积，用字符串表示

**示例：**

输入：72106547548473106236 982161082972751393
输出：70820244829634538040848656466105986748

#### 2.2 解题思路
所谓大数相乘（Multiplication algorithm），就是指数字比较大，相乘的结果超出了基本类型的表示范围，所以这样的数不能够直接做乘法运算。目前大整数算法主要有一下几种思路：
1. 模拟小学乘法：最简单的乘法竖式手算的累加型；
2. 分治乘法：最简单的是Karatsuba乘法，一般化以后有Toom-Cook乘法；
3. 快速傅里叶变换FFT：（为了避免精度问题，可以改用快速数论变换FNTT），时间复杂度O(N lgN lglgN)。具体可参照Schönhage–Strassen algorithm；
4. 中国剩余定理：把每个数分解到一些互素的模上，然后每个同余方程对应乘起来就行；
5. Furer’s algorithm：在渐进意义上FNTT还快的算法。不过好像不太实用，本文就不作介绍了。大家可以参考维基百科Fürer’s algorithm
----
### 解法 

**1、模拟乘法手算累加**

![模拟乘法手算累加](/assets/images/nowcoder_sum/calculate_hand1.jpg)

如上图所示，乘法运算可以拆分为两步：

第一步：是将乘数与被乘数逐位相乘；

第二步：将逐位相乘得到的结果，对应加起来。

这类似于小学乘法中的“竖式运算”。
```
/**
 * 大数相乘 - 模拟乘法手算累加
 */
public static Integer[] bigNumberMultiply(int[] arr1， int[] arr2){
    ArrayList<Integer> result = new ArrayList<>();  //中间求和的结果

    //arr2 逐位与arr1相乘
    for(int i = arr2.length - 1; i >= 0; i--){
        int carry = 0;
        ArrayList<Integer> singleList = new ArrayList<>();

        //arr2 逐位单次乘法的结果
        for(int j = arr1.length - 1; j >= 0; j--){
            int r = arr2[i] * arr1[j] + carry;
            int digit = r % 10;
            carry = r / 10;

            singleList.add(digit);
        }
        if(carry != 0){
            singleList.add(carry);
        }

        int resultCarry = 0, count = 0;
        int k = 0;
        int l = 0;
        int offset = arr2.length - 1 - i;       //加法的偏移位
        ArrayList<Integer> middleResult = new ArrayList<>();

        //arr2每位乘法的结果与上一轮的求和结果相加，从右向左做加法并进位
        while (k < singleList.size() || l < result.size()) {
            int kv = 0, lv = 0;
            if (k < singleList.size() && count >= offset) {
                kv = singleList.get(k++);
            }
            if (l < result.size()) {
                lv = result.get(l++);
            }
            int sum = resultCarry + kv + lv;
            middleResult.add(sum % 10);     //相加结果从右向左（高位到低位）暂时存储，最后需要逆向输出
            resultCarry = sum / 10;
            count++;
        }
        if(resultCarry != 0){
            middleResult.add(resultCarry);
        }
        result.clear();
        result = middleResult;
    }

    Collections.reverse(result);    //逆向输出结果
    return result.toArray(new Integer[result.size()]);
}
```
思路虽然很简单，但是实现起来却很麻烦

**2、模拟乘法累加 - 改进**

简单来说，方法二就是先不算任何的进位，也就是说，将每一位相乘，相加的结果保存到同一个位置，到最后才计算进位。

例如：计算98×21，步骤如下：

![模拟乘法手算累加改进](/assets/images/nowcoder_sum/calculate_hand2.jpg)

这里唯一要注意的是进位问题，我们可以先不考虑进位，当所有位对应相加，产生结果之后，再考虑。从右向左依次累加，如果该位的数字大于10，那么我们用取余运算，在该位上只保留取余后的个位数，而将十位数进位（通过模运算得到）累加到高位便可，循环直到累加完毕。
```
/**
 * 大数相乘方法二
 */
public static int[] bigNumberMultiply2(int[] num1, int[] num2){
    // 分配一个空间，用来存储运算的结果，num1长的数 * num2长的数，结果不会超过num1+num2长
    int[] result = new int[num1.length + num2.length];

    // 先不考虑进位问题，根据竖式的乘法运算，num1的第i位与num2的第j位相乘，结果应该存放在结果的第i+j位上
    for (int i = 0; i < num1.length; i++){
        for (int j = 0; j < num2.length; j++){
            result[i + j + 1] += num1[i] * num2[j];	 // (因为进位的问题，最终放置到第i+j+1位)
        }
    }

    //单独处理进位
    for(int k = result.length-1; k > 0; k--){
        if(result[k] > 10){
            result[k - 1] += result[k] / 10;
            result[k] %= 10;
        }
    }
    return result;
}
```
>
！！注意：这里的进位有个大坑，因为result[]数组是从左到右记录相对位的和（还没有进位），而最后的进位是从右向左累加进位，这样的话，如果最高位，也就是最左侧那一位的累加结果需要进位的话，result[]数组就没有空间存放了。

而正好result[]数组的最后一位空置，不可能被占用，我们就相应地把num1的第i位与num2的第j位相乘，结果应该存放在结果的第i+j位上的这个结果往后顺移一位（放到第i+j+1位），最后从右向左累加时就多了一个空间。

**3、分治 - Karatsuba算法**

以上两种模拟乘法的手算累加型算法，他们都是模拟普通乘法的计算方式，时间复杂度都是O(n^2)，而这个Karatsuba算法，时间复杂度仅有 O(n^log2^3) 。

Karatsuba于1960年发明在 O(n^log2^3) 步骤内将两个n位数相乘的Karatsuba算法。它反证了安德雷·柯尔莫哥洛夫于1956年认为这个乘法需要 Ω(n^2) 步骤的猜想。

首先来看看这个算法是怎么进行计算的，见下图：

![分治法计算大整数相乘](/assets/images/nowcoder_sum/mulitiply_fenzhi.jpg)

图中显示了计算5678 * 1234的过程，首先是拆分成abcd四个部分，然后分别计算ac, bd, (a+b)*(c+d)，最后再用第三个算式的结果减去前面两个（其实得到的就是bc+ad，但是减少了乘法步骤），然后，计算式1后面加4个0，计算式2后面不加，计算式3后面加2个0，再把这三者相加，就是正确结果。

接下来，就来证明一下这个算法的正确性

![证明分治法的正确性](/assets/images/nowcoder_sum/karatsuba.jpg)

我们假设要相乘的两个数是x * y。我们可以把x，y写成：

x=a∗10^(n/2)+b

y=c∗10^(n/2)+d

这里的n是数字的位数。如果是偶数，则a和b都是n/2位的。如果n是奇数，则你可以让a是n/2+1位，b是n/2位。（例如a = 12，b = 34；a = 123，b = 45），那么x*y就可以换算为：

![分治法的步骤](/assets/images/nowcoder_sum/mulitiply_fenzhi.jpg)

注意最后一步，这个式子倒数第二步中的(ad + bc)，没必要另外进行两次乘法，可以使用((a+b)*(c+d) - ac - bd)来重复利用前面的两次乘积结果ac和bd。

也就是说，我们发现最终只需要计算三次乘法 a*c， b*d， (a+b)*(c+d) 以及六次加法。因此这样复杂度就变为

![时间复杂度](/assets/images/nowcoder_sum/fenzhi_buzhou.jpg)

对比之前的计算过程，结果已经呼之欲出了。这里唯一需要注意的两点就是：
>
1. (a*d + b*c)的计算为了防止两次乘法，应该使用之前的计算也就是第一幅图第四步的③-②-①
2. 这些乘法在算法里应该是递归实现的，数字很大时，先拆分，然后拆分出来的数字还是很大的话，就继续拆分，直到a * b已经是一个非常简单的小问题为之。这也是分治的思想。

![举例](/assets/images/nowcoder_sum/fenzhi_example.jpg)

```
/**
 * Karatsuba乘法
 */
public static long karatsuba(long num1, long num2){
    //递归终止条件
    if(num1 < 10 || num2 < 10) return num1 * num2;

    // 计算拆分长度
    int size1 = String.valueOf(num1).length();
    int size2 = String.valueOf(num2).length();
    int halfN = Math.max(size1, size2) / 2;

    /* 拆分为a, b, c, d */
    long a = Long.valueOf(String.valueOf(num1).substring(0, size1 - halfN));
    long b = Long.valueOf(String.valueOf(num1).substring(size1 - halfN));
    long c = Long.valueOf(String.valueOf(num2).substring(0, size2 - halfN));
    long d = Long.valueOf(String.valueOf(num2).substring(size2 - halfN));

    // 计算z2, z0, z1, 此处的乘法使用递归
    long z2 = karatsuba(a, c);
    long z0 = karatsuba(b, d);
    long z1 = karatsuba((a + b), (c + d)) - z0 - z2;

    return (long)(z2 * Math.pow(10, (2*halfN)) + z1 * Math.pow(10, halfN) + z0);
}
```
*总结：*
Karatsuba 算法是比较简单的递归乘法，把输入拆分成 2 部分，不过对于更大的数，可以把输入拆分成 3 部分甚至 4 部分。拆分为 3 部分时，可以使用下面的Toom-Cook 3-way 乘法，复杂度降低到 O(n^1.465)。拆分为 4 部分时，使用Toom-Cook 4-way 乘法，复杂度进一步下降到 O(n^1.404)。对于更大的数字，可以拆成 100 段，使用快速傅里叶变换FFT，复杂度接近线性，大约是 O(n^1.149)。可以看出，分割越大，时间复杂度就越低，但是所要计算的中间项以及合并最终结果的过程就会越复杂，开销会增加，因此分割点上升，对于公钥加密，暂时用不到太大的整数，所以使用 Karatsuba 就合适了，不用再去弄更复杂的递归乘法。

