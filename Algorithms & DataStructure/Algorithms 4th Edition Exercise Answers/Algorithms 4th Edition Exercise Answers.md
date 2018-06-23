
[TOC]

# 算法（第四版）练习题答案

使用jar包：

- algs4
- junit-4.12
- hamcrest-core-1.3

## 第一章 基础

### 1.1 基础编程模型

***

#### 练习题

***
**1.1.1 给出以下表达式的值：**

a. (0 + 15) / 2
b. 2.0e-6 * 100000000.1
c. true && false || true && true

*Answer：*

a. 表达式默认为int，所以15 / 2取整得到结果7。
b. 表达式等价于2.0e-6 * 100000000.1d，默认为double，所以计算结果为200.0000002。
c. &&优先级高于||，表达式等价于(true && false) || (true && true), 计算结果为true。

***
**1.1.2 给出以下表达式的类型和值：**

a. (1 + 2.236) / 2
b. 1 + 2 + 3 + 4.0
c. 4.1 >= 4
d. 1 + 2 + "3"

*Answer：*

a. double，计算结果为1.618
b. double，计算结果为10.0
c. boolean，计算结果为true
d. String，计算结果为"33"

***
**1.1.3 编写一个程序，从命令行得到三个整数参数。如果它们都相等则打印equal，否则打印not equal。**

*Answer：*

```java
public void check3Int() {

    int a = StdIn.readInt();
    int b = StdIn.readInt();
    int c = StdIn.readInt();

    if (a == b && b == c) {
        StdOut.println("equal");
    } else {
        StdOut.println("not equal");
    }
}
```

***
**1.1.4 下列语句各有什么问题（如果有的话）？**

a. if (a > b) then c = 0;
b. if a > b { c = 0; }
c. if (a > b) c = 0;
d. if (a > b) c = 0 else b = 0;

*Answer：*

a. Java中无关键字then。
b. 条件必须用“()”包围。
c. 正确。
d. 赋值语句需用“;”结束。

***
**1.1.5 编写一段程序，如果double类型的变量x和y都严格位于0和1之间则打印true，否则打印false。**

*Answer：*

```java
public void check2double(double x, double y) {
    StdOut.println((x > 0 && x < 1) && (y > 0 && y < 1));
}
```

***
**1.1.6 下面这段程序会打印出什么？**

```java
int f = 0;
int g = 1;
for (int i = 0; i <= 15; i++) {
    StdOut.println(f);
    f = f + g;
    g = f - g;
}
```

*Answer：*

    0
    1
    1
    2
    3
    5
    8
    13
    21
    34
    55
    89
    144
    233
    377
    610

***
**1.1.7 分别给出以下代码段打印出的值**

a. 

```java
double t = 9.0;
while (Math.abs(t - 9.0 / t) > .001) {
    t = (9.0 / t + t) / 2.0;
}
StdOut.printf("%.5f\n", t);
```

b.

```java
int sum = 0;
for (int i = 1; i < 1000; i++) {
    for (int j = 0; j < i; j++) {
        sum++;
    }
}
StdOut.println(sum);
```

c.

```java
int sum = 0;
for (int i = 1; i < 1000; i *= 2) {
    for (int j = 0; j < 1000; j++) {
        sum++;
    }
}
StdOut.println(sum);
```

*Answer：*

a. 3.00009
b. 499500
c. 10000

***
**1.1.8 下列语句会打印出什么结果？给出解释。**

a. System.out.println('b');
b. System.out.println('b' + 'c');
c. System.out.println((char) ('a' + 4));

*Answer：*

a. b （直接打印char类型数据则直接输出该字符）
b. 197 （char类型数据相加会转为int值相加）
c. e （int类型强转为char类型，则输出该数值对应的字符）

***
**1.1.9 编写一段代码，将一个正整数N用二进制表示并转换为一个String类型的值。**

*Answer：*

```java
public void ex119(int n) {
    
    StringBuilder sb = new StringBuilder();

    for (int i = n; i > 0; i /= 2) {
        sb.insert(0, i % 2);
    }
    
    StdOut.println(sb.toString());
}
```

***
**1.1.10 下面这段代码有什么问题？**

```java
int[] a;
for (int i = 0; i < 10; i++)
    a[i] = i * i;
```

*Answer：*

没有使用new为数组分配内存。

***
**1.1.11 编写一段代码，打印出一个二维布尔数组的内容。其中，使用*表示真，空格表示假。打印出行号和列号。**

*Answer：*

```java
public void ex1111() {

    // 创建二维数组
    boolean[][] bools = new boolean[10][10];

    // 对二维数组进行赋值
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 10; j++) {
            bools[i][j] = StdRandom.bernoulli();
        }
    }
    
    // 打印列号（0-9）
    StdOut.print(" ");
    for (int k = 0; k < 10; k++) {
        StdOut.print(k);
    }
    StdOut.println();
    
    // 逐行打印
    for (int i = 0; i < 10; i++) {
        // 打印当前行号
        StdOut.print(i);
        // 打印当前行的布尔值
        for (int j = 0; j < 10; j++) {
            if (bools[i][j]) {
                StdOut.print("*");
            } else {
                StdOut.print(" ");
            }
        }
        // 打印换行
        StdOut.println();
    }
}
```

***
**1.1.12 以下代码会打印出什么结果？**

```java
int[] a = new int[10];
for (int i = 0; i < 10; i++) {
    a[i] = 9 - i;
}
for (int i = 0; i < 10; i++) {
    a[i] = a[a[i]];
}
for (int i = 0; i < 10; i++) {
    System.out.println(a[i]);
}
```

*Answer：*

    0
    1
    2
    3
    4
    4
    3
    2
    1
    0

***
**1.1.13 编写一段代码，打印出一个M行N列的二维数组的转置。**

*Answer：*

```java
public void ex1113(int m, int n) {

    // 以boolean型二维数组为例
    boolean[][] arr = new boolean[m][n];
    
    // 创建转置后的数组
    boolean[][] transp = new boolean[n][m];

    // 对数组进行赋值
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            arr[i][j] = StdRandom.bernoulli();
        }
    }

    // 进行转置
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            transp[i][j] = arr[j][i];
        }
    }
}
```

***
**1.1.14 编写一个静态方法lg()，接受一个整形参数N，返回不大于log2N（2是底数）的最大整数。不要使用Math库。**

*Answer：*

```java
public static int lg(int n) {

    int maxInt = 0;
    while (n != 1 && n != 0) {
        n /= 2;
        maxInt++;
    }
    StdOut.println(maxInt);
    return maxInt;
}
```

***
**1.1.15 编写一个静态方法histogram()，接受一个整形数组a[]和一个整数M为参数并返回一个大小为M的参数，其中第i各元素的值为整数i在参数数组中出现的次数。如果a[]中的值均在0到M-1之间，返回数组中的所有元素之和应该和a.length相等。**

*Answer：*

```java
public static Integer[] histogram(int[] arr, int m) {

    Integer[] result = new Integer[m];

    // 统计arr中各元素的数量
    Map<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < arr.length; i++) {
        if (!map.containsKey(arr[i])) {
            map.put(arr[i], 1);
        } else {
            map.put(arr[i], map.get(arr[i]) + 1);
        }
    }

    // 对数组result进行赋值
    for (int j = 0; j < result.length; j++) {
        Integer tmp = map.get(j);
        result[j] = (tmp == null ? null : tmp); 
    }
    
    return result;
}
```

***
**1.1.16 给出exR1(6)的返回值：**

```java
public static String exR1(int n) {
    if (n <= 0) {
        return "";
    }
    return exR1(n - 3) + n + exR1(n - 2) + n;
}
```

*Answer：*

    311361142246

***
**1.1.17 找出以下递归函数的问题：**

```java
public static String exR2(int n) {
    String s = exR2(n - 3) + n + exR2(n - 2) + n;
    if (n <= 0) {
        return "";
    }
    return s;
}
```

*Answer：*

上述代码的基准情况永远不会被访问，会造成StackOverflowError。基准情况应该放在开头。

***
**1.1.18 请看以下递归函数：**

```java
public static int mystery(int a, int b) {
    if (b == 0) {
        return 0;
    }
    if (b % 2 == 0) {
        return mystery(a + a, b / 2);
    }
    return mystery(a + a, b / 2) + a;
}
```

mystery(2, 25)和mystery(3, 11)的返回值是多少？给定正整数a和b，myster(a, b)计算结果是什么？将代码中的+替换为*并将`return 0`改为`return 1`，然后回答相同的问题。

*Answer：*

    50, 33
    ab
    33554432, 177147
    a^b

***
**1.1.19 在计算机上运行以下程序：**

```java
public class Fibonacci {
    public static long F(int n) {
        if (n == 0) {
            return 0;
        }
        if (n == 1) {
            return F(n - 1) + F(n - 2);
        }
    }
    public static void main(String[] args) {
        for (int n = 0; n < 100; n++) {
            StdOut.println(n + " " + F(n));
        }
    }
}
```

计算机用这段程序在一个小时之内能够得到F(n)结果最大n值是多少？开发F(n)的一个更好的实现，用数组保存已经计算过的值。

*Answer：*

```java
public static long[] F(int n) {
    
    long[] arr = new long[n];
    
    arr[0] = 0;
    arr[1] = 1;
    for (int i = 2; i < arr.length; i++) {
        arr[i] = arr[i - 1] + arr[i - 2];
    }
    return arr;
}
```

***
**1.1.20 编写一个递归的静态方法计算ln(N!)的值。**

*Answer：*

```java
public static double ex1120(int n) {
    
    if (n == 1) {
        return 0;
    }
    
    return Math.log(n) + ex1120(n - 1);
}
```

***
**1.1.21 编写一段程序，从标准输入按行读取数据u，其中每行都包含一个名字和两个整数。然后用printf()打印一张表格，每行的若干列数据包含名字、两个整数和第一个整数除以第二个整数的结果，精确到小数点后三位。**

*Answer：*

```java
public void ex1121() {
    
    List<String[]> list = new ArrayList<>();
    
    while (true) {
        String string = StdIn.readLine();
        // 设置结束输入的标志
        if (!string.equals("STOP")) {
            // 将输入的字符串拆分为字符串数组并储存
            list.add(string.split(","));
        } else {
            break;
        }
    }

    // 这里没有使用printf()
    StdOut.println("name\t" + "num1\t" + "num2\t" + "(num1 / num2)");
    for (int i = 0; i < list.size(); i++) {
        String[] tmp = list.get(i);
        StdOut.println(tmp[0] + "\t" + tmp[1] + "\t" + tmp[2] + "\t" + String.format("%.2f", (Double.parseDouble(tmp[1]) / Double.parseDouble(tmp[2]))));
    }   
}
```

***
**1.1.22 使用1.1.6.4节的rank()递归方法重新实现BinarySearch并跟组该方法的调用。每当该方法被调用时，打印出它的参数lo和hi并按照递归的深度缩进。**

*Answer：*

```java
public static int rank(int key, int[] a) {
    return rank(key, a, 0, a.length - 1, 0);
}

private static int rank(int key, int[] a, int lo, int hi, int depth) {

    // 设置基准情况
    if (lo > hi) {
        return -1;
    }

    // 打印递归深度
    StdOut.println("Recursive depth: " + depth + "->");

    // 打印lo、hi前的缩进，使用*表示
    for (int i = 0; i < depth; i++) {
        StdOut.print("*");
    }
    StdOut.println("lo = " + lo + " hi = " + hi);

    // 开始二分查找
    int mid = lo + (hi - lo) / 2;

    if (key < a[mid]) {
        return rank(key, a, lo, mid - 1, depth + 1);
    } else if (key > a[mid]) {
        return rank(key, a, mid + 1, hi, depth + 1);
    } else {
        return mid;
    }
}
```

***
**1.1.23 为BinarySearch的测试用例添加一个参数：+，打印出标准输入中不在白名单上的值；-，则打印出标准输入中在白名单上的值。**

*Answer：*

```java
public int binarySearch(int key, int[] arr) {
    return binarySearch(key, arr, 0, arr.length - 1);
}

private int binarySearch(int key, int[] arr, int lo, int hi) {

    if (lo > hi) {
        return -1;
    }
    int mid = lo + (hi - lo) / 2;

    if (key < arr[mid]) {
        return binarySearch(key, arr, lo, mid - 1);
    } else if (key > arr[mid]) {
        return binarySearch(key, arr, mid + 1, hi);
    } else {
        return mid;
    }
}

public void ex1123() {

    // 设置标记
    char symbol = '+';

    // 读取硬盘上的白名单
    String whitelistpath = "datafiles\\tinyW.txt";
    int[] whitelist = new In(new File(whitelistpath)).readAllInts();
    
    // 将白名单排序
    Arrays.sort(whitelist);

    // 输入待查整数
    int key = StdIn.readInt();

    // 获取查询结果
    int result = binarySearch(key, whitelist);

    // 判断并输出
    if (symbol == '+' && result == -1) {
        StdOut.println(key);
    }
    if (symbol == '-' && result != -1) {
        StdOut.println(key);
    }
}
```

***
**1.1.24 给出欧几里得算法计算105和24的最大公约数的过程中得到的一些列p和q的值。扩展该算法中的代码得到一个程序Euclid，从命令行接受两个参数，计算它们的最大公约数，并打印出每次调用递归方法时的两个参数。使用你的程序计算1111111和1234567的最大公约数。**

*Answer：*

```java
    public int euclid() {
        int p = StdIn.readInt();
        int q = StdIn.readInt();
        return euclid(p, q, 0);
    }
    
    private int euclid(int p, int q, int depth) {
        
        StdOut.println("Recursive depth: " + depth + " p = " + p + " q = " + q);
        
        if (p % q == 0) {
            return q;
        } else {
            return euclid(q, p % q, depth + 1);
        }
    }
```

- 1111111和1234567的最大公约数为1。
- 105和24的最大公约数为12;

***

#### 提高题

***
**1.1.26 将三个数排序。假设a、b、c和t都是同一种原始数字类型的变量。证明以下代码能够将a、b、c按照升序排列：**

```java
if (a > b) { t = a; a = b; b = t; }
if (a > c) { t = a; a = c; c = t; }
if (b > c) { t = b; b = c; c = t; }
```

*Answer：*

实际上每行语句都在进行数值的交换。
第一行结束后，a被交换为a、b两数中较小的；
第二行结束后，a被交换为a、c两数中较小的，此时a < b并且a < c；
第三行结束后，b被交换为b、c两数中较小的；

***
**1.1.27 二项分布。估计以下代码计算binomial(100, 50, 0.25)将会产生的递归调用次数：**

```java
public static double binomial(int n, int k, double p) {
    if (n == 0 && k == 0) return 1.0;
    if (n < 0 || k < 0) return 0.0;
    return (1.0 - p) * binomial(n - 1, k, p) + p * binomial(n - 1, k - 1, p);
}
```

将已经计算过的值保存在数组中并给出一个更好的实现。

*Answer：*

```java
/*
 *  该方法用于创建一个存储各种实验情况概率的二维数组。
 *  n为实验次数，k为事件发生的次数，p为事件发生的概率。
 *  数组中的每个元素代表：n次实验中，事件发生了k次的概率。
 */
public static double[][] createBinomial2DArray(int n, int k, double p) {
    
    // 创建二维数组用于存储每一个计算值
    double[][] array = new double[n + 1][k + 1];

    // 初始化数组的第一个元素，即0次实验，事件发生0次的概率为1。
    array[0][0] = 1.0;

    // 对代表“n次实验中事件发生0次”的元素进行赋值，即二维数组中的第一列k=0进行赋值
    for (int i = 1; i <= n; i++) {
        array[i][0] = array[i - 1][0] * (1 - p);
    }

    // 逐行对二维数组的剩余部分进行赋值
    for (int i = 1; i <= n; i++) {
        // 由于事件发生次数不会超过实验次数，所以限制条件j<=i
        for (int j = 1; j <= i && j <= k; j++) {
            // 这里使用推论：P(n,k,p)=(1-p)*P(n-1,k,p)+p*P(n-1,k-1,p)
            array[i][j] = (1 - p) * array[i - 1][j] + p * array[i - 1][j - 1];
        }
    }
    return array;
}
```

***
**1.1.28 删除重复元素。修改BinarySearch类中的测试用例来删去排序之后白名单中的所有重复元素。**

*Answer：*

```java
public Integer[] ex1128() {
    
    // 声明数组
    Integer[] result = null;
    
    // 读取硬盘上的白名单
    String whitelistpath = "datafiles\\tinyW.txt";
    int[] whitelist = new In(new File(whitelistpath)).readAllInts();
    
    // 使用Set来处理重复元素
    Set<Integer> set = new HashSet<>();
    
    // 循环遍历白名单数组并存入Set
    for (int i = 0; i < whitelist.length; i++) {
        set.add(whitelist[i]);
    }
    
    // 将Set转为数组
    result = set.toArray(new Integer[0]);
    
    // 对result数组进行排序
    Arrays.sort(result);
    
    return result;
}
```
***
**1.1.29 等值键。为BinarySearch类添加一个静态方法rank()，它接受一个键和一个整型有序数组（可能存在重复键）作为参数并返回数组中小于该键的元素数量，以及一个类似的方法count()来返回数组中等于该键的元素数量。注意：如果i和j分别是rank(key, a)和count(key, a)的返回值，那么a[i..i+j-1]就是数组中所有和key相等的元素。**

*Answer：*

二分查找方法：

```java
public static int binarySearch(int key, int[] arr) {
    return binarySearch(key, arr, 0, arr.length - 1);
}

private static int binarySearch(int key, int[] arr, int lo, int hi) {

    if (lo > hi) {
        return -1;
    }
    int mid = lo + (hi - lo) / 2;

    if (key < arr[mid]) {
        return binarySearch(key, arr, lo, mid - 1);
    } else if (key > arr[mid]) {
        return binarySearch(key, arr, mid + 1, hi);
    } else {
        return mid;
    }
}
```

rank()方法：该方法返回数组arr中小于key的元素的数量

```java
public static int rank(int key, int[] arr) {

    int index = binarySearch(key, arr);

    // 如果在arr中没有找到该key，遍历数组确定key在arr中的位置
    if (index == -1) {

        // 如果0号元素大于key
        if (arr[0] > key) {
            // 则arr中不存在比key小的元素
            return 0;
        }

        // 如果key比最大的元素大，则arr的全部元素均小于key
        if (arr[arr.length - 1] < key) {
            return arr.length;
        }

        // 判断key在arr中的位置，并返回小于key的元素的个数
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] < key && arr[i + 1] > key) {
                return i + 1;
            }
        }
    }

    // 如果找到了该key，由于数组可能存在重复元素，所以需要找到等于key的最小序号的元素
    while (index > 0) {
        // 对find逐次减1，并与key进行比较
        if (arr[index - 1] < key) {
            // 如果左侧元素小于key，直接跳出循环
            break;
        }
        // 如果不小于，意味着左侧元素与key相等，index--，进行下一次比较
        index--;
    }
    return index;
}
```

count()方法：该方法返回数组中等于该键的元素的数量

```java
public static int count(int key, int[] arr) {

    int index = binarySearch(key, arr);

    int num = 0;

    // 如果没有在数组中找到key，则返回0
    if (index == -1) {
        return num;
    } else {
        // 如果找到key，则令num = 1
        num = 1;
    }

    // 向该元素的两侧进行查找
    int left = index - 1, right = index + 1;

    // 查找左侧
    while (left >= 0) {
        if (arr[left--] < key) {
            // 如果左侧元素小于key，直接跳出循环
            break;
        } else {
            // 否则，说明左侧元素等于key，num++
            num++;
        }
    }

    // 查找右侧
    while (right <= arr.length - 1) {
        if (arr[right++] > key) {
            // 如果右侧元素大于key，直接跳出循环
            break;
        } else {
            // 否则，说明右侧元素等于key，num++
            num++;
        }
    }
    return num;
}
```

***
**1.1.30 数组练习。编写一段程序，创建一个N * N的布尔数组a[][]。其中当i和j互质时（没有相同因子），a[i][j]为true，否则为false。**

*Answer：*

>
公因数只有1的两个非零自然数，叫做互质数。

```java
//欧几里得算法：
private int euclid(int i, int j) {
    
    if (i % j == 0) {
        return j;
    }
    
    return euclid(j, i % j);
}

//判断i、j是否互质：
private boolean relatprime(int i, int j) {
    
    boolean mark = false;
    
    // 如果两个数中存在0，返回false
    if (i == 0 || j == 0) {
        return mark;
    } 
    
    // 如果两个数都等于1，这种情况不用欧几里得算法判断，设置mark为true。
    // 其余情况使用欧几里得算法计算两个数的最大公约数。
    if (i == 1 && j == 1) {
        mark = true;
    } else if (euclid(i, j) == 1) {
        mark = true;
    }
    
    return mark;        
}

//填充数组：
public boolean[][] ex1130(int n) {
    
    boolean[][] arr = new boolean[n][n];
    
    // 遍历数组进行赋值
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            arr[i][j] = relatprime(i, j);
        }
    }
    
    return arr;
}
```

***
**1.1.31 随机连接。编写一段程序，从命令行接受一个整数N和double值p（0到1之间）作为参数，在一个圆上话初大小为0.05且间距相等的N个点，然后将每对点按照概率p用灰线连接。**

*Answer：*

```java
/*
 * 画圆
 */
private static void drawCircle(double x, double y, double r) {
    StdDraw.setXscale(0, 2 * x);
    StdDraw.setYscale(0, 2 * y);
    StdDraw.setPenRadius(0.003);
    StdDraw.setPenColor(StdDraw.BOOK_LIGHT_BLUE);
    StdDraw.circle(x, y, r);
}

/*
 * 在圆上画点
 */
private static double[][] drawPoints(double x0, double y0, double r, int N) {
    double[][] points = new double[N][2];
    StdDraw.setPenRadius(0.005);
    StdDraw.setPenColor(StdDraw.BOOK_RED);
    for (int idx = 0; idx < N; ++idx) {
        // 计算点的坐标值
        double x = x0 + r * Math.cos(2 * Math.PI * idx / N);
        double y = y0 + r * Math.sin(2 * Math.PI * idx / N);
        StdDraw.point(x, y);
        points[idx][0] = x;
        points[idx][1] = y;
    }
    return points;
}

/*
 * 将点进行随机连接
 */
private static void randomLinkPoints(double[][] points, double p) {
    StdDraw.setPenRadius(0.002);
    StdDraw.setPenColor(StdDraw.LIGHT_GRAY);
    int length = points.length;
    for (int i = 0; i < length; ++i) {
        for (int j = 0; j < length; ++j) {
            if (true == StdRandom.bernoulli(p)) {
                StdDraw.line(points[i][0], points[i][1], points[j][0], points[j][1]);
            }
        }
    }
}

// 随机连接函数：
public static void randomLink() {

    // 从控制台读取n、p
    int n = StdIn.readInt();
    double p = StdIn.readDouble();

    // 设置圆的圆心和半径
    double x = 10.0;
    double y = 10.0;
    double r = 9.0;
    
    // 画园
    drawCircle(x, y, r);
    
    // 在圆上进行描点
    double[][] points = drawPoints(x, y, r, n);
    randomLinkPoints(points, p);
}
```

***
**1.1.32 直方图.假设标准输入流中有一系列double值。编写一段程序，从命令行接受一个整数N和两个double值l和r。将(l, r)分为N段并使用StdDraw话初输入流中的值落入每段的数量的直方图。**

*Answer：*

```java
public static void dataHistogram(int N, double l, double r, double[] arr) {
    int length = arr.length;
    int[] statistics = new int[N];
    double interval = (r - l) / N;
    // 统计数据分布
    for(int i = 0; i < length; ++i) {
        double remain = arr[i] - l;
        int idx = (int)(remain / interval);
        ++statistics[idx];
    }
    // 查找统计结果中最大值，用于绘制直方图时计算柱状图高时
    double max = statistics[0];
    for(int i = 1; i < statistics.length; ++i) {
        if(max < statistics[i])
            max = statistics[i];
    }
    // 绘制直方图
    StdDraw.setXscale(l, r);
    StdDraw.setPenColor(StdDraw.BOOK_BLUE);
    double x0 = l + interval / 2.0;
    for(int i = 0; i < statistics.length; ++i) {
        double x = x0 + i * interval;
        double y = statistics[i] / (max + 1) / 2.0;
        double hw = 0.99 * interval / 2.0;
        double hh = y;
        StdDraw.filledRectangle(x, y, hw, hh);
    }
}

public static void main(String[] args) {
   
    In in = new In(new File("datafiles\\largeW.txt"));
    double[] whiteList = in.readAllDoubles();
    double min = Double.POSITIVE_INFINITY;
    double max = Double.NEGATIVE_INFINITY;
    for(int i = 0; i < whiteList.length; ++i) {
        if(min > whiteList[i])
            min = whiteList[i];
        if(max < whiteList[i])
            max = whiteList[i];
    }
    // 从控制台读取应该将数据分割的段数
    StdOut.print("将数据分割的段数：");
    int N = StdIn.readInt();
    dataHistogram(N, min, max + 10.0, whiteList);
}
```

***
**1.1.33 矩阵库。编写一个Matrix并实现以下API：**

- static double dot(double[] x, double[] y) - - - 向量点乘
- static double[][] mult(double[][] a, double[][] b) - - - 矩阵和矩阵之积
- static double[][] transpose(double[][] a) - - - 转置矩阵
- static double[] mult(double[][] a, double[] x) - - - 矩阵和向量之积
- static double[] mult(double[] y, double[][] a) - - - 向量和矩阵之积

*Answer：*

```java
// 向量点乘：
public static double dot(double[] x, double[] y) {

    // 点乘必须是向量x的长度等于向量y的长度才能运算
    if (x.length != y.length) {
        System.exit(1);
    }

    double result = 0d;

    for (int i = 0; i < x.length; i++) {
        result += x[i] * y[i];
    }

    return result;
}

//矩阵和矩阵之积：
public static double[][] multiple(double[][] a, double[][] b) {

    // 只有矩阵a的列数等于矩阵b的行数时，相乘才有意义
    if (a[0].length != b.length) {
        System.exit(1);
    }

    double[][] result = new double[a.length][b[0].length];

    for (int i = 0; i < a.length; i++) {
        for (int j = 0; j < b[0].length; j++) {
            for (int k = 0; k < a[0].length; k++) {
                result[i][j] += a[i][k] * b[k][j];
            }
        }
    }

    return result;
}

// 转置矩阵：
public static double[][] transpose(double[][] a) {

    int m = a.length;
    int n = a[0].length;

    double[][] result = new double[n][m];

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            result[i][j] = a[j][i];
        }
    }

    return result;
}

// 矩阵和向量之积：
public static double[] mult(double[][] a, double[] x) {
    // 只有矩阵的列数和向量的长度相等时才有意义
    if (a[0].length != x.length) {
        System.exit(1);
    }

    double[] result = new double[a.length];

    for (int i = 0; i < a.length; i++) {
        for (int j = 0; j < x.length; j++) {
            result[i] += a[i][j] * x[j];
        }
    }

    return result;
}

// 向量与矩阵之积：
public static double[] mult(double[] y, double[][] a) {
    // 只有向量的列数和矩阵的行数相等时才有意义
    if (y.length != a.length) {
        System.exit(1);
    }

    double[] result = new double[a[0].length];

    for (int i = 0; i < a[0].length; i++) {
        for (int j = 0; j < y.length; j++) {
            result[i] += y[j] * a[j][i];
        }
    }

    return result;
}

// 用于打印向量和矩阵的方法：
private static void printVector(double[] arr) {
    for (int i = 0; i < arr.length; i++) {
        System.out.print(arr[i] + " ");
    }
    System.out.println();
}

private static void printMatrix(double[][] arr) {
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr[0].length; j++) {
            System.out.print(arr[i][j] + " ");
        }
        System.out.println();
    }
}

// 测试用例：
@Test
public void test_matrix() {
    
    // 2 x 3 矩阵
    double[][] a = { { 1, 2, 3 }, { 4, 5, 6 } };
    // 3 x 2矩阵
    double[][] b = { { 1, 2 }, { 3, 4 }, { 5, 6 } };
    // 向量x
    double[] x = { 2, 3, 4 };
    // 向量y
    double[] y = { 3, 4, 5 };
    
    //------------测试-------------       
    System.out.println("测试向量点乘开始");
    System.out.println(dot(x, y));
    System.out.println("测试向量点乘结束");
    
    System.out.println();
    
    System.out.println("测试矩阵乘矩阵开始");
    double[][] matMulMat = multiple(a, b);
    printMatrix(matMulMat);
    System.out.println("测试矩阵乘矩阵结束");
    
    System.out.println();
    
    System.out.println("测试矩阵转置开始");
    printMatrix(transpose(a));
    System.out.println("测试矩阵转置结束");
    
    System.out.println();
    
    System.out.println("测试矩阵乘向量开始");
    double[] matMulVec = mult(a, x);
    printVector(matMulVec);
    System.out.println("测试矩阵乘向量结束");
    
    System.out.println();
    
    System.out.println("测试向量乘矩阵开始");
    double[] vecMulMat = mult(y, b);
    printVector(vecMulMat);
    System.out.println("测试向量乘矩阵结束");
}
```

***
**1.1.34 过滤。（a）以下哪些任务需要（在数组中，比如）保存标准输入中的所有值？（b）哪些可以被实现为一个过滤器且仅使用固定数量的变量和固定大小的数组（和N无关）？在每个问题中，输入都来自于标准输入且含有N个0到1的实数。**

- 打印出最大和最小的数            （b）                
- 打印出所有数的中位数            （a）              
- 打印出第k小的数，k小于100       （b）              
- 打印出所有数的平方和            （b）             
- 打印出N数的平均值               （b）            
- 打印出大于平均值的数的百分比     （a）            
- 将N个数按照升序打印             （a）            
- 将N个数按照随机顺序打印          （a）  

***

#### 实验题

***
**1.1.35 模拟掷骰子。以下代码能够计算每种两个骰子之和的准确概率分布：**

```java
int SIDES = 6;
double[] dist = new double[2 * SIDES + 1];
for (int i = 1; i <= SIDES; i++) {
    for (int j = 1; j <= SIDES; j++) {
        dist[i + j] += 1.0;
    }
}

for (int k = 2; k <= 2 * SIDES; k++) {
    dist[k] /= 36.0;
}
```

**dist[i]的值就是两个骰子之和为i的概率。用实验模拟N次掷骰子，并在计算两个1到6之间的随机整数之和时记录每个值出现频率以验证它们的概率。N要多大才能够保证你的经验数据和准确数据吻合程度达到小数点后三位？**

*Answer：*

```java
// 实验题1.1.35
public class TossDice {

    // 定义骰子的面数
    private final static int SIDES = 6;

    // 定义用来存储理论概率的数组
    private static double[] dist = new double[13];

    // 初始化dist数组
    static {
        for (int i = 1; i <= SIDES; i++) {
            for (int j = 1; j < SIDES; j++) {
                dist[i + j] += 1.0;
            }
        }
        for (int k = 2; k <= 12; k++) {
            dist[k] /= 36.0;
        }
    }

    // 比较偏差的方法
    public boolean testErr(double[] arr) {
        // 循环比较两个数组的对应元素值
        for (int i = 2; i <= 12; i++) {
            // 如果有一项偏差超过0.0001，则返回false
            if (Math.abs(dist[i] - arr[i]) >= 0.001) {
                return false;
            }
        }
        return true;
    }

    // 模拟掷骰子的过程
    public double[] tossSimulate(long n) {
        // 用于存储各个和出现次数的数组
        double[] exp = new double[13];

        // 统计次数
        for (int i = 1; i <= n; i++) {
            exp[StdRandom.uniform(1, 7) + StdRandom.uniform(1, 7)] += 1.0;
        }

        // 计算频率
        for (int j = 2; j <= 12; j++) {
            exp[j] /= n;
        }

        return exp;
    }

    // 实验方法，n为初始实验次数
    public long experment(long n) {

        while (true) {
            
            System.out.println("本次实验次数为：" + n);
            
            // 经过n次实验后，如果达到要求，跳出循环
            if (testErr(tossSimulate(n))) {
                break;
            } else {
                // 否则实验次数乘10倍
                n *= 10;
            }
        }
        return n;
    }
    
    @Test
    public void testExp() {
        System.out.print("实验次数为：" + experment(100));
    }
}
```

***
**1.1.36 乱序检查。通过实验检查表1.1.10中的乱序代码是否能够产生预期的效果。编写一个程序ShuffleTest，接受命令行参数M和N，将大小为M的数组打乱N次且再每次打乱之前都将数组重新初始化为a[i] = i。打印一个MxM的表格，对于所有的列j，行i表示的是i在打乱后落到j的位置的次数。数组中的所有元素的值都应该接近N/M。**

*Answer：*

```java
public class ShuffleTest {

    public int[][] shuffle(int m, int n) {

        // 创建大小为m的数组
        int[] arr = new int[m];

        // 创建用于记录结果的二维数组
        int[][] result = new int[m][m];

        // 进行n次打乱
        for (int i = 1; i <= n; i++) {

            // 打乱前对数组进行初始化
            for (int j = 0; j < arr.length; j++) {
                arr[j] = j;
            }

            // 对数组进行打乱
            StdRandom.shuffle(arr);

            // 记录每次打乱后的结果
            record(result, arr);
        }

        return result;

    }

    // 记录打乱结果
    private void record(int[][] result, int[] arr) {

        for (int j = 0; j < arr.length; j++) {
            result[arr[j]][j]++;
        }
    }

    // 打印二维数组
    public void printArray(int[][] result) {
        for (int i = 0; i < result.length; i++) {
            for (int j = 0; j < result[0].length; j++) {
                StdOut.print(result[i][j] + "\t");
            }
            StdOut.println();
        }
    }

    @Test
    public void testShuffle() {
        int m = StdIn.readInt();
        int n = StdIn.readInt();
        printArray(shuffle(m, n));
    }
}
```

***
**1.1.37 糟糕的打乱。假设在我们的乱序代码中你选择的是一个0到N-1而非i
到N-1之间的随机整数。证明得到的结果并非均匀地分布在N!种可能性之间。用上一题种的测试检验这个版本。**

>
使用 0~N-1 的随机数会导致每次交换的数字可能相同。

*Answer：*

```java
// 实验题1.1.37
public class BadShuffleTest {

    public int[][] shuffle(int m, int n) {

        // 创建大小为m的数组
        int[] arr = new int[m];

        // 创建用于记录结果的二维数组
        int[][] result = new int[m][m];

        // 进行n次打乱
        for (int i = 1; i <= n; i++) {

            // 打乱前对数组进行初始化
            for (int j = 0; j < arr.length; j++) {
                arr[j] = j;
            }

            // 对数组进行打乱，不使用StdRandom的标准打乱
            shuffle(arr);

            // 记录每次打乱后的结果
            record(result, arr);
        }

        return result;

    }

    // 直接在原有StdRandom的源码上修改
    private void shuffle(int[] a) {
        int n = a.length;
        for (int i = 0; i < n; i++) {
            // 此时返回的是 0 ~ n-1 之间的随机整数
            // 而不是i ~ n-1之间的随机整数
            int r = StdRandom.uniform(n);
            int temp = a[i];
            a[i] = a[r];
            a[r] = temp;
        }
    }

    // 记录打乱结果
    private void record(int[][] result, int[] arr) {

        for (int j = 0; j < arr.length; j++) {
            result[arr[j]][j]++;
        }
    }

    // 打印二维数组
    public void printArray(int[][] result) {
        for (int i = 0; i < result.length; i++) {
            for (int j = 0; j < result[0].length; j++) {
                StdOut.print(result[i][j] + "\t");
            }
            StdOut.println();
        }
    }

    @Test
    public void testShuffle() {
        int m = StdIn.readInt();
        int n = StdIn.readInt();
        printArray(shuffle(m, n));
    }
}
```

***
**1.1.38 二分查找与暴力查找。根据1.1.10.4节给出的暴力查找法编写一个程序BruteForceSearch，在你的计算机上比较它和BinarySearch处理largeW.txt和largeT.txt所需的时间。**

*Answer：*

```java
public class RankTest {
    
    // 二分查找的循环实现
    private int binarySearch(int key, int[] arr, int lo, int hi) {

        while (lo <= hi) {
            int mid = (lo + hi) / 2;
            if (key < arr[mid]) {
                hi = mid - 1;
            } else if (key > arr[mid]) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }
    
    public int binarySearch(int key, int[] arr) {
        return binarySearch(key, arr, 0, arr.length - 1);
    }

    // 暴力查找
    public int bruteForceSearch(int key, int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            if (arr[i] == key) {
                return i;
            } 
        }
        return -1;
    }
    
    @Test
    public void testSearch() {
        // 测试largeW，查找值846942
        int[] arr = new In(new File("datafiles\\largeW.txt")).readAllInts();
        Arrays.sort(arr);
        
        StdOut.println("二分查找测试largeW开始");
        long start1 = System.currentTimeMillis();
        binarySearch(846942, arr);
        long end1 = System.currentTimeMillis();
        StdOut.println("使用二分查找在largeW.txt中查找846942耗时：" + (end1 - start1) + "ms");
        StdOut.println("二分查找测试largeW结束");
        StdOut.println();
        
        StdOut.println("暴力查找测试largeW开始");
        long start2 = System.currentTimeMillis();
        bruteForceSearch(846942, arr);
        long end2 = System.currentTimeMillis();
        StdOut.println("使用暴力查找在largeW.txt中查找846942耗时：" + (end2 - start2) + "ms");
        StdOut.println("暴力查找测试largeW结束");
        StdOut.println();
        
        // 测试largeT，查找值17672230
        int[] arr2 = new In(new File("datafiles\\largeT.txt")).readAllInts();
        Arrays.sort(arr2);
        
        StdOut.println("二分查找测试largeT开始");
        long start3 = System.currentTimeMillis();
        binarySearch(17672230, arr2);
        long end3 = System.currentTimeMillis();
        StdOut.println("使用二分查找在largeT.txt中查找912684耗时：" + (end3 - start3) + "ms");
        StdOut.println("二分查找测试largeT结束");
        StdOut.println();
        
        StdOut.println("暴力查找测试largeT开始");
        long start4 = System.currentTimeMillis();
        bruteForceSearch(17672230, arr2);
        long end4 = System.currentTimeMillis();
        StdOut.println("使用暴力查找在largeT.txt中查找912684耗时：" + (end4 - start4) + "ms");
        StdOut.println("暴力查找测试largeT结束");
        StdOut.println();
    }
}
```

***
**1.1.39 随机匹配。编写一个使用BinarySearch的程序，它从命令行接受一个整型参数T，并会分别针对$N=10^3、10^4、10^5$和$10^6$ 将以下实验运行T遍：生成两个大小为N的随机6位正整数数组并找出同时存在于两个数组中的整数的数量。打印一个表格，对于每个N，给出T次实验中该数量的平均值。**

*Answer：*

```java
// 实验题1.1.39
public class RandomMatch {

    // 二分查找的循环实现
    private int binarySearch(int key, int[] arr, int lo, int hi) {

        while (lo <= hi) {
            int mid = (lo + hi) / 2;
            if (key < arr[mid]) {
                hi = mid - 1;
            } else if (key > arr[mid]) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }

    public int binarySearch(int key, int[] arr) {
        return binarySearch(key, arr, 0, arr.length - 1);
    }

    // 生成6位数的随机数组
    public int[] randomArray(int length) {
        int[] arr = new int[length];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = StdRandom.uniform(100000, 1000000);
        }
        return arr;
    }

    // 对两个数组进行比较，统计两个数组中的相同元素个数
    public int countSameElements(int[] arr1, int[] arr2) {
        int count = 0;
        for (int i = 0; i < arr1.length; i++) {
            if (binarySearch(arr1[i], arr2) != -1) {
                count++;
            }
        }
        return count;
    }

    // 随机匹配，t为实验次数
    public void randomMatch(int t) {

        for (int i = 1000; i <= 1000000; i *= 10) {

            int count = 0;

            // 循环进行实验
            for (int j = 0; j < t; j++) {

                int[] arr1 = randomArray(i);
                int[] arr2 = randomArray(i);

                count += countSameElements(arr1, arr2);
            }

            StdOut.println("N = " + i + "时，" + t + "次实验的平均值为：" + (double) (count / t));
        }
    }
    
    @Test
    public void testRandomMatch() {
        randomMatch(100);
    }
}
```

### 1.2 数据抽象

***

#### 练习题

***
**1.2.1 编写一个Point2D的用例，从命令行接受一个整数N。在单位正方形中生成N个随机点，然后计算两点之间的最近距离。**

*Answer：*

```java
public static void shortestDistance() {
    int N = StdIn.readInt();
    Point2D[] arr = new Point2D[N];
    double min = Math.sqrt(2d);
    
    // 生成N个点的坐标，并描点
    for (int i = 0; i < arr.length; i++) {
        double x = StdRandom.uniform();
        double y = StdRandom.uniform();
        
        arr[i] = new Point2D(x, y);
        arr[i].draw();
    }
    
    // 计算最小距离，对N个点进行两两比较
    for (int i = 0; i < arr.length; i++) {
        for (int j = 0; j < arr.length; j++) {
            if (i != j) {
                double tmp = arr[i].distanceTo(arr[j]);
                min = (tmp < min ? tmp : min);
            }
        }
    }
    StdOut.println("min = " + min);
}
```

***
**1.2.2 编写一个Interval1D用例，从命令行接受一个整数N。从标准输入中读取N个间隔（每个间隔由一对double值定义）并打印出所有相交的间隔对。**

*Answer：*

```java
public void testInterval1D() {

    // 从控制台读取Interval的个数
    int N = StdIn.readInt();
    // 创建保存Interval的数组
    Interval1D[] intervals = new Interval1D[N];

    // 对数组中的每个Interval进行初始化，min和max从控制台读取
    for (int i = 0; i < N; i++) {
        double min = StdIn.readDouble();
        double max = StdIn.readDouble();
        intervals[i] = new Interval1D(min, max);
    }

    // 判断两个Interval是否相交，无重复比较
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            if (intervals[i].intersects(intervals[j])) {
                StdOut.println("Interval[" + i + "] : " + intervals[i]
                        + " intersects with interval[" + j + "] : " + intervals[j]);
            }
        }
    }
}
```

***
**1.2.3 编写一个Interval2D的用例，从命令行接受参数N、min和max。生成N个随机的2D间隔，其宽度和高度均匀地分布在单位正方形的min和max之间。用StdDraw画出它们并打印相交的间隔对的数量以及有包含关系的间隔对的数量。**

*Answer：*

```java
// 用于测试两个Interval2D是否包含
private static boolean isContain(Interval1D inter1x, Interval1D inter1y, Interval1D inter2x, Interval1D inter2y) {

    // 读取两个Interval1D的min和max
    double inter1xmin = inter1x.min();
    double inter1xmax = inter1x.max();
    double inter1ymin = inter1y.min();
    double inter1ymax = inter1y.max();

    double inter2xmin = inter2x.min();
    double inter2xmax = inter2x.max();
    double inter2ymin = inter2y.min();
    double inter2ymax = inter2y.max();

    // 进行判断
    if (inter1xmin <= inter2xmin && inter1xmax >= inter2xmax && inter1ymin <= inter2ymin
            && inter1ymax >= inter2ymax) {

        return true;
    }
    return false;

}

public static void testInterval2D() {

    // 从控制台读取N、min和max
    int N = StdIn.readInt();
    double min = StdIn.readDouble();
    double max = StdIn.readDouble();

    if (min >= max || min >= 1 || max >= 1) {
        throw new IllegalArgumentException();
    }

    // 创建用于存放Interval2D的数组
    Interval2D[] intervals = new Interval2D[N];
    
    // 创建用于存放组成每个Interval2D的Interval1D数组
    Interval1D[] intervalx = new Interval1D[N];
    Interval1D[] intervaly = new Interval1D[N];

    // 用于记录两个Interval是否相交的变量
    int countIntersect = 0;

    // 用于记录两个Interval是否存在包含关系的变量
    int countContain = 0;

    // 对数组进行初始化
    for (int i = 0; i < N; i++) {
        while (intervals[i] == null) {
            try {
                intervalx[i] = new Interval1D(StdRandom.uniform(min, max), StdRandom.uniform(min, max));
                intervaly[i] = new Interval1D(StdRandom.uniform(min, max), StdRandom.uniform(min, max));
                intervals[i] = new Interval2D(intervalx[i], intervaly[i]);
            } catch (IllegalArgumentException e) {
                intervals[i] = null;
            }
        }
    }

    // 设置画笔磅数和颜色
    StdDraw.setPenRadius(0.005);
    StdDraw.setPenColor(StdDraw.DARK_GRAY);

    // 绘制数组中的每个元素
    for (int i = 0; i < N; i++) {
        intervals[i].draw();
    }

    // 判断两个Interval是否相交，无重复比较
    for (int i = 0; i < N; i++) {
        for (int j = i + 1; j < N; j++) {
            if (intervals[i].intersects(intervals[j])) {
                StdOut.println("Interval[" + i + "] : " + intervals[i] + " intersects with interval[" + j + "] : "
                        + intervals[j]);
                countIntersect++;
            }
        }
    }

    // 判断两个Interval是否存在包含关系，需重复比较
    for (int i = 0; i < N; i++) {
        for (int j = 0; j < N; j++) {
            if (i != j && isContain(intervalx[i], intervaly[i], intervalx[j], intervaly[j])) {
                StdOut.println("Interval[" + i + "] : " + intervals[i] + " contains interval[" + j + "] : "
                        + intervals[j]);
                countContain++;
            }
        }
    }
    StdOut.println("相交的间隔对数：" + countIntersect + "，包含的间隔对数：" + countContain);
}

```

***
**1.2.4 以下这段代码会打印什么？**

```java
String string1 = "hello";
String string2 = string1;
string1 = "world";
StdOut.println(string1);
StdOut.println(string2);
```

*Answer：*

```java
world
hello
```

***
**1.2.5 以下这段代码会打印什么？**

```java
String s = "Hello World";
s.toUpperCase();
s.substring(6, 11);
StdOut.println(s);
```

*Answer：*

```java
Hello World
```

***
**1.2.6 如果字符串s中的字符循环移动任意位置之后能够得到另一个字符串t，那么s就被称为t的回环变位（circular rotation）。例如，ACTGACG就是TGACGAC的一个回环变位，反之亦然。判定这个条件在基因组序列的研究中是很重要的。编写一个程序检查两个给定的字符串s和t是否互为回环变位。**

*Answer：*

```java
    public boolean ex1126(String s1, String s2) {
        return (s1.length() == s2.length() && (s1 + s1).indexOf(s2) != -1);
    }
```

***
**1.2.7 以下递归的返回值是什么？**

```java
public static String mystery(String s) {
    int N = s.length();
    if (N <= 1) {
        return s;
    }
    String a = s.substring(0, N / 2);
    String b = s.substring(N / 2, N);
    return mystery(b) + mystery(a);
}
```

*Answer：*

逆序输出s。

***
**1.2.8 设a[]和b[]均为长数百万的整型数组。以下代码的作用是什么？有效吗？**
 
```java   
    int[] t = a;
    a = b;
    b = t;
```

*Answer：*

这段代码会将它们交换。
效率很高，因为这段代码复制的是引用而不是具体的数百万个元素。

***
**1.2.9 修改BinarySearch，使用Counter统计在有查找中被检查的键的总数并在查找全部结束后打印该值。**

*Answer：*

```java
public int count(int key, int[] arr, Counter counter) {

    int index = binarySearch(key, arr);

    // 如果没有在数组中找到key，则返回0
    if (index == -1) {
        return 0;
    } else {
        // 如果找到key
        counter.increment();
    }

    // 向该元素的两侧进行查找
    int left = index - 1, right = index + 1;

    // 查找左侧
    while (left >= 0) {
        if (arr[left--] < key) {
            // 如果左侧元素小于key，直接跳出循环
            break;
        } else {
            // 否则，说明左侧元素等于key
            counter.increment();
        }
    }

    // 查找右侧
    while (right <= arr.length - 1) {
        if (arr[right++] > key) {
            // 如果右侧元素大于key，直接跳出循环
            break;
        } else {
            // 否则，说明右侧元素等于key
            counter.increment();
        }
    }
    return counter.tally();
}

// 二分查找
public int binarySearch(int key, int[] arr) {
    return binarySearch(key, arr, 0, arr.length);
}

private int binarySearch(int key, int[] arr, int lo, int hi) {

    if (lo > hi) {
        return -1;
    }

    int mid = lo + (hi - lo) / 2;

    if (key < arr[mid]) {
        return binarySearch(key, arr, lo, mid - 1);
    } else if (key > arr[mid]) {
        return binarySearch(key, arr, mid + 1, hi);
    } else {
        return mid;
    }
}
```

***
**1.1.20 编写一个VisualCounter，支持加一和减一操作。它的构造函数接受两个参数N和max，其中N指定了操作的最大次数，max指定了计数器的最大绝对值。作为副作用，用图像显示每次计数器变化后的值。**

*Answer：*

```java
package pers.tavish.ex.chapter1.dataabstraction.exercises;

import edu.princeton.cs.algs4.StdDraw;
import edu.princeton.cs.algs4.StdRandom;

// 练习题1.2.10
public class VisualCounter {

    // 最大操作次数和计数器的最大绝对值
    private final int N;
    private final int max;

    // 记录当前操作次数的值
    private int opTimes;

    // 进行计数的值
    private int value;

    // 初始化计数器
    public VisualCounter(int N, int max) {

        // 最大操作次数不能小于0，计数器的最大绝对值不能小于等于0
        if (N < 0 || max <= 0) {
            throw new IllegalArgumentException();
        }

        this.N = N;
        this.max = max;

        opTimes = 0;
        value = 0;
    }

    public void increment() {
        if (isDepeleted()) {
            System.out.println("Counter Depeleted, Try a New One.");
            return;
        }

        opTimes++;

        if (isMax() && isPositive()) {
            System.out.println("Counter Has Reached Positive Limit, No More Increment.");
            return;
        } else {
            value++;
        }
    }

    public void decrement() {
        if (isDepeleted()) {
            System.out.println("Counter Depeleted, Try a New One.");
            return;
        }

        opTimes++;

        if (isMax() && !isPositive()) {
            System.out.println("Counter Has Reached Negative Limit, No More Decrement.");
            return;
        } else {
            value--;
        }
    }

    // 描点的方法
    public void draw() {
        StdDraw.point(opTimes, value);
    }

    // 返回计数值
    public int tally() {
        return value;
    }

    // 判断当前value是否是正数
    private boolean isPositive() {
        return value > 0;
    }

    // 判断当前value的绝对值是否等于max
    private boolean isMax() {
        return Math.abs(value) == max;
    }

    // 判断操作次数是否等于最大操作次数
    private boolean isDepeleted() {
        return N == opTimes;
    }

    // 测试用主函数
    public static void main(String[] args) {

        int max = 3;
        int count = 100;

        StdDraw.setXscale(0, count + 1);
        StdDraw.setYscale(-max, max);
        StdDraw.line(0, 0, count + 1, 0);

        VisualCounter vcCounter = new VisualCounter(count, max);

        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.setPenRadius(0.01);

        vcCounter.draw();
        for (int i = 0; i < count; i++) {
            if (StdRandom.bernoulli()) {
                vcCounter.increment();
            } else {
                vcCounter.decrement();
            }
            vcCounter.draw();
        }
    }
}
```

***
**1.2.1 根据Date的API实现一个SmartDate类型，在日期非法时抛出一个异常。**

直接使用原Date类作为SmartDate类：

*Answer：*

```java

    public class SmartDate implements Comparable<SmartDate> {

        // 每个月的天数，DAYS[O]不使用
        private static final int[] DAYS = { 0, 31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31 };

        private final int month; // 月份，1-12之间
        private final int day; // 日期 1-DAYS[month]之间
        private final int year; // 年份

        // 使用三个int值初始化
        public SmartDate(int month, int day, int year) {
            if (!isValid(month, day, year))
                throw new IllegalArgumentException("Invalid date");
            this.month = month;
            this.day = day;
            this.year = year;
        }

        // 使用字符串格式初始化
        public SmartDate(String date) {
            String[] fields = date.split("/");
            if (fields.length != 3) {
                throw new IllegalArgumentException("Invalid date");
            }
            month = Integer.parseInt(fields[0]);
            day = Integer.parseInt(fields[1]);
            year = Integer.parseInt(fields[2]);
            if (!isValid(month, day, year))
                throw new IllegalArgumentException("Invalid date");
        }

        // 返回月份
        public int month() {
            return month;
        }

        // 返回日期
        public int day() {
            return day;
        }

        // 返回年份
        public int year() {
            return year;
        }

        // 判断日期是否合法
        private static boolean isValid(int m, int d, int y) {
            if (m < 1 || m > 12)
                return false;
            if (d < 1 || d > DAYS[m])
                return false;
            if (m == 2 && d == 29 && !isLeapYear(y))
                return false;
            return true;
        }

        // 判断是否为闰年
        private static boolean isLeapYear(int y) {
            if (y % 400 == 0)
                return true;
            if (y % 100 == 0)
                return false;
            return y % 4 == 0;
        }

        // 返回当前日期的下一天
        public SmartDate next() {
            if (isValid(month, day + 1, year))
                return new SmartDate(month, day + 1, year);
            else if (isValid(month + 1, 1, year))
                return new SmartDate(month + 1, 1, year);
            else
                return new SmartDate(1, 1, year + 1);
        }

        // 判断当前日期是否在某个日期之后
        public boolean isAfter(SmartDate that) {
            return compareTo(that) > 0;
        }

        // 判断当前日期是否在某个日期之前
        public boolean isBefore(SmartDate that) {
            return compareTo(that) < 0;
        }

        // 按照日历比较两个日期
        @Override
        public int compareTo(SmartDate that) {
            if (this.year < that.year)
                return -1;
            if (this.year > that.year)
                return +1;
            if (this.month < that.month)
                return -1;
            if (this.month > that.month)
                return +1;
            if (this.day < that.day)
                return -1;
            if (this.day > that.day)
                return +1;
            return 0;
        }

        // 覆盖toString()方法
        @Override
        public String toString() {
            return month + "/" + day + "/" + year;
        }

        // 覆盖equals方法，判断两个日期是否相等
        @Override
        public boolean equals(Object other) {
            if (other == this)
                return true;
            if (other == null)
                return false;
            if (other.getClass() != this.getClass())
                return false;
            SmartDate that = (SmartDate) other;
            return (this.month == that.month) && (this.day == that.day) && (this.year == that.year);
        }

        // 覆盖hashCode()方法
        @Override
        public int hashCode() {
            int hash = 17;
            hash = 31 * hash + month;
            hash = 31 * hash + day;
            hash = 31 * hash + year;
            return hash;
        }

        public static void main(String[] args) {
            StdOut.println(new SmartDate("4/4/2018").dayOfWeek());
        }
    }
```

***
**1.2.12 为SmartDate添加一个方法dayOfWeek()，为日期中每周的日返回Monday、Tuesday、Wednesday、Thursday、Friday、Saturday或Sunday中的适当值。**

*Answer：*

```java
public String dayOfWeek() {

    int monthTmp = month;
    int yearTmp = year;
    
    if (month < 3) {
        monthTmp += 12;
        --yearTmp;
    }
    int week = (day + 1 + 2 * monthTmp + 3 * (monthTmp + 1) / 5 + yearTmp + (yearTmp >> 2) - yearTmp / 100 + yearTmp / 400) % 7;

    String weekstr = "";
    
    switch (week) {
    case 0:
        weekstr = "Sunday";
        break;
    case 1:
        weekstr = "Monday";
        break;
    case 2:
        weekstr = "Tuesday";
        break;
    case 3:
        weekstr = "Wednesday";
        break;
    case 4:
        weekstr = "Thursday";
        break;
    case 5:
        weekstr = "Friday";
        break;
    case 6:
        weekstr = "Saturday";
        break;
    }
    return weekstr;
}
```

***
**1.2.13 用我们对Date的实现作为模板实现Transaction类型。**

Transaction类的API：（compareTo()、equals()、equals()方法暂不实现）

- Transaction(String who, Date when, double amount)
- Transaction(String transaction)
- String who()
- Date when()
- double amount()
- String toString()
- boolean equals(Object that)
- int compareTo(Transaction that)
- int hashCode()

*Answer：*

```java

    public class Transaction {
        private final String who;
        private final Date when;
        private final double amount;

        public Transaction(String who, Date when, double amount) {
            this.who = who;
            this.when = when;
            this.amount = amount;
        }

        public Transaction(String transaction) {
            String[] fields = transaction.split(" ");
            if (fields.length != 3) {
                throw new IllegalArgumentException();
            }
            this.who = fields[0];
            this.when = new Date(fields[1]);
            this.amount = Double.parseDouble(fields[2]);
        }

        public String who() {
            return who;
        }

        public Date when() {
            return when;
        }

        public double amount() {
            return amount;
        }

        @Override
        public String toString() {
            return who + "-" + when.toString() + "-" + amount;
        }
    }
```

***
**1.2.14 用我们对Date中的equals方法的实现作为模板实现Transaction中的equals()方法。**

*Answer：*

```java
@Override
public int hashCode() {
    final int prime = 31;
    int result = 1;
    long temp;
    temp = Double.doubleToLongBits(amount);
    result = prime * result + (int) (temp ^ (temp >>> 32));
    result = prime * result + when.hashCode();
    result = prime * result + who.hashCode();
    return result;
}

@Override
public boolean equals(Object obj) {
    if (this == obj)
        return true;
    if (obj == null)
        return false;
    if (getClass() != obj.getClass())
        return false;
    Transaction other = (Transaction) obj;
    if (Double.doubleToLongBits(amount) != Double.doubleToLongBits(other.amount))
        return false;
    if (when == null) {
        if (other.when != null)
            return false;
    } else if (!when.equals(other.when))
        return false;
    if (who == null) {
        if (other.who != null)
            return false;
    } else if (!who.equals(other.who))
        return false;
    return true;
}
```

***

#### 提高题

***
**1.2.15 文件输入。基于String的split()方法实现In中的静态方法readInts()。**

*Answer：*

```java
public static int[] readInts(String name) {
    In in = new In(name);
    String[] words = in.readAll().split("\\s+");
    int[] ints = new int[words.length];
    for (int i = 0; i < words.length; i++) {
        ints[i] = Integer.parseInt(words[i]);
    }
    return ints;
}
```

***
**1.2.16 有理数。为有理数实现一个不可变数据类型Rational，支持加减乘除操作。**

Rational的API：

- Rational(long numerator, long denominator)
- Rational plus(Rational b)
- Rational minus(Rational b)
- Rational times(Rational b)
- Rational divides(Rational b)
- boolean equals(Rational that)
- int hashCode()
- String toString()

**使用欧几里得算法来保证分子和分母没有公因子。编写一个测试用例检测你实现的所有方法。**

*Answer：*

```java

    public class Rational {
        private final long numerator;
        private final long denominator;

        public Rational(long numerator, long denominator) {
            if (denominator == 0) {
                throw new IllegalArgumentException();
            }

            long gcd = euclid(Math.abs(numerator), Math.abs(denominator));

            this.numerator = numerator / gcd;
            this.denominator = denominator / gcd;
        }

        public Rational plus(Rational b) {
            long ntmp = numerator * b.denominator + denominator * b.numerator;
            long dtmp = denominator * b.denominator;
            return new Rational(ntmp, dtmp);
        }

        public Rational minus(Rational b) {
            long ntmp = numerator * b.denominator - denominator * b.numerator;
            long dtmp = denominator * b.denominator;
            return new Rational(ntmp, dtmp);
        }

        public Rational times(Rational b) {
            long ntmp = numerator * b.numerator;
            long dtmp = denominator * b.denominator;
            return new Rational(ntmp, dtmp);
        }

        public Rational divides(Rational b) {
            if (b.numerator == 0) {
                throw new IllegalArgumentException();
            }
            long ntmp = numerator * b.denominator;
            long dtmp = denominator * b.numerator;
            return new Rational(ntmp, dtmp);
        }

        // 欧几里得算法
        private long euclid(long numerator, long denominator) {
            if (numerator % denominator == 0) {
                return denominator;
            } else {
                return euclid(denominator, numerator % denominator);
            }
        }

        @Override
        public String toString() {
            return "Rational [" + numerator + "/" + denominator + "]";
        }

        @Override
        public int hashCode() {
            final int prime = 31;
            int result = 1;
            result = prime * result + (int) (denominator ^ (denominator >>> 32));
            result = prime * result + (int) (numerator ^ (numerator >>> 32));
            return result;
        }

        @Override
        public boolean equals(Object obj) {
            if (this == obj)
                return true;
            if (obj == null)
                return false;
            if (getClass() != obj.getClass())
                return false;
            Rational other = (Rational) obj;
            if (denominator != other.denominator)
                return false;
            if (numerator != other.numerator)
                return false;
            return true;
        }
        
        public static void main(String[] args) {
            Rational r1 = new Rational(-1, 7);
            Rational r2 = new Rational(2, 5);
            
            Rational r3 = new Rational(-1, 7);
            
            Rational resultplus = r1.plus(r2);
            Rational resultMinus = r1.minus(r2);
            Rational resultTimes = r1.times(r2);
            Rational resultDivides = r1.divides(r2);
            
            StdOut.println(resultplus);
            StdOut.println(resultMinus);
            StdOut.println(resultTimes);
            StdOut.println(resultDivides);
            
            StdOut.println(r1.equals(r1));
            StdOut.println(r1.equals(r2));
            StdOut.println(r1.equals(r3));
        }
    }
```

***
**1.2.17 有理数实现的健壮性。在Rational的开发中使用断言来防止溢出。**

*Answer：* 略。

***
**1.2.18 累加器的方差。以下代码为Accumulator类添加了var()和stddev()方法，它们计算了addDataValue()方法的参数的方差和标准差，验证这段代码。**

```java
public class Accumulator {
    private double m;
    private double s;
    private int N;

    public void addDataValue(double x) {
        N++;
        s = s + 1.0 * (N - 1) / N * (x - m) * (x - m);
        m = m + (x - m) / N;
    }

    public double mean() {
        return m;
    }

    public double var() {
        return s / (N - 1);
    }

    public double stddev() {
        return Math.sqrt(this.var());
    }
}
```

与直接对所有数据的平方求和的方法相比较，这种实现能够更好地避免四舍五入产生的误差。

*Answer：*

略。

***
**1.2.19 字符串解析。为联系1.2.13中实现的Date和Transaction类型编写能够解析字符串数据的构造函数。它接受一个String参数指定的初始值，格式如下所示：**

类型：

- Date：由斜杠分隔的整数，示例：5/22/1939
- Transaction：由空白字符分隔，Turing 5/22/1939 11.99

*Answer：*

```java
public Date(String date) {
    String[] fields = date.split("/");
    if (fields.length != 3) {
        throw new IllegalArgumentException("Invalid date");
    }
    month = Integer.parseInt(fields[0]);
    day   = Integer.parseInt(fields[1]);
    year  = Integer.parseInt(fields[2]);
    if (!isValid(month, day, year)) throw new IllegalArgumentException("Invalid date");
}

public Transaction(String transaction) {
    String[] fields = transaction.split(" ");
    if (fields.length != 3) {
        throw new IllegalArgumentException();
    }
    this.who = fields[0];
    this.when = new Date(fields[1]);
    this.amount = Double.parseDouble(fields[2]);
}
```

### 1.3 背包、队列和栈

***

#### 练习题

***
**1.3.1 为FixedCapacityStackOfString添加一个方法isFull()。**

*Answer：*

```java
public boolean isFull() {
    return N == a.length;
}
```

***
**1.3.2 给定以下输入，java Stack的输出是什么？**

输入：

    it was - the best - of times - - - it was - the - - 

*Answer：*
    
输入不等于“-”时则将字符串压入栈中；如果等于，则弹出栈顶元素。

```java
    public void ex132() {
        Stack<String> stack = new Stack<String>();
        while (!StdIn.isEmpty()) {
            String item = StdIn.readString();
            if (!item.equals("-"))
                stack.push(item);
            else if (!stack.isEmpty())
                StdOut.print(stack.pop() + " ");
        }
        StdOut.println("(" + stack.size() + " left on stack)");
    }
```

输出：

```java
    was best times of the was the it
```

***
**1.3.3 假设某个用例程序会进行一系列入栈和出栈的混合栈操作。入栈操作会将整数0到9按照顺序压入栈；出栈操作会打印出返回值。下面哪种序列是不可能产生的？**

- a. 4 3 2 1 0 9 8 7 6 5
- b. 4 6 8 7 5 3 2 9 0 1
- c. 2 5 6 7 4 8 9 3 1 0
- d. 4 3 2 1 0 5 6 7 8 9
- e. 1 2 3 4 5 6 9 8 7 0
- f. &nbsp;0 4 6 5 3 8 1 7 2 9
- g. 1 4 7 9 8 6 5 3 0 2
- h. 2 1 4 3 6 5 8 7 9 0

*Answer：*

    b、f、g

- b：在弹出1前不可能先弹出0
- f：在弹出2前不可能先弹出1
- g：在弹出2前不可能先弹出0

***
**1.3.4 编写一个Stack的用例Parentheses，从标准输入中读取一个文本流并使用栈判定其中的括号是否配对完整。例如对于`[()]{}{[()()]()}`应该打印true，对于`[(])`则打印false。**

*Answer：*

```java
public boolean parentheses(String s) {
    Stack<Character> stack = new Stack<>();
    for (int i = 0; i < s.length(); i++) {
        if (s.charAt(i) == '(') {
            stack.push('(');
        }
        if (s.charAt(i) == '{') {
            stack.push('{');
        }
        if (s.charAt(i) == '[') {
            stack.push('[');
        }
        if (s.charAt(i) == ')') {
            if (stack.isEmpty()) {
                return false;
            }
            if (stack.pop() != '(') {
                return false;
            }
        } else if (s.charAt(i) == '}') {
            if (stack.isEmpty()) {
                return false;
            }
            if (stack.pop() != '{') {
                return false;
            }
        } else if (s.charAt(i) == ']') {
            if (stack.isEmpty()) {
                return false;
            }
            if (stack.pop() != '[') {
                return false;
            }
        }
    }
    return stack.isEmpty();
}
```

***
**1.3.5 当N为20时下面这段代码会打印什么？从较高的抽象层次描述给定正整数N时这段代码的行为。**

```java
Stack<Integer> stack = new Stack<>();
while (N > 0) {
    stack.push(N % 2);
    N = N / 2;
}
for (int d : stack) {
    StdOut.print(d);
}
StdOut.println();
```

*Answer：*

```java
110010
```

上述程序打印N的二进制表示。

***
**1.3.6 下面这段的代码对队列q进行了什么操作？**

```java
Stack<String> stack = new Stack<>();
while (!q.isEmpty()) {
    stack.push(q.dequeue());
}
while (!stack.isEmpty()) {
    q.enqueue(stack.pop());
}
```

*Answer：*

将原队列的元素进行逆序排列。

***
**1.3.7 为Stack添加一个方法peek()，返回栈中最近添加的元素（而不弹出它）。**

*Answer：*

```java
public Item peek() {
    if (isEmpty()) {
        throw new NoSuchElementException("Stack underflow");
    } 
    return first.item;
}
```

***
**1.3.8 给定以下输入，给出DoublingStackOfStrings的数组的内容和大小。**

输入：

    it was - the best - of times - - - it was - the - - 

*Answer：*

DoublingStackOfStrings类：

```java
public class DoublingStackOfStrings implements Iterable<String> {
    private String[] a;
    private int N;

    public DoublingStackOfStrings() {
        a = new String[2];
        N = 0;
    }

    public boolean isEmpty() {
        return (N == 0);
    }

    private void resize(int capacity) {
        String[] temp = new String[capacity];
        for (int i = 0; i < N; i++) {
            temp[i] = a[i];
        }
        a = temp;
    }

    public void push(String item) {
        if (N == a.length) {
            resize(2 * a.length);
        }
        a[N++] = item;
    }

    public String pop() {
        if (isEmpty()) {
            throw new RuntimeException("Stack underflow error");
        }
        String item = a[--N];
        a[N] = null;
        if (N > 0 && N == a.length / 4) {
            resize(a.length / 2);
        }
        return item;
    }

    public Iterator<String> iterator() {
        return new ReverseArrayIterator();
    }

    // an iterator, doesn't implement remove() since it's optional
    private class ReverseArrayIterator implements Iterator<String> {
        private int i = N;

        public boolean hasNext() {
            return i > 0;
        }

        public void remove() {
            throw new UnsupportedOperationException();
        }

        public String next() {
            if (!hasNext())
                throw new NoSuchElementException();
            return a[--i];
        }
    }

    public static void main(String[] args) {
        DoublingStackOfStrings s = new DoublingStackOfStrings();
        while (!StdIn.isEmpty()) {
            String item = StdIn.readString();
            if (!item.equals("-")) {
                s.push(item);
            } else if (s.isEmpty()) {
                StdOut.println("BAD INPUT");
            } else
                StdOut.print(s.pop() + " ");
        }
    }
}
```

输出结果：

    was best times of the was the it 

***
**1.3.9 编写一段程序，从标准输入得到一个缺少左括号的表达式并打印出补全括号之后的中序表达式。**

例如给定输入：

    1 + 2 ) * 3 - 4 ) * 5 - 6 ) ) )

你的程序应该输出：

    ( ( 1 + 2 ) * ( ( 3 - 4 ) * ( 5 - 6 ) ) )

*Answer：*

```java
public void ex139() {
    // 符号栈
    Stack<String> ops = new Stack<>();
    // 数值栈，使用泛型String
    Stack<String> vals = new Stack<>();

    while (!StdIn.isEmpty()) {
        String s = StdIn.readString();
        if (s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/")) {
            ops.push(s);
        } else if (s.equals(")")) {
            // 遇到右括号，弹出两个操作数和一个运算符，进行字符串链接，而后作为一个字符串（整体）重新压入操作数栈
            String tmp = ") " + vals.pop() + " " + ops.pop() + " " + vals.pop() + " " + "(";
            vals.push(tmp);
        } else {
            vals.push(s);
        }
    }

    // 结果是逆序，需要调整顺序
    String tmp = vals.pop();
    String result = "";
    for (int i = tmp.length() - 1; i >= 0; i--) {
        result += tmp.charAt(i);
    }

    StdOut.println(result);
}
```

***
**1.3.10 编写一个过滤器InfixToPostfix，将算术表达式由中序表达式转为后序表达式。**

示例：

    ( 2 + ( ( 3 + 4 ) * ( 5 * 6 ) ) )
    -- >
    2 3 4 + 5 6 * * +

*Answer：*

思路：

- 遇到数字直接打印，或顺序保存到字符串中；
- 遇到左括号“（”直接跳过；
- 遇到运算符就压入栈顶；
- 遇到“）”时，就将弹出一个运算符，打印出来，或者接到字符串后面；

代码如下：

```java
public void infixtoPostfix() {
    Stack<String> stack = new Stack<>();
    while (!StdIn.isEmpty()) {
        String s = StdIn.readString();
        if (s.equals("+") || s.equals("-") || s.equals("*") || s.equals("/")) {
            // 如果是运算符，则压入栈
            stack.push(s);
        } else if (s.equals(")")) {
            // 如果是右括号，则弹出一个运算符
            StdOut.print(stack.pop() + " ");
        } else if (s.equals("(")) {
            // 如果是左括号，则忽略
        } else {
            // 如果是数字，则直接打印
            StdOut.print(s + " ");
        }
    }
    StdOut.println();
}
```

***
**1.3.11 编写一段程序EvaluatePostfix，从标准输入中得到一个后序表达式，求值并打印结果。**

*Answer：*

```java
public double evaluatePostfix(String postfix) {

    Stack<Double> vals = new Stack<>();

    for (int i = 0; i < postfix.length(); i++) {
        char c = postfix.charAt(i);
        switch (c) {
        case '+':
            vals.push(vals.pop() + vals.pop());
            break;
        case '-':
            vals.push(vals.pop() - vals.pop());
            break;
        case '*':
            vals.push(vals.pop() * vals.pop());
            break;
        case '/':
            vals.push(vals.pop() / vals.pop());
            break;
        default:
            vals.push(Double.parseDouble(c + "d"));
            break;
        }
    }
    return vals.pop();
}
```

***
**1.3.12 编写一个可迭代的Stack用例，它含有一个静态的copy()方法，接受一个字符串的栈作为参数并返回该栈的一个副本。**

*Answer：*

```java
public static <Item> Stack<Item> copy(Stack<Item> stack) {
    @SuppressWarnings("unchecked")
    Item[] tmp = (Item[]) new Object[stack.size()];

    Iterator<Item> iterator = stack.iterator();

    // 临时存放数据的数组
    for (int i = 0; i < tmp.length; i++) {
        tmp[i] = iterator.next();
    }

    // 使用数组对新的Stack进行赋值
    Stack<Item> newStack = new Stack<>();
    for (int i = tmp.length - 1; i >= 0; i--) {
        newStack.push(tmp[i]);
    }
    return newStack;
}
```

***
**1.3.13 假设某个用例程序会进行一系列入列和出列的混合队列操作。入列操作会将整数0到9按顺序插入队列；出列操作会打印出返回值。下面哪种序列是不可能产生的？**

- a. 0 1 2 3 4 5 6 7 8 9
- b. 4 6 8 7 5 3 2 9 0 1
- c. 2 5 6 7 4 8 9 3 1 0
- d. 4 3 2 1 0 5 6 7 8 9 

*Answer：*

    b、c、d

队列是先进先出结构，出列的顺序只能与入列的顺序一致，所以b、c、d不可能。

***
**1.3.14 编写一个类ResizingArrayQueueOfString，使用定长数组实现队列的抽象，然后扩展实现，使用调整数组的方法突破大小的限制。**

*Answer：*

```java
// 练习题1.3.14
public class ResizingArrayQueueOfString {
    // 用于存储数据的数组
    private String[] arr;
    // 队列头index
    private int head;
    // 队列尾index
    private int tail;
    // 队列中元素个数
    private int N;

    public ResizingArrayQueueOfString(int cap) {
        if (cap <= 0) {
            throw new IllegalArgumentException();
        }
        // 创建队列时初始化head和tail指向-1
        arr = new String[cap];
        this.head = -1;
        this.tail = -1;
    }

    // 返回元素个数是否为0
    public boolean isEmpty() {
        return N == 0;
    }

    // 返回元素个数
    public int size() {
        return N;
    }

    // 返回tail是否指向数组末尾
    public boolean isFull() {
        return tail == arr.length - 1;
    }

    // 入列
    public void enqueue(String s) {
        // 第一次入列时，将数据存入arr[0]，手动调整head和tail指向0
        if (head == -1 && tail == -1) {
            arr[0] = s;
            head = 0;
            tail = 0;
            N++;
            return;
        }

        // 如果tail指向数组末尾，数组大小调整为原来的2倍
        if (isFull()) {
            resize(arr.length * 2);
        }
        
        // tail向前移动并存储数据，元素个数加一
        arr[++tail] = s;
        N++;
    }

    // 出列
    public String dequeue() {
        // 如果队列中没有元素，则抛出异常
        if (isEmpty()) {
            throw new NoSuchElementException("No Elemenet in queue");
        }

        // 如果元素个数小于数组的四分之一，数组大小调整为原来的一半
        if (N <= arr.length / 4) {
            resize(arr.length / 2);
        }

        // 返回head指向的数据，将原数组元素至为null，并向前移动，同时元素个数减一
        String tmp = arr[head];
        arr[head++] = null;
        N--;
        return tmp;
    }

    @Override
    public String toString() {

        if (N == 0) {
            return "Head--> [] <--Tail" + ", size = " + N + ", length(arr) : " + arr.length;
        }

        StringBuffer sb = new StringBuffer();
        sb.append("Head--> [");
        for (int i = head; i < tail; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.append(arr[tail] + "] <--Tail");
        return sb.toString() + ", size = " + N + ", length(arr) : " + arr.length;
    }

    // 调整数组大小
    private void resize(int max) {
        // 创建临时数组
        String[] tmp = new String[max];
        // 对临时数组进行赋值
        for (int i = head, j = 0; i <= tail; i++, j++) {
            tmp[j] = arr[i];
        }
        // 将临时数组赋值给原数组
        arr = tmp;
        // 调整head和tail的指向
        head = 0;
        tail = N - 1;
    }
}
```

***
**1.3.15 编写一个Queue用例，接受一个命令行参数k并打印出标准输入中倒数第k个字符串（假设标准输入中至少有k个字符串）。**

*Answer：*

```java
// 练习题1.3.15
public static void main(String[] args) {
    
    ResizingArrayQueueOfString queue = new ResizingArrayQueueOfString(10);
    while(!StdIn.isEmpty()) {
        queue.enqueue(StdIn.readString());
    }
    
    int k = Integer.parseInt(args[0]);
    System.out.println("k = " + k);
    
    int size = queue.size();
    
    for (int i = 1; i <= size - k; i++) {
        queue.dequeue();
    }
    
    System.out.println(queue.dequeue());
}
```

***
**1.3.16 使用1.3.1.5节中的readInts()作为模板为Date编写一个静态方法readDates()，从标准输入中读取由联系1.2.19的表格所指定的格式的多个日期并返回一个它们的数组。**

*Answer：*

```java
public static Date[] readDates(String name) {
    In in = new In(name);
    Queue<Date> queue = new Queue<>();
    while (!in.isEmpty() ) {
        queue.enqueue(new Date(in.readString()));
    }
    int N = queue.size();
    Date[] arr = new Date[N];
    for (int i = 0; i < N; i++) {
        arr[i] = queue.dequeue();
    }
    return arr;
}
```

***
**1.3.17 为Transaction类完成练习1.3.16。**

*Answer：*

```java
public static Transaction[] readTransactions(String name) {
    In in = new In(name);
    Queue<Transaction> queue = new Queue<>();
    while (!in.isEmpty()) {
        String[] str = in.readLine().split(" ");
        Transaction tran = new Transaction(str[0], new Date(str[1]), Double.parseDouble(str[2]));
        queue.enqueue(tran);
    }

    int N = queue.size();
    Transaction[] arr = new Transaction[N];
    for (int i = 0; i < N; i++)
        arr[i] = queue.dequeue();
    return arr;
}
```

***

#### 链表练习

***

**下述练习题配合该单链表使用。**

```java
// 单链表：
public class SLinkedList<T> implements Iterable<T> {

    private Node<T> beginMarker; // 链表开始标记，first结点为beginMaker.next
    private Node<T> endMarker; // 链表结束标记
    private int modCount = 0; // 记录修改次数
    private int theSize; // 记录链表大小

    // 静态内部类，用于生成Node
    private static class Node<T> {

        public T data;
        public Node<T> next;

        public Node(T d, Node<T> n) {
            data = d;
            next = n;
        }
    }

    // 初始化链表
    public SLinkedList() {
        doClear();
    }

    // 返回链表大小
    public int size() {
        return theSize;
    }

    // 返回链表是否为空
    public boolean isEmpty() {
        return theSize == 0;
    }

    // 重置链表
    public void clear() {
        doClear();
    }

    public String toString() {
        if (size() == 0) {
            return "[]";
        } else {
            StringBuilder sb = new StringBuilder();
            sb.append("[");
            Node<T> node = beginMarker.next;
            for (int i = 0; i < theSize; i++) {
                sb.append(node.data + ", ");
                node = node.next;
            }
            sb.replace(sb.length() - 2, sb.length(), "]");
            return sb.toString();
        }
    }

    // 在尾部添加一个结点
    public boolean add(T x) {
        return add(theSize, x);
    }

    // 在指定位置添加一个结点
    public boolean add(int idx, T x) {
        if (idx < 0 || idx > theSize) {
            return false;
        }
        return add(getPreNode(idx), x);
    }

    // 替换指定位置结点的值
    public T set(int idx, T newVal) {
        if (idx < 0 || idx > theSize - 1) {
            return null;
        }
        Node<T> p = getPreNode(idx).next;
        T oldVal = p.data;
        p.data = newVal;
        return oldVal;
    }

    // 返回指定位置上结点的值
    public T get(int idx) {
        if (idx < 0 || idx > theSize - 1) {
            return null;
        }
        return getPreNode(idx).next.data;
    }

    // 删除指定位置上的结点
    public T remove(int idx) {
        if (idx < 0 || idx > theSize - 1) {
            return null;
        }
        return remove(getPreNode(idx));
    }

    // 测试x是否在链表中
    public boolean contains(T x) {
        Iterator<T> it = iterator();
        while (it.hasNext()) {
            if (it.next().equals(x)) {
                return true;
            }
        }
        return false;
    }

    // 如果被测试元素x不在链表中，则添加x
    // true: x在链表中
    // false: x不在链表中，将x添加到链表末段
    public boolean containsAdd(T x) {
        return contains(x) ? true : !add(x);
    }

    // 如果被测试元素x在链表中则删除x
    // true: x在链表中，并将其删除
    // false: x不在链表中
    public boolean containsRemove(T x) {
        Iterator<T> it = iterator();
        while (it.hasNext()) {
            if (it.next().equals(x)) {
                it.remove();
                return true;
            }
        }
        return false;
    }
     
    // 对链表进行初始化
    private void doClear() {
        // 创建两个空结点，并将beginMaker.next指向endMaker
        beginMarker = new Node<>(null, null);
        endMarker = new Node<>(null, null);
        beginMarker.next = endMarker;
        // 初始化数组大小
        theSize = 0;
        modCount++;
    }

    private boolean add(Node<T> p, T x) {
        Node<T> newNode = new Node<>(x, p.next);
        p.next = newNode;
        theSize++;
        modCount++;
        return true;
    }

    // 删除结点p.next
    private T remove(Node<T> p) {
        Node<T> tmp = p.next;
        T data = tmp.data;
        p.next = tmp.next;
        tmp = null;
        theSize--;
        modCount++;
        return data;
    }

    // 返回idx指代的结点的前一个结点
    private Node<T> getPreNode(int idx) {
        Node<T> p = beginMarker;
        for (int i = 0; i < idx; i++) {
            p = p.next;
        }
        return p;
    }

    // 重载形式: 返回结点n的前一个结点
    private Node<T> getPreNode(Node<T> n) {

        Node<T> p = beginMarker;
        for (int i = 0; i < theSize; i++) {
            if (p.next == n) {
                break;
            }
            p = p.next;
        }
        return p;
    }

    @Override
    public Iterator<T> iterator() {
        return new Iterator<T>() {

            public Node<T> current = beginMarker;
            private boolean okToRemove = false;
            private int expectedModCount = modCount;

            @Override
            public boolean hasNext() {
                return current.next != endMarker;
            }

            @Override
            public T next() {
                if (modCount != expectedModCount) {
                    throw new ConcurrentModificationException();
                }
                if (!hasNext()) {
                    throw new NoSuchElementException();
                }
                
                T nextItem = current.next.data;
                current = current.next;
                okToRemove = true;
                return nextItem;
            }

            @Override
            public void remove() {
                if (modCount != expectedModCount) {
                    throw new ConcurrentModificationException();
                }
                if (!okToRemove) {
                    throw new IllegalStateException();
                }
                SLinkedList.this.remove(getPreNode(current));
                expectedModCount++;
                okToRemove = false;
            }
        };
    }

    public static void main(String[] args) {
        
    }
}
```

***
**1.3.20 编写一个方法delete()，接受一个int参数k，删除链表的第k个元素（如果它存在的话）。**

*Answer：*

```java
public T remove(int idx) {
    if (idx < 0 || idx > theSize - 1) {
        return null;
    }
    return remove(getPreNode(idx));
}

private T remove(Node<T> p) {
    T data = p.next.data;
    p.next = p.next.next;
    theSize--;
    modCount++;
    return data;
}

private Node<T> getPreNode(int idx) {
    Node<T> p = beginMarker;
    for (int i = 0; i < idx; i++) {
        p = p.next;
    }
    return p;
}
```

***
**1.3.21 编写一个方法find()，接受一条链表和一个字符串key作为参数。如果链表中大的某个结点的item域的值为key，则返回true，否则返回false。**

*Answer：*

```java
public static <T> boolean find(SLinkedList<T> list, T key) {
    return list.contains(key);
}
```

***
**1.3.22 假设x是一条链表中的某个结点，下面这段代码做了什么？**

```java
t.next = x.next;
x.next = t;
```

*Answer：*

插入结点t并使它称为x的后续结点。

****
**1.3.23 为什么下面这段代码和上一道题的代码效果不同？**

```java
    x.next = t;
    t.next = x.next;
```

*Answer：*

上述代码先将x的后续结点指定为t，再将t的后续结点指定为t本身。

***
**1.3.24 编写一个方法removeAfter()，接受一个链表结点作为参数并删除该结点的后续结点（如果参数结点或参数结点的后续结点为空则什么也不做）。**

*Answer：*

将题目的接受一个结点作为参数转为接受一个index作为参数。

```java
// 删除idx指代的结点的后续结点
public T removeAfter(int idx) {
    // 如果idx小于0或大于等于theSize - 1，直接返回null
    if (idx < 0 || idx >= theSize - 1) {
        return null;
    }
    Node<T> cur = getPreNode(idx).next;
    return remove(cur);
}
```

***
**1.3.25 编写一个方法insertAfter()，接受两个链表结点作为参数，将第二个结点插入链表并使之成为第一个结点的后续结点（如果两个参数为空，则什么都不做）。**

*Answer：*

将题目的接受两个结点作为参数转为接受两个Item作为参数。

```java
public boolean insertAfter(T x, T y) {
    return add(x) && add(y);
}
```

***
**1.3.26 编写一个方法remove()，接受一条链表和一个字符串key作为参数，删除链表中所有item域为key的结点。**

*Answer：*

```java
public static <T> void remove(SLinkedList<T> list, T key) {
    Iterator<T> iterator = list.iterator();
    while (iterator.hasNext()) {
        if (iterator.next().equals(key)) {
            iterator.remove();
        }
    }
}
```

***
**1.3.27 编写一个方法max()，接受一条链表的首结点作为参数，返回链表中键最大的结点值。假设所有键均为正整数，如果链表为空则返回0。**

*Answer：*

将题目的接受一条链表的首结点作为参数转为接受一条链表作为参数，将假设所有键均为整数转为泛型T，并且链表为空时返回null。

```java
public static <T extends Comparable<? super T>> T max(SLinkedList<T> list) {
    
    if (list.size() == 0) {
        return null;
    }
    
    Iterator<T> iterator = list.iterator();
    T max = iterator.next();
    while (iterator.hasNext()) {
        T tmp = iterator.next();
        if (tmp.compareTo(max) > 0) {
            max = tmp;
        }
    }
    return max;
}
```

***
**1.3.28 用递归的方法解答上一道练习。**

*Answer：*

略。

***
**1.3.29 用环形链表实现Queue。环形链表也是一条链表，只是没有任何结点的链接为空，且只要链表非空，则last.next的值为first。只能使用一个Node类型的实例变量（last）。**

*Answer：*

```java
public class CLinkedListQueue<T> {

    private int size; // 队列大小
    private Node<T> last; // 队列尾部标记，指向队列头；队列尾指向该标记

    // 静态内部类，用于生成Node
    private static class Node<T> {

        public T data;
        public Node<T> next;

        public Node(T d, Node<T> n) {
            data = d;
            next = n;
        }
    }

    // 返回队列是否为空
    public boolean isEmpty() {
        return size == 0;
    }

    // 返回size
    public int size() {
        return size;
    }

    // 出列
    public T dequeue() {
        if (size == 0) {
            return null;
        }

        Node<T> tmp = last.next;
        T data = tmp.data;
        last.next = tmp.next;
        tmp.next = null;
        size--;
        return data;
    }

    // 入列
    public boolean enqueue(T x) {

        Node<T> p = last;
        for (int i = 0; i < size; i++) {
            p = p.next;
        }
        return add(x, p);
    }

    private boolean add(T x, Node<T> p) {

        Node<T> tmp = new Node<>(x, last);
        p.next = tmp;
        size++;
        return true;
    }

    // 构造函数
    public CLinkedListQueue() {
        doClear();
    }

    // 重置队列
    public void doClear() {
        last = new Node<T>(null, null);
        size = 0;
    }

    public static void main(String[] args) {
        
    }
}
```

***
**1.3.30 编写一个函数，接受一条链表的首结点作为参数，（破坏性地）将链表反转并返回结果链表的首结点。**

*Answer：*

```java

// 返回该链表的beginMaker
private Node<T> getBM() {
    return beginMarker;
}

// 返回该链表的endMaker
private Node<T> getEM() {
    return endMarker;
}

// 破坏性地反转list
public static <T> SLinkedList<T> reverse(SLinkedList<T> list) {
    if (list == null) {
        throw new IllegalArgumentException();
    }

    // 获取该链表的beginMaker和endMaker
    Node<T> bm = list.getBM();
    Node<T> em = list.getEM();

    Node<T> tmp = em;
    // 假设原链表：bm --> a --> b --> c --> em, tmp = em
    // 经过一轮变换：bm --> b --> c --> em, a --> em, tmp = a
    // 经过两轮变换：bm --> c --> em, b --> a --> em, tmp = b
    // 经过三轮变换：bm --> em, c --> b --> a --> em, tmp = c
    // 最后bm.next = tmp：bm --> c --> b --> a --> em
    for (Node<T> p = bm.next; p != em; p = bm.next) {
        bm.next = p.next;
        p.next = tmp;
        tmp = p;
    }
    bm.next = tmp;

    return list;
}
```

***
**1.3.31 实现一个嵌套类DoubleNode用来构造双向链表，其中每个结点都含有一个指向前驱元素的引用和一项指向后续元素的引用（如果不存在则为null）。为以下任务实现若干静态方法：在表头插入结点、在表尾插入结点、从表头删除结点、从表尾删除结点、在指定结点之前插入新结点、在指定结点之后插入新结点、删除指定结点。**

*Answer：*

将实现静态方法转为实现成员方法，将指定结点的操作转为指定元素序号的操作。

```java

public class DLinkedList<T> {
    
    private int size; // 链表元素个数
    private DoubleNode<T> beginMaker; // 链表的起始标记，无prev，指向链表中第一个结点
    private DoubleNode<T> endMaker; // 链表结束标记，无next，链表的最后一个结点指向该标记

    private static class DoubleNode<T> {
        public T data;
        public DoubleNode<T> prev;
        public DoubleNode<T> next;

        public DoubleNode(T data, DoubleNode<T> prev, DoubleNode<T> next) {
            this.data = data;
            this.prev = prev;
            this.next = next;
        }
    }

    public DLinkedList() {
        doClear();
    }

    // 初始化链表
    public void doClear() {
        beginMaker = new DoubleNode<>(null, null, null);
        endMaker = new DoubleNode<>(null, beginMaker, null);
        beginMaker.next = endMaker;
        size = 0;
    }

    public int size() {
        return size;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    // 根据data查找其结点编号，如果没有找到返回-1
    public int indexOf(T key) {
        DoubleNode<T> p = beginMaker.next;
        for (int i = 0; i < size; i++) {
            if (p.data.equals(key)) {
                return i;
            } 
            p = p.next;
        }
        return -1;
    }

    // 在表头插入结点
    public boolean addFirst(T x) {
        return add(beginMaker, x);
    }

    // 在表尾插入结点
    public boolean addLast(T x) {
        return add(endMaker.prev, x);
    }

    // 在指定位置之前插入新结点
    public boolean addBefore(int idx, T x) {
        if (idx == 0) {
            return addFirst(x);
        }
        return add(idx - 1, x);
    }

    // 在指定位置之后插入结点
    public boolean addAfter(int idx, T x) {
        if (idx == size - 1) {
            return addLast(x);
        }
        return add(idx, x);
    }

    // 从表头删除结点
    public T removeFirst() {
        if (isEmpty()) {
            return null;
        }
        return remove(beginMaker.next);
    }

    // 从表尾删除结点
    public T removeLast() {
        if (isEmpty()) {
            return null;
        }
        return remove(endMaker.prev);
    } 

    // 在指定位置删除结点
    public T remove(int idx) {
        return remove(getNode(idx));
    }
    
    // 在idx指代的结点之后添加结点
    private boolean add(int idx, T x) {
        if (idx < 0 || idx > size - 1) {
            return false;
        }
        
        return add(getNode(idx), x);
    }
    
    // 在结点p之后添加结点
    private boolean add(DoubleNode<T> p, T x) {
        DoubleNode<T> tmp = new DoubleNode<>(x, p, p.next);
        p.next.prev = tmp;
        p.next = tmp;
        size++;
        return true;
    }

    // 删除结点p
    private T remove(DoubleNode<T> p) {
        T data = p.data;
        p.prev.next = p.next;
        p.next.prev = p.prev;
        p.prev = null;
        p.next = null;
        size--;
        return data;
    }

    // 返回idx指代的结点
    private DoubleNode<T> getNode(int idx) {
                
        if (idx < 0 || idx > size - 1) {
            throw new IllegalArgumentException();
        }

        DoubleNode<T> p = null;
        if (idx <= size / 2) {
            // 如果idx小于等于size的一半，则从beginMaker.next开始向后遍历
            p = beginMaker.next;
            for (int i = 0; i < idx; i++) {
                p = p.next;
            }
        } else {
            // 否则从endMaker.prev开始向前遍历
            p = endMaker.prev;
            for (int i = size - 1; i > idx; i--) {
                p = p.prev;
            }
        }
        // 遍历结束，结点p为idx指定的结点
        return p;
    }

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }

        StringBuilder sb = new StringBuilder();
        sb.append("[");
        DoubleNode<T> p = beginMaker.next;
        for (int i = 0; i < size; i++) {
            sb.append(p.data + ", ");
            p = p.next;
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();

    }

    public static void main(String[] args) {

    }
}
```

***

#### 提高题

***
**1.3.32 Steque。一个以栈为目标的队列（或称steque），是一种支持push、pop和enqueue操作的数据类型。为这种抽象数据类型定义一份API并给出一份基于链表的实现。**

*Answer：*

API：

- Steque()：创建一个空的Steque
- void push(Item item)：将数据压入队列头
- Item pop()：弹出队列头的元素
- void enqueue(Item item)：将元素放入队列尾

```java

// 内部封装SLinkedList（单链表），链表的beginMaker指向Steque的头：
public class Steque<Item> {
    private SLinkedList<Item> sll;
    
    public int size() {
        return sll.size();
    }
    
    public boolean isEmpty() {
        return sll.isEmpty();
    }
    
    public Steque() {
        sll = new SLinkedList<>();
    }
    
    public void push(Item item) {
        sll.add(0, item);
    }
    
    public Item pop() {
        return sll.remove(0);
    }
    
    public void enqueue(Item item) {
        sll.add(item);
    }
    
    @Override
    public String toString() {
        return sll.toString();
    }
    
    public static void main(String[] args) {
        
    }
}
```

***
**1.3.33 Deque。一个双向队列（或者称为deque）和栈或队列类似，但它同时支持在两端添加或删除元素。Deque能够存储一组元素并支持下表的API：**

API：

- Deque：创建空双向队列
- boolean isEmpty()：双向队列是否为空
- int size()：双向队列中的元素数量
- void pushLeft(Item item)：向左端添加一个新元素
- void pushRight(Item item)：向有段添加一个新元素
- Item popLeft()：从左端删除一个元素
- Item popRight()：从右端删除一个元素

**编写一个使用双向链表实现这份API的Deque类，以及一个使用动态数组调整实现这份API的ResizingArrayDeque类。**

Deque：

```java
// 内部封装DLinkedList
public class Deque<Item> {

    private DLinkedList<Item> dll;
    
    public Deque() {
        dll = new DLinkedList<>();
    }
    
    public boolean isEmpty() {
        return dll.isEmpty();
    }
    
    public int size() {
        return dll.size();
    }
    
    public void pushLeft(Item item) {
        dll.addFirst(item);
    }
    
    public void pushRight(Item item) {
        dll.addLast(item);
    }
    
    public Item popLeft() {
        return dll.removeFirst();
    }
    
    public Item popRight() {
        return dll.removeLast();
    }
}
```

ResizingArrayDeque：

```java

public class ResizingArrayDeque<Item> {
    private Item[] arr; // 内部使用的数组
    private int N; // 元素个数
    private int head; // 队列头，指向队列最左端的元素
    private int tail; // 队列尾，指向队列最右端的元素

    public ResizingArrayDeque() {
        this(10);
    }

    @SuppressWarnings("unchecked")
    public ResizingArrayDeque(int cap) {
        if (cap <= 0) {
            throw new IllegalArgumentException();
        }
        arr = (Item[]) new Object[cap];
        head = -1;
        tail = -1;
    }

    // 返回元素个数是否为0
    public boolean isEmpty() {
        return N == 0;
    }

    // 返回元素个数
    public int size() {
        return N;
    }

    public void pushLeft(Item item) {
        // 如果head和tail均指向-1，则将元素添加到0角标上，并设置head和tail指向0
        if (head == -1 && tail == -1) {
            arr[0] = item;
            head = 0;
            tail = 0;
            N++;
            return;
        }
        
        // 如果从左侧添加元素时，head指向0，说明元素填充已达到数组左边缘
        // 则将数组大小调整为当前元素个数的3倍
        if (head == 0) {
            enlarge();
        }

        // head指针向左移动并添加item
        arr[--head] = item;
        N++;
    }

    public void pushRight(Item item) {
        // 如果head和tail均指向-1，则将元素添加到0角标上，并设置head和tail指向0
        if (head == -1 && tail == -1) {
            arr[0] = item;
            head = 0;
            tail = 0;
            N++;
            return;
        }

        // 如果从右侧添加元素时，tail指向arr.length - 1，说明元素填充已达到数组右边缘
        // 则将数组大小调整为当前元素个数的3倍
        if (tail == arr.length - 1) {
            enlarge();
        }

        // tail指针向右移动并添加item
        arr[++tail] = item;
        N++;
    }

    public Item popLeft() {
        // 如果数组中没有元素则返回null
        if (N == 0) {
            return null;
        }
        
        // 记录head指针指向的元素
        Item data = arr[head];
        // 将head指针当前指向的元素置为null，head指针向右移动
        arr[head++] = null;
        N--;
        // 如果当前元素数量不为0，且元素数量的4倍小于等于arr.length
        // 说明此时数组长度过大，将数组大小调整为当前元素个数的2倍
        if (4 * N <= arr.length && N != 0) {
            reduce();
        }
        return data;
    }
    
    public Item popRight() {
        // 如果数组中没有元素则返回null
        if (N == 0) {
            return null;
        }
        // 记录tail指针指向的元素
        Item data = arr[tail];
        // 将tail指针当前指向的元素置为null，tail指针向左移动
        arr[tail--] = null;
        N--;
        // 如果当前元素数量不为0，且元素数量的4倍小于等于arr.length
        // 说明此时数组长度过大，将数组大小调整为当前元素个数的2倍
        if (4 * N <= arr.length && N != 0) {
            reduce();
        }
        return data;
    }

    // 减小数组长度
    @SuppressWarnings("unchecked")
    private void reduce() {
        Item[] tmp = (Item[]) new Object[2 * N];
        // 调整大小时将原数组的数据放在中间，左右留出一定的空间用于可能的push操作
        for (int i = head, j = N / 2; i <= tail; i++, j++) {
            tmp[j] = arr[i];
        }
        arr = tmp;
        head = N / 2;
        tail = N + head - 1;
    }

    // 增大数组长度
    @SuppressWarnings("unchecked")
    private void enlarge() {
        Item[] tmp = (Item[]) new Object[3 * N];

        // 调整大小时将原数组的数据放在中间，左右留出足够的空间用于可能的push操作
        for (int i = head, j = N; i <= tail; i++, j++) {
            tmp[j] = arr[i];
        }
        arr = tmp;
        head = N;
        tail = N + head - 1;
    }

    @Override
    public String toString() {
        
        if (N == 0) {
            return "[]";
        }

        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = head; i <= tail; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    public static void main(String[] args) {
        
    }
}
```

***
**1.3.34 随机背包。随机背包能够存储一组元素并支持下表的API：**

- RandomBag()：创建一个空随机背包
- boolean isEmpty()：背包是否为空
- int size()：背包中的元素数量
- void add(Item item)：添加一个元素

**编写一个RandomBag了类来实现这份API。内部使用数组，迭代器应当随机访问背包中的所有元素。**

*Answer：*

```java
public class RandomBag<Item> implements Iterable<Item> {

    private Item[] arr; // 内部维护的数组
    private int tail; // 指向数组最后一个元素
    private int size; // 元素数量

    // 构造函数
    @SuppressWarnings("unchecked")
    public RandomBag(int cap) {
        if (cap <= 0) {
            throw new IllegalArgumentException();
        }
        // 初始化时指定tail指向-1
        arr = (Item[]) new Object[cap];
        size = 0;
        tail = -1;
    }

    // 返回元素个数是否为0
    public boolean isEmpty() {
        return size == 0;
    }

    // 返回元素个数
    public int size() {
        return size;
    }

    // 添加元素
    public void add(Item item) {

        // 如果tail指向数组右侧边界，则扩大数组
        if (tail == arr.length - 1) {
            resize();
        }

        // tail指针向右移动并保存数据
        arr[++tail] = item;
        size++;
    }

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = 0; i <= tail; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    // 扩大数组大小
    @SuppressWarnings("unchecked")
    private void resize() {
        Item[] tmp = (Item[]) new Object[arr.length * 2];
        for (int i = 0; i <= tail; i++) {
            tmp[i] = arr[i];
        }
        arr = tmp;
        tail = size - 1;
    }

    @Override
    public Iterator<Item> iterator() {
        return new RandomIterator();
    }

    // 迭代器
    private class RandomIterator implements Iterator<Item> {

        private Item[] tmp; // 去除arr数组中tail指针的所有右侧元素
        private List<Item> list; // tmp数组转为list
        private Iterator<Item> iter; // 内部维护的迭代器

        @SuppressWarnings("unchecked")
        public RandomIterator() {

            // 对tmp数组进行赋值
            tmp = (Item[]) new Object[size];
            for (int i = 0; i <= tail; i++) {
                tmp[i] = arr[i];
            }

            list = Arrays.asList(tmp); // 将tmp数组转为list
            Collections.shuffle(list); // 对list进行随机打乱（此时tmp数组）
            iter = list.iterator(); // 初始化iter为打乱后的list的迭代器
        }

        @Override
        public boolean hasNext() {
            return iter.hasNext();
        }

        @Override
        public Item next() {
            return iter.next();
        }
    }

    public static void main(String[] args) {

    }
}

```

***
**1.3.35 随机队列。随机队列能够存储一组元素并支持下表的API：**

- RandomQueue()：创建一条空的随机队列
- boolean isEmpty()：队列是否为空
- void enqueue(Item item)：添加一个元素
- Item dequeue()：删除并随机返回一个元素
- Item sample()：随机返回一个元素但不删除它

*Answer：*

```java
public class RandomQueue<Item> {
    private Item[] arr; // 内部维护的数组
    private int tail; // 指向数组最后一个元素
    private int size; // 元素数量

    // 构造函数
    @SuppressWarnings("unchecked")
    public RandomQueue(int cap) {
        if (cap <= 0) {
            throw new IllegalArgumentException();
        }
        // 初始化时指定tail指向-1
        arr = (Item[]) new Object[cap];
        size = 0;
        tail = -1;
    }

    // 返回元素个数是否为0
    public boolean isEmpty() {
        return size == 0;
    }

    // 返回元素个数
    public int size() {
        return size;
    }

    // 添加元素
    public void enqueue(Item item) {

        // 如果tail指向数组右侧边界，则扩大数组
        if (tail == arr.length - 1) {
            resize(arr.length * 2);
        }

        // tail指针向右移动并保存数据
        arr[++tail] = item;
        size++;
    }

    // 出列
    public Item dequeue() {
        if (size == 0) {
            return null;
        }

        swap(randomIdx(), tail);

        Item item = arr[tail];
        arr[tail--] = null;
        size--;

        if (size <= arr.length / 4) {
            resize(arr.length / 2);
        }

        return item;
    }

    // 取样
    public Item sample() {
        if (size == 0) {
            return null;
        }
        return arr[randomIdx()];
    }

    // 返回[0, tail]之间的一个随机整数
    private int randomIdx() {
        return new Random().nextInt(tail + 1);
    }

    // 交换idx和tail指向的数据
    private void swap(int idx, int tail) {
        Item tmp = arr[idx];
        arr[idx] = arr[tail];
        arr[tail] = tmp;
    }

    // 调整数组大小
    @SuppressWarnings("unchecked")
    private void resize(int n) {
        Item[] tmp = (Item[]) new Object[n];
        for (int i = 0; i <= tail; i++) {
            tmp[i] = arr[i];
        }
        arr = tmp;
        tail = size - 1;
    }

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = 0; i <= tail; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }
    
    public static void main(String[] args) {
        
    }
}
```

***
**1.3.36 随机迭代器。为上一题的RandomQueue&lt;Item&gt;编写一个随机迭代器，随机返回队列中的所有元素。**

*Answer：*

RandomQueue实现Iterable。

```java
// 提高题 1.3.36 随机迭代器
public Iterator<Item> iterator() {
    return new RandomIterator();

}

private class RandomIterator implements Iterator<Item> {

    private Item[] tmp; // 去除arr数组中tail指针的所有右侧元素
    private List<Item> list; // tmp数组转为list
    private Iterator<Item> iter; // 内部维护的迭代器

    @SuppressWarnings("unchecked")
    public RandomIterator() {
        // 对tmp数组进行赋值
        tmp = (Item[]) new Object[size];
        for (int i = 0; i <= tail; i++) {
            tmp[i] = arr[i];
        }

        list = Arrays.asList(tmp); // 将tmp数组转为list
        Collections.shuffle(list); // 对list进行随机打乱（此时tmp数组）
        iter = list.iterator(); // 初始化iter为打乱后的list的迭代器
    }

    @Override
    public boolean hasNext() {
        return iter.hasNext();
    }

    @Override
    public Item next() {
        return iter.next();
    }

    @Override
    public void remove() {
        throw new UnsupportedOperationException();
    }
}
```

***
**1.3.37 Josephus问题。在这个古老的问题中，N个深陷绝境的人一致同意通过以下方式减少生存人数。他们围坐一圈（位置记为0到N-1）并从第一个人开始保数，报到M的人会被杀死，知道最后一个人留下来。传说Josephus找到了不会被杀死的位置。编写一个Queue的用例Josephus，从命令行接受N和M并打印出人们被杀死的顺序。**

*Answer：*

```java
public class Josephus {

    public static void main(String[] args) {

        int N = Integer.parseInt(args[0]);
        int M = Integer.parseInt(args[1]);

        Queue<Integer> queue = new Queue<Integer>();
        for (int i = 0; i < N; i++) {
            queue.enqueue(i);
        }

        while (!queue.isEmpty()) {
            
            // 将报数小于M的人依次出列并加入队尾
            for (int i = 1; i < M; i++) {
                queue.enqueue(queue.dequeue());
            }
            
            // 将报数为M的人出列，最后出列的位置就是Josephus的位置
            StdOut.print(queue.dequeue() + " ");
        }
    }
}
```

***
**1.3.38 删除第k个元素。实现一个类并支持下表的API：**

- GeneralizedQueue()：创建一条空队列
- boolean isEmpty()：队列是否为空
- void insert(Item x)：添加一个元素
- Item delete(int k)：删除并返回最早插入的第k个元素

**首先用数组实现该数据类型，然后用链表实现该数据类型。**

*Answer：*

数组实现：

```java
public class GeneralizedQueueArr<Item> {
    private Item[] arr;
    private int size;
    private int tail;

    @SuppressWarnings("unchecked")
    public GeneralizedQueueArr(int cap) {
        if (cap <= 0) {
            throw new IllegalArgumentException();
        }
        arr = (Item[]) new Object[cap];
        size = 0;
        tail = -1;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void insert(Item x) {
        // 如果tail指向数组右侧边界，则扩大数组
        if (tail == arr.length - 1) {
            resize(arr.length * 2);
        }

        // tail指针向右移动并保存数据
        arr[++tail] = x;
        size++;
    }

    public Item delete(int k) {
        if (k - 1 < 0 || k - 1 > tail) {
            throw new NoSuchElementException();
        }
        Item data = arr[k - 1];
        arr[k - 1] = null;
        size--;
        return data;
    }

    @SuppressWarnings("unchecked")
    private void resize(int size) {
        Item[] tmp = (Item[]) new Object[size];
        for (int i = 0; i <= tail; i++) {
            tmp[i] = arr[i];
        }
        arr = tmp;
    }
    
    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        for (int i = 0; i <= tail; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }
    
    public static void main(String[] args) {
        
    }
}
```

链表实现：

```java
public class GeneralizedQueueLinked<Item> {

    private int count; // 下一个结点的编号，不包括bm和em
    private int size; // 记录有数据的结点个数
    private Node<Item> beginMaker;
    private Node<Item> endMaker;

    private static class Node<Item> {

        public Item data;
        public Node<Item> next;
        public int index;

        // 除了bm和em每个结点都有他们加入链表时的编号，从1开始
        public Node(Item d, Node<Item> n, int idx) {
            data = d;
            next = n;
            index = idx;
        }

        public Node(Item d, Node<Item> n) {
            data = d;
            next = n;
        }
    }

    public GeneralizedQueueLinked() {
        endMaker = new Node<>(null, null);
        beginMaker = new Node<>(null, endMaker);
        size = 0;
        count = 0;
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void insert(Item x) {
        add(endMaker, x);
    }

    private void add(Node<Item> p, Item x) {
        Node<Item> tmp = beginMaker;
        while (tmp.next != p) {
            tmp = tmp.next;
        }
        tmp.next = new Node<>(x, endMaker, ++count);
        size++;
    }

    public Item delete(int k) {

        if (k < 1 || k > count) {
            throw new IllegalArgumentException();
        }
        Node<Item> tmp = beginMaker;
        boolean find = false; // 设置标记
        while (tmp.next != endMaker) {
            if (tmp.next.index == k) {
                find = true;
                break;
            }
            tmp = tmp.next;
        }

        // 如果链表中已经没有第k次插入的数据了
        if (!find) {
            return null;
        }

        Node<Item> p = tmp.next;
        Item data = p.data;
        tmp.next = p.next;
        p.next = null;
        size--;

        return data;
    }

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        Node<Item> p = beginMaker.next;
        while (p != endMaker) {
            sb.append(p.data + ", ");
            p = p.next;
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    public static void main(String[] args) {

    }
}
```

***
**1.3.39 环形缓冲区。环形缓冲区，又称为环形队列，是一种定长为N的先进先出的数据结构。它在进程间的一部数据传输或记录日志文件时十分有用。当缓冲区为空时，消费者会在数据存入缓冲区前等待，当缓冲区满时，生产者会等待将数据存入缓冲区。为RingBuffer设计一份API并用回环数组将其实现。**


*Answer：*

```java
public class RingBuffer<Item> {

    private Item[] arr;
    private int putMarker = 0; // 放入数据标记
    private int getMarker = 0; // 取出数据标记

    @SuppressWarnings("unchecked")
    public RingBuffer(int N) {
        if (N <= 0) {
            throw new IllegalArgumentException();
        }
        arr = (Item[]) new Object[N];
    }

    public boolean isEmpty() {
        return putMarker == getMarker;
    }

    public boolean isFull() {
        return arr.length == putMarker - getMarker;
    }

    public void putItem(Item item) {
        while (isFull()) {
            System.out.println("Buffer is Full ... ");
            return;
        }
        arr[(putMarker++) % arr.length] = item;
    }

    public Item getItem() {
        // 当空的时候，等待
        while (isEmpty()) {
            System.out.println("Buffer is Empty ... ");
            return null;
        }

        Item data = arr[getMarker % arr.length];
        arr[getMarker++ % arr.length] = null;
        return data;
    }

    @Override
    public String toString() {
        StringBuffer sb = new StringBuffer();
        sb.append("[");
        for (int i = 0; i < arr.length; i++) {
            sb.append(arr[i] + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    public static void main(String[] args) {

    }
}
```

***
**1.3.40 前移编码。从标准输入读取一串字符，使用链表保存这些字符并清除重复字符。当你读取了一个从未见过的字符时，将它插入表头。当你读取了一个重复的字符时，将它从链表中删去并再次插入表头。将你的程序命名为MoveToFront：它实现了著名的前移编码策略，这种策略假设最近访问过的元素很可能会再次访问，因此可以用于缓存、数据压缩等许多场景。**

*Answer：*

```java
public class MoveToFront {

    public static void main(String[] args) {

        DLinkedList<Character> dll = new DLinkedList<>();

        while (!StdIn.isEmpty()) {
            char[] chars = StdIn.readString().toCharArray();
            for (int i = 0; i < chars.length; i++) {
                int idx = dll.indexOf(chars[i]);
                if (idx != -1) {
                    dll.remove(idx);
                }
                dll.addFirst(chars[i]);
            }
        }
        System.out.println(dll);
    }
}
```

***
**1.3.41 复制队列。编写一个新的构造函数，使以下代码：**

```java
Queue<Item> r = new Queue<>(q);
```

**得到的r指向队列q的一个新的独立副本。可以对q或r进行任意入列或出列操作但它们不会相互影响。**

*Answer：*

```java
public Queue(Queue<Item> q) {
    for (Item item : q) {
        enqueue(item);
    }
}
```

***
**1.3.42 复制栈。为基于链表实现的栈编写一个新的构造函数，使以下代码：**

```java
Stack<Item> t = new Stack<>(s);
```

**得到t指向栈s的一个新的独立的副本。**

*Answer：*

```java
public Stack(Stack<Item> s) {
    Stack<Item> tmp = new Stack<Item>();
    for (Item item : s) {
        temp.push(i);
    }
    for (Item item : tmp) {
        push(i);
    }
}
```

***
**1.3.43 文件列表。文件夹就是一列文件和文件夹的列表。编写一个程序，从命令行接受一个文件夹名作为参数，打印出该文件夹下的所有文件并用递归的方式在所有子文件夹名下（缩进）列出其下所有的文件。**

*Answer：*

未使用队列的做法：

```java
public class PrintFiles {
    
    private static void printIndent(int n) {
        for (int i = 0; i < n; i++) {
            System.out.print("|----");
        }
    }

    private static void printFiles(String path, int depth) {
        
        File file = new File(path);
        printIndent(depth);
        System.out.println(file.getName());
        
        if (file.isDirectory()) {
            File[] files = file.listFiles();
            for (File f : files) {
                printFiles(f.getAbsolutePath(), depth + 1);
            }
        } 
    }

    public static void printFiles(String path) {
        printFiles(path, 0);
    }
    
    public static void main(String[] args) {
        printFiles("C:\\Users\\Tavish\\Desktop\\temp\\Test\\");
    }
}
```

使用队列的做法：

*略。*

***
**1.3.44 文本编辑器的缓冲区。为文本编辑器的缓冲区设计一种数据类型并实现下表中的API：**

- Buffer()：创建一块空缓冲区
- void insert(char c)：在光标位置插入字符c
- char delete()：删除并返回光标位置的字符
- void left(int k)：将光标向左移动k个位置
- void right(int k)：将光标向右移动k个位置
- int size()：缓冲区中的字符数量

*Answer：*

```java
public class Buffer {

    private int size;
    private int cursor; // 光标，链表中每个节点的编号为[0, size - 1]，bm为-1，em为size

    private Node<Character> beginMaker;
    private Node<Character> endMaker;

    @SuppressWarnings("hiding")
    private static class Node<Character> {

        public Node<Character> prev;
        public Node<Character> next;
        public Character data;

        public Node(Node<Character> prev, Node<Character> next, Character c) {
            this.prev = prev;
            this.next = next;
            this.data = c;
        }

    }

    public Buffer() {
        endMaker = new Node<>(null, null, null);
        beginMaker = new Node<>(null, endMaker, null);
        endMaker.prev = beginMaker;
        size = 0;
        cursor = -1;
    }

    // 在光标位置之后添加数据，添加后光标指向新节点
    public void insert(char c) {
        Node<Character> p = getCursorNode();
        Node<Character> newNode = new Node<>(p, p.next, c);

        p.next = newNode;
        newNode.next.prev = newNode;

        cursor++;
        size++;
    }

    // 删除光标指向的节点，删除后光标指向该节点的前一个节点
    public char delete() {
        if (cursor == -1) {
            return '\u0000';
        }

        Node<Character> p = getCursorNode();

        char data = p.data;

        p.next.prev = p.prev;
        p.prev.next = p.next;
        p.next = null;
        p.prev = null;

        cursor--;
        size--;

        return data;
    }

    public void left(int k) {
        for (int i = 0; i < k; i++) {
            cursor--;
        }
        // 如果移动之后光标在bm或bm左边，则令cusor指向bm
        cursor = cursor <= -1 ? -1 : cursor;
    }

    public void right(int k) {
        for (int i = 0; i < k; i++) {
            cursor++;
        }
        // 如果移动之后光标在em或em右边，则令cursor指向size - 1
        cursor = cursor >= size ? size - 1 : cursor;
    }

    public int size() {
        return size;
    }

    @Override
    public String toString() {
        if (size == 0) {
            return "[]";
        }
        StringBuilder sb = new StringBuilder();
        sb.append("[");
        Node<Character> p = beginMaker.next;
        for (int i = 0; i < size; i++) {
            sb.append(p.data + ", ");
            p = p.next;
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    private Node<Character> getCursorNode() {

        if (cursor == -1) {
            return beginMaker;
        }

        Node<Character> p = beginMaker.next;
        for (int i = 0; i < cursor; i++) {
            p = p.next;
        }

        return p;
    }

    public static void main(String[] args) {
        
    }
}
```

***
**1.3.45 栈的可生成性。假设我们的栈测试用例将会进行一系列混合的入栈和出栈操作，序列中的整数$0，1，...，N-1$（按此先后顺序排列）表示入栈操作，N个减号表示出栈操作。设计一个算法，判定给定的混合序列是否会使数组向下溢出（你所使用的空间量与N无关，即不能用某种数据结构存储所有整数）。设计一个线性时间的算法判定我们的测试用例能否产生某个给定的排列（这取决于出栈操作指令的出现位置）。**

*Answer：*

```java
public void ifUnderFlow(String sequence ) {
    Stack<Integer> stack = new Stack<>();
    String tmp = "";

    while (!StdIn.isEmpty()) {
        String s = StdIn.readString();
        if (s.equals("-")) {
            tmp += stack.pop();
        } else {
            stack.push(Integer.parseInt(s));
        }
    }
    System.out.println(tmp.equals(sequence));
}
```

***
**1.3.46 栈可生成性问题中禁止出现的排列。若三元组（a，b，c）中$a<b<c$且c最先被弹出，a第二、b第三（c和a以及a和b之间可以间隔其他整数），那么当且仅当排列中不含这样的三元组时栈才可以生成它。**

*Answer：*

入栈前检查，若栈中有两个元素小于该值，并且这两个元素的先后顺序为较大的一个更靠近栈底，则该元素不能入栈。

```java
public class Stack1346<Item extends Comparable<? super Item>> {

    private Stack<Item> stack;

    private Item lastForbidItem;

    public Stack1346() {
        stack = new Stack<>();
        lastForbidItem = null;
    }

    public void push(Item key) {

        if ((lastForbidItem != null && key.compareTo(lastForbidItem) > 0) || !check(key)) {
            lastForbidItem = key;
            System.out.println("违反规则， " + key + "不能入栈");
            return;
        }

        stack.push(key);
    }

    private boolean check(Item key) {

        // 如果栈大小小于等于1，直接返回true
        if (stack.size() <= 1) {
            return true;
        }

        List<Item> list = new ArrayList<>();
        // 将stack中所有小于key的值存入list
        for (Item item : stack) {
            if (item.compareTo(key) < 0) {
                list.add(item);
            }
        }

        // 如果list的大小大于等于2，进行进一步检查
        if (list.size() >= 2) {
            return check(list);
        } else {
            return true;
        }
    }

    private boolean check(List<Item> list) {

        // 循环比较
        for (int i = list.size() - 1; i > 0; i--) {
            for (int j = i - 1; j >= 0; j--) {
                if (list.get(i).compareTo(list.get(j)) > 0) {
                    return false;
                }
            }
        }
        return true;
    }

    @Override
    public String toString() {
        return stack.toString();
    }

    public static void main(String[] args) {

    }
}
```

***
**1.3.47 可连接的队列、栈或steque。为队列、栈或steque添加一个能够（破坏性地）链接两个同类对象的额外操作catenation。**

*Answer：*

```java
public void catenation(Steque<Item> steque) {
    while(steque.size() != 0) {
        enqueue(steque.pop());
    }
}
```

***
**1.3.48 双向队列与栈。用一个双向队列实现两个栈，保证每个栈操作只需要常数次的双向队列操作。**

*Answer：*

两个栈分别操作双向队列的一端，实现略。

***
**1.3.49 栈与队列。用有限个栈实现一个队列，保证每个队列操作（在最坏情况下）都只需要常数次的栈操作。**

*Answer：*

略。

***
**1.3.50 快速出错的迭代器。修改Stack的迭代器代码，确保一旦用例在迭代器中修改集合数据就抛出一个java.util.ConcurrentModificationException异常。**

*Answer：*

参见链表练习部分对于SLinkedList的实现，该类已实现快速出错机制。

### 1.4 算法分析

***

#### 练习题

***

**1.4.1 证明N个数中取三个整数的不同组合的总数为$N(N-1)(N-2)/6$。**

*Answer：*

数学归纳法：

（1）$N=3$时，左边三个整数的不同组合数为$C_3^3=1$，右边$\frac{N(N-1)(N-2)}{6}=\frac{3\times(3-1)\times(3-2)}{6}=1$，左右两边相等。
（2）假设公式在$N=M (M > 3)$时成立，即$C_M^3=\frac{M(M-1)(M-2)}{6}$。
（3）左边带入$N=M+1$得$C_{M+1}^3$。
展开得$\frac{(m+1)!}{3!(m-2)!}=\frac{(m+1)(m)(m-1)(m-2)!}{3!(m-2)!}=\frac{(m+1)(m)(m-1)}{6}=\frac{(m+1)[(m+1)-1][(m+1)-2]}{6}$。
上述式子等于右边带入N=M+1。
证毕。

***
**1.4.2 修改ThreeSum，正确处理两个较大的int值相加可能溢出的情况。**

*Answer：*

```java
public class ThreeSumBigInteger {

    // 统计和为0的元组的数量
    public static int count(int[] a) {

        // 将int型数组转为BigInteger型数组
        BigInteger[] b = new BigInteger[a.length];

        // 遍历赋值
        int N = b.length;
        for (int i = 0; i < N; i++) {
            b[i] = BigInteger.valueOf(a[i]);
        }

        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                for (int k = j + 1; k < N; k++) {
                    // 使用大数加法
                    // 当三个数的和为0时，signum方法返回0
                    if (b[i].add(b[j]).add(b[k]).signum() == 0) {
                        cnt++;
                    }
                }
            }
        }
        return cnt;
    }

    public static void main(String[] args) {
        int[] a = new In(new File(args[0])).readAllInts();
        StdOut.println(count(a));
    }
}
```

***
**1.4.3 修改DoublingTest，使用StdDraw产生类似于正文中的标准图像和对数图像，根据需要调整比例使图像总能够充满窗口的大部分区域。**

*Answer：*

略。

***
**1.4.4 参照表1.4.4为TwoSum建立一张类似的表格。**

*Answer：*

TwoSum源码：

```java
public class TwoSum {

    public static int count(int[] a) {

        int N = a.length;
        int cnt = 0;

        for (int i = 0; i < N; i++) {

            for (int j = i + 1; j < N; j++) {

                if (a[i] + a[j] == 0) {

                    cnt++;

                }

            }

        }
        return cnt;
    }
}
```

![ex144](images\chapter01\ex144.png)

语句块 | 运行时间（以秒记）| 频率 | 总时间
-----|-----|-----|-----
D | $t_0$ | $x$ | $t_0x$
C | $t_1$ | $N^2-N/2$ | $t_1(N^2/2-N/2)$
B | $t_2$ | $N$ | $t_2N$
A | $t_3$ | $1$ | $t_3$
 | | 总时间 | $t_1(N^2/2-N/2)+t_2N+t_3+t_0x$
 | | 近似 | ~$(t_1/2)N^2$
 | | 增长的数量级 | $N^2$

***
**1.4.5 给出下面这些量的近似：**

- a. $N+1$
- b. $1+1/N$
- c. $(1+1/N)(1+2/N)$
- d. $2N^3-15N^2+N$
- e. $lg(2N)/lgN$
- f. $lg(N^2+1)/lgN$
- g. $N^{100}/2^N$

**Answer：**

- a. ~$N$
- b. ~$1$
- c. ~$1$
- d. ~$2N^3$
- e. ~$1$
- f. ~$2$
- g. ~$0$

***
**1.4.6 给出以下代码的运行时间的增长数量级（作为N的函数）：**

a：

```java
    int sum = 0;
    for (int n = N; n > 0; n /= 2) {
        for (int i = 0; i < n; i++) {
            sum++;
        }
    }
```

b：

    int sum = 0;
    for (int i = 1; i < N; i *= 2) {
        for (int j = 0; j < i; j++) {
            sum++;
        }
    }

c：

```java
int sum = 0;
for (int i = 1; i < N; i *= 2) {
    for (int j = 0; j < N; j++) {
        sum++;
    }
}
```

*Answer：*

增长数量级取决于 sum++的运行次数。

a： 执行次数$S=\frac{N}{2^0}+\frac{N}{2^1}+\frac{N}{2^2}+...+\frac{N}{2^k}$，总项数$T=k+1$。所以$S=\frac{N\times(1-({\frac{1}{2}})^T)}{1-\frac{1}{2}}=2N(1-2^{-T})=2N(1-2^{-k-1})=2N-2^{-k}$。所以$k=-log_2^{2N-S}$。因为实际末项$\frac{N}{2^K}>1$，即$N>2^k$，所以$k<log_2^N$。故得$-log_2^{2N-S}<log_2^N$，化简得$S<2N-\frac{1}{N}$。所以增长数量级为~$2N$。

b： 执行次数$S=1+2+4+8+...+2^k$，总项数$T=k+1$。所以$S=\frac{1\times(1-2^T)}{1-2}=2^T-1=2^{k+1}-1$。因为末项$2^k<N$，所以$k<log_2^N$，故$S=2^{k+1}-1<2^{log_2^{2N}}-1=2N-1$。所以增长数量级为~$2N$。

c： 执行次数$S=N+N+N...$，总项数$T=k+1$，其中$2^k<N$。所以$S=N(k+1)<N(log_2^N+1)$，即$S<Nlog_2^N + N$。所以增长数量级为~$Nlog_2^N$。

***
**1.4.7 以统计涉及输入数字的算数操作（和比较）的成本模型分析ThreeSum。**

*Answer：*

即统计`if (a[i] + a[j] + a[k] == 0)`语句执行的次数，~$N^3/6$。

***
**1.4.8 编写一个程序，计算输入文件中相等的整数对的数量。如果你得第一个程序是平方级别得，请继续思考并用Arrays.sort()给出一个线性对数级别的解答。**

*Answer：*

```java
public class TwoNumEquals {
    
    // 平方级别
    public static int count(int[] arr) {
        int N = arr.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                if (arr[i] == arr[j]) {
                    cnt++;
                }
            }
        }
        return cnt;
    }
    
    // NlogN级别
    public static int countFast(int[] arr) {
        Arrays.sort(arr);
        int N = arr.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            if (BinarySearch.indexOf(arr, -arr[i]) > i) {
                cnt++;
            }
        }
        return cnt;
    }
}
```

***
**1.4.9 已知由倍率实验可得某个程序的时间倍率为$2^b$且问题规模为$N_0$时程序的运行时间为T，给出一个公式预测该程序在处理规模为N的问题时所需的运行时间。**

*Answer：*

由题意，$\frac{T(2N_0)}{T(N_0)}=2^b$，所以$T(2N_0)=2^bT$，同理可得$T(4N_0)=2^{2b}T$...，即$T(2^rN_0)=2^{rb}T$。设$N=2^rN_0$，带入得$T(N)={(\frac{N}{N_0})}^bT$。

***
**1.4.10 修改二分查找算法，使之总是返回和被查找得键匹配的索引最小的元素（且仍然能够保证对数级别的运行时间）。**

*Answer：*

```java
public static int rank(int[] arr, int key) {

    int lo = 0;
    int hi = arr.length - 1;
    while (lo <= hi) {
        // Key is in a[lo..hi] or not present.
        int mid = lo + (hi - lo) / 2;
        if (key < arr[mid]) {
            hi = mid - 1;
        } else if (key > arr[mid]) {
            lo = mid + 1;
        } else {
            // mid等于0时，说明已经遍历到数组的最左侧
            // 且此时角标0的值等于key，直接返回0
            while (mid != 0) {
                // 如果mid左侧元素小于key，说明当前mid为最小角标
                if (arr[mid - 1] < key) {
                    // 跳出循环
                    break;
                } else {
                    // 如果mid左侧元素不小于key，此时说明mid左侧元素等于key
                    // mid减1，进入下一次循环
                    mid--;
                }
            }
            return mid;
        }
    }
    return -1;
}
```

***
**1.4.11 为StaticSETofInts添加一个实例方法howMany()，找出给定键的出现次数，且在最坏情况下所需的运行时间和$logN$成正比。**

*Answer：*

```java
public class StaticSETofInts {

    private int[] a;

    public StaticSETofInts(int[] keys) {

        a = new int[keys.length];
        for (int i = 0; i < keys.length; i++) {
            a[i] = keys[i];
        }

        Arrays.sort(a);
    }

    public boolean contains(int key) {
        return rank(key) != -1;
    }

    public int rank(int key) {
        int lo = 0;
        int hi = a.length - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (key < a[mid]) {
                hi = mid - 1;
            } else if (key > a[mid]) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }

    public int howMany(int key) {

        // 如果数组中不包含key，直接返回0
        int idx = rank(key); // 耗时logN
        if (idx == -1) {
            return 0;
        }

        int cnt = 1;
        int tmp = idx;
        // 向idx的左侧查找
        while (tmp != 0) {
            if (a[tmp - 1] < key) {
                break;
            } else {
                tmp--;
                cnt++;
            }
        }
        tmp = idx;
        // 向idx右侧查找
        while (tmp != a.length - 1) {
            if (a[tmp + 1] > key) {
                break;
            } else {
                tmp++;
                cnt++;
            }
        }
        return cnt;
    }
}
```

上述算法在通常情况下耗时$T$~$logN+t$，其中$t<N$并取决于向左右遍历的总次数。极端情况下，当数组全部由同一个数字组成时，$t=N$。此时总耗时为$T$~$N$。(**需要改进**)

***
**1.4.12 编写一个程序，有序打印给定的两个有序数组（含有N个int值）中所有公共元素，程序在最坏情况下所需的运行时间应该和N成正比。**

*Answer：*

```java
public static void printTheCommon(int[] a, int[] b) {
    for (int i = 0, j = 0; i < a.length && j < b.length;) {
        if (a[i] < b[j]) {
            i++;
        } else if (a[i] > b[j]) {
            j++;
        } else {
            System.out.println("Common Element: " + a[i]);
            i++;
            j++;
        }
    }
}
```

总输入元素数$N=a.length+b.length$，上述算法在最坏情况下将两个数组各遍历一遍，所需时间$T$~$N$。

***
**1.4.13 根据正文中的假设分别给出表示以下数据类型的一个对象所需的内存量：**

- a. Accumulator
- b. Transaction
- c. FixedCapacityStackOfStrings，其容量为$C$且含有$N$个元素。
- d. Point2D
- e. Interval1D
- f. Interval2D
- g. Double

*Answer：*

略。

***

#### 提高题

***
**1.4.14 4-sum。为4-sum设计一个算法。**

*Answer：*

```java
    public static int fourSum(int[] arr) {
        Arrays.sort(arr);
        int N = arr.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                for (int k = j + 1; k < N; k++) {
                    if (BinarySearch.indexOf(arr, -arr[i] - arr[j] - arr[k]) > k) {
                        cnt++;
                    }
                }
            }
        }
        return cnt;
    }
```

***
**1.4.15 快速3-sum。作为热身，使用一个线性级别的算法（而非基于二分查找的线性对数级别的算法）实现TwoSumFaster来计算已排序的数组中和为0的整数对的数量。用相同的思想为3-sum问题给出一个平方级别的算法。**

*Answer：*

**线性级别的TwoSumFaster：**

思路：

数组经过排序后，负数在左，正数在右。设置0角标为lo，arr.length - 1为hi。

- a[lo] + a[hi] > 0, 说明正数太大，hi--。
- a[lo] + a[hi] < 0，说明负数太小，lo++。
- 否则就找到了一对和为零的整数对，lo++, hi--。

循环条件要设置lo < hi，不能设置lo != hi，否则可能会发生角标越界。

线性级别的TwoSumFaster：

```java
public static int linearTwoSumFaster(int[] arr) {
    Arrays.sort(arr);
    int lo = 0;
    int hi = arr.length - 1;
    int cnt = 0;
    while (lo < hi) {
        if (arr[lo] + arr[hi] > 0) {
            hi--;
        } else if (arr[lo] + arr[hi] < 0) {
            lo++;
        } else {
            cnt++;
            hi--;
            lo++;
        }
    }
    return cnt;
}
```

平方级别的ThreeSumFaster：

思路：

在外侧加入一层for循环，该层循环表示三个数中的第一个数，然后进入while循环。

```java
public static int squareThreeSumFaster(int[] arr) {
    Arrays.sort(arr);
    int cnt = 0;
    for (int i = 0; i < arr.length; i++) {
        int lo = i + 1;
        int hi = arr.length - 1;
        while (lo < hi) {
            if (arr[i] + arr[lo] + arr[hi] > 0) {
                hi--;
            } else if (arr[i] + arr[lo] + arr[hi] < 0) {
                lo++;
            } else {
                cnt++;
                hi--;
                lo++;
            }
        }
    }
    return cnt;
}
```

***
**1.4.16 最接近的一对（一维）。编写一个程序，给定一个含有N个double值得数组a[]，在其中找到一对最接近的值：两者之差（绝对值）最小得两个数。程序在最坏情况下所需得运行时间应该是线性对数级别的。**

*Answer：*

思路：先排序，再进行比较

```java
public static void ex1416(double[] arr) {
    
    Arrays.sort(arr); // 该排序操作为NlogN

    double minDiff = Double.MAX_VALUE;
    double minA = 0;
    double minB = 0;

    // 遍历数组的操作为N
    for (int i = 0; i < arr.length; i++) {
        if (arr[i + 1] - arr[i] < minDiff) {
            minA = arr[i];
            minA = arr[i + 1];
            minDiff = arr[i + 1] - arr[i];
        }
    }
    System.out.println("最接近的一对为：" + minA + " " + minB);
}
```

***
**1.4.17 最遥远的一对（一维）。编写一个程序，给定一个含有 N 个 double 值的数组 a[]，在其中找到一对最遥远的值：两者之差（绝对值）最大的两个数。程序在最坏情况下所需的运行时间应该是线性级别的。**

*Answer：*

思路：遍历数组找到最大值和最小值即可。

```java
public static void ex1417(double[] arr) {
    double min = Double.MAX_VALUE;
    double max = Double.MIN_VALUE;
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    System.out.println("最遥远的一对为：" + min + " " + max);
}
```

***
**1.4.18 数组的局部最小元素。编写一个程序，给定一个含有N个不同整数的数组，找到一个局部最小元素：满足a[i] < a[i-1]，且a[i] < a[i+1]的索引i。程序在最坏情况下所需的比较次数为 ~ 2lgN。**

*Answer：*

思路：检查数组的中间值a[N/2]以及和它相邻的元素a[N/2-1]和a[N/2+1]。如果a[N/2]是一个局部最小值则算法终止；否则则在较小的相邻元素的半边中继续查找。

递归实现：

```java
public static int ex1418(int[] arr) {
    return ex1418(arr, 0, arr.length - 1);
}

private static int ex1418(int[] arr, int lo, int hi) {
    
    int mid = (lo + hi) / 2;

    // 当mid到达数组边缘时，此时边缘值就是局部最小值，直接返回
    // 如果满足条件则mid就是局部最小值角标，否则向mid-1和mid+1中较小的一边进行二分查找
    if ((mid == 0 || mid == arr.length - 1) || (arr[mid] < arr[mid - 1] && arr[mid] < arr[mid + 1])) {
        return mid;
    } else if (arr[mid - 1] < arr[mid + 1]) {
        return ex1418(arr, lo, mid - 1);
    } else {
        return ex1418(arr, mid + 1, hi);
    }
}
```

循环写法：

```java
public static int ex1418_2(int[] arr) {
    
    int lo = 0;
    int hi = arr.length - 1;
    
    while (true) {
        int mid = (lo + hi) / 2;
        // 当mid到达数组边缘时，此时边缘值就是局部最小值，直接返回
        // 如果满足条件则mid就是局部最小值角标，否则向mid-1和mid+1中较小的一边进行二分查找
        if ((mid == 0 || mid == arr.length - 1) || (arr[mid] < arr[mid - 1] && arr[mid] < arr[mid + 1])) {
            return mid;
        } else if (arr[mid - 1] < arr[mid + 1]) {
            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }
}
```

***
**1.4.19 矩阵的局部最小元素。给定一个含有$N^2$个不同整数的N×N数组a[]。设计一个运行时间和N成正比的算法来找出一个局部最小元素：满足a[i][j] < a[i+1][j]、a[i][j] < a[i][j+1]、a[i][j] < a[i-1][j]以及a[i][j] < a[i][j-1]的索引i和j。程序运行时间在最坏情况下应该和N成正比。**

*Answer：*

下述算法忽略了局部最小值在矩阵边界上的情况。该算法从[N/2][N/2]位置开始向四个方向之一进行搜索。

（待修改）

```java
class Element {

    private int row;
    private int col;
    private Integer val;

    public Element() {
        this.row = -1;
        this.col = -1;
        this.val = null;
    }

    public void setRow(int row) {
        this.row = row;
    }
    
    public void setCol(int col) {
        this.col = col;
    }

    public void setVal(int val) {
        this.val = val;
    }

    public String toString() {
        return "Element:[" + row + "][" + col + "], val = " + val;
    }
}

// 提高题 1.4.19
public class MatrixLocalMinimum {

    // 判断是否为该行的局部最小值
    private static boolean isRowLocalMinimum(int[] arr, int colIdx) {
        return arr[colIdx] < arr[colIdx - 1] && arr[colIdx] < arr[colIdx + 1];
    }

    // 判断是否为该列的局部最小值
    private static boolean isColLocalMinimum(int[][] matrix, int rowIdx, int colIdx) {
        return matrix[rowIdx][colIdx] < matrix[rowIdx - 1][colIdx]
                && matrix[rowIdx][colIdx] < matrix[rowIdx + 1][colIdx];
    }

    public static Element getLocalMinimum(int[][] matrix) {
        int N = matrix.length;

        if (N < 3) {
            throw new IllegalArgumentException();
        }

        Element e = new Element();

        int rowIdx = N / 2; // 行号
        int colIdx = N / 2; // 列号

        while (rowIdx > 0 && rowIdx < N - 1 && colIdx > 0 && colIdx < N - 1) {
            int[] arr = matrix[rowIdx];
            // 判断是否是该行的局部最小值
            if (isRowLocalMinimum(arr, colIdx)) {
                // 判断是否是该列的局部最小值
                if (isColLocalMinimum(matrix, rowIdx, colIdx)) {
                    // 如果该值即是行局部最小值又是列局部最小值，则对e进行赋值
                    e.setRow(rowIdx);
                    e.setCol(colIdx);
                    e.setVal(matrix[rowIdx][colIdx]);
                    break;
                } else {
                    // 如果该值不是列的局部最小值，对行号进行调整
                    if (matrix[rowIdx + 1][colIdx] > matrix[rowIdx - 1][colIdx]) {
                        rowIdx--;
                    } else {
                        rowIdx++;
                    }
                }
            } else {
                // 如果该值不是行的局部最小值，对列号进行调整
                if (arr[colIdx + 1] > arr[colIdx - 1]) {
                    colIdx--;
                } else {
                    colIdx++;
                }
            }
        }
        return e;
    }

    public static void main(String[] args) {
        int[][] matrix = { 
                { 3, 5, 10, 23 }, 
                { 6, 2, 8, 40 }, 
                { 4, 12, 11, 45 }, 
                { 90, 28, 17, 22 } };
        Element element = getLocalMinimum(matrix);
        System.out.println(element);
    }
}
```

***
**1.4.20 双调查找。如果一个数组中的所有元素是先递增后递减的,则称这个数组为双调的。编写一个程序，给定一个含有N个不同int值的双调数组，判断它是否含有给定的整数。程序在最坏情况下所需的比较次数为~$3lgN$。**

*Answer：*

思路：先找到数组的最大元素，然后以它为界，在数组左右进行二分查找。

```java
public static int ex1420(int[] arr, int key) {
    // 查找双调数组中最大值的角标
    int lo = 0;
    int hi = arr.length - 1;

    int max = (lo + hi) / 2;
    // 寻找最大元素的角标，logN
    while (true) {
        if (arr[max] > arr[max - 1] && arr[max] > arr[max + 1]) {
            break;
        }
        if (arr[max] < arr[max + 1]) {
            lo = max;
        } else {
            hi = max;
        }
        max = (lo + hi) / 2;
    }

    // 遍历结束，此时arr[max]为最大元素
    if (arr[max] == key) {
        return max;
    }

    // 以max为界，将数组拆分为两部分，分别进行二分查找

    // 查找max左边，log(N/2)
    lo = 0;
    hi = max - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (key < arr[mid]) {
            hi = mid - 1;
        } else if (key > arr[mid]) {
            lo = mid + 1;
        } else {
            return mid;
        }
    }

    // 查找max右边，log(N/2)
    lo = max + 1;
    hi = arr.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (key < arr[mid]) {
            lo = mid + 1;
        } else if (key > arr[mid]) {
            hi = mid - 1;
        } else {
            return mid;
        }
    }

    // 程序如果运行到这里，说明没有找到
    return -1;
}
```

***
**1.4.21 无重复之值中的二分查找。用二分查找实现StaticSETofInts。保证contains() 的运行时间为~$lgR$，其中R为参数数组中不同整数的数量。**

*Answer：*

参见1.4.11，其中StaticSETofInts已使用二分查找。

***
**1.4.22 仅用加减实现的二分查找（Mihai Patrascu）。编写一个程序，给定一个含有 N 个不同 int 值的按照升序排列的数组，判断它是否含有给定的整数。只能使用加法和减法以及常数的额外内存空间。程序的运行时间在最坏情况下应该和logN成正比。**

实现一：不对数组进行补齐

```java
public static int fibonacciRank(int[] arr, int key) {
    int fk = 1;
    int fk_1 = 1;
    int fk_2 = 0;
    
    while (fk < arr.length) {
        fk = fk + fk_1;
        fk_1 = fk_1 + fk_2;
        fk_2 = fk - fk_1;
    }
    System.out.println(fk);
    
    int lo = 0;
    
    while (fk_2 >= 0) {
        int i = lo + fk_2 > arr.length - 1 ? arr.length - 1 : lo + fk_2;
        if (arr[i] == key) {
            return i;
        } else if (arr[i] < key) {
            lo = lo + fk_2;
        }
        
        fk = fk_1;
        fk_1 = fk_2;
        fk_2 = fk - fk_1;
    }
    return -1;
}
```

实现二：对数组进行补齐

详情参见：斐波那契查找

```java
// 创建斐波那契数列，该数列的最后一位是斐波那契数列中第一个大于等于i的值
private static Integer[] createFibonacci(int i) {
    
    // 使用List保存每一个数值，最后将List转为Array
    List<Integer> fibList = new ArrayList<>();
    fibList.add(0);
    fibList.add(1);
    
    // 计算斐波那契数列，当数列中的最后一个元素大于等于i时结束循环
    for (int idx = 0; fibList.get(fibList.size() - 1) < i; idx++) {
        fibList.add(fibList.get(idx) + fibList.get(idx + 1));
    }
    
    // 返回数组
    return fibList.toArray(new Integer[0]);
}

public static int fibonacciRank_2(int[] arr, int key) {
    
    // 创建斐波那契数列
    Integer[] fib = createFibonacci(arr.length + 1);

    // 对数组进行扩充
    int[] extArr = new int[fib[fib.length - 1] - 1];
    for (int i = 0; i < extArr.length; i++) {
        if (i >= arr.length) {
            extArr[i] = arr[arr.length - 1];
        } else {
            extArr[i] = arr[i];
        }
    }

    int k = fib.length - 1;
    int lo = 0;
    int hi = arr.length - 1;

    while (lo <= hi) {
        int mid = lo + fib[k - 1] - 1;
        if (key < extArr[mid]) {
            hi = mid - 1;
            k -= 1;
        } else if (key > extArr[mid]) {
            lo = mid + 1;
            k -= 2;
        } else {
            if (mid < arr.length) {
                return mid;
            } else {
                return arr.length - 1;
            }
        }
    }
    return -1;
}
```

***
**1.4.23 分数的二分查找。设计一个算法，使用对数级别的比较次数找出有理数$p/q$其中$0<p<q<N$，比较形式为给定的数是否小于$x$?**

*Answer：*


***
**1.4.24 扔鸡蛋。假设你面前有一栋N层的大楼和许多鸡蛋，假设将鸡蛋从F层或者更高的地方扔下鸡蛋才会摔碎，否则则不会。首先，设计一种策略来确定F的值，其中扔~$lgN$次鸡蛋后摔碎的鸡蛋数量为~$lgN$。然后想办法将成本降低到~$2lgF$。**

*Answer：*

（1）确定F的值，直接使用二分查找即可。
（2）按照$2^0-->2^1-->2^2-->...-->2^{k}$层顺序查找，其中$2^{k}\geq F$，$2^{k-1}<F$。所以实验次数$log_2^F\leq k<log_2^F+1$，并摔碎一个鸡蛋。接下来，在$2^{k-1}+1$~$2^k-1$之间进行二分查找。实验次数$log_2^{({2^{k}-1-2^{k-1}-1+1})}$\>$k-1$。此时最小总试验次数：$T=k+k-1=2k-1$。所以得到$2log_2^F-1\leq T<2log_2^F+1$，即$T$~$2log_2^F$。此时总摔碎鸡蛋数为$S$~$k-1+1=k$，所以$S$~$log_2^F$。

***
**1.4.25 扔两个鸡蛋。和上一题相同的问题，但现在假设你只有两个鸡蛋，而你的成本模型则是扔鸡蛋的次数。设计一种策略，最多扔$2\sqrt{N}$次鸡蛋即可判断出F的值，然后想办法把这个成本降低到~$c\sqrt{F}$次。这和查找命中（鸡蛋完好无损）比未命中（鸡蛋被摔碎）的成本小得多的情形类似。**

*Answer：*

（1）第一个鸡蛋按照$\sqrt{N}$，$2\sqrt{N}$，$3\sqrt{N}$，$4\sqrt{N}$，$5\sqrt{N}$，...，$k\sqrt{N}$顺序进行测试，直至鸡蛋摔碎。此时实验次数$k<=\sqrt{N}$。第二个鸡蛋在$\(k-1)\sqrt{N}$~$\sqrt{N}$之间依次进行，此时最多进行$\sqrt{N}$次实验。所以总实验次数最多为$T=2\sqrt{N}$。
（2）第一个鸡蛋按照$\frac{1\times(1+1)}{2}$，$\frac{2\times(2+1)}{2}$，$\frac{3\times(3+1)}{2}$，...，$\frac{k\times(k+1)}{2}$顺序进行测试，直至鸡蛋摔碎。此时试验次数为k，其中$\frac{k\times(k+1)}{2}\geq F$，$\frac{(k-1)\times k}{2}<F$。解得$\frac{-1+\sqrt{1+8F}}{2}\leq k<\frac{1+\sqrt{1+8F}}{2}$。第二个鸡蛋在$\frac{(k-1)\times(k-1+1)}{2}+1$~$\frac{k\times(k+1)}{2}-1$之间顺序进行实验，此时实验次数为$\frac{k\times(k+1)}{2}-1-\frac{(k-1)\times(k-1+1)}{2}-1+1=k-1$。即总试验次数$T=2k-1$。所以$-2+\sqrt{1+8F}\leq T<\sqrt{1+8F}$，得$T$~$c\sqrt{F}$。

***
**1.4.26 三点共线。假设有一个算法，接受平面上的N个点并能够返回在同一直线上的三个点的组数。证明你能够用这个算法解决3-sum问题。**

*Answer：*

解决3-sum的关键在于对a、b、c三个数的和是否等于0进行判定。这里可以将判定a、b、c三个数的和是否为0转为判定三个点A(a,$a^3$)，B(b,$b^3$)，C(c,$c^3$)是否共线。

![ex1426](images\chapter01\ex1426.PNG)

由上证明可得，若要判断数组中三个数arr[i]、arr[j]、arr[k]的和是否为0，可以构造三个点A(arr[i], ${arr[i]}^3$)，B(arr[j], ${arr[j]}^3$)，C(arr[k], ${arr[k]}^3$)并判断是否共线。

***
**1.4.27 两个栈实现的队列。用两个栈实现一个队列，使得每个队列操作所需要的堆栈操作均摊后为一个常数。**

*Answer：*

当stackDe为空时就把stackEn中的元素弹出并压入到stackDe，这个过程是O(n)的。
但在这个过程之前必然有n个元素入栈，均摊后即为O(1)。

```java
public class TwoStackQueue<T> {
    
    // 用于接受入队的元素
    private Stack<T> stackEn;
    // 用于弹出出队的元素
    private Stack<T> stackDe;
    
    public TwoStackQueue() {
        stackEn = new Stack<>();
        stackDe = new Stack<>();
    }
    
    public void enqueue(T t) {
        stackEn.push(t);
    }
    
    public T dequeue() {
        // 如果stackDe内没有元素，则将stackEn中的元素弹出并压入stackDe中
        if (stackDe.isEmpty()) {
            while (!stackEn.isEmpty()) {
                stackDe.push(stackEn.pop());
            }
        }
        return stackDe.pop();
    }
}
```

***
**1.4.28 一个队列实现的栈。使用一个队列实现一个栈，使得每个栈操作所需的队列操作数量为线性级别。**

*Answer：*

```java
public class QueueStack<Item> {

    private Queue<Item> queue;
    
    public QueueStack() {
        queue = new Queue<>();
    }
    
    // 每次入队后将队列反转
    public void push(Item item) {
        queue.enqueue(item);
        for (int i = 0; i < queue.size() - 1; i++) {
            queue.enqueue(queue.dequeue());
        }
    }
    
    public Item pop() {
        return queue.dequeue();
    }
    
    public static void main(String[] args) {
        
    }
}
```

***
**1.4.29 两个栈实现的 steque。用两个栈实现一个steque（请见练习 1.3.32），使得每个 steque 操作所需的栈操作均摊后为一个常数。**

*Answer：*

```java
public class TwoStackSteque<Item> {
    
    private Stack<Item> stackL;
    private Stack<Item> stackR;
    
    public TwoStackSteque() {
        stackL = new Stack<>();
        stackR = new Stack<>();
    }
    
    // 压入item前，将stackR中的数据弹出并压入stackL
    public void push(Item item) {
        stackRToL();
        stackL.push(item);
    }
    
    // 弹出item前，将stackR中的数据弹出并压入stackL
    public Item pop() {
        stackRToL();
        return stackL.pop();
    }
    
    // 入队item前，将stackL中的数据弹出并压入stackR
    public void enqueue(Item item) {
        stackLToR();
        stackR.push(item);
    }

    // 将stackR中的数据弹出并压入stackL
    private void stackRToL() {
        while (!stackR.isEmpty()) {
            stackL.push(stackR.pop());
        }
    }
    
    // 将stackL中的数据弹出并压入stackR
    private void stackLToR() {
        while (!stackL.isEmpty()) {
            stackR.push(stackL.pop());
        }
    }
    
    public boolean isEmpty() {
        return stackL.isEmpty() && stackR.isEmpty();
    }
}
```

***
**1.4.30 一个栈和一个steque实现的双向队列。使用一个栈和一个steque实现一个双向队列（请见练习 1.3.32），使得双向队列的每个操作所需的栈和steque操作均摊后为一个常数。**

*Answer：*

Steque负责队列头和队列尾入列以及队列头出列，Stack负责队列尾出列。

- 每次在进行队列尾出列之前先检查Steque中是否存在元素，如果存在就弹出所有元素并压入Stack。
- 在入列操作和队列头出列操作前先检查Stack中是否有元素，如果有就弹出所有元素并压入Steque。

经过上述操作，元素不会同时存在于Stack和Steque中。

```java
public class StackStequeQueue<Item> {
    
    private Stack<Item> stack;
    private TwoStackSteque<Item> steque;
    
    public StackStequeQueue() {
        stack = new Stack<>();
        steque = new TwoStackSteque<>();
    }
    
    // 队列头入列
    public void enqueueHead(Item item) {
        stackToSteque();
        steque.push(item);
    }
    
    // 队列尾入列
    public void enqueueTail(Item item) {
        stackToSteque();
        steque.enqueue(item);
    }
    
    // 队列头出列
    public Item dequeueHead() {
        stackToSteque();
        return steque.pop();
    }
    
    // 队列尾出列
    public Item dequeueTail() {
        stequeToStack();
        return stack.pop();
    }
    
    // 将stack中的元素弹出并压入steque
    private void stackToSteque() {
        while (!stack.isEmpty()) {
            steque.push(stack.pop());
        }
    }
    
    // 将steque中的元素弹出并压入stack
    private void stequeToStack() {
        while (!steque.isEmpty()) {
            stack.push(steque.pop());
        }
    }

    public boolean isEmpty() {
        return stack.isEmpty() && steque.isEmpty();
    }
    
    public static void main(String[] args) {
        
    }
}
```

***
**1.4.31 三个栈实现的双向队列。使用三个栈实现一个双向队列，使得双向队列的每个操作所需的栈操作均摊之后为一个常数。**

*Answer：*

```java
public class ThreeStackQueue<Item> {

    private Stack<Item> left; // 操作队列的head
    private Stack<Item> mid;
    private Stack<Item> right; // 操作队列的tail
    private String midMark; // mid数据来源标记

    public ThreeStackQueue() {
        left = new Stack<>();
        mid = new Stack<>();
        right = new Stack<>();
        midMark = "";
    }

    public void enqueueHead(Item item) {
        // 如果mid中有数据，就将mid中的数据倒回其数据源
        if (!mid.isEmpty()) {
            midCtrl();
        }
        left.push(item);
    }

    public void enqueueTail(Item item) {
        // 如果mid中有数据，就将mid中的数据倒回其数据源
        if (!mid.isEmpty()) {
            midCtrl();
        }
        right.push(item);
    }

    public Item dequeueHead() {
        
        // 如果left中有数据，就直接弹出
        if (!left.isEmpty()) {
            return left.pop();
        }
        // 如果left中没有数据，right中有数据，将right倒入mid，并弹出
        if (!right.isEmpty()) {
            rightToMid();
        }
        return mid.pop();
    }

    public Item dequeueTail() {
        
        // 如果right中有数据，就直接弹出
        if (!right.isEmpty()) {
            return right.pop();
        }
        // 如果right中没有数据，left中有数据，将left倒入mid，并弹出
        if (!left.isEmpty()) {
            leftToMid();
        }
        return mid.pop();
    }
    
    // 控制mid数据倒出的方向，方向由midMark决定
    private void midCtrl() {
        if (midMark.equals("Left")) {
            midToLeft();
        } else if (midMark.equals("Right")) {
            midToRight();
        }
    }
    
    // 将mid中数据倒入left
    private void midToLeft() {
        while (!mid.isEmpty()) {
            left.push(mid.pop());
        }
    }

    // 将left中的数据倒入mid
    private void leftToMid() {
        while (!left.isEmpty()) {
            mid.push(left.pop());
        }
        // 记录mid中的数据来自left
        midMark = "Left";
    }

    // 将mid中数据倒入right
    private void midToRight() {
        while (!mid.isEmpty()) {
            right.push(mid.pop());
        }
    }

    // 将right中的数据倒入mid
    private void rightToMid() {
        while (!right.isEmpty()) {
            mid.push(right.pop());
        }
        // 记录mid中的数据来自right
        midMark = "Right";
    }
    
    public static void main(String[] args) {
        
    }
}
```

***
**1.4.32 均摊分析。请证明，对一个基于大小可变的数组实现的空栈的M次操作访问数组的次数和M成正比。**

*Answer：*

***
**1.4.33 32位计算机中的内存需求。给出32位计算机中 Integer、Date、Counter、int[]、double[]、double[][]、String、Node和Stack（链表表示）对象所需的内存，设引用需要 4 字节，表示对象的开销为8字节，所需内存均会被填充为4字节的倍数。**

*Answer：*

- Integer = 4(int) + 8(对象开销) = 12
- Date = 3 * 4(int * 3) + 8(对象开销) = 20
- Counter = 4(String的引用) + 4(int) + 8(对象开销) = 16
- int[] = 8(对象开销) + 4(数组长度) + 4N(元素数量) = 12 + 4N
- double[] = 8(对象开销) + 4(数组长度) + 8N(元素数量) = 12 + 8N
- double[][] = 8(对象开销) + 4(数组长度) + 4M(引用) + M(12 + 8N)(M 个一维数组) = 12 + 16M + 8MN
- String = 8(对象开销) + 3*4(int * 3) + 4(字符数组的引用) = 24
- Node = 8(对象开销) + 4*2(引用*2) = 16
- Stack = 8(对象开销) + 4(引用) + 4(int) = 16

***
**1.4.34 热还是冷。你的目标是猜出1到N之间的一个秘密的整数。每次猜完一个整数后，你会知道你的猜测和这个秘密整数是否相等（如果是则游戏结束）。如果不相等，你会知道你的猜测相比上一次猜测距离该秘密整数是比较热（接近），还是比较冷（远离）。设计一个算法在~$2lgN$之内找到这个秘密整数，然后设计一个算法在~$1lgN$之内找到这个秘密整数。**

*Answer：*

（1）二分查找。对边界值进行猜测：先猜测左边界(lo)，再猜测右边界(hi)，如果边界值猜中的话直接返回。否则：如果右边界比较热，那么左边界向右边界靠，lo=mid；否则，右边界向左边界靠，hi=mid。每次二分查找都要猜测两次（猜测的是当前lo和hi，而不是mid，mid只用于调整猜测边界，而不进行猜测。），~$2lgN$。
（2）

***
**1.4.35 下压栈的时间成本。解释下表中的数据，它显示了各种下压栈的实现的一般时间成本，其中成本模型会同时记录数据引用的数量（指向被压入栈之中的数据的引用，指向的可能是数组，也可能是某个对象的实例变量）和被创建的变量数量。**

数据结构  |  元素类型  |  数据的引用 |  创建的对象
-----|-----|-----|-----
基于链表  |  int | 2N |  N
基于链表  |  Integer | 3N  | 2N
基于大小可变的数组  |  int | ~5N | lgN
基于大小可变的数组  | Integer |~5N |~N

*Answer：*

- 基于链表：
    + int：
    + Integer：Integer对象的引用，当前Node的引用，当前Node的next的引用；创建一个Integer对象，创建一个Node对象。
- 基于大小可变的数组：
    + int：
    + Integer：

***
**1.4.36 下压栈的空间成本。解释下表中的数据，它显示了各种下压栈实现的一般空间成本，其中链表的结点为一个静态的嵌套类，从而避免非静态嵌套类的开销。**

数据结构  |  元素类型  |  N个int值所需的空间（字节）
-----|-----|-----
基于链表  |  int | ~32N
基于链表  |  Integer | ~56N
基于大小可变的数组 |  int | ~4N到~16N之间
基于大小可变的数组 |  Integer | ~32N到~56N之间

*Answer：*

- 基于链表：
    + int：
    + Integer：
- 基于大小可变的数组：
    + int：
    + Integer：

***

#### 实验题

***

**1.4.37 自动装箱的性能代价。通过实验在你的计算机上计算使用自动装箱所付出的性能代价。实现一个FixedCapacityStackOfInts，并使用类似DoublingRatio的用例比较它和泛型FixedCapacityStack在进行大量push()和pop()时的性能。**

*Answer：*

FixedCapacityStackOfInts：

```java
public class FixedCapacityStackOfInts {
    
    private int[] a;  
    private int N;  

    public FixedCapacityStackOfInts(int cap) {
        this.a = new int[cap];
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void push(int item) {
        a[N++] = item;
    }

    public int pop() {
        return a[--N];
    }
}
```

FixedCapacityStack<T>：

```java
public class FixedCapacityStack<T> {
    
    private T[] a;
    private int N;

    @SuppressWarnings("unchecked")
    public FixedCapacityStack(int cap) {
        a = (T[]) new Object[cap];
        N = 0;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void push(T item) {
        a[N++] = item;
    }

    public T pop() {
        return a[--N];
    }
}
```

DoublingRatio：

```java
public class DoublingRatio {
    
    // 测试int类型
    public static double timeTrialofInts(int n) {
        FixedCapacityStackOfInts fcsi = new FixedCapacityStackOfInts(n);
        int[] a = new int[n];
        Random random = new Random(System.currentTimeMillis());
        for (int i = 0; i < n; i++) {
            a[i] = random.nextInt(Integer.MAX_VALUE);
        }
        Stopwatch timer = new Stopwatch();
        // n次push
        for (int i = 0; i < n; i++) {
            fcsi.push(a[i]);
        }
        // n次pop
        for (int i = 0; i < n; i++) {
            fcsi.pop();
        }
        return timer.elapsedTime();
    }

    // 测试Integer类型
    public static double timeTrialInteger(int n) {
        FixedCapacityStack<Integer> fcs = new FixedCapacityStack<>(n);
        int[] a = new int[n];
        Random random = new Random(System.currentTimeMillis());
        for (int i = 0; i < n; i++) {
            a[i] = random.nextInt(Integer.MAX_VALUE);
        }
        Stopwatch timer = new Stopwatch();
        // n次push
        for (int i = 0; i < n; i++) {
            fcs.push(a[i]);
        }
        // n次pop
        for (int i = 0; i < n; i++) {
            fcs.pop();
        }
        return timer.elapsedTime();
    }

    public static void main(String[] args) {
        System.out.println("N int(s) Integer(s) Diff");
        for (int n = 250; true; n += n) {
            double timeInt = timeTrialofInts(n);
            double timeInteger = timeTrialInteger(n);
            System.out.printf("%d %5.3f %5.3f %5.3f\n", n, timeInt, timeInteger, (timeInteger - timeInt));
        }
    }
}
```

测试结果：

N | int(s) | Integer(s) | Diff
-----|-----|-----|-----
250 | 0.000 | 0.000 | 0.000
500 | 0.000 | 0.000 | 0.000
1000 | 0.000 | 0.000 | 0.000
2000 | 0.000 | 0.000 | 0.000
4000 | 0.000 | 0.000 | 0.000
8000 | 0.000 | 0.001 | 0.001
16000 | 0.000 | 0.001 | 0.001
32000 | 0.000 | 0.001 | 0.001
64000 | 0.000 | 0.000 | 0.000
128000 | 0.001 | 0.002 | 0.001
256000 | 0.000 | 0.002 | 0.002
512000 | 0.000 | 0.004 | 0.004
1024000 | 0.001 | 0.006 | 0.005
2048000 | 0.001 | 0.051 | 0.050
4096000 | 0.002 | 0.013 | 0.011
8192000 | 0.003 | 0.048 | 0.045
16384000 | 0.007 | 0.140 | 0.133
32768000 | 0.014 | 4.922 | 4.908
65536000 | 0.029 | 6.230 | 6.201
131072000 | 0.058 | 43.889 | 43.831

***
**1.4.38 3-sum 的初级算法的实现。通过实验评估以下 ThreeSum 内循环的实现性能。**

```java
for (int i = 0; i < N; i++)
    for (int j = 0; j < N; j++)
        for (int k = 0; k < N; k++)
            if (i < j && j < k)
                if (a[i] + a[j] + a[k] == 0)
                    cnt++;
```

*Answer：*

```java
public class DoublingTest3Sum {

    // 3-sum的初级算法
    private static int primary3sum(int[] a) {
        int N = a.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                for (int k = 0; k < N; k++) {
                    if (i < j && j < k) {
                        if (a[i] + a[j] + a[k] == 0) {
                            cnt++;
                        }
                    }
                }
            }
        }
        return cnt;
    }

    // 原threeSum算法
    private static int threeSum(int[] a) {
        int N = a.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                for (int k = j + 1; k < N; k++) {
                    if (a[i] + a[j] + a[k] == 0) {
                        cnt++;
                    }
                }
            }
        }
        return cnt;
    }
    
    public static double timeTrialofprimary3sum(int n) {
        int[] a = new int[n];
        Random random = new Random(System.currentTimeMillis());
        for (int i = 0; i < n; i++) {
            a[i] = random.nextInt(Integer.MAX_VALUE / 2 - Integer.MIN_VALUE / 2) + Integer.MIN_VALUE / 2;
        }
        Stopwatch timer = new Stopwatch();
        primary3sum(a);
        return timer.elapsedTime();
    }

    public static double timeTrialThreeSum(int n) {
        int[] a = new int[n];
        Random random = new Random(System.currentTimeMillis());
        for (int i = 0; i < n; i++) {
            a[i] = random.nextInt(Integer.MAX_VALUE / 2 - Integer.MIN_VALUE / 2) + Integer.MIN_VALUE / 2;
        }
        Stopwatch timer = new Stopwatch();
        threeSum(a);
        return timer.elapsedTime();
    }
    
    public static void main(String[] args) {
        
        System.out.println("N primary3Sum threeSum");
        for (int n = 250; true; n += n) {
            double primary3Sum = timeTrialofprimary3sum(n);
            double threeSum = timeTrialThreeSum(n);
            System.out.printf("%d %5.4f %5.4f\n", n, primary3Sum, threeSum);
        }
    }
}
```

结果：

N |primary3Sum | threeSum
-----|-----|-----
250 | 0.0070 | 0.0040
500 | 0.0280 | 0.0090
1000 | 0.1990 | 0.0380
2000 | 1.6720 | 0.5970
4000 | 15.3850 | 5.0760

***
**1.4.39 改进倍率测试的精度。修改 DoublingRatio，使它接受另一个命令行参数来指定对于每个 N 值调用timeTrial()方法的次数。用程序对每个N执行10、100和1000遍实验并评估结果的准确程度。**

*Answer：*

使用3-sum的初级算法进行测试。

```java
public class DoublingRatioPrecise {
    
    // 3-sum的初级算法
    private static int primary3sum(int[] a) {
        int N = a.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N; j++) {
                for (int k = 0; k < N; k++) {
                    if (i < j && j < k) {
                        if (a[i] + a[j] + a[k] == 0) {
                            cnt++;
                        }
                    }
                }
            }
        }
        return cnt;
    }

    public static double timeTrialofprimary3sum(int n) {
        int[] a = new int[n];
        Random random = new Random(System.currentTimeMillis());
        for (int i = 0; i < n; i++) {
            a[i] = random.nextInt(Integer.MAX_VALUE / 2 - Integer.MIN_VALUE / 2) + Integer.MIN_VALUE / 2;
        }
        Stopwatch timer = new Stopwatch();
        primary3sum(a);
        return timer.elapsedTime();
    }

    public static void main(String[] args) {
        System.out.println("数据量 重复次数 平均耗时");
        for (int n = 250; true; n += n) {
            for (int i = 10; i <= 1000; i *= 10) {
                double time = 0;
                for (int j = 0; j < i; j++) {
                    time += timeTrialofprimary3sum(n);
                }
                System.out.printf("%d %d %10.7f\n", n, i, time / i);
            }
            System.out.println();
        }
    }
}
```

结果：

数据量 | 重复次数 | 平均耗时(s)
-----|-----|-----
250 | 10  |  0.0038000
250 | 100 |  0.0033600
250 | 1000  |  0.0037960
 | | | 
500 | 10  |  0.0313000
500 | 100  |  0.0314200
500 | 1000  |  0.0313880
 | | | 
1000 | 10  |  0.2454000
1000 | 100  |  0.2450400
1000 | 1000  |  0.2458910

***
**1.4.40 随机输入下的3-sum问题。猜测找出N个随机int值中和为0的整数三元组的数量所需的时间并验证你的猜想。如果你擅长数学分析，请为此问题给出一个合适的数学模型，其中所有值均匀的分布在-M和M之间，且M不能是一个小整数。**

*Answer：*

***
**1.4.41 运行时间。使用DoublingRatio估计在你的计算机上用TwoSumFast、TwoSum、ThreeSumFast以及ThreeSum处理一个含有100万个整数的文件所需的时间。**

*Answer：*

分别对四个算法进行倍率实验得到倍率，然后再估计$N = 1M$所需的时间即可。

***
**1.4.42 问题规模。设在你的计算机上用 TwoSumFast、TwoSum、ThreeSumFast以及ThreeSum能够处理的问题规模为$2^P\times10^3$个整数。使用DoublingRatio估计P的最大值。**

*Answer：*

假设设定用时为3600s，即求1个小时可以处理的问题规模。

根据倍率实验可得：

问题规模 | 时间 | 倍率
-----|-----|-----
...|...|...
N | t | m
2N | mt | m
...|...|...
$2^P\times10^3$ | 3600 | m

此时N、m、t均已知。则当$m^kt=3600$时，$2^kN=2^P\times1000$，解出P即可。

实现略。

***
**1.4.43 大小可变的数组与链表。通过实验验证对于栈来说基于大小可变的数组的实现快于基于链表的实现的猜想（请见练习1.4.35和练习1.4.36）。为此实现另一个版本的DoublingRatio，计算两个程序的运行时间的比例。**

*Answer：*

略。

***
**1.4.44 生日问题。编写一个程序，从命令行接受一个整数 N 作为参数并使用 StdRandom.uniform()生成一系列0到N-1之间的随机整数。通过实验验证产生第一个重复的随机数之前生成的整数数量为~$\sqrt{(πN/2)}$。**

*Answer：*

```java
public int ex1444(int N) {
    
    List<Integer> list = new ArrayList<>();

    while (true) {
        int num = StdRandom.uniform(0, N);
        if (!list.contains(num)) {
            list.add(num);
        } else {
            System.out.println("生成第" + list.size() + "个数字后发生重复。");
            break;
        }
    }
    return list.size();
}

@Test
public void junitTest() {
    int N = 20000;
    int cnt = 0;
    for (int i = 0; i < 5000; i++) {
        cnt += ex1444(N);
    }
    System.out.println("√(πN/2) = " + Math.sqrt(Math.PI * N / 2) + "，平均生成" + cnt / 5000 + "个数字后发生重复。");
}
```

结果：

    ...
    生成第230个数字后发生重复。
    生成第267个数字后发生重复。
    生成第100个数字后发生重复。
    生成第154个数字后发生重复。
    生成第216个数字后发生重复。
    生成第157个数字后发生重复。
    生成第103个数字后发生重复。
    √(πN/2) = 177.2453850905516，平均生成178个数字后发生重复。

***
**1.4.45 优惠券收集问题。用和上一题相同的方式生成随机整数。通过实验验证生成所有可能的整数值所需生成的随机数总量为 ~$NH_N$（调和级数）。**

*Answer：*

```java
// 判断boolean数组是否都为true
private boolean isAllTrue(boolean[] a) {
    for (int i = 0; i < a.length; i++) {
        if (!a[i]) {
            return false;
        }
    }
    return true;
}

public int ex1445(int N) {

    boolean[] mark = new boolean[N];
    
    int cnt = 0;
    
    // 一直循环，直到mark的所有元素均为true
    while (true) {
        mark[StdRandom.uniform(0, N)] = true;
        cnt++;
        if (isAllTrue(mark)) {
            System.out.println("生成" + cnt + "个数字后所有可能均已出现");
            break;
        }
    }
    return cnt;
}

@Test
public void junitTest() {
    int N = 10;
    int cnt = 0;
    for (int i = 0; i < 500; i++) {
        cnt += ex1445(N);
    }
    System.out.println("N = 10，NHN = " + 29.2897 + "，平均生成" + cnt/500 + "个数字后所有可能都出现。");
}
```

结果：

    ...
    生成33个数字后所有可能均已出现
    生成25个数字后所有可能均已出现
    生成51个数字后所有可能均已出现
    生成47个数字后所有可能均已出现
    N = 10，NHN = 29.2897，平均生成29个数字后所有可能都出现。

### 1.5 案例研究：union-find算法

***

#### 练习题

***
**1.5.1 使用quick-find算法处理序列9-0 3-4 5-8 7-2 2-1 5-7 0-3 4-2。对于输入的每一对整数，给出id[]数组的内容和访问数组的次数。**

*Answer：*

```java
public class QuickFindEx151 {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    private int cost; // 用于记录访问数组的次数

    public QuickFindEx151(int N) {
        count = N;
        cost = 0;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-find算法
     */
    private int find(int p) {
        cost++; // 调用find()方法会访问一次数组
        return id[p];
    }
    
    public void resetCost() {
        cost = 0;
    }

    public void union(int p, int q) {
        // 将p和q归并到相同分量中
        int pID = find(p);
        int qID = find(q);

        // 如果p和q已经在同一个分量中则不需要采取任何行动
        if (pID == qID) {
            return;
        }

        // 将p的分量重命名为q的名称
        for (int i = 0; i < id.length; i++) {
            if (id[i] == pID) {
                cost++; // 赋值操作
                id[i] = qID;
            }
        }
        cost += id.length; // 遍历数组操作
        count--;
    }

    // 打印当前数组和访问次数的方法
    public void printInf() {
        for (int i = 0; i < id.length; i++) {
            System.out.print(id[i] + " ");
        }
        System.out.println("访问数组次数：" + cost);
    }

    public static void main(String[] args) {
        int N = StdIn.readInt();
        QuickFindEx151 uf = new QuickFindEx151(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            uf.printInf();
            uf.resetCost();
        }
        StdOut.println(uf.count + " components");
    }
}
```

结果：

    0 1 2 3 4 5 6 7 8 0 访问数组次数：15
    0 1 2 4 4 5 6 7 8 0 访问数组次数：15
    0 1 2 4 4 8 6 7 8 0 访问数组次数：15
    0 1 2 4 4 8 6 2 8 0 访问数组次数：15
    0 1 1 4 4 8 6 1 8 0 访问数组次数：16
    0 1 1 4 4 1 6 1 1 0 访问数组次数：16
    4 1 1 4 4 1 6 1 1 4 访问数组次数：16
    1 1 1 1 1 1 6 1 1 1 访问数组次数：18
    2 components

***
**1.5.2 使用quick-union算法（请见 1.5.2.3 节代码框）完成练习1.5.1。另外，在处理完输入的每对整数之后画出id[]数组表示的森林。**

*Answer：*

```java
public class QuickUnionEx152 {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量
    
    @SuppressWarnings("unused")
    private int visitArrayCount; // 记录访问数组的次数

    public QuickUnionEx152(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-union算法
     */
    private int find(int p) {
        // 找出分量的名称
        while (p != id[p]) {
            p = id[p];
            visitArrayCount++;
        }
        return p;
    }

    public void union(int p, int q) {
        // 将p和q的根节点统一
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) {
            return;
        }

        id[pRoot] = qRoot;
        visitArrayCount++;
        count--;
    }
    
    // 打印数组的方法
    public void printTree() {
        // 待实现
    }

    public static void main(String[] args) {
        int N = StdIn.readInt(); // 读取触点数量
        QuickUnionEx152 uf = new QuickUnionEx152(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            uf.printTree();
        }
        StdOut.println(uf.count + " components");
    }
}
```

***
**1.5.3 使用加权quick-union算法（请见算法 1.5）完成练习 1.5.1 。**

*Answer：*

```java
public class WeightedQuickUnionEx153 {
    private int[] id; // 父链接数组（由触点索引）
    private int[] sz; // （由触点索引的）各个根节点所对应的分量的大小
    private int count; // 连通分量的数量

    private int cost; // 记录访问id数组的次数

    public WeightedQuickUnionEx153(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            sz[i] = 1;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        while (p != id[p]) {
            p = id[p];
            cost += 2; // 若进入while循环，则判断条件一次，赋值一次
        }
        cost++; // 若未进入while循环，则只有判断条件的一次
        return p;
    }

    public void resetCost() {
        cost = 0;
    }

    public void union(int p, int q) {
        int i = find(p);
        int j = find(q);
        if (i == j) {
            return;
        }

        if (sz[i] < sz[j]) {
            id[i] = j;
            sz[j] += sz[i];
        } else {
            id[j] = i;
            sz[i] += sz[j];
        }
        cost++; // 对id数组赋值
        count--;
    }

    public void printInf() {
        for (int i = 0; i < id.length; i++) {
            System.out.print(id[i] + " ");
        }
        System.out.println("访问数组次数：" + cost);
    }

    public static void main(String[] args) {
        int N = StdIn.readInt();
        WeightedQuickUnionEx153 uf = new WeightedQuickUnionEx153(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            uf.printInf();
            uf.resetCost();
        }
        StdOut.println(uf.count + " components");
    }
}
```

结果：

    9 1 2 3 4 5 6 7 8 9 访问数组次数：5
    9 1 2 3 3 5 6 7 8 9 访问数组次数：5
    9 1 2 3 3 5 6 7 5 9 访问数组次数：5
    9 1 7 3 3 5 6 7 5 9 访问数组次数：5
    9 7 7 3 3 5 6 7 5 9 访问数组次数：9
    9 7 7 3 3 7 6 7 5 9 访问数组次数：5
    9 7 7 9 3 7 6 7 5 9 访问数组次数：9
    9 7 7 9 3 7 6 7 5 7 访问数组次数：17
    2 components

***
**1.5.4 在正文的加权quick-union算法示例中，对于输入的每一对整数（包括对照输入和最坏情况下的输入），给出id[]和sz[]数组的内容以及访问数组的次数。**

*Answer：*

```java
public class WeightedQuickUnionEx154 {
    private int[] id; // 父链接数组（由触点索引）
    private int[] sz; // （由触点索引的）各个根节点所对应的分量的大小
    private int count; // 连通分量的数量
    
    private int visitIdCount; // 记录访问id数组的次数
    private int visitSzCount; // 记录访问Sz数组的次数

    public WeightedQuickUnionEx154(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            sz[i] = 1;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        while (p != id[p]) {
            p = id[p];
            visitIdCount++;
        }
        return p;
    }

    public void union(int p, int q) {
        int i = find(p);
        int j = find(q);
        if (i == j) {
            return;
        }

        if (sz[i] < sz[j]) {
            id[i] = j;
            sz[j] += sz[i];
        } else {
            id[j] = i;
            sz[i] += sz[j];
        }
        
        visitIdCount++;
        visitSzCount += 2;
        count--;
    }
    
    public void printInf() {
        
        System.out.print("id[]: ");
        for (int i = 0; i < id.length; i++) {
            System.out.print(id[i] + " ");
        }
        System.out.println();
        
        System.out.print("sz[]: ");
        for (int i = 0; i < sz.length; i++) {
            System.out.print(sz[i] + " ");
        }
        System.out.println();
        
        System.out.println("访问id数组次数：" + visitIdCount);
        System.out.println("访问sz数组次数：" + visitSzCount);
    }
    
    public static void main(String[] args) {
        int N = StdIn.readInt();
        WeightedQuickUnionEx154 uf = new WeightedQuickUnionEx154(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            uf.printInf();
        }
        StdOut.println(uf.count + " components");
    }
}
```

结果：

*对照输入：*

    id[]: 0 1 2 4 4 5 6 7 8 9 
    sz[]: 1 1 1 1 2 1 1 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 1 2 4 4 5 6 7 4 9 
    sz[]: 1 1 1 1 3 1 1 1 1 1 
    访问id数组次数：9
    访问sz数组次数：5
    id[]: 0 1 2 4 4 6 6 7 4 9 
    sz[]: 1 1 1 1 3 1 2 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 1 2 4 4 6 6 7 4 4 
    sz[]: 1 1 1 1 4 1 2 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 2 2 4 4 6 6 7 4 4 
    sz[]: 1 1 2 1 4 1 2 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 6 2 2 4 4 6 6 7 4 4 
    sz[]: 1 1 2 1 4 1 3 1 1 1 
    访问id数组次数：9
    访问sz数组次数：5
    id[]: 6 2 2 4 4 6 6 2 4 4 
    sz[]: 1 1 3 1 4 1 3 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 6 2 6 4 4 6 6 2 4 4 
    sz[]: 1 1 3 1 4 1 6 1 1 1 
    访问id数组次数：9
    访问sz数组次数：5
    2 components

*最坏输入：*

    id[]: 0 0 2 3 4 5 6 7 
    sz[]: 2 1 1 1 1 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 2 2 4 5 6 7 
    sz[]: 2 1 2 1 1 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 2 2 4 4 6 7 
    sz[]: 2 1 2 1 2 1 1 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 2 2 4 4 6 6 
    sz[]: 2 1 2 1 2 1 2 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 0 2 4 4 6 6 
    sz[]: 4 1 2 1 2 1 2 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 0 2 4 4 4 6 
    sz[]: 4 1 2 1 4 1 2 1 
    访问id数组次数：5
    访问sz数组次数：5
    id[]: 0 0 0 2 0 4 4 6 
    sz[]: 8 1 2 1 4 1 2 1 
    访问id数组次数：5
    访问sz数组次数：5
    1 components

***
**1.5.5 在一台每秒能够处理$10^9$条指令的计算机上，估计quick-find算法解决含有$10^9$个触点和$10^6$条连接的动态连通性问题所需的最短时间（以天记）。假设内循环for的每一次迭代需要执行10条机器指令。**

*Answer：*

触点：$N=10^9$，连接：$M=10^6$，对于quick-find算法，每次union()都要遍历整个数组。则for循环（访问数组）的次数最少为：$MN=10^{15}$。由于每次for循环迭代都需要10条机器指令，所以共需要$10^{16}$条机器指令。所以耗时$T=10^{16}/10^9=10^7$秒，约为115.7天。

***
**1.5.6 使用加权quick-union算法完成练习1.5.5 。**

*Answer：*

当有N个触点时，加权quick-union的增长数量级为$logN$。所以根据上题思路，总时间$T=log10^9\times10^6*10/10^9=0.299$秒。

***
**1.5.7 分别为quick-find算法和quick-union算法实现QuickFindUF类和QuickUnionUF类。**

*Answer：*

QuickFindUF：

```java
public class QuickFindUF {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    public QuickFindUF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-find算法 该算法的运行时间对于最终只能得到少数连通分量的一般应用是平方级别的
     */
    private int find(int p) {
        return id[p];
    }

    public void union(int p, int q) {
        // 将p和q归并到相同分量中
        int pID = find(p);
        int qID = find(q);

        // 如果p和q已经在同一个分量中则不需要采取任何行动
        if (pID == qID) {
            return;
        }

        // 将p的分量重命名为q的名称
        for (int i = 0; i < id.length; i++) {
            if (id[i] == pID) {
                id[i] = qID;
            }
        }
        count--;
    }

    public static void main(String[] args) {
        int N = StdIn.readInt(); // 读取触点数量
        QuickFindUF uf = new QuickFindUF(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (uf.connected(p, q)) { // 如果已连通，则忽略
                continue;
            }
            uf.union(p, q); // 归并分量
            StdOut.println(p + " " + q); // 打印链接
        }
        StdOut.println(uf.count + " components");
    }
}
```

QuickUnionUF：

```java
public class QuickUnionUF {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    public QuickUnionUF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        // 找出分量的名称
        while (p != id[p]) {
            p = id[p];
        }
        return p;
    }

    public void union(int p, int q) {
        // 将p和q的根节点统一
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) {
            return;
        }

        id[pRoot] = qRoot;
        count--;

    }

    public static void main(String[] args) {
        int N = StdIn.readInt(); // 读取触点数量
        QuickUnionUF uf = new QuickUnionUF(N); // 创建对象，初始化分量
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (uf.connected(p, q)) { // 如果已连通，则忽略
                continue;
            }
            uf.union(p, q); // 归并分量
            StdOut.println(p + " " + q); // 打印链接
        }
        StdOut.println(uf.count + " components");
    }
}
```

***
**1.5.8 用一个反例证明quick-find算法中的union()方法的以下直观实现是错误的：**

```java
public void union(intp, int q) {
    if (connected(p, q)) {
        return;
    }
    for (int i = 0; i < id.length; i++) {
        if (id[i] == id[p]) {
            id[i] == id[q];
        }
    }
    count--;
}
```

*Answer：*

正确的union方法如下：

```java
public void union(int p, int q) {
    // 将p和q归并到相同分量中
    int pID = find(p);
    int qID = find(q);

    // 如果p和q已经在同一个分量中则不需要采取任何行动
    if (pID == qID) {
        return;
    }

    // 将p的分量重命名为q的名称
    for (int i = 0; i < id.length; i++) {
        if (id[i] == pID) {
            id[i] = qID;
        }
    }
    count--;
}
```

假设现在数组为：

    idx:  0 1 2 3 4 5 6 7 8 9
    id[]: 0 0 0 0 0 5 5 5 5 5 

此时读入p=0，q=6。

根据正确的算法有：
    
    pID = find(p) = 0;
    qID = find(q) = 5;
    for循环开始遍历数组：
    i = 0时，id[0] == 0成立，id[0]重新赋值为qID，5。
    i = 1时，id[1] == 0成立，id[1]重新赋值为qID，5。
    ...
    i = 5时，id[5] == 0，不成立。
    ...
    循环结束。

根据错误的算法有：

    for循环开始遍历数组：此时id[p] == 0, id[q] == 5。
    i = 0时，id[0] == id[p]成立，id[0]重新赋值为id[q]，5。
    i = 1时，id[1] == id[p]不成立，因为此时id[p]，即id[0]已在上一次循环中被赋值为5。
    ...
    在上述情况下，循环结束时只有id[0]被修改，而其后应该被修改的id[1]~id[4]的值仍为0。

由于id[p]在循环中会被修改，所以如果在id[p]之后还有要修改的元素，就会出现错误，因为if语句的条件已经发生改变。

而正确的算法则在循环开始前使用pID记录了id[p]的值，所以即使id[p]的值在循环被改变也不会影响if语句的条件。

***
**1.5.9 画出下面的id[]数组所对应的树。这可能是加权quick-union算法得到的结果吗？解释为什么不可能，或者给出能够得到该数组的一系列操作。**

    idx : 0 1 2 3 4 5 6 7 8 9
    id[]: 1 1 3 1 5 6 1 3 4 5

*Answer：*

树如下：

![ex159](images\chapter01\ex159.PNG)

根据定义：一棵树的**大小**是它的节点数量。树中的一个节点的**深度**是它到根节点的路径上的链接数，树的**高度**是它的所有节点中的最大深度。

上述树的最大深度为4。而对于N个触点，加权quick-union算法构造的树的任意节点的深度最多为$logN$。

所以对于$N=10$的树，其最大深度为$log10=3.32$。而当前这颗树的最大深度为4，所以这颗树不可能由加权quick-union算法得到。

***
**1.5.10 在加权quick-union算法中，假设我们将id[find(p)]的值设为q而非id[find[q]]，所得的算法是正确的吗？**

*Answer：*

是正确的。

设为q，代表将id[find(p)]这棵树的根挂在q上；
设为id[find[q]]，代表将id[find[p]]这颗树的根挂在q所在树的根上。

相比之下，前者会显著增加树的高度，进而增加每次find()操作的开销。

***
**1.5.11 实现加权quick-find算法，其中我们总是将较小的分量重命名为较大分量的标识符。这种改变会对性能产生怎样的影响？**

*Answer：*

```java
public class WeightedQuickFindUF {

    private int[] id; // 分量id，以触点为索引
    private int[] sz; // 记录每个连通分量的大小
    private int count; // 分量数量

    public WeightedQuickFindUF(int N) {
        count = N;
        id = new int[N];
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
        for (int i = 0; i < N; i++) {
            sz[i] = 1;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-find算法 该算法的运行时间对于最终只能得到少数连通分量的一般应用是平方级别的
     */
    private int find(int p) {
        return id[p];
    }

    public void union(int p, int q) {
        // 将p和q归并到相同分量中
        int pID = find(p);
        int qID = find(q);

        // 如果p和q已经在同一个分量中则不需要采取任何行动
        if (pID == qID) {
            return;
        }

        // 将连通分量数量较小的归并到较大的中
        if (sz[pID] > sz[qID]) {
            for (int i = 0; i < id.length; i++) {
                if (id[i] == qID) {
                    id[i] = pID;
                }
            }
            // 较大的连通分量数量加上较小的连通分量的数量
            sz[pID] += sz[qID];
        } else {
            for (int i = 0; i < id.length; i++) {
                if (id[i] == pID) {
                    id[i] = qID;
                }
            }
            sz[qID] += sz[pID];
        }
        count--;
    }
}
```

这样做可以轻微减小union()方法中数组赋值的次数，提升性能。

***

#### 提高题

***
**1.5.12 使用路径压缩的quick-union算法。根据路径压缩修改quick-union算法（请见 1.5.2.3 节），在find()方法中添加一个循环来将从p到根节点的路径上的每个触点都连接到根节点。给出一列输入，使该方法能够产生一条长度为4的路径。注意：该算法的所有操作的均摊成本已知为对数级别。**

*Answer：*

直接参照官方实现：

```java
public class QuickUnionPathCompressionUF {
    private int[] id;
    private int count;

    public QuickUnionPathCompressionUF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public int find(int p) {
        int root = p;
        while (root != id[root]) {
            root = id[root];
        }
        // 对路径进行压缩
        // 找到根节点后，再访问一遍p到根节点这条路径上的所有结点，将它们直接和根节点相连。
        while (p != root) {
            int newp = id[p];
            id[p] = root;
            p = newp;
        }
        return root;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ) {
            return;
        }
        id[rootP] = rootQ;
        count--;
    }
}
```

上述算法在使用书中quick-union算法的对照输入时应该产生如下的树：

![ex1512](images\chapter01\ex1512.png)

路径发生压缩，原quick-union算法在使用上述输入时生成的最大树深度为3，经过路径压缩后为2。

上述算法在使用书中quick-union算法的最坏输入（0-1，0-2，0-3，0-4，0-5，...）时应该产生如下的树：

![ex1512worst](images\chapter01\ex1512worst.png)

0-5时即得到路径长度为4的树。

***
**1.5.13 使用路径压缩的加权quick-union算法。修改加权quick-union算法（算法1.5），实现如练习1.5.12所述的路径压缩。给出一列输入，使该方法能产生一棵高度为4的树。注意：该算法的所有操作的均摊成本已知被限制在反Ackermann函数的范围之内，且对于实际应用中可能出现的所有N值均小于5。**

*Answer：*

直接参照官方实现：

```java
public class WeightedQuickUnionPathCompressionUF {

    private int[] parent;
    private int[] size;
    private int count;

    public WeightedQuickUnionPathCompressionUF(int N) {
        count = N;
        parent = new int[N];
        size = new int[N];
        for (int i = 0; i < N; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    // validate that p is a valid index
    private void validate(int p) {
        int n = parent.length;
        if (p < 0 || p >= n) {
            throw new IllegalArgumentException("index " + p + " is not between 0 and " + (n - 1));
        }
    }

    public int find(int p) {
        validate(p);
        int root = p;
        while (root != parent[root]) {
            root = parent[root];
        }
        // 路径压缩
        while (p != root) {
            int newp = parent[p];
            parent[p] = root;
            p = newp;
        }
        return root;
    }

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ)
            return;

        // make smaller root point to larger one
        if (size[rootP] < size[rootQ]) {
            parent[rootP] = rootQ;
            size[rootQ] += size[rootP];
        } else {
            parent[rootQ] = rootP;
            size[rootP] += size[rootQ];
        }
        count--;
    }
}
```

上述算法在使用书中加权quick-union算法的对照输入时应该产生如下的树：

![ex1513](images\chapter01\ex1513.png)

由于该输入产生的情况没有可能发生路径压缩，所以得到的树和不使用路径压缩得到的树一致。

上述算法在使用书中加权quick-union算法的最坏输入时应该产生如下的树：

![ex1513worst](images\chapter01\ex1513worst.png)

由于要生成高度（最大深度）为4的树，所以此时N最小应为16，输入顺序为：0-1，2-3，4-5，6-7，8-9，10-11，12-13，14-15，0-4，8-12，0-8。此时最高度为4。

***
**1.5.14 根据高度加权的quick-union算法。给出UF的一个实现，使用和加权quick-union算法相同的策略，但记录的是树的高度并总是将较矮的树连接到较高的树上。用算法证明N个触点的树的高度不会超过其大小的对数级别。**

*Answer：*

```java
public class HeightWeightedQuickUnionUF {
    
    private int[] id; // 父链接数组（由触点索引）
    private int[] height; // 记录每棵树的高度
    private int count; // 连通分量的数量

    public HeightWeightedQuickUnionUF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
        height = new int[N];
        for (int i = 0; i < N; i++) {
            height[i] = 0; // 由于记录的是高度，所以初始化为0
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        while (p != id[p]) {
            p = id[p];
        }
        return p;
    }

    public void union(int p, int q) {
        int i = find(p);
        int j = find(q);
        if (i == j) {
            return;
        }

        // 小树归并到大树时，大树的高度不变
        if (height[i] < height[j]) {
            id[i] = j;
        } else if (height[i] > height[j]){
            id[j] = i;
        } else {
            // 只有当两颗高度相同的树进行归并时，才将树的高度增加一
            // 这里将q所在树归并到p所在树，并增加p所在树的高度
            id[j] = i;
            height[i]++;
        }
        count--;
    }
}
```

证明：

由于树的高度（h）只有在合并两颗等高的树时才会增加，所以每次都使用相同高度的最小树进行合并。

$N=2$，$h=1$；$N=4$，$h=2$；$N=8$，$h=3$；$N=16$，$h=4$；$N=32$，$h=5$...，即在最坏情况下有$2^h=N$，所以对于通常情况$2^h\leq N$，故得$h\leq log_2^N$。

***
**1.5.15 二项树。请证明，对于加权quick-union算法，在最坏情况下树中的每一层的结点数均为二项式系数。在这种情况下，计算含有$N=2^n$个节点的树中节点的平均深度。**

*Answer：*

（1）n分别为2、3、4时的最坏情况如下图所示：

$n=2$，此时每层结点数分别为：$1=C_2^0$、$2=C_2^1$、$1=C_2^2$
![ex1515n=2](images\chapter01\ex1515n=2.png)
$n=3$，此时每层结点数分别为：$1=C_3^0$、$3=C_3^1$、$3=C_3^2$、$1=C_3^3$
![ex1515n=3](images\chapter01\ex1515n=3.png)
$n=4$，此时每层结点数分别为：$1=C_4^0$、$4=C_4^1$、$6=C_4^2$、$4=C_4^3$、$1=C_4^4$
![ex1515n=4](images\chapter01\ex1515n=4.png)

$n=3$的树由两颗$n=2$的树合并而来，$n=4$的树由两颗$n=3$的树合并而来。
由上我们可以观察得到，新树S中任意一行（$k$）的结点数等于树A的第$k$行的结点数加上树B的第$k-1$行的结点数。即$T_k^S=T_k^A+T_{k-1}^B$（S树的高度（$n$）比A、B大1）。这符合二项式系数的构造特点，令$T_k^S=C_n^k$、$T_k^A=C_{n-1}^k$、$T_{k-1}^B=C_{n-1}^{k-1}$，得$C_n^k=C_{n-1}^k+C_{n-1}^{k-1}$。所以在最坏情况下树中的每一层的结点数均为二项式系数。（证明不严谨）

（2）在$N=2^n$和最坏情况下，第$k$层结点数和其深度分别为：$C_n^{k-1}$和$k-1$。

所以平均深度$D$：

\begin{align}
D & = \frac{\sum_{k=1}^{n+1}[(k-1)C_n^{k-1}]}{2^n} \\
& = \frac{\sum_{m=0}^n(mC_n^m)}{2^n} \\
& = \frac{n2^{n-1}}{2^n} \\
& = \frac{n}{2}
\label{eq:ex1515}
\end{align}

***
**1.5.16 均摊成本的图像。修改你为练习1.5.7给出的实现，绘出如正文所示的均摊成本的图像。**

*Answer：*

QuickFind：

```java
public class QuickFindEx1516 {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    private int total; // 记录访问数组的总次数
    private int cost; // 本次操作访问数组次数

    public QuickFindEx1516(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-find算法 该算法的运行时间对于最终只能得到少数连通分量的一般应用是平方级别的
     */
    private int find(int p) {
        cost++;
        total += cost;
        return id[p];
    }

    // 重置本次操作的数组访问次数
    public void resetCost() {
        cost = 0;
    }
    
    public int getCost() {
        return cost;
    }
    
    public int getTotal() {
        return total;
    }

    public void union(int p, int q) {
        // 将p和q归并到相同分量中
        int pID = find(p);
        int qID = find(q);

        // 如果p和q已经在同一个分量中则不需要采取任何行动
        if (pID == qID) {
            return;
        }

        // 将p的分量重命名为q的名称
        for (int i = 0; i < id.length; i++) {
            if (id[i] == pID) {
                cost++;
                id[i] = qID;
            }
        }
        cost += id.length;
        total += cost;
        count--;
    }

    // 画图的方法
    public static void draw(int cost, int total, int num) {
        
        StdDraw.setXscale(0, 1000);
        StdDraw.setYscale(0, 1500);
        StdDraw.setPenRadius(0.005);
        StdDraw.setPenColor(StdDraw.BLACK);
        StdDraw.point(num, cost);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.point(num, total / num);

    }

    public static void main(String[] args) {
        int N = StdIn.readInt(); // 读取触点数量
        QuickFindEx1516 uf = new QuickFindEx1516(N); // 创建对象，初始化分量

        int num = 0; // 记录读取的连接对的数量

        StdDraw.setCanvasSize(500, 500);
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            draw(uf.getCost(), uf.getTotal(), ++num);
            uf.resetCost();
        }
        StdOut.println(uf.count + " components");
    }
}
```

结果：

横坐标为处理的连接对数，纵坐标为访问数组的总次数；黑线为本次操作访问数组的次数，红线为均摊成本。

![ex1516-quickFind](images\chapter01\ex1516-quickFind.png)

QuickUnion：

```java
public class QuickUnionEx1516 {

    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    private int total; // 记录访问数组的总次数
    private int cost; // 本次操作访问数组次数

    public QuickUnionEx1516(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        // 找出分量的名称
        while (p != id[p]) {
            cost += 2;
            p = id[p];
        }
        cost++;
        total += cost;
        return p;
    }

    public void union(int p, int q) {
        // 将p和q的根节点统一
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) {
            return;
        }

        id[pRoot] = qRoot;
        cost++;
        total += cost;
        count--;

    }

    // 重置本次操作的数组访问次数
    public void resetCost() {
        cost = 0;
    }

    public int getCost() {
        return cost;
    }

    public int getTotal() {
        return total;
    }

    // 画图的方法
    public static void draw(int cost, int total, int num) {

        StdDraw.setXscale(0, 1000);
        StdDraw.setYscale(0, 250);
        StdDraw.setPenRadius(0.005);
        StdDraw.setPenColor(StdDraw.BLACK);
        StdDraw.point(num, cost);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.point(num, total / num);

    }

    public static void main(String[] args) {
        int N = StdIn.readInt(); // 读取触点数量
        QuickUnionEx1516 uf = new QuickUnionEx1516(N); // 创建对象，初始化分量

        int num = 0; // 记录读取的连接对的数量

        StdDraw.setCanvasSize(500, 500);
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt(); // 读取整数对
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            draw(uf.getCost(), uf.getTotal(), ++num);
            uf.resetCost();
        }
        StdOut.println(uf.count + " components");
    }
}
```

结果：

横坐标为处理的连接对数，纵坐标为访问数组的总次数；黑线为本次操作访问数组的次数，红线为均摊成本。

![ex1516-quickUnion](images\chapter01\ex1516-quickUnion.png)

***
**1.5.17 随机连接。设计UF的一个用例ErdosRenyi，从命令行接受一个整数 N，在 0 到 N-1 之间产生随机整数对，调用connected()判断它们是否相连，如果不是则调用union()方法（和我们的开发用例一样）。不断循环直到所有触点均相互连通并打印出生成的连接总数。将你的程序打包成一个接受参数N并返回连接总数的静态方法count()，添加一个main()方法从命令行接受N，调用count()并打印它的返回值。**

*Answer：*

```java
public class ErdosRenyi {
    
    public static int count(int N) {
        UF uf = new UF(N);
        int linkCount = 0;
        
        while (uf.count() != 1) {
            int p = StdRandom.uniform(0, N);
            int q = StdRandom.uniform(0, N);
            if (!uf.connected(p, q)) {
                uf.union(p, q);
                linkCount++;
                System.out.println(p + " link " + q);
            }
        }
        return linkCount;
    }
    
    public static void main(String[] args) {
        int N = StdIn.readInt();
        System.out.println(count(N));
    }
}
```

***
**1.5.18 随机网格生成器。编写一个程序RandomGrid，从命令行接受一个int值N，生成一个N×N的网格中的所有连接。它们的排列随机且方向随机（即(pq)和(qp)出现的可能性是相等的），将这个结果打印到标准输出中。可以使用RandomBag将所有连接随机排列（请见练习1.3.34），并使用如右下所示的Connection嵌套类来将p和q封装到一个对象中。将程序打包成两个静态方法：generate()，接受参数N并返回一个连接的数组；main()，从命令行接受参数N，调用generate()，便利返回的数组并打印出所有连接。**

```java
private class Connection
{
    int p;
    int q;

    public Connection(int p, int q)
    {
        this.p = p;
        this.q = q;
    }
}
```

*Answer：*

没有使用RandomBag。

```java
class Connection {
    
    private int p;
    private int q;

    public Connection(int p, int q) {
        this.p = p;
        this.q = q;
    }

    @Override
    public String toString() {
        return "Connection: (" + p + ", " + q + ")";
    }

    public int getP() {
        return p;
    }

    public int getQ() {
        return q;
    }
}

public class RandomGrid {

    public static Connection[] generate(int N) {

        List<Connection> list = new ArrayList<>();

        List<Integer> listp = new ArrayList<>();
        List<Integer> listq = new ArrayList<>();

        for (int i = 0; i < N; i++) {
            listp.add(i);
            listq.add(i);
        }

        Collections.shuffle(listp);
        Collections.shuffle(listq);

        for (int i = 0; i < N; i++) {

            int p = listp.get(i);
            int q = listq.get(i);

            // 随机连接p、q或q、p
            if (StdRandom.bernoulli()) {
                list.add(new Connection(p, q));
            } else {
                list.add(new Connection(q, p));
            }
        }
        return list.toArray(new Connection[0]);
    }

    public static void main(String[] args) {

        int N = StdIn.readInt();
        Connection[] connections = generate(N);

        for (int i = 0; i < connections.length; i++) {
            System.out.println(connections[i]);
        }
    }
}
```

返回结果：

    20
    Connection: (6, 10)
    Connection: (1, 8)
    Connection: (19, 10)
    Connection: (11, 15)
    Connection: (13, 3)
    Connection: (7, 11)
    Connection: (12, 14)
    Connection: (16, 5)
    Connection: (6, 18)
    Connection: (17, 0)
    Connection: (2, 14)
    Connection: (4, 9)
    Connection: (5, 2)
    Connection: (9, 19)
    Connection: (1, 16)
    Connection: (0, 3)
    Connection: (15, 13)
    Connection: (4, 18)
    Connection: (8, 12)
    Connection: (17, 7)

***
**1.5.19 动画。编写一个RandomGrid（请见练习1.5.18）的用例，和我们开发用例一样使用UnionFind来检查触点的连通性并在处理时用StdDraw将它们绘出。**

*Answer：*

```java
public class RandomGridTest {
    
    public static void main(String[] args) {
        
        int N = StdIn.readInt();
        
        Connection[] connections = RandomGrid.generate(N);
        
        UF uf = new UF(N);
        
        for (int i = 0; i < connections.length; i++) {
            int p = connections[i].getP();
            int q = connections[i].getQ();
            
            if (!uf.connected(p, q)) {
                uf.union(p, q);
                System.out.println(connections[i]);
            }
        }
        System.out.println(uf.count() + " components");
    } 
}
```

结果：

    20
    Connection: (12, 2)
    Connection: (10, 9)
    Connection: (5, 19)
    Connection: (18, 14)
    Connection: (17, 7)
    Connection: (13, 4)
    Connection: (1, 8)
    Connection: (8, 11)
    Connection: (19, 7)
    Connection: (16, 6)
    Connection: (5, 0)
    Connection: (11, 0)
    Connection: (15, 9)
    Connection: (18, 15)
    Connection: (16, 12)
    Connection: (14, 13)
    4 components

绘图略。

***
**1.5.20 动态生长。使用链表或大小可变的数组实现加权quick-union算法，去掉需要预先知道对象数量的限制。为API添加一个新方法newSite()，它应该返回一个类型为int的标识符。**

*Answer：*

使用可变长数组：

（没有实现count以及newSite()方法，待改进）

```java
public class ResizingArrayWeightedUnionFind {
    
    private int[] id;
    private int[] sz;
    
    public ResizingArrayWeightedUnionFind() {
        
        // 初始化时设定数组长度为20
        int N = 20;
        id = new int[N];
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
            sz[i] = 1;
        }
    }
    
    private void resize(int times) {
        
        int size = id.length * times;
        
        int[] tmpId = new int[size];
        int[] tmpSz = new int[size];
        
        // 将原有值赋值给临时数组
        for (int i = 0; i < id.length; i++) {
            tmpId[i] = id[i];
            tmpSz[i] = sz[i];
        }
        
        // 新增的位置与初始化时相同，id数组初始为与角标相同，sz数组初始为1
        for (int i = id.length; i < size; i++) {
            tmpId[i] = i;
            tmpSz[i] = 1;
        }
        
        id = tmpId;
        sz = tmpSz;
    }
    
    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        // 如果p超出数组
        if (p > id.length - 1) {
            // 按倍数调整数组大小
            resize(1 + p / (id.length - 1));
        }
        while (p != id[p]) {
            p = id[p];
        }
        return p;
    }

    public void union(int p, int q) {
        
        int i = find(p);
        int j = find(q);
        if (i == j) {
            return;
        }

        if (sz[i] < sz[j]) {
            id[i] = j;
            sz[j] += sz[i];
        } else {
            id[j] = i;
            sz[i] += sz[j];
        }
    }
    
    public static void main(String[] args) {
        
        ResizingArrayWeightedUnionFind rawuf = new ResizingArrayWeightedUnionFind();
        
        while (!StdIn.isEmpty()) {
            int p = StdIn.readInt();
            int q = StdIn.readInt();
            if (!rawuf.connected(p, q)) {
                rawuf.union(p, q);
                System.out.println(p + " link " + q);
            } else {
                System.out.println(p + " already link " + q);
            }
        }
    }
}
```

***

#### 实验题

***
**1.5.21Erdös-Renyi模型。使用练习1.5.17的用例验证这个猜想：得到单个连通分量所需生成的整数对数量为~$1/2NlnN$。**

*Answer：*

修改ErdosRenyi的count()方法，使其可以打印实际生成整数对数量和理论所需数量。

```java
public static int count(int N) {
    
    UF uf = new UF(N);
    int linkCount = 0;
    int pairCount = 0; // 1.5.21 修改
    
    while (uf.count() != 1) {
        int p = StdRandom.uniform(0, N);
        int q = StdRandom.uniform(0, N);
        pairCount++; // 1.5.21 修改
        if (!uf.connected(p, q)) {
            uf.union(p, q);
            linkCount++;
            // System.out.println(p + " link " + q); // 1.5.21 修改
        }
    }
    System.out.println("实际生成整数对数量：" + pairCount + " ，理论所需数量：" + (N * Math.log(N) / 2)); // 1.5.21 修改
    return linkCount;
}

public class ErdosRenyiModel {
    public static void main(String[] args) {
        for (int i = 100; i < 20000; i *= 2) {
            ErdosRenyi.count(i);
        }
    }
}
```

输出结果：

    实际生成整数对数量：244 ，理论所需数量：230.25850929940458
    实际生成整数对数量：840 ，理论所需数量：529.8317366548036
    实际生成整数对数量：1458 ，理论所需数量：1198.2929094215963
    实际生成整数对数量：3684 ，理论所需数量：2673.844691067171
    实际生成整数对数量：7508 ，理论所需数量：5902.207126582298
    实际生成整数对数量：13759 ，理论所需数量：12913.44974206051
    实际生成整数对数量：28084 ，理论所需数量：28044.97046191284
    实际生成整数对数量：59306 ，理论所需数量：60526.08287940933

**1.5.22 Erdös-Renyi的倍率实验。开发一个性能测试用例，从命令行接受一个int值T并进行T次以下实验：使用练习1.5.17的用例生成随机连接，和我们的开发用例一样使用UnionFind来检查触点的连通性，不断循环直到所有触点都相互连通。对于每个N，打印出N值和平均所需的连接数以及前后两次运行时间的比值。使用你的程序验证正文中的猜想：quick-find算法和quick-union算法的运行时间是平方级别的，加权quick-union算法则接近线性级别。**

*Answer：*

略。

***
**1.5.23 在Erdös-Renyi模型下比较quick-find算法和quick-union算法。开发一个性能测试用例，从命令行接受一个int值T并进行T次以下实验：使用练习1.5.17的用例生成随机连接。保存这些连接并和我们的开发用例一样分别用quick-find和quick-union算法检查触点的连通性，不断循环直到所有触点均相互连通。对于每个N，打印出N值和两种算法的运行时间比值。**

*Answer：*

略。

***
**1.5.24 适用于Erdös-Renyi模型的快速算法。在练习1.5.23的测试中增加加权quick-union算法和使用路径压缩的加权quick-union算法。你能分辨出这两种算法的区别吗？**

*Answer：*

路径压缩更快。
代码略。

**1.5.25 随机网格的倍率测试。开发一个性能测试用例，从命令行接受一个int值T并进行T次以下实验：使用练习1.5.18的用例生成一个N×N的随机网格，所有连接的方向随机且排列随机。和我们的开发用例一样使用UnionFind来检查触点的连通性，不断循环直到所有触点均相互连通。对于每个N，打印出N值和平均所需的连接数以及前后两次运行时间的比值。使用你的程序验证正文中的猜想：quick-find和quick-union算法的运行时间是平方级别的，加权quick-union算法则接近线性级别。注意：随着N值加倍，网格中触点的数量会乘以4，因此平方级别的算法运行时间会变为原来的16倍，线性级别的算法的运行时间则变为原来的4倍。**

*Answer：*

略。

***
**1.5.26 Erdös-Renyi模型的均摊成本图像。开发一个用例，从命令行接受一个int值N，在0到N-1之间产生随机整数对，调用connected()判断它们是否相连，如果不是则用union()方法（和我们的开发用例一样）。不断循环直到所有触点互通。按照正文的样式将所有操作的均摊成本绘制成图像。**

*Answer：*

QuickFind：

```java
public class ErdosRenyiQFEx1526 {
    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    private int total; // 记录访问数组的总次数
    private int cost; // 本次操作访问数组次数

    public ErdosRenyiQFEx1526(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    /*
     * quick-find算法 该算法的运行时间对于最终只能得到少数连通分量的一般应用是平方级别的
     */
    private int find(int p) {
        cost++;
        total += cost;
        return id[p];
    }

    // 重置本次操作的数组访问次数
    public void resetCost() {
        cost = 0;
    }

    public int getCost() {
        return cost;
    }

    public int getTotal() {
        return total;
    }

    public void union(int p, int q) {
        // 将p和q归并到相同分量中
        int pID = find(p);
        int qID = find(q);

        // 如果p和q已经在同一个分量中则不需要采取任何行动
        if (pID == qID) {
            return;
        }

        // 将p的分量重命名为q的名称
        for (int i = 0; i < id.length; i++) {
            if (id[i] == pID) {
                cost++;
                id[i] = qID;
            }
        }
        cost += id.length;
        total += cost;
        count--;
    }

    // 画图的方法
    public static void draw(int cost, int total, int num) {

        StdDraw.setXscale(0, 2000);
        StdDraw.setYscale(0, 4500);
        StdDraw.setPenRadius(0.005);
        StdDraw.setPenColor(StdDraw.BLACK);
        StdDraw.point(num, cost);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.point(num, total / num);

    }

    public static void main(String[] args) {

        int N = StdIn.readInt(); 

        ErdosRenyiQFEx1526 uf = new ErdosRenyiQFEx1526(N); // 创建对象，初始化分量

        int num = 0; // 记录读取的连接对的数量

        StdDraw.setCanvasSize(500, 500);
        
        while (uf.count() != 1) {
            int p = StdRandom.uniform(0, N);
            int q = StdRandom.uniform(0, N);
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            draw(uf.getCost(), uf.getTotal(), ++num);
            uf.resetCost();
        }
    }
}
```

结果：

![ex1526-quickFind](images\chapter01\ex1526-quickFind.png)

QuickUnion：

```java
public class ErdosRenyiQUEx1526 {
    private int[] id; // 分量id，以触点为索引
    private int count; // 分量数量

    private int total; // 记录访问数组的总次数
    private int cost; // 本次操作访问数组次数

    public ErdosRenyiQUEx1526(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        // 找出分量的名称
        while (p != id[p]) {
            cost += 2;
            p = id[p];
        }
        cost++;
        total += cost;
        return p;
    }

    public void union(int p, int q) {
        // 将p和q的根节点统一
        int pRoot = find(p);
        int qRoot = find(q);

        if (pRoot == qRoot) {
            return;
        }

        id[pRoot] = qRoot;
        cost++;
        total += cost;
        count--;

    }

    // 重置本次操作的数组访问次数
    public void resetCost() {
        cost = 0;
    }

    public int getCost() {
        return cost;
    }

    public int getTotal() {
        return total;
    }
    
    // 画图的方法
    public static void draw(int cost, int total, int num) {

        StdDraw.setXscale(0, 2000);
        StdDraw.setYscale(0, 1000);
        StdDraw.setPenRadius(0.005);
        StdDraw.setPenColor(StdDraw.BLACK);
        StdDraw.point(num, cost);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.point(num, total / num);

    }

    public static void main(String[] args) {

        int N = StdIn.readInt(); 

        ErdosRenyiQUEx1526 uf = new ErdosRenyiQUEx1526(N); // 创建对象，初始化分量

        int num = 0; // 记录读取的连接对的数量

        StdDraw.setCanvasSize(500, 500);
        
        while (uf.count() != 1) {
            int p = StdRandom.uniform(0, N);
            int q = StdRandom.uniform(0, N);
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            draw(uf.getCost(), uf.getTotal(), ++num);
            uf.resetCost();
        }
    }
}

```
结果：

![ex1526-quickUnion](images\chapter01\ex1526-quickUnion.png)

WeightQuickUnion：

```java
public class ErdosRenyiWQUEx1526 {

    private int[] id; // 父链接数组（由触点索引）
    private int[] sz; // （由触点索引的）各个根节点所对应的分量的大小
    private int count; // 连通分量的数量
    private int total; // 记录访问数组的总次数
    private int cost; // 本次操作访问数组次数

    public ErdosRenyiWQUEx1526(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            sz[i] = 1;
        }
    }

    public int count() {
        return count;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int p) {
        while (p != id[p]) {
            cost += 2;
            p = id[p];
        }
        cost++;
        total += cost;
        return p;
    }

    public void union(int p, int q) {
        int i = find(p);
        int j = find(q);
        if (i == j) {
            return;
        }

        if (sz[i] < sz[j]) {
            id[i] = j;
            sz[j] += sz[i];
        } else {
            id[j] = i;
            sz[i] += sz[j];
        }
        cost++;
        total += cost;
        count--;
    }

    // 重置本次操作的数组访问次数
    public void resetCost() {
        cost = 0;
    }

    public int getCost() {
        return cost;
    }

    public int getTotal() {
        return total;
    }

    // 画图的方法
    public static void draw(int cost, int total, int num) {

        StdDraw.setXscale(0, 2000);
        StdDraw.setYscale(0, 50);
        StdDraw.setPenRadius(0.005);
        StdDraw.setPenColor(StdDraw.BLACK);
        StdDraw.point(num, cost);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.point(num, total / num);

    }

    public static void main(String[] args) {

        int N = StdIn.readInt();

        ErdosRenyiWQUEx1526 uf = new ErdosRenyiWQUEx1526(N); // 创建对象，初始化分量

        int num = 0; // 记录读取的连接对的数量

        StdDraw.setCanvasSize(500, 500);

        while (uf.count() != 1) {
            int p = StdRandom.uniform(0, N);
            int q = StdRandom.uniform(0, N);
            if (!uf.connected(p, q)) { // 如果未连通
                uf.union(p, q); // 归并分量
            }
            draw(uf.getCost(), uf.getTotal(), ++num);
            uf.resetCost();
        }
    }
}
```

结果：

![ex1526-weightedQuickUnion](images\chapter01\ex1526-weightedQuickUnion.png)

## 第二章 排序

### 2.1 初级排序算法

***

#### 练习题

***
**2.1.1 按照算法2.1所示轨迹的格式给出选择排序是如何将数组 E A S Y Q U E S T I O N 排序的。**

*Answer：*

```java
public class Ex211 {
    // 选择排序
    public static <T> void sort(Comparable<T>[] a) {
        
        int N = a.length; // 数组长度
        for (int i = 0; i < N; i++) {
            int min = i; // 最小元素索引
            for (int j = i + 1; j < N; j++) {
                // 在a[i + 1]...a[N - 1]之间查找比a[min]小的元素
                if (less(a[j], a[min])) {
                    // 如果找到，则重新记录最小元素索引
                    min = j;
                }
            }
            // 内循环结束，交换i与min的值
            exch(a, i, min);
            // 打印i和min的值
            System.out.print(i + "\t" + min + "\t");
            // 打印数组
            show(a);
        }
    }

    @SuppressWarnings("unchecked")
    private static <T> boolean less(Comparable<T> v, Comparable<T> w) {
        return v.compareTo((T) w) < 0;
    }

    private static <T> void exch(Comparable<T>[] a, int i, int j) {
        Comparable<T> t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(Comparable<T>[] a) {
        // 在单行中打印数组
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }

    public static <T> boolean isSorted(Comparable<T>[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }
    
    public static void main(String[] args) {
        
        String strings = "EASYQUESTION";
        char[] chars = strings.toCharArray();
        Character[] characters = new Character[chars.length];
        
        System.out.print("i\tmin\t");
        for (int i = 0; i < characters.length; i++) {
            characters[i] = chars[i];
            System.out.print(i + " ");
        }
        System.out.println();
        
        System.out.print("\t\t");
        for (int i = 0; i < characters.length; i++) {
            System.out.print(characters[i] + " ");
        }
        System.out.println();
        
        sort(characters);
    }
}
```

结果：

    i   min 0 1 2 3 4 5 6 7 8 9 10 11 
            E A S Y Q U E S T I O N 
    0   1   A E S Y Q U E S T I O N 
    1   1   A E S Y Q U E S T I O N 
    2   6   A E E Y Q U S S T I O N 
    3   9   A E E I Q U S S T Y O N 
    4   11  A E E I N U S S T Y O Q 
    5   10  A E E I N O S S T Y U Q 
    6   11  A E E I N O Q S T Y U S 
    7   7   A E E I N O Q S T Y U S 
    8   11  A E E I N O Q S S Y U T 
    9   11  A E E I N O Q S S T U Y 
    10  10  A E E I N O Q S S T U Y 
    11  11  A E E I N O Q S S T U Y 

***
**2.1.2 在选择排序中，一个元素最多可能会被交换多少次？平均可能会被交换多少次？**

*Answer：*

（1）将一个有序数列循环右移一位，使最大的元素位于0号索引上，此时该最大元素会被交换N次。
（2）在上述情况下，平均每个元素的交换次数为$N/N=1$。

***
**2.1.3 构造一个含有N个元素的数组，使选择排序（算法 2.1）运行过程中a[j] < a[min]（由此 min 会不断更新）成功的次数最大。**

*Answer：*

构建一个降序的数组：

    9 8 7 6 5 4 3 2 1

i=0，条件满足8次，1和9交换：

    1 8 7 6 5 4 3 2 9。

i=1，条件满足6次，2和8交换：

    1 2 7 6 5 4 3 8 9。

i=2，条件满足4次，3和7交换：

    1 2 3 6 5 4 7 8 9。

i=3，条件满足2次，4和6交换：

    1 2 3 4 5 6 7 8 9。

***
**2.1.4 按照算法2.2所示轨迹的格式给出插入排序是如何将数组 E A S Y Q U E S T I O N排序的。**

*Answer：*

```java
public class Ex214 {
    public static <T> void sort(Comparable<T>[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
            int j = 0; // 声明j，用于记录离开for循环时的j值
            for (j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
            }
            // 打印i和j的值
            System.out.print(i + "\t" + j + "\t");
            // 打印数组
            show(a);
        }
    }

    @SuppressWarnings("unchecked")
    private static <T> boolean less(Comparable<T> v, Comparable<T> w) {
        return v.compareTo((T) w) < 0;
    }

    private static <T> void exch(Comparable<T>[] a, int i, int j) {
        Comparable<T> t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(Comparable<T>[] a) {
        // 在单行中打印数组
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }

    public static <T> boolean isSorted(Comparable<T>[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        String strings = "EASYQUESTION";
        char[] chars = strings.toCharArray();
        Character[] characters = new Character[chars.length];

        System.out.print("i\tj\t");
        for (int i = 0; i < characters.length; i++) {
            characters[i] = chars[i];
            System.out.print(i + " ");
        }
        System.out.println();
        System.out.print("\t\t");
        for (int i = 0; i < characters.length; i++) {
            System.out.print(characters[i] + " ");
        }
        System.out.println();
        sort(characters);
    }
}
```

结果：

    i   j   0 1 2 3 4 5 6 7 8 9 10 11 
            E A S Y Q U E S T I O N 
    1   0   A E S Y Q U E S T I O N 
    2   2   A E S Y Q U E S T I O N 
    3   3   A E S Y Q U E S T I O N 
    4   2   A E Q S Y U E S T I O N 
    5   4   A E Q S U Y E S T I O N 
    6   2   A E E Q S U Y S T I O N 
    7   5   A E E Q S S U Y T I O N 
    8   6   A E E Q S S T U Y I O N 
    9   3   A E E I Q S S T U Y O N 
    10  4   A E E I O Q S S T U Y N 
    11  4   A E E I N O Q S S T U Y 

***
**2.1.5 构造一个含有N个元素的数组，使插入排序（算法2.2）运行过程中内循环（for）的两个判断结果总是假。**

*Answer：*

插入排序代码：

```java
public static <T> void sort(Comparable<T>[] a) {
    int N = a.length;
    for (int i = 1; i < N; i++) {
        // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
        for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
            exch(a, j, j - 1);
        }
    }
}
```

内循环的判断条件为`j > 0 && less(a[j], a[j - 1])`，第一个条件属于循环计数条件，所以循环判断条件主要由第二个条件决定。

要使该条件为false，只需a[j]>a[j - 1]即可，即输入数组已经为升序的。

***
**2.1.6 在所有主键都相同时，选择排序和插入排序谁更快？**

*Answer：*

所有主键都相同，不涉及交换，只涉及比较。

- 选择排序：需要$N(N-1)/2$次比较。
- 插入排序：需要$N-1$次比较。

插入排序更快。

***
**2.1.7 对于逆序数组，选择排序和插入排序谁更快？**

*Answer：*

最坏情况下，选择排序和插入排序的开销如下：

- | 比较次数 | 交换次数
-----|-----|-----
选择排序 | ~$N^2/2$ | $N$
插入排序 | ~$N^2/2$ | $N^2/2$

假设比较开销小于交换开销，则选择排序更快；反之二者速度大致相等。

***
**2.1.8 假设元素只可能有三种值，使用插入排序处理这样一个随机数组的运行时间是线性的还是平方级别的？或是介于二者之间？**

*Answer：*

假设数组由元素1、2、3组成，在插入排序过程中的某一时刻数组的状态如下：

$11...1\,\,\,22...2\,\,\,33...3\,\,\,123123123...$

其中已排序部分包括$a$个1、$b$个2和$c$个3。

假设当前为第i次操作，如果插入的数字是1，则需要交换$b+c$次；如果插入的是2则需要要交换$c$次；如果插入的数字是3则不需要交换。

由于是随机数组，所以未排序部分三个数字的排列是随机的，即三种值出现的几率相等。则第i次插入所需要的平均交换次数为：

$$
\frac{b+c+c}{3}=\frac{b+2c}{3}
$$

第 i 次插入后，$b+2c$视插入的元素不同会出现不同的变化：

- 如果插入的是1，那么$b+2c$的值不会变化。
- 如果插入的是2，那么$b+2c$的值增加1。
- 如果插入的是3，那么$b+2c$的值增加2。

同样由于三种取值的概率相等，我们得出第$i+1$次插入平均需要交换的次数为：

$$
\frac{b+2c+\frac{0+1+2}{3}}{3}=\frac{b+2c+1}{3}
$$

即平均每次插入都会使下一次插入的交换次数增加$1/3$：

- i = 0 时，平均交换次数为0
- i = 1 时，平均交换次数为1/3
- i = 2 时，平均交换次数为2/3
- ...

所以总交换次数：

$$
\frac{1+2+3+\cdots+N-1}{3}=\frac{N(N-1)}{6}
$$

比较次数在一般情况下与交换次数近似，所以在题设情况下运行时间是平方级别的。

***
**2.1.9 按照算法 2.3 所示轨迹的格式给出希尔排序是如何将数组 E A S Y S H E L L S O R T Q U E S T I O N 排序的。**

*Answer：*

```java
public class Ex219 {
    public static <T> void sort(Comparable<T>[] a) {
        int N = a.length;
        int h = 1;
        while (h < N / 3) {
            h = 3 * h + 1;
        }
        while (h >= 1) {
            int i = 0;
            for (i = h; i < N; i++) {
                int j = 0; // 声明j，用于记录离开for循环时的j值
                for (j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                    exch(a, j, j - h);
                }
                System.out.print(h + "\t" + i + "\t" + j + "\t");
                show(a);
            }
            h = h / 3;
        }
    }

    @SuppressWarnings("unchecked")
    private static <T> boolean less(Comparable<T> v, Comparable<T> w) {
        return v.compareTo((T) w) < 0;
    }

    private static <T> void exch(Comparable<T>[] a, int i, int j) {
        Comparable<T> t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(Comparable<T>[] a) {
        // 在单行中打印数组
        for (int i = 0; i < a.length; i++) {
            if (i < 10) {
                System.out.print(a[i] + " ");
            } else {
                System.out.print(" " + a[i] + " ");
            }
        }
        System.out.println();
    }

    public static <T> boolean isSorted(Comparable<T>[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        String strings = "EASYSHELLSORTQUESTION";
        char[] chars = strings.toCharArray();
        Character[] characters = new Character[chars.length];

        System.out.print("h\ti\tj\t");
        for (int i = 0; i < characters.length; i++) {
            characters[i] = chars[i];
            System.out.print(i + " ");
        }
        System.out.println();
        System.out.print("\t\t\t");
        for (int i = 0; i < characters.length; i++) {
            if (i < 10) {
                System.out.print(characters[i] + " ");
            } else {
                System.out.print(" " + characters[i] + " ");
            }
        }
        System.out.println();
        sort(characters);
    }
}
```

结果：

    h   i   j   0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 
                E A S Y S H E L L S  O  R  T  Q  U  E  S  T  I  O  N 
    13  13  13  E A S Y S H E L L S  O  R  T  Q  U  E  S  T  I  O  N 
    13  14  14  E A S Y S H E L L S  O  R  T  Q  U  E  S  T  I  O  N 
    13  15  2   E A E Y S H E L L S  O  R  T  Q  U  S  S  T  I  O  N 
    13  16  3   E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    13  17  17  E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    13  18  18  E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    13  19  19  E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    13  20  20  E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   4   4   E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   5   5   E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   6   6   E A E S S H E L L S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   7   3   E A E L S H E S L S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   8   4   E A E L L H E S S S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   9   9   E A E L L H E S S S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   10  10  E A E L L H E S S S  O  R  T  Q  U  S  Y  T  I  O  N 
    4   11  7   E A E L L H E R S S  O  S  T  Q  U  S  Y  T  I  O  N 
    4   12  12  E A E L L H E R S S  O  S  T  Q  U  S  Y  T  I  O  N 
    4   13  9   E A E L L H E R S Q  O  S  T  S  U  S  Y  T  I  O  N 
    4   14  14  E A E L L H E R S Q  O  S  T  S  U  S  Y  T  I  O  N 
    4   15  15  E A E L L H E R S Q  O  S  T  S  U  S  Y  T  I  O  N 
    4   16  16  E A E L L H E R S Q  O  S  T  S  U  S  Y  T  I  O  N 
    4   17  17  E A E L L H E R S Q  O  S  T  S  U  S  Y  T  I  O  N 
    4   18  10  E A E L L H E R S Q  I  S  T  S  O  S  Y  T  U  O  N 
    4   19  7   E A E L L H E O S Q  I  R  T  S  O  S  Y  T  U  S  N 
    4   20  8   E A E L L H E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   1   0   A E E L L H E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   2   2   A E E L L H E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   3   3   A E E L L H E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   4   4   A E E L L H E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   5   3   A E E H L L E O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   6   3   A E E E H L L O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   7   7   A E E E H L L O N Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   8   7   A E E E H L L N O Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   9   9   A E E E H L L N O Q  I  R  S  S  O  S  T  T  U  S  Y 
    1   10  5   A E E E H I L L N O  Q  R  S  S  O  S  T  T  U  S  Y 
    1   11  11  A E E E H I L L N O  Q  R  S  S  O  S  T  T  U  S  Y 
    1   12  12  A E E E H I L L N O  Q  R  S  S  O  S  T  T  U  S  Y 
    1   13  13  A E E E H I L L N O  Q  R  S  S  O  S  T  T  U  S  Y 
    1   14  10  A E E E H I L L N O  O  Q  R  S  S  S  T  T  U  S  Y 
    1   15  15  A E E E H I L L N O  O  Q  R  S  S  S  T  T  U  S  Y 
    1   16  16  A E E E H I L L N O  O  Q  R  S  S  S  T  T  U  S  Y 
    1   17  17  A E E E H I L L N O  O  Q  R  S  S  S  T  T  U  S  Y 
    1   18  18  A E E E H I L L N O  O  Q  R  S  S  S  T  T  U  S  Y 
    1   19  16  A E E E H I L L N O  O  Q  R  S  S  S  S  T  T  U  Y 
    1   20  20  A E E E H I L L N O  O  Q  R  S  S  S  S  T  T  U  Y 

***
**2.1.10 在希尔排序中为什么在实现h有序时不使用选择排序？**

*Answer：*

对于部分有序数组，插入排序比选择排序快。

***
**2.1.11 将希尔排序中实时计算递增序列改为预先计算并存储在一个数组中。**

*Answer：*

```java
// 构建希尔数组
private static Integer[] createShells(int N) {
    List<Integer> shells = new ArrayList<>();
    int h = 1;
    shells.add(0); // 将0添加到数组的第一项，作为结束标志
    while (h < N / 3) {
        shells.add(h);
        h = 3 * h + 1;
    }
    shells.add(h);
    return shells.toArray(new Integer[0]);
}

public static <T> void sort(Comparable<T>[] a) {
    int N = a.length;

    Integer[] shells = createShells(N);

    // while循环改为for循环遍历shells数组，数组的第一项为0
    for (int k = shells.length - 1; k > 0; k--) {
        int h = shells[k];
        // 将数组变为h有序
        for (int i = h; i < N; i++) {
            // 将a[i]插入到a[i - h]、a[i - 2 * h]、a[i - 3 * h]...之中
            for (int j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                exch(a, j, j - h);
            }
        }
    }
}
```

***
**2.1.12 令希尔排序打印出递增序列的每个元素所带来的比较次数和数组大小的比值。编写一个测试用例对随机Double数组进行希尔排序，验证该值是一个小常数，数组大小按照10的幂次递增，不小于100。**

*Answer：*

**题干没看懂...（递增序列？每个元素所带来的比较次数？）**

以下统计的为总比较次数和数组大小的比值：

```java
public class ShellEx2112 {

    private static int cnt;

    public static <T> void sort(Comparable<T>[] a) {
        int N = a.length;
        int h = 1;
        while (h < N / 3) {
            h = 3 * h + 1;
        }
        while (h >= 1) {
            // 将数组变为h有序
            for (int i = h; i < N; i++) {
                // 将a[i]插入到a[i - h]、a[i - 2 * h]、a[i - 3 * h]...之中
                for (int j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                    exch(a, j, j - h);
                }
            }
            h = h / 3;
        }
    }

    @SuppressWarnings("unchecked")
    private static <T> boolean less(Comparable<T> v, Comparable<T> w) {
        cnt++;
        return v.compareTo((T) w) < 0;
    }

    private static <T> void exch(Comparable<T>[] a, int i, int j) {
        Comparable<T> t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    public static int getCnt() {
        return cnt;
    }

    public static void resetCnt() {
        cnt = 0;
    }

    public static void main(String[] args) {
        int size = 100;
        for (int i = 0; i < 5; i++) {
            System.out.print("实验 " + i);
            Double[] a = new Double[size];
            for (int j = 0; j < size; j++) {
                a[j] = StdRandom.uniform() * 100;
            }
            System.out.println(", ArraySize: " + size);
            sort(a);
            System.out.printf("%s %5.3f\n", "cnt/size = ", (double) getCnt() / size);
            resetCnt();
            size *= 10;
        }
    }
}
```

结果：

    实验 0, ArraySize: 100
    cnt/size =  7.430
    实验 1, ArraySize: 1000
    cnt/size =  14.210
    实验 2, ArraySize: 10000
    cnt/size =  24.468
    实验 3, ArraySize: 100000
    cnt/size =  40.454
    实验 4, ArraySize: 1000000
    cnt/size =  65.523

***

#### 提高题

***
**2.1.13 纸牌排序。说说你会如何将一副扑克牌按花色排序（花色排序是黑桃、红桃、梅花和方片），限制条件是所有牌都是背面朝上排成一列，而你一次只能翻看两张牌或者交换两张牌（保持背面朝上）。**

*Answer：*

可以采用冒泡排序的思路：

1. 翻一二两张，是逆序对就交换，否则什么也不做
2. 翻二三两张，是逆序对就交换，否则什么也不做
3. 一直到最后，可以保证最右侧的是最大花色的牌
4. 然后不断重复上述过程，就可以完成排序

***
**2.1.14 出列顺序。说说你会如何将一副扑克牌排序，限制条件是只能查看最上面的两张牌，交换最上面的两张牌，或是将最上面的一张牌放到这摞牌的最下面。**

*Answer：*

设牌总数为N，进行以下操作：

1. 第一步，先执行N-1次以下操作：
    - 如果顶端两张牌逆序，那么交换它们。
    - 将第一张牌放到牌堆底部。
    - 如果N-1次操作都没有发生两张牌逆序交换，排序完成。
2. 第二步，将第一张牌放入牌堆底部。
3. 第三步，返回第一步。

***
**2.1.15 昂贵的交换。一家货运公司的一位职工得到了一项任务，需要将若干大货箱按照发货时间摆放。比较发货时间很容易（对照标签即可），但将两个货箱交换位置则很困难（移动麻烦）。仓库已经快满了，只有一个空闲的仓位。这位职员应该使用哪种排序算法呢？**

*Answer：*

由题意，此时比较操作是廉价操作，交换操作是昂贵操作，同时存在临时空间。所以应该选择选择排序。

***
**2.1.16 验证。编写一个check()方法，调用sort()对任意数组排序。如果排序成功而且数组中的所有对象均没有被修改则返回true，否则返回false。不要假设sort()只能通过exch()来移动数据，可以信任并使用Array.sort()。**

*Answer：*

```java
public class Ex2116 {

    // 检查Arrays.sort()方法
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> boolean checkArraySort(T[] a) {

        // 创建一个与a等长的Object数组
        Object[] backup = new Object[a.length];

        // 将数组的每一个元素转为T
        for (int i = 0; i < backup.length; i++) {
            backup[i] = (T) backup[i];
        }

        // 拷贝a数组的元素到backup数组
        System.arraycopy(a, 0, backup, 0, a.length);

        // 对数组a进行排序
        Arrays.sort(a);

        // 检查a数组中的每个元素是否与backup数组中的元素对应
        labelA: for (int i = 0; i < a.length; i++) {
            for (int j = 0; j < backup.length; j++) {
                // 如果查到对应元素，则跳出内层for循环，继续外层循环
                if (a[i] == backup[j]) {
                    continue labelA;
                }
            }
            // 内循环正常结束，说明没有找到对应元素
            return false;
        }
        return true;
    }

    // 检查选择排序
    public static boolean checkSelectionSort(String[] a) {
        String[] backup = new String[a.length];
        System.arraycopy(a, 0, backup, 0, a.length);
        sort(a);

        labelA: for (int i = 0; i < a.length; i++) {
            for (int j = 0; j < backup.length; j++) {
                // 如果查到对应元素，则跳出内层for循环，继续外层循环
                if (a[i] == backup[j]) {
                    continue labelA;
                }
            }
            // 内循环正常结束，说明没有找到对应元素
            return false;
        }
        return true;
    }

    // 用于对字符串进行排序的选择排序
    public static void sort(String[] a) {
        int N = a.length; // 数组长度
        for (int i = 0; i < N; i++) {
            int min = i; // 最小元素索引
            for (int j = i + 1; j < N; j++) {
                // 在a[i + 1]...a[N - 1]之间查找比a[min]小的元素
                if (less(a[j], a[min])) {
                    // 如果找到，则重新记录最小元素索引
                    min = j;
                }
            }
            // 内循环结束，交换i与min的值
            exch(a, i, min);
        }
    }

    private static boolean less(String v, String w) {
        return v.compareTo(w) < 0;
    }

    private static void exch(String[] a, int i, int j) {
        // 创建一个新的String对象
        String t = new String(a[i]);
        a[i] = a[j];
        a[j] = t;
    }

    public static void main(String[] args) {
        String[] test = new String[] { "d", "c", "b", "a" };
        String[] test2 = new String[] { "d", "c", "b", "a" };
        System.out.println(checkArraySort(test));
        System.out.println(checkSelectionSort(test2));
    }
}
```

***
**2.1.17 动画。修改插入排序和选择排序的代码，使之将数组内容绘制成正文中所示的棒状图。在每一轮排序后重绘图片来产生动画效果，并以一张“有序”的图片作为结束，即所有的圆棒均已按照高度有序排列。提示：使用类似于正文中的用例来随机生成Double值，在排序代码的适当位置调用show()方法，并在show()方法中清理画布并绘制棒状图。**

*Answer：*

选择排序：

```java
public class Ex2127Selection {
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        int N = a.length; // 数组长度
        for (int i = 0; i < N; i++) {
            int min = i; // 最小元素索引
            for (int j = i + 1; j < N; j++) {
                // 在a[i + 1]...a[N - 1]之间查找比a[min]小的元素
                if (less(a[j], a[min])) {
                    // 如果找到，则重新记录最小元素索引
                    min = j;
                }
            }
            // 内循环结束，交换i与min的值
            exch(a, i, min);
            show(a);
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    private static <T> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(T[] a) {
        StdDraw.clear();
        int n = a.length;
        for (int i = 0; i < n; i++) {
            double x = 1.0 * i / n;
            double y = (double) a[i] / 2.0;
            double rw = 0.5 / n;
            double rh = (double) a[i] / 2.0;

            StdDraw.filledRectangle(x, y, rw, rh);
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static <T extends Comparable<? super T>> boolean isSorted(T[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        int n = 50;
        Double[] doubles = new Double[n];
        for (int i = 0; i < n; i++) {
            // 用随机函数生成随机数
            doubles[i] = StdRandom.uniform();
        }
        sort(doubles);
    }
}
```

结果：*实际程序运行起来是一个动画*

![ex2117Selection](images\chapter02\ex2117Selection.PNG)

插入排序：

```java
public class Ex2127Selection {
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
                show(a);
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    private static <T> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(T[] a) {
        StdDraw.clear();
        int n = a.length;
        for (int i = 0; i < n; i++) {
            double x = 1.0 * i / n;
            double y = (double) a[i] / 2.0;
            double rw = 0.5 / n;
            double rh = (double) a[i] / 2.0;

            StdDraw.filledRectangle(x, y, rw, rh);
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static <T extends Comparable<? super T>> boolean isSorted(T[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        int n = 50;
        Double[] doubles = new Double[n];
        for (int i = 0; i < n; i++) {
            // 用随机函数生成随机数
            doubles[i] = StdRandom.uniform();
        }
        sort(doubles);
    }
}
```

结果：*实际程序运行起来是一个动画*

![ex2117Insertion](images\chapter02\ex2117Insertion.PNG)

***
**2.1.18 可视轨迹。修改你为上一题给出的解答，为插入排序和选择排序生成和正文中类似的可视轨迹。提示：使用setYscale()函数是一个明智的选择。附加题：添加必要的代码，与正文中的图片一样用红色和灰色强调不同角色的元素。**

*Answer：*

插入排序：

```java
public class Ex2128Insertion {
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                show(a, j);
                exch(a, j, j - 1);
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    private static <T> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(T[] a, int j) {
        StdDraw.clear();
        int n = a.length;
        for (int i = 0; i < n; i++) {
            if (i < j) {
                StdDraw.setPenColor(StdDraw.GRAY);
            } else if (i == j) {
                StdDraw.setPenColor(StdDraw.RED);
            } else {
                StdDraw.setPenColor(StdDraw.BLACK);
            }
            double x = 1.0 * i / n;
            double y = (double) a[i] / 2.0;
            double rw = 0.5 / n;
            double rh = (double) a[i] / 2.0;

            StdDraw.filledRectangle(x, y, rw, rh);
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static <T extends Comparable<? super T>> boolean isSorted(T[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        int n = 50;
        Double[] doubles = new Double[n];
        for (int i = 0; i < n; i++) {
            // 用随机函数生成随机数
            doubles[i] = StdRandom.uniform();
        }
        sort(doubles);
    }
}
```

选择排序：

```java
public class Ex2128Selection {
        public static <T extends Comparable<? super T>> void sort(T[] a) {
        int N = a.length; // 数组长度
        for (int i = 0; i < N; i++) {
            int min = i; // 最小元素索引
            for (int j = i + 1; j < N; j++) {
                // 在a[i + 1]...a[N - 1]之间查找比a[min]小的元素
                if (less(a[j], a[min])) {
                    // 如果找到，则重新记录最小元素索引
                    min = j;
                }
            }
            // 内循环结束，交换i与min的值
            show(a, i, min);
            exch(a, i, min);
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    private static <T> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T> void show(T[] a, int i, int min) {
        StdDraw.clear();
        int n = a.length;
        for (int i = 0; i < n; i++) {
            if (i < j) {
                StdDraw.setPenColor(StdDraw.GRAY);
            } else if (i == j) {
                StdDraw.setPenColor(StdDraw.RED);
            } else {
                StdDraw.setPenColor(StdDraw.BLACK);
            }
            double x = 1.0 * i / n;
            double y = (double) a[i] / 2.0;
            double rw = 0.5 / n;
            double rh = (double) a[i] / 2.0;     
            // x 代表线在那个位置 y代表这个线的高度
            // rw rh 理解成平面坐标参数       
            StdDraw.filledRectangle(x, y, rw, rh);
        }

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static <T extends Comparable<? super T>> boolean isSorted(T[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static void main(String[] args) {
        int n = 50;
        Double[] doubles = new Double[n];
        for (int i = 0; i < n; i++) {
            // 用随机函数生成随机数
            doubles[i] = StdRandom.uniform();
        }
        sort(doubles);
    }
}
```

图略。

***

**2.1.19 希尔排序的最坏情况。用1到100构造一个含有100个元素的数组并用希尔排序和递增序列 1 4 13 40 对其排序，使比较次数尽可能多。**

*Answer：*

***
**2.1.20 希尔排序的最好情况。最好情况是什么？证明你的结论。**

*Answer：*

与插入排序相同，最好情况就是已排序的数组。

***
**2.1.21 可比较的交易。用我们的Date类（请见2.1.1.4节）作为模板扩展你的Transaction类（请见练习1.2.13），实现Comparable接口，使交易能够按照金额排序。**

*Answer：*

直接使用官方实现的Transaction类。

```java
public class Transaction implements Comparable<Transaction> {
    private final String  who;      // customer
    private final Date    when;     // date
    private final double  amount;   // amount

    public Transaction(String who, Date when, double amount) {
        if (Double.isNaN(amount) || Double.isInfinite(amount))
            throw new IllegalArgumentException("Amount cannot be NaN or infinite");
        this.who    = who;
        this.when   = when;
        this.amount = amount;
    }

    public Transaction(String transaction) {
        String[] a = transaction.split("\\s+");
        who    = a[0];
        when   = new Date(a[1]);
        amount = Double.parseDouble(a[2]);
        if (Double.isNaN(amount) || Double.isInfinite(amount))
            throw new IllegalArgumentException("Amount cannot be NaN or infinite");
    }

    public String who() {
        return who;
    }

    public Date when() {
        return when;
    }

    public double amount() {
        return amount;
    }

    @Override
    public String toString() {
        return String.format("%-10s %10s %8.2f", who, when, amount);
    }

    public int compareTo(Transaction that) {
        return Double.compare(this.amount, that.amount);
    }  

    @Override
    public boolean equals(Object other) {
        if (other == this) return true;
        if (other == null) return false;
        if (other.getClass() != this.getClass()) return false;
        Transaction that = (Transaction) other;
        return (this.amount == that.amount) && (this.who.equals(that.who))
                                            && (this.when.equals(that.when));
    }

    public int hashCode() {
        int hash = 1;
        hash = 31*hash + who.hashCode();
        hash = 31*hash + when.hashCode();
        hash = 31*hash + ((Double) amount).hashCode();
        return hash;
        // return Objects.hash(who, when, amount);
    }

    public static class WhoOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.who.compareTo(w.who);
        }
    }

    public static class WhenOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.when.compareTo(w.when);
        }
    }

    public static class HowMuchOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return Double.compare(v.amount, w.amount);
        }
    }
}
```

***
**2.1.22 交易排序测试用例。编写一个SortTransaction类，在静态方法main()中从标准输入读取一系列交易，将它们排序并在标准输出中打印结果。**

*Answer：*

```java
public class SortTransaction {
    public static void main(String[] args) {
        Transaction[] a = new Transaction[4];
        a[0] = new Transaction("Turing   6/17/1990  644.08");
        a[1] = new Transaction("Tarjan   3/26/2002 4121.85");
        a[2] = new Transaction("Knuth    6/14/1999  288.34");
        a[3] = new Transaction("Dijkstra 8/22/2007 2678.40");
        
        Selection.sort(a);
        
        for (int i = 0; i < a.length; i++) {
            System.out.println(a[i]);
        }
    }
}
```

***

#### 实验题

***
**2.1.23 纸牌排序。请几位朋友分别将一副扑克牌排序（见练习2.1.13）。仔细观察并记录他们所使用的方法。**

*Answer：*

略。

***
**2.1.24 插入排序的哨兵。在插入排序的实现中先找出最小的元素并将其置于数组的最左边，这样就能去掉内循环的判断条件j>0。使用 SortCompare 来评估这种做法的效果。注意：这是一种常见的规避边界测试的方法，能够省略判断条件的元素通常称为哨兵。**

*Answer：*

使用官方的实现InsertionX.java：

```java
public static void sort(Comparable[] a) {
    int n = a.length;

    // put smallest element in position to serve as sentinel
    int exchanges = 0;
    for (int i = n - 1; i > 0; i--) {
        if (less(a[i], a[i-1])) {
            exch(a, i, i-1);
            exchanges++;
        }
    }
    // 一次交换都没发生说明数组此时已经有序，直接返回
    if (exchanges == 0) return; 

    // insertion sort with half-exchanges
    for (int i = 2; i < n; i++) {
        Comparable v = a[i];
        int j = i;
        while (less(v, a[j-1])) {
            a[j] = a[j-1];
            j--;
        }
        a[j] = v;
    }

    assert isSorted(a);
}
```

与Insertion进行对比：

```java
public class SortCompare2125 {

    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("Insertion")) {
            Insertion.sort(a);
        }
        if (alg.equals("InsertionX")) {
            InsertionX.sort(a);
        }
        if (alg.equals("Selection")) {
            Selection.sort(a);
        }
        if (alg.equals("Shell")) {
            Shell.sort(a);
        }
        if (alg.equals("Merge")) {
            Merge.sort(a);
        }
        if (alg.equals("Quick")) {
            Quick.sort(a);
        }
        if (alg.equals("Heap")) {
            Heap.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N, int T) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int t = 0; t < T; t++) {
            for (int i = 0; i < N; i++) {
                a[i] = StdRandom.uniform();
            }
            total += time(alg, a);
        }
        return total;
    }

    public static void main(String[] args) {
        String alg1 = args[0];
        String alg2 = args[1];

        int N = Integer.parseInt(args[2]);
        int T = Integer.parseInt(args[3]);

        double t1 = timeRandomInput(alg1, N, T);
        double t2 = timeRandomInput(alg2, N, T);

        StdOut.printf("For %d random Doubles\n    %s is", N, alg1);
        StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
    }
}
```

运行参数：InsertionX Insertion 10000 100

结果：

    For 10000 random Doubles
        InsertionX is 1.4 times faster than Insertion

***
**2.1.25 不需要交换的插入排序。在插入排序的实现中使较大元素右移一位只需要访问一次数组（而不用使用exch()）。使用 SortCompare 来评估这种做法的效果。**

*Answer：*

官方实现的InsertionX.java就是这种做法：

```java
// insertion sort with half-exchanges
for (int i = 2; i < n; i++) {
    Comparable v = a[i];
    int j = i;
    while (less(v, a[j-1])) {
        a[j] = a[j-1];
        j--;
    }
    a[j] = v;
}
```

评估见上一题。

***
**2.1.26 原始数据类型。编写一个能够处理int值的插入排序的新版本，比较它和正文中所给出的实现（能够隐式地用自动装箱和拆箱转换Integer值并排序）的性能。**

*Answer：*

直接将类型实现为int：

```java
public class InsertionInt {
    public static void sort(int[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
            }
        }
    }

    private static boolean less(int v, int w) {
        return v < w;
    }

    private static void exch(int[] a, int i, int j) {
        int t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    @SuppressWarnings("unused")
    private static void show(int[] a) {
        // 在单行中打印数组
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }

    public static boolean isSorted(int[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }
}
```

性能比较略。

**2.1.27 希尔排序的用时是次平方级的。在你的计算机上用SortCompare比较希尔排序和插入排序以及选择排序。测试数组的大小按照2的幂次递增，从128开始。**

*Answer：*

```java
public class SortCompare2127 {

    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("Insertion")) {
            Insertion.sort(a);
        }
        if (alg.equals("Selection")) {
            Selection.sort(a);
        }
        if (alg.equals("Shell")) {
            Shell.sort(a);
        }
        if (alg.equals("Merge")) {
            Merge.sort(a);
        }
        if (alg.equals("Quick")) {
            Quick.sort(a);
        }
        if (alg.equals("Heap")) {
            Heap.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int i = 0; i < N; i++) {
            a[i] = StdRandom.uniform();
        }
        total += time(alg, a);
        return total;
    }

    // args = {"Selection", "Insertion",  "Shell", "128"}
    public static void main(String[] args) {
        String alg1 = args[0];
        String alg2 = args[1];
        String alg3 = args[2];

        int N = Integer.parseInt(args[3]);

        for (int i = N, k = 1; k < 10; i *= 2, k++) {
            double t1 = timeRandomInput(alg1, i);
            double t2 = timeRandomInput(alg2, i);
            double t3 = timeRandomInput(alg3, i);
            System.out.println("ArrayLength = " + i);
            System.out.println(alg1 + "耗时：" + t1);
            System.out.println(alg2 + "耗时：" + t2);
            System.out.println(alg3 + "耗时：" + t3);
            System.out.println();
        }
    }
}
```

结果：

    ArrayLength = 128
    Selection耗时：0.001
    Insertion耗时：0.001
    Shell耗时：0.0

    ArrayLength = 256
    Selection耗时：0.001
    Insertion耗时：0.0
    Shell耗时：0.001

    ArrayLength = 512
    Selection耗时：0.001
    Insertion耗时：0.002
    Shell耗时：0.0

    ArrayLength = 1024
    Selection耗时：0.009
    Insertion耗时：0.003
    Shell耗时：0.0

    ArrayLength = 2048
    Selection耗时：0.012
    Insertion耗时：0.004
    Shell耗时：0.001

    ArrayLength = 4096
    Selection耗时：0.012
    Insertion耗时：0.012
    Shell耗时：0.001

    ArrayLength = 8192
    Selection耗时：0.047
    Insertion耗时：0.052
    Shell耗时：0.001

    ArrayLength = 16384
    Selection耗时：0.201
    Insertion耗时：0.225
    Shell耗时：0.003

    ArrayLength = 32768
    Selection耗时：0.898
    Insertion耗时：1.025
    Shell耗时：0.007

***
**2.1.28 相等的主键。对于主键仅可能取两种值的数组，评估和验证插入排序和选择排序的性能，假设两种主键值出现的概率相同。**

*Answer：*

```java
public class SortCompare2128 {

    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("Insertion")) {
            Insertion.sort(a);
        }
        if (alg.equals("Selection")) {
            Selection.sort(a);
        }
        if (alg.equals("Shell")) {
            Shell.sort(a);
        }
        if (alg.equals("Merge")) {
            Merge.sort(a);
        }
        if (alg.equals("Quick")) {
            Quick.sort(a);
        }
        if (alg.equals("Heap")) {
            Heap.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int i = 0; i < N; i++) {
            if(StdRandom.bernoulli()) {
                a[i] = 0d;
            } else {
                a[i] = 1d;
            }
        }
        total += time(alg, a);
        return total;
    }


    // args = {"Selection", "Insertion",  "128"}
    public static void main(String[] args) {
        String alg1 = args[0];
        String alg2 = args[1];

        int N = Integer.parseInt(args[2]);

        for (int i = N, k = 1; k < 10; i *= 2, k++) {
            double t1 = timeRandomInput(alg1, i);
            double t2 = timeRandomInput(alg2, i);
            System.out.println("ArrayLength = " + i);
            System.out.println(alg1 + "耗时：" + t1);
            System.out.println(alg2 + "耗时：" + t2);
            System.out.println();
        }
    }
}
```

结果：

    ArrayLength = 128
    Selection耗时：0.002
    Insertion耗时：0.001

    ArrayLength = 256
    Selection耗时：0.001
    Insertion耗时：0.0

    ArrayLength = 512
    Selection耗时：0.002
    Insertion耗时：0.001

    ArrayLength = 1024
    Selection耗时：0.007
    Insertion耗时：0.002

    ArrayLength = 2048
    Selection耗时：0.017
    Insertion耗时：0.002

    ArrayLength = 4096
    Selection耗时：0.025
    Insertion耗时：0.006

    ArrayLength = 8192
    Selection耗时：0.119
    Insertion耗时：0.025

    ArrayLength = 16384
    Selection耗时：0.533
    Insertion耗时：0.102

    ArrayLength = 32768
    Selection耗时：2.216
    Insertion耗时：0.418

***
**2.1.29 希尔排序的递增序列。通过实验比较算法2.3中所使用的递增序列和递增序列 1, 5, 19, 41, 109, 209, 505, 929, 2161, 3905, 8929, 16001, 36289, 64769, 146305, 260609 （这是通过序列 $9×4^k-9×2^k+1$ 和 $4^k-3×2^k+1$ 综合得到的）。**

*Answer：*

题目给出的递增序列更快。验证略。

***
**2.1.30 几何级数递增序列。通过实验找到一个t，使得对于大小为N=10^6的任意随机数组，使用递增序列1,[$t$],[$t^2$],[$t^3$],[$t^4$],...的希尔排序的运行时间最短。给出你能找到的三个最佳t值以及相应的递增序列。以下练习描述的是各种用于评估排序算法的测试用例。它们的作用是用随机数据帮助你增进对性能特性的理解。随着命令行指定的实验测试的增大，可以和SortCompare一样在它们中使用time()函数来得到更精确的结果。在以后的几节中我们会使用这些练习来评估更为复杂的算法**

*Answer：*

```java
public class SortCompare2130 {
    private static Integer[] createShells(int N, int t) {
        List<Integer> shells = new ArrayList<>();
        int h = 1;
        int k = 0;
        while (h < N / 3) {
            h = (int) Math.pow(t, k++);
            shells.add(h);
        }
        shells.add(h);
        return shells.toArray(new Integer[0]);
    }

    public static <T> double sort(Comparable<T>[] a, int t) {
        int N = a.length;

        Stopwatch timer = new Stopwatch();
        Integer[] shells = createShells(N, t);
        double time = timer.elapsedTime(); // 记录创建shells数组所用的时间
        
        // while循环改为for循环遍历shells数组，数组的第一项为0
        for (int k = shells.length - 1; k > 0; k--) {
            int h = shells[k];
            // 将数组变为h有序
            for (int i = h; i < N; i++) {
                // 将a[i]插入到a[i - h]、a[i - 2 * h]、a[i - 3 * h]...之中
                for (int j = i; j >= h && less(a[j], a[j - h]); j -= h) {
                    exch(a, j, j - h);
                }
            }
        }
        return time;
    }

    @SuppressWarnings("unchecked")
    private static <T> boolean less(Comparable<T> v, Comparable<T> w) {
        return v.compareTo((T) w) < 0;
    }

    private static <T> void exch(Comparable<T>[] a, int i, int j) {
        Comparable<T> t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    public static <T> boolean isSorted(Comparable<T>[] a) {
        // 测试数组元素是否有序
        for (int i = 0; i < a.length; i++) {
            if (less(a[i], a[i - 1]))
                return false;
        }
        return true;
    }

    public static double time(Double[] a, int t) {
        Stopwatch timer = new Stopwatch();
        double time = sort(a, t);
        return timer.elapsedTime() - time; // 返回的时间里扣除了创建shells数组的时间
    }

    public static double timeRandomInput(Double[] a, int t) {
        double total = 0d;
        total += time(a, t);
        return total;
    }

    public static void main(String[] args) {

        int N = 1000000;
        Double[] a = new Double[N];
        for (int i = 0; i < N; i++) {
            a[i] = StdRandom.uniform();
        }
        
        Double[] backup = new Double[N];
        for (int t = 2; t <= 10; t++) {
            System.arraycopy(a, 0, backup, 0, N);
            double time = timeRandomInput(backup, t);
            System.out.println("N = " + N + " t = " + t + " 耗时：" + time);
        }
    }
}
```

结果：

    N = 1000000 t = 2 耗时：2.147
    N = 1000000 t = 3 耗时：2.425
    N = 1000000 t = 4 耗时：2.38
    N = 1000000 t = 5 耗时：2.634
    N = 1000000 t = 6 耗时：2.628
    N = 1000000 t = 7 耗时：2.497
    N = 1000000 t = 8 耗时：2.479
    N = 1000000 t = 9 耗时：2.635
    N = 1000000 t = 10 耗时：2.609

所以t = 2，3，4。

***
**2.1.31 双倍测试。编写一个能够对排序算法进行双倍测试的用例。数组规模N的起始值为1000，排序后打印N、估计排序用时、实际排序用时以及在N倍增之后两次用时的比例。用这段程序验证在随机输入模型下插入排序和选择排序的运行时间都是平方级别的。对希尔排序的性能做出猜想并验证你的猜想。**

*Answer：*

主函数以测试Insertion为例：

```java
public class DoubleTest2131 {

    public static void insertionSort(Double[] a) {
        Insertion.sort(a);
    }

    public static void selectionSort(Double[] a) {
        Selection.sort(a);
    }

    public static void shellSort(Double[] a) {
        Shell.sort(a);
    }

    public static double timeTrialInsertion(Double[] a) {
        Stopwatch timer = new Stopwatch();
        insertionSort(a);
        return timer.elapsedTime();
    }

    public static double timeTrialSelection(Double[] a) {
        Stopwatch timer = new Stopwatch();
        selectionSort(a);
        return timer.elapsedTime();
    }

    public static double timeTrialShell(Double[] a) {
        Stopwatch timer = new Stopwatch();
        shellSort(a);
        return timer.elapsedTime();
    }

    public static Double[] createRandomDoubles(int N) {
        Double[] a = new Double[N];
        for (int i = 0; i < N; i++) {
            a[i] = StdRandom.uniform();
        }
        return a;
    }

    public static void main(String[] args) {
        
        Double[] arr = createRandomDoubles(50);
        Double[] backup = new Double[arr.length];
        System.arraycopy(arr, 0, backup, 0, arr.length);
        double prev = timeTrialInsertion(backup);

        for (int i = 100; i <= 200000; i *= 2) {

            arr = createRandomDoubles(i);
            backup = new Double[arr.length];
            System.arraycopy(arr, 0, backup, 0, arr.length);
            double time = timeTrialInsertion(backup);
            System.out.printf("%6d %7.2f ", i, time);
            System.out.printf("%7.2f\n", time / prev);
            prev = time;
        }
    }
}
```

结果：

Insertion：

       800    0.00    1.00
      1600    0.00    2.00
      3200    0.01    3.00
      6400    0.03    2.75
     12800    0.14    4.09
     25600    0.65    4.80
     51200    2.87    4.43
    102400   12.00    4.18

Selection：

       800    0.00    1.50
      1600    0.02    5.67
      3200    0.01    0.47
      6400    0.03    3.50
     12800    0.12    4.11
     25600    0.50    4.30
     51200    2.12    4.28
    102400    8.83    4.17

Shell：

       800    0.00    1.00
      1600    0.00    1.00
      3200    0.00    2.00
      6400    0.00    1.00
     12800    0.00    1.50
     25600    0.01    1.67
     51200    0.01    2.40
    102400    0.03    2.25

由此可见选择排序和插入排序是平方级别的，希尔排序比平方级别小比线性级别大。

***
**2.1.32 - 2.1.38**

这部分习题均为生成不同的输入并对三种排序算法进行比较，略。


### 2.2 归并排序

***

#### 练习题

***
**2.2.1 按照本节开头所示轨迹的格式给出原地归并的抽象merge()方法是如何将数组A E Q S U Y E I N O S T 排序的。**

*Answer：*

```java
public class Ex221 {
    
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        int N = a.length;
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), N);

        merge(a, aux, 0, (N - 1) / 2, N - 1);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
            show(a, k);
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static <T> void show(T[] a, int k) {
        for (int i = 0; i <= k; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {
        String[] strings = new String("A E Q S U Y E I N O S T").split(" ");
        for (int i = 0; i < strings.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i = 0; i < strings.length; i++) {
            System.out.print(strings[i] + " ");
        }
        System.out.println();
        sort(strings);
    }
}
```

结果：

    0 1 2 3 4 5 6 7 8 9 10 11 
    A E Q S U Y E I N O S T 
    A 
    A E 
    A E E 
    A E E I 
    A E E I N 
    A E E I N O 
    A E E I N O Q 
    A E E I N O Q S 
    A E E I N O Q S S 
    A E E I N O Q S S T 
    A E E I N O Q S S T U 
    A E E I N O Q S S T U Y 

***
**2.2.2 按照算法2.4所示轨迹的格式给出自定向下的归并排序是如何将数组E A S Y Q U E S T I O N排序的。**

*Answer：*

```java
public class Ex222 {
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
        show(a, lo, hi);
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
    
    public static <T> void show(T[] a, int lo, int hi) {
        for (int i = 0; i < lo; i++) {
            System.out.print("  ");
        }
        for (int i = lo; i <= hi; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        String[] strings = new String("E A S Y Q U E S T I O N").split(" ");
        for (int i = 0; i < strings.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i = 0; i < strings.length; i++) {
            System.out.print(strings[i] + " ");
        }
        System.out.println();
        sort(strings);
    }
}
```

结果：

    0 1 2 3 4 5 6 7 8 9 10 11 
    E A S Y Q U E S T I O N 
    A E 
    A E S 
          Q Y 
          Q U Y 
    A E Q S U Y 
                E S 
                E S T 
                      I O 
                      I N O 
                E I N O S T 
    A E E I N O Q S S T U Y 

***
**2.2.3 用自底向上的归并排序解答练习2.2.2。**

*Answer：*

```java
public class Ex223 {
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 进行lgN次两两归并
        int N = a.length;
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        for (int sz = 1; sz < N; sz = sz + sz) {
            for (int lo = 0; lo < N - sz; lo += sz + sz) {
                merge(a, aux, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
            }
        }
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        int i = lo, j = mid + 1;
        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
        show(a, lo, hi);
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
    
    public static <T> void show(T[] a, int lo, int hi) {
        for (int i = 0; i < lo; i++) {
            System.out.print("  ");
        }
        for (int i = lo; i <= hi; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        String[] strings = new String("E A S Y Q U E S T I O N").split(" ");
        for (int i = 0; i < strings.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i = 0; i < strings.length; i++) {
            System.out.print(strings[i] + " ");
        }
        System.out.println();
        sort(strings);
    }
}
```

结果：

    0 1 2 3 4 5 6 7 8 9 10 11 
    E A S Y Q U E S T I O N 
    A E 
        S Y 
            Q U 
                E S 
                    I T 
                        N O 
    A E S Y 
            E Q S U 
                    I N O T 
    A E E Q S S U Y 
    A E E I N O Q S S T U Y 

***
**2.2.4 是否当且仅当两个输入的子数组都有序时原地归并的抽象方法才能得到正确的结果？证明你的结论，或者给出一个反例。**

*Answer：*

是。lo...mid和mid+1...hi，两个子数组必须有序。

反例：

使用练习题2.2.1的程序，输入数组：F E Q S U Y E I N O S T，其中lo...mid不是有序的，得到排序路径和结果：

    0 1 2 3 4 5 6 7 8 9 10 11 
    F E Q S U Y E I N O S T 
    E 
    E F 
    E F E 
    E F E I 
    E F E I N 
    E F E I N O 
    E F E I N O Q 
    E F E I N O Q S 
    E F E I N O Q S S 
    E F E I N O Q S S T 
    E F E I N O Q S S T U 
    E F E I N O Q S S T U Y 

显然排序失败了。

***
**2.2.5 当输入数组的大小N=39时，给出自顶向下和自底向上的归并排序中各次归并子数组的大小及顺序。**

*Answer：*

数组长度没有取39。

自顶向下：

```java
public class Ex225MergeTD {
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
        show(a, lo, mid, hi);
    }

    public static <T> void show(T[] a, int lo, int mid, int hi) {
        for (int i = 0; i < lo; i++) {
            System.out.print("  ");
        }
        for (int i = lo; i <= hi; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println("(" + (mid - lo + 1) + ")" + "(" + (hi - mid - 1 + 1) + ")");
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        String[] strings = new String("E A S Y Q U E S T I O N").split(" ");
        for (int i = 0; i < strings.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i = 0; i < strings.length; i++) {
            System.out.print(strings[i] + " ");
        }
        System.out.println();
        sort(strings);
    }
}
```

结果：

例如最后一行A E E I N O Q S S T U Y (6)(6) 表示两个子数组的长度分别为6、6。来源为上面两个长度为6的子数组：A E Q S U Y (3)(3)、E I N O S T (3)(3)。

    0 1 2 3 4 5 6 7 8 9 10 11 
    E A S Y Q U E S T I O N 
    A E (1)(1)
    A E S (2)(1)
          Q Y (1)(1)
          Q U Y (2)(1)
    A E Q S U Y (3)(3)
                E S (1)(1)
                E S T (2)(1)
                      I O (1)(1)
                      I N O (2)(1)
                E I N O S T (3)(3)
    A E E I N O Q S S T U Y (6)(6)

自底向上：

```java
public class Ex225MergeBU {
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 进行lgN次两两归并
        int N = a.length;
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        for (int sz = 1; sz < N; sz = sz + sz) {
            for (int lo = 0; lo < N - sz; lo += sz + sz) {
                merge(a, aux, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
            }
        }
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        int i = lo, j = mid + 1;
        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
        show(a, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static <T> void show(T[] a, int lo, int mid, int hi) {
        for (int i = 0; i < lo; i++) {
            System.out.print("  ");
        }
        for (int i = lo; i <= hi; i++) {
            System.out.print(a[i] + " ");
        }
        System.out.println("(" + (mid - lo + 1) + ")" + "(" + (hi - mid - 1 + 1) + ")");
    }

    public static void main(String[] args) {
        String[] strings = new String("E A S Y Q U E S T I O N").split(" ");
        for (int i = 0; i < strings.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i = 0; i < strings.length; i++) {
            System.out.print(strings[i] + " ");
        }
        System.out.println();
        sort(strings);
    }
}
```

结果：

    0 1 2 3 4 5 6 7 8 9 10 11 
    E A S Y Q U E S T I O N 
    A E (1)(1)
        S Y (1)(1)
            Q U (1)(1)
                E S (1)(1)
                    I T (1)(1)
                        N O (1)(1)
    A E S Y (2)(2)
            E Q S U (2)(2)
                    I N O T (2)(2)
    A E E Q S S U Y (4)(4)
    A E E I N O Q S S T U Y (8)(4)

***
**2.2.6 编写一个程序来计算自顶向下和自底向上的归并排序访问数组的准确次数。使用这个程序将N=1至512的结果绘成曲线图，并将其和上限6NlgN比较。**

*Answer：*

```java
public class Ex226CountTimes {

    public static void main(String[] args) {

        Double[] arr = null;
        Double[] backup = null;

        StdDraw.setXscale(0, 600);
        StdDraw.setYscale(0, 6 * 512 * Math.log10(512) / Math.log10(2));
        StdDraw.setPenRadius(.005);

        for (int i = 1; i <= 512; i++) {
            // 创建数组
            arr = new Double[i];
            backup = new Double[i];
            for (int j = 0; j < i; j++) {
                arr[j] = StdRandom.uniform();
            }
            System.arraycopy(arr, 0, backup, 0, i);

            // 排序并统计访问数组次数
            MergeTD.sort(arr);
            MergeBU.sort(backup);
            int cntTD = MergeTD.getCnt();
            int cntBU = MergeBU.getCnt();

            // 输出访问次数
            System.out.println("i = " + i + ", cntTD = " + cntTD + ", cntBU = " + cntBU);

            // 画图
            StdDraw.setPenColor(StdDraw.RED);
            StdDraw.point(i, cntTD);
            StdDraw.setPenColor(StdDraw.BLUE);
            StdDraw.point(i, cntBU);

            // 重置cnt
            MergeTD.resetCnt();
            MergeBU.resetCnt();
        }
        System.out.println("理论访问数组次数：" + 6 * 512 * Math.log10(512) / Math.log10(2));
    }
}

// 自底向上
class MergeBU {

    private static int cnt;

    public static int getCnt() {
        return cnt;
    }

    public static void resetCnt() {
        cnt = 0;
    }

    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 进行lgN次两两归并
        int N = a.length;
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        for (int sz = 1; sz < N; sz = sz + sz) {
            for (int lo = 0; lo < N - sz; lo += sz + sz) {
                merge(a, aux, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
            }
        }
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        cnt += 2 * (hi - lo + 1);

        int i = lo, j = mid + 1;
        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
            cnt += 2;
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        cnt += 2;
        return v.compareTo(w) < 0;
    }
}

// 自顶向下
class MergeTD {

    private static int cnt;

    public static int getCnt() {
        return cnt;
    }

    public static void resetCnt() {
        cnt = 0;
    }

    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        cnt += 2 * (hi - lo + 1);

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
            cnt += 2;
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        cnt += 2;
        return v.compareTo(w) < 0;
    }
}
```

结果：

红线为自顶向下，蓝线为自底向上。
![ex226CountTimes.pngex226CountTimes](images\chapter02\ex226CountTimes.png)

结果：

    ...
    i = 500, cntTD = 25722, cntBU = 25738
    i = 501, cntTD = 25732, cntBU = 25754
    i = 502, cntTD = 25814, cntBU = 25806
    i = 503, cntTD = 25876, cntBU = 25880
    i = 504, cntTD = 25894, cntBU = 25914
    i = 505, cntTD = 25954, cntBU = 25996
    i = 506, cntTD = 26024, cntBU = 26062
    i = 507, cntTD = 26080, cntBU = 26080
    i = 508, cntTD = 26142, cntBU = 26120
    i = 509, cntTD = 26172, cntBU = 26158
    i = 510, cntTD = 26264, cntBU = 26240
    i = 511, cntTD = 26262, cntBU = 26262
    i = 512, cntTD = 26354, cntBU = 26354
    理论访问数组次数：27648.0

***
**2.2.7 证明归并排序的比较次数是单调递增的（即对$N>0$,$C(N+1)>C(N)$）。**

*Answer：*

对于长度为$N$的数组，自顶向下和自底向上的归并排序都需要$\frac{1}{2}NlgN$~$NlgN$次比较（证明参见中文版p173-176）。

取$C(N)=N\lg N$，$C(N+1)=(N+1)\lg(N+1)$，则$C(N+1)-C(N)=N\lg (1+\frac{1}{N})+\lg (N+1)$，由于$N>0$，所以$C(N+1)-C(N)>0$，所以得到$C(N+1)>C(N)$。

***
**2.28 假设将算法2.4修改为：只要a[mid]<=a[mid+1]就不用调用merge()方法，请证明用归并排序处理一个已经有序的数组所需的比较次数是线性级别的。**

*Answer：*

***
**2.2.9 在库函数中使用aux[]这样的静态数组是不妥当的，因为可能会有多个程序同时使用这个类。实现一个不用静态数组的Merge类，但也不要将aux[]变为merge()的局部变量。**

*Answer：*

官方实现的方法，将aux[]作为sort()的局部变量然后传递给merge()。

```java
public static void sort(Comparable[] a) {
    Comparable[] aux = new Comparable[a.length];
    sort(a, aux, 0, a.length-1);
}
private static void sort(Comparable[] a, Comparable[] aux, int lo, int hi) {
    if (hi <= lo) return;
    int mid = lo + (hi - lo) / 2;
    sort(a, aux, lo, mid);
    sort(a, aux, mid + 1, hi);
    merge(a, aux, lo, mid, hi);
}
```

使用泛型：

```java
public static <T extends Comparable<? super T>> void sort(T[] a) {
    T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
    sort(a, aux, 0, a.length - 1);
}

private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
    // 将数组a[lo...hi]排序
    if (hi <= lo) {
        return;
    }
    int mid = lo + (hi - lo) / 2;
    sort(a, aux, lo, mid);
    sort(a, aux, mid + 1, hi);
    merge(a, aux, lo, mid, hi);
}
```

***

#### 提高题

***
**2.2.10 快速归并。实现一个merge()方法，按降序将a[]的后半部分复制到aux[]，然后将其归并回a[]中。这样就可以去掉内循环中检测某半边是否用尽的代码。**

*Answer：*

这样产生排序的结果是不稳定的。

```java
public class FastMerge {
    
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    // 实现FastMerge
    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        
        // 复制数组的前半边
        for (int i = lo; i <= mid; i++) {
            aux[i] = a[i];
        }

        // 复制数组的后半边
        for (int j = mid + 1; j <= hi; j++) {
            aux[j] = a[hi - j + mid + 1];
        }

        int i = lo, j = hi;
        for (int k = lo; k <= hi; k++) {
            if (less(aux[j], aux[i])) {
                a[k] = aux[j--];
            } else {
                a[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

***
**2.2.11 改进。实现2.2.2节所述的对归并排序的三项改进：加快小数组的排序速度，检测数组是否已经有序以及通过在递归中交换参数避免数组复制。**

*Answer：*

官方实现的MergeX.java已经实现了题目中的三项改进。

```java
public class MergeX {
    private static final int CUTOFF = 7;  // cutoff to insertion sort

    // This class should not be instantiated.
    private MergeX() { }

    private static void merge(Comparable[] src, Comparable[] dst, int lo, int mid, int hi) {

        // precondition: src[lo .. mid] and src[mid+1 .. hi] are sorted subarrays
        assert isSorted(src, lo, mid);
        assert isSorted(src, mid+1, hi);

        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)              dst[k] = src[j++];
            else if (j > hi)               dst[k] = src[i++];
            else if (less(src[j], src[i])) dst[k] = src[j++];   // to ensure stability
            else                           dst[k] = src[i++];
        }

        // postcondition: dst[lo .. hi] is sorted subarray
        assert isSorted(dst, lo, hi);
    }

    private static void sort(Comparable[] src, Comparable[] dst, int lo, int hi) {
        // if (hi <= lo) return;
        if (hi <= lo + CUTOFF) { 
            insertionSort(dst, lo, hi); // 对小数组直接使用插入排序
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(dst, src, lo, mid); // 不断交换参数
        sort(dst, src, mid+1, hi);

        // if (!less(src[mid+1], src[mid])) {
        //    for (int i = lo; i <= hi; i++) dst[i] = src[i];
        //    return;
        // }

        // using System.arraycopy() is a bit faster than the above loop
        if (!less(src[mid+1], src[mid])) { // 检查src数组是否已经有序
            System.arraycopy(src, lo, dst, lo, hi - lo + 1);
            return;
        }

        merge(src, dst, lo, mid, hi);
    }

    /**
     * Rearranges the array in ascending order, using the natural order.
     * @param a the array to be sorted
     */
    public static void sort(Comparable[] a) {
        Comparable[] aux = a.clone();
        sort(aux, a, 0, a.length-1);  // 交换参数
        assert isSorted(a);
    }

    // sort from a[lo] to a[hi] using insertion sort
    private static void insertionSort(Comparable[] a, int lo, int hi) {
        for (int i = lo; i <= hi; i++)
            for (int j = i; j > lo && less(a[j], a[j-1]); j--)
                exch(a, j, j-1);
    }


    /*******************************************************************
     *  Utility methods.
     *******************************************************************/

    // exchange a[i] and a[j]
    private static void exch(Object[] a, int i, int j) {
        Object swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }

    // is a[i] < a[j]?
    private static boolean less(Comparable a, Comparable b) {
        return a.compareTo(b) < 0;
    }

    // is a[i] < a[j]?
    private static boolean less(Object a, Object b, Comparator comparator) {
        return comparator.compare(a, b) < 0;
    }


    /*******************************************************************
     *  Version that takes Comparator as argument.
     *******************************************************************/

    /**
     * Rearranges the array in ascending order, using the provided order.
     *
     * @param a the array to be sorted
     * @param comparator the comparator that defines the total order
     */
    public static void sort(Object[] a, Comparator comparator) {
        Object[] aux = a.clone();
        sort(aux, a, 0, a.length-1, comparator);
        assert isSorted(a, comparator);
    }

    private static void merge(Object[] src, Object[] dst, int lo, int mid, int hi, Comparator comparator) {

        // precondition: src[lo .. mid] and src[mid+1 .. hi] are sorted subarrays
        assert isSorted(src, lo, mid, comparator);
        assert isSorted(src, mid+1, hi, comparator);

        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)                          dst[k] = src[j++];
            else if (j > hi)                           dst[k] = src[i++];
            else if (less(src[j], src[i], comparator)) dst[k] = src[j++];
            else                                       dst[k] = src[i++];
        }

        // postcondition: dst[lo .. hi] is sorted subarray
        assert isSorted(dst, lo, hi, comparator);
    }


    private static void sort(Object[] src, Object[] dst, int lo, int hi, Comparator comparator) {
        // if (hi <= lo) return;
        if (hi <= lo + CUTOFF) { 
            insertionSort(dst, lo, hi, comparator);
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(dst, src, lo, mid, comparator);
        sort(dst, src, mid+1, hi, comparator);

        // using System.arraycopy() is a bit faster than the above loop
        if (!less(src[mid+1], src[mid], comparator)) {
            System.arraycopy(src, lo, dst, lo, hi - lo + 1);
            return;
        }

        merge(src, dst, lo, mid, hi, comparator);
    }

    // sort from a[lo] to a[hi] using insertion sort
    private static void insertionSort(Object[] a, int lo, int hi, Comparator comparator) {
        for (int i = lo; i <= hi; i++)
            for (int j = i; j > lo && less(a[j], a[j-1], comparator); j--)
                exch(a, j, j-1);
    }


   /***************************************************************************
    *  Check if array is sorted - useful for debugging.
    ***************************************************************************/
    private static boolean isSorted(Comparable[] a) {
        return isSorted(a, 0, a.length - 1);
    }

    private static boolean isSorted(Comparable[] a, int lo, int hi) {
        for (int i = lo + 1; i <= hi; i++)
            if (less(a[i], a[i-1])) return false;
        return true;
    }

    private static boolean isSorted(Object[] a, Comparator comparator) {
        return isSorted(a, 0, a.length - 1, comparator);
    }

    private static boolean isSorted(Object[] a, int lo, int hi, Comparator comparator) {
        for (int i = lo + 1; i <= hi; i++)
            if (less(a[i], a[i-1], comparator)) return false;
        return true;
    }

    // print array to standard output
    private static void show(Object[] a) {
        for (int i = 0; i < a.length; i++) {
            StdOut.println(a[i]);
        }
    }

    /**
     * Reads in a sequence of strings from standard input; mergesorts them
     * (using an optimized version of mergesort); 
     * and prints them to standard output in ascending order. 
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        MergeX.sort(a);
        show(a);
    }
}
```

***
**2.2.12 次线性的额外空间。用大小M将数组分为N/M块（简单起见，设M是N的约数）。实现一个归并方法，使之所需的额外空间减少到max(M,N/M)：（i）可以先将一个块看做一个元素，将块的第一个元素作为块的主键，用选择排序将块排序；（ii）遍历数组，将第一块和第二块归并，完成后将第二块和第三块归并，等等。**

*Answer：*

***
**2.2.13 平均情况的下限。请证明任意基于比较的排序算法的预期比较次数至少为~$NlgN$（假设输入元素的所有排列的出现概率是均等的）。**

*Answer：*

参见p177页命题I。

***
**2.2.14 归并有序的队列。编写一个静态方法，将两个有序队列作为参数，返回一个归并后的有序队列。**

*Answer：*

```java
public static <T extends Comparable<? super T>> Queue<T> mergeQueue(Queue<T> a, Queue<T> b) {
    // 创建一个新的Queue
    Queue<T> queue = new Queue<T>();

    int N = a.size() + b.size();

    while (queue.size() != N) {
        if (a.size() == 0 && b.size() != 0) { // 如果a取没了，则取b
            queue.enqueue(b.dequeue());
        } else if (a.size() != 0 && b.size() == 0) { // 如果b没了，则取a
            queue.enqueue(a.dequeue());
        } else if (a.peek().compareTo(b.peek()) < 0) { // 比较二者头元素的大小
            queue.enqueue(a.dequeue());
        } else {
            queue.enqueue(b.dequeue());
        }
    }
    return queue;
}
```

***
**2.2.15 自底向上的有序队列归并排序。用下面的方法编写一个自底向上的归并排序：给定N个元素，创建N个队列，每个队列包含其中一个元素。创建一个由这N个队列组成的队列，然后不断用练习2.2.14中的方法将队列的头两个元素归并，并将结果重新加入到队列结尾，知道队列的队列只剩下一个元素。**

*Answer：*

```java
public static <T extends Comparable<? super T>> void mergeQueue(Queue<Queue<T>> queue) {
    
    while (queue.size() != 1) {
        
        Queue<T> a = queue.dequeue();
        Queue<T> b = queue.dequeue();
        // mergeQueue()方法参见上一题
        Queue<T> result = MergeSortQueue.mergeQueue(a, b);
        queue.enqueue(result);
    }
}
```

***
**2.2.16 自然的归并排序。编写一个自底向上的归并排序，当需要将两个子数组排序时能够利用数组中已经有序的部分。首先找到一个有序的子数组（移动指针直到当前元素比上一个元素小为止），然后再找出另一个并将他们归并。根据数组大小和数组中递增子数组的最大长度分析算法的运行时间。**

*Answer：*

***
**2.2.17 链表排序。实现对链表的自然排序（这是将链表排序的最佳方法，因为它不需要额外的空间，且运行时间是线性对数级别的。）**

*Answer：*

以下方法在排序时不需要额外的空间，但运行时间应该是平方级别的，因为其排序方式为选择排序。

```java
public class LinkedListEx2217Selection<T> {

    private int n; // 链表大小
    private Node<T> beginMarker; // 链表起始标记
    private Node<T> endMarker; // 链表结束标记

    private static class Node<T> {
        private Node<T> next;
        private T value;

        public Node(T value) {
            this.next = null;
            this.value = value;
        }
    }

    public LinkedListEx2217Selection() {
        doClear();
    }

    public void doClear() {
        n = 0;
        beginMarker = new Node<>(null);
        endMarker = new Node<>(null);
        beginMarker.next = endMarker;
    }

    public boolean isEmpty() {
        return n == 0;
    }

    public int size() {
        return n;
    }

    // 在链表尾部添加一个结点，其value为t
    public boolean add(T t) {
        Node<T> newNode = new Node<>(t);
        Node<T> node = getPreNode(endMarker);
        node.next = newNode;
        newNode.next = endMarker;
        n++;
        return true;
    }

    // 获得node的前一个结点
    private Node<T> getPreNode(Node<T> node) {
        Node<T> tmp = beginMarker;
        while (tmp.next != node) {
            tmp = tmp.next;
        }
        return tmp;
    }

    // 对链表中的结点，按照value进行升序排序
    // 该方法没有创建新的结点，即没有产生额外的空间消耗
    // 基于选择排序的思想
    public void sort(Comparator<T> comparator) {
        
        if (n <= 1 || isSorted(comparator)) {
            return;
        }

        Node<T> start = beginMarker.next;
        
        for (int i = 0; i < n - 1; i++) {
            
            Node<T> minNode = start; // 记录最小结点
            T minValue = minNode.value; // 最小结点的值

            // 寻找最小结点
            Node<T> tmp = start;
            while (tmp.next != endMarker) {
                tmp = tmp.next;
                if (comparator.compare(tmp.value, minValue) < 0) {
                    minNode = tmp;
                    minValue = tmp.value;
                }
            }

            // 入如果最小结点没有发生变化，start结点向后移动一位，重新开始循环
            if (minNode == start) {
                start = start.next;
                continue;
            }

            // 对结点进行操作，将最小结点移动到当前start结点之后
            Node<T> preMinNode = getPreNode(minNode);
            preMinNode.next = minNode.next;
            Node<T> preStart = getPreNode(start);
            preStart.next = minNode;
            minNode.next = start;
        }
    }

    // 判断链表是否已经排序
    private boolean isSorted(Comparator<T> comparator) {
        Node<T> tmp = beginMarker.next;
        while (tmp.next != endMarker) {
            if (comparator.compare(tmp.next.value, tmp.value) < 0) {
                return false;
            }
            tmp = tmp.next;
        }
        return true;
    }

    @Override
    public String toString() {
        if (n == 0) {
            return "[]";
        } else {
            StringBuilder sb = new StringBuilder();
            sb.append("[");
            Node<T> node = beginMarker.next;
            for (int i = 0; i < n; i++) {
                sb.append(node.value + ", ");
                node = node.next;
            }
            sb.replace(sb.length() - 2, sb.length(), "]");
            return sb.toString();
        }
    }

    public static void main(String[] args) {
        LinkedListEx2217Selection<Integer> list = new LinkedListEx2217Selection<>();

        for (int i = 0; i < 10; i++) {
            list.add(StdRandom.uniform(100));
        }

        // 传入比较器
        list.sort(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1 - o2;
            }
        });
        System.out.println(list);
    }
}
```

使用归并排序的思想：

```java
class ListNode<T> {
    T value;
    ListNode<T> next;

    ListNode(T value) {
        this.value = value;
        next = null;
    }
}

public class Ex2217Solution {

    // 查找链表的中间结点
    public <T> ListNode<T> getMiddle(ListNode<T> head) {
        if (head == null) {
            return head;
        }
        // 使用快慢指针
        ListNode<T> slow, fast;
        slow = fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    // 单链表归并排序
    public <T extends Comparable<? super T>> ListNode<T> sort(ListNode<T> head) {
        if (head == null || head.next == null) {
            return head;
        }
        // 得到链表中间的数
        ListNode<T> middle = getMiddle(head);
        ListNode<T> half = middle.next;
        // 拆分链表
        middle.next = null;
        // 递归调用
        return merge(sort(head), sort(half));
    }

    // 合并两个有序链表
    public <T extends Comparable<? super T>> ListNode<T> merge(ListNode<T> a, ListNode<T> b) {
        ListNode<T> dummyHead, curr;
        dummyHead = new ListNode<>(null);
        curr = dummyHead;
        while (a != null && b != null) {
            if (a.value.compareTo(b.value) <= 0) {
                curr.next = a;
                a = a.next;
            } else {
                curr.next = b;
                b = b.next;
            }
            curr = curr.next;
        }
        curr.next = (a == null ? b : a);
        return dummyHead.next;
    }
}
```

***
**2.2.18 打乱链表。实现一个分治算法，使用线性对数级别的时间和对数级别的额外空间随机打乱一条链表。**

*Answer：*

***
**2.2.19 倒置。编写一个线性对数级别的算法统计给定数组中“倒置”的数量（即插入排序所需的交换次数，请见2.1节）。这个数量和Kendall tau距离有关，请见2.5节。**

*Answer：*

使用官方实现的Inversions.java：

```java
public class Inversions {

    // do not instantiate
    private Inversions() { }

    // merge and count
    private static long merge(int[] a, int[] aux, int lo, int mid, int hi) {
        long inversions = 0;

        // copy to aux[]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k]; 
        }

        // merge back to a[]
        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)           a[k] = aux[j++];
            else if (j > hi)            a[k] = aux[i++];
            else if (aux[j] < aux[i]) { a[k] = aux[j++]; inversions += (mid - i + 1); }
            else                        a[k] = aux[i++];
        }
        return inversions;
    }

    // return the number of inversions in the subarray b[lo..hi]
    // side effect b[lo..hi] is rearranged in ascending order
    private static long count(int[] a, int[] b, int[] aux, int lo, int hi) {
        long inversions = 0;
        if (hi <= lo) return 0;
        int mid = lo + (hi - lo) / 2;
        inversions += count(a, b, aux, lo, mid);  
        inversions += count(a, b, aux, mid+1, hi);
        inversions += merge(b, aux, lo, mid, hi);
        assert inversions == brute(a, lo, hi);
        return inversions;
    }


    /**
     * Returns the number of inversions in the integer array.
     * The argument array is not modified.
     * @param  a the array
     * @return the number of inversions in the array. An inversion is a pair of 
     *         indicies {@code i} and {@code j} such that {@code i < j}
     *         and {@code a[i] > a[j]}.
     */
    public static long count(int[] a) {
        int[] b   = new int[a.length];
        int[] aux = new int[a.length];
        for (int i = 0; i < a.length; i++)
            b[i] = a[i];
        long inversions = count(a, b, aux, 0, a.length - 1);
        return inversions;
    }

    // merge and count (Comparable version)
    private static <Key extends Comparable<Key>> long merge(Key[] a, Key[] aux, int lo, int mid, int hi) {
        long inversions = 0;

        // copy to aux[]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k]; 
        }

        // merge back to a[]
        int i = lo, j = mid+1;
        for (int k = lo; k <= hi; k++) {
            if      (i > mid)                a[k] = aux[j++];
            else if (j > hi)                 a[k] = aux[i++];
            else if (less(aux[j], aux[i])) { a[k] = aux[j++]; inversions += (mid - i + 1); }
            else                             a[k] = aux[i++];
        }
        return inversions;
    }

    // return the number of inversions in the subarray b[lo..hi]
    // side effect b[lo..hi] is rearranged in ascending order
    private static <Key extends Comparable<Key>> long count(Key[] a, Key[] b, Key[] aux, int lo, int hi) {
        long inversions = 0;
        if (hi <= lo) return 0;
        int mid = lo + (hi - lo) / 2;
        inversions += count(a, b, aux, lo, mid);  
        inversions += count(a, b, aux, mid+1, hi);
        inversions += merge(b, aux, lo, mid, hi);
        assert inversions == brute(a, lo, hi);
        return inversions;
    }


    /**
     * Returns the number of inversions in the comparable array.
     * The argument array is not modified.
     * @param  a the array
     * @param <Key> the inferred type of the elements in the array
     * @return the number of inversions in the array. An inversion is a pair of 
     *         indicies {@code i} and {@code j} such that {@code i < j}
     *         and {@code a[i].compareTo(a[j]) > 0}.
     */
    public static <Key extends Comparable<Key>> long count(Key[] a) {
        Key[] b   = a.clone();
        Key[] aux = a.clone();
        long inversions = count(a, b, aux, 0, a.length - 1);
        return inversions;
    }


    // is v < w ?
    private static <Key extends Comparable<Key>> boolean less(Key v, Key w) {
        return (v.compareTo(w) < 0);
    }

    // count number of inversions in a[lo..hi] via brute force (for debugging only)
    private static <Key extends Comparable<Key>> long brute(Key[] a, int lo, int hi) {
        long inversions = 0;
        for (int i = lo; i <= hi; i++)
            for (int j = i + 1; j <= hi; j++)
                if (less(a[j], a[i])) inversions++;
        return inversions;
    }

    // count number of inversions in a[lo..hi] via brute force (for debugging only)
    private static long brute(int[] a, int lo, int hi) {
        long inversions = 0;
        for (int i = lo; i <= hi; i++)
            for (int j = i + 1; j <= hi; j++)
                if (a[j] < a[i]) inversions++;
        return inversions;
    }

    /**
     * Reads a sequence of integers from standard input and
     * prints the number of inversions to standard output.
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        int[] a = StdIn.readAllInts();
        int n = a.length;
        Integer[] b = new Integer[n];
        for (int i = 0; i < n; i++)
            b[i] = a[i];
        StdOut.println(Inversions.count(a));
        StdOut.println(Inversions.count(b));
    }
}
```

***
**2.2.20 间接排序。编写一个不改变数组的归并排序，它返回一个int[]数组perm，其中perm[i]的值是原数组中第i小的元素的位置。**

*Answer：*

使用官方实现的indexSort()：

```java
/**
 * Returns a permutation that gives the elements in the array in ascending order.
 * @param a the array
 * @return a permutation {@code p[]} such that {@code a[p[0]]}, {@code a[p[1]]},
 *    ..., {@code a[p[N-1]]} are in ascending order
 */
public static int[] indexSort(Comparable[] a) {
    int n = a.length;
    int[] index = new int[n];
    for (int i = 0; i < n; i++)
        index[i] = i;

    int[] aux = new int[n];
    sort(a, index, aux, 0, n-1);
    return index;
}

// mergesort a[lo..hi] using auxiliary array aux[lo..hi]
private static void sort(Comparable[] a, int[] index, int[] aux, int lo, int hi) {
    if (hi <= lo) return;
    int mid = lo + (hi - lo) / 2;
    sort(a, index, aux, lo, mid);
    sort(a, index, aux, mid + 1, hi);
    merge(a, index, aux, lo, mid, hi);
}

// print array to standard output
private static void show(Comparable[] a) {
    for (int i = 0; i < a.length; i++) {
        StdOut.println(a[i]);
    }
}
```

***
**2.2.21 一式三份。给定三个列表，每个列表中包含N个名字，编写一个线性对数级别的算法来判定三份列表中是否含有公共的名字，如果有，返回第一个被找到的这种名字。**

*Answer：*

- 对三个列表先进行归并排序：~$3NlogN$
- 遍历其中一份列表：~$N$
- 在遍历上述列表时对每一个元素在另外两个列表中进行两次二分查找：~$2logN$

所以总耗时：~$NlogN$

实现略。

***
**2.2.22 三向归并排序。假设每次我们是把数组分成三个部分而不是两个部分并将它们分别排序，然后进行三向归并。这种算法的运行时间的增长数量级是多少？**

*Answer：*

***

#### 实验题

***
**2.2.23 改进。用实验评估正文中所提到的归并排序的三项改进（请见练习2.2.11）的效果，并比较正文中实现的归并和联系2.2.10所实现的归并之间的性能。根据经验给出应该在何时为子数组切换到插入排序。**

*Answer：*

```java
public class SortCompareEx2223 {
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("MergeX")) {
            MergeX.sort(a);
        }
        if (alg.equals("Merge")) {
            Merge.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N, int T) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int t = 0; t < T; t++) {
            for (int i = 0; i < N; i++) {
                a[i] = StdRandom.uniform();
            }
            total += time(alg, a);
        }
        return total;
    }

    public static void main(String[] args) {

        int N = StdIn.readInt();
        int T = StdIn.readInt();

        String alg1 = "MergeX";
        String alg2 = "Merge";

        double t1 = timeRandomInput(alg1, N, T);
        double t2 = timeRandomInput(alg2, N, T);

        StdOut.printf("For %d random Doubles\n    %s is", N, alg1);
        StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
    }
}
```

结果：

    For 1000000 random Doubles
        MergeX is 1.1 times faster than Merge

使用插入排序的条件：

```java
if (hi <= lo + CUTOFF) { 
    insertionSort(dst, lo, hi); // 对小数组直接使用插入排序
    return;
}
```

***
**2.2.24 改进的有序测试。在试验中用大型随机数组评估联系2.2.8所做的修改的效果。根据经验用N（被排序的原始数组的大小）的函数描述条件语句（a[mid] <= a[mid+1]）成立（无论数组是否有序）的平均次数。**

*Answer：*

***
**2.2.25 多向归并排序。实现一个k向（相对双向而言）归并排序程序。分析你的算法，估计最佳的k值并通过实验验证猜想。**

*Answer：*

***
**2.2.26 创建数组。使用SortCompare粗略比较在你的计算机上在merge()中和在sort()中创建aux[]数组的性能差异。**

*Answer：*

在sort()中创建aux数组：

```java
public class MergeTDT {

    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
    
    public static void main(String[] args) {
        String[] test = {"b", "a", "c", "d", "f", "e"};
        sort(test);
        for (String string : test) {
            System.out.print(string + " ");
        }
    }
}
```

在merge()中创建aux数组：

```java
public class MergeEx2226 {
    
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        merge(a, lo, mid, hi);
    }

    // 在Merge中创建aux数组
    private static <T extends Comparable<? super T>> void merge(T[] a, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        @SuppressWarnings("unchecked")
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

SortCompare：

```java
public class SortCompareEx2226 {
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("MergeTDT")) {
            MergeTDT.sort(a);
        }
        if (alg.equals("MergeEx2226")) {
            MergeEx2226.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N, int T) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int t = 0; t < T; t++) {
            for (int i = 0; i < N; i++) {
                a[i] = StdRandom.uniform();
            }
            total += time(alg, a);
        }
        return total;
    }

    public static void main(String[] args) {

        int N = StdIn.readInt();
        int T = StdIn.readInt();

        String alg1 = "MergeTDT";
        String alg2 = "MergeEx2226";

        double t1 = timeRandomInput(alg1, N, T);
        double t2 = timeRandomInput(alg2, N, T);

        StdOut.printf("For %d random Doubles\n    %s is", N, alg1);
        StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
    }
}
```

结果：

    For 100000 random Doubles
        MergeTDT is 114.5 times faster than MergeEx2226

***
**2.2.27 子数组长度。用归并将大型随机数组排序，根据经验用N（某次归并时两个子数组的长度之和）的函数估计当一个子数组用尽时另一个子数组的平均长度。**

*Answer：*

***
**2.2.28 自顶向下与自底向上。对于$N=10^3$、$N=10^4$、$N=10^5$和$N=10^6$，使用SortCompare比较自顶向下与自底向上的归并排序的性能。**

*Answer：*

自顶向下：

```java
public class MergeTDT {

    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        sort(a, aux, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, T[] aux, int lo, int hi) {
        // 将数组a[lo...hi]排序
        if (hi <= lo) {
            return;
        }
        int mid = lo + (hi - lo) / 2;
        sort(a, aux, lo, mid);
        sort(a, aux, mid + 1, hi);
        merge(a, aux, lo, mid, hi);
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {
        // 将a[lo...mid]和a[mid + 1...hi]归并
        int i = lo;
        int j = mid + 1;

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

自底向上：

```java
public class MergeBUT {

    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 进行lgN次两两归并
        int N = a.length;
        T[] aux = (T[]) Array.newInstance(a.getClass().getComponentType(), a.length);
        for (int sz = 1; sz < N; sz = sz + sz) {
            for (int lo = 0; lo < N - sz; lo += sz + sz) {
                merge(a, aux, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, N - 1));
            }
        }
    }

    private static <T extends Comparable<? super T>> void merge(T[] a, T[] aux, int lo, int mid, int hi) {

        // 将a[lo...hi]复制到aux[lo...hi]
        for (int k = lo; k <= hi; k++) {
            aux[k] = a[k];
        }

        int i = lo, j = mid + 1;
        for (int k = lo; k <= hi; k++) {
            if (i > mid) {
                a[k] = aux[j++];
            } else if (j > hi) {
                a[k] = aux[i++];
            } else if (less(aux[j], aux[i])) {
                a[k] = aux[j++];
            } else {
                a[k] = aux[i++];
            }
        }
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

SortCompare：TDT和BUT使用不同的数组

```java
public class SortCompareEx2227 {
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("MergeTDT")) {
            MergeTDT.sort(a);
        }
        if (alg.equals("MergeBUT")) {
            MergeBUT.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, int N, int T) {
        double total = 0d;
        Double[] a = new Double[N];
        for (int t = 0; t < T; t++) {
            for (int i = 0; i < N; i++) {
                a[i] = StdRandom.uniform();
            }
            total += time(alg, a);
        }
        return total;
    }

    public static void main(String[] args) {

        String alg1 = "MergeTDT";
        String alg2 = "MergeBUT";

        for (int i = 1000; i <= 1000000; i *= 10) {
            double t1 = timeRandomInput(alg1, i, 20);
            double t2 = timeRandomInput(alg2, i, 20);
            
            StdOut.printf("For %d random Doubles\n    %s is", i, alg1);
            StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
        }
    }
}
```

结果：

    For 1000 random Doubles
        MergeTDT is 1.5 times faster than MergeBUT
    For 10000 random Doubles
        MergeTDT is 0.6 times faster than MergeBUT
    For 100000 random Doubles
        MergeTDT is 1.1 times faster than MergeBUT
    For 1000000 random Doubles
        MergeTDT is 1.1 times faster than MergeBUT

SortCompare：TDT和BUT使用相同的数组

```java
public class SortCompareEx2227SameArray {
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("MergeTDT")) {
            MergeTDT.sort(a);
        }
        if (alg.equals("MergeBUT")) {
            MergeBUT.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, Double[] a) {
        return time(alg, a);
    }

    public static void main(String[] args) {

        String alg1 = "MergeTDT";
        String alg2 = "MergeBUT";

        Double[] a = null;
        Double[] b = null;
        
        for (int i = 1000; i <= 1000000; i *= 10) {
            
            double t1 = 0d;
            double t2 = 0d;
            for (int k = 0; k < 20; k++) {
                
                a = new Double[i];
                b = new Double[i];
                
                for (int j = 0; j < i; j++) {
                    a[j] = StdRandom.uniform();
                }
                System.arraycopy(a, 0, b, 0, i);
                
                t1 += timeRandomInput(alg1, a);
                t2 += timeRandomInput(alg2, b);
            }

            StdOut.printf("For %d random Doubles\n    %s is", i, alg1);
            StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
        }
    }
}
```

结果：

    For 1000 random Doubles
        MergeTDT is 1.2 times faster than MergeBUT
    For 10000 random Doubles
        MergeTDT is 0.7 times faster than MergeBUT
    For 100000 random Doubles
        MergeTDT is 1.1 times faster than MergeBUT
    For 1000000 random Doubles
        MergeTDT is 1.0 times faster than MergeBUT

***
**2.2.29 自然的归并排序。对于$N=10^3$、$10^6$、$10^9$，类型为Long的随机主键数组，根据经验给出自然的归并排序（请见联系2.2.16）所需要的遍数。**

### 2.3 快速排序

***

#### 练习题

***
**2.3.1 按照partition()方法的轨迹的格式给出该方法是如何切分数组E A S Y Q U E S T I O N的。**

*Answer：*

只进行一次partition()。

```java
public class Ex231 {
    
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        StdRandom.shuffle(a);
        show(a, null, null);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        System.out.println("j = " + j);
//      sort(a, lo, j - 1); // 排序左半边
//      sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
            show(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        show(a, i, j);
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T> void show(T[] a, Integer i, Integer j) {
        if (i == null && j == null) {
            System.out.print(" " + "\t" + " " + "\t");
        } else {
            System.out.print(i + "\t" + j + "\t");
        }
        for (int k = 0; k < a.length; k++) {
            if (k < 10) {
                System.out.print(a[k] + " ");
            } else {
                System.out.print(" " + a[k] + " ");
            }
        }
        System.out.println();
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        String[] a = new String("E A S Y Q U E S T I O N").split(" ");
        System.out.print("i\tj\t");
        for (int i = 0; i < a.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        System.out.print(" \t \t");
        for (int i = 0; i < a.length; i++) {
            if (i < 10) {
                System.out.print(a[i] + " ");
            } else {
                System.out.print(" " + a[i] + " ");
            }
        }
        System.out.println();
        sort(a);
    }
}
```

结果：

    i   j   0 1 2 3 4 5 6 7 8 9 10 11 
            E A S Y Q U E S T I  O  N 
            S A U Y E I N Q T S  E  O 
    2   11  S A O Y E I N Q T S  E  U 
    3   10  S A O E E I N Q T S  Y  U 
    8   9   S A O E E I N Q S T  Y  U 
    9   8   S A O E E I N Q S T  Y  U 
    j = 8

***
**2.3.2 按照本节中快速排序所示轨迹的格式给出快速排序是如何将数组E A S Y Q U E S T I O N排序的（出于练习的目的，可以忽略开头打乱数组的部分）。**

*Answer：*

```java
public class Ex232 {
    
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            show(a, lo, null, hi);
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        show(a, lo, j, hi);
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T> void show(T[] a, Integer lo, Integer j, Integer hi) {
        if (j == null) {
            System.out.print(lo + "\t" + " " + "\t" + hi + "\t");
        } else {
            System.out.print(lo + "\t" + j + "\t" + hi + "\t");
        }
        for (int k = 0; k < a.length; k++) {
            if (k < 10) {
                System.out.print(a[k] + " ");
            } else {
                System.out.print(" " + a[k] + " ");
            }
        }
        System.out.println();
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        String[] a = new String("E A S Y Q U E S T I O N").split(" ");
        System.out.print("lo\tj\thi\t");
        for (int i = 0; i < a.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        System.out.print("  \t \t  \t");
        for (int i = 0; i < a.length; i++) {
            if (i < 10) {
                System.out.print(a[i] + " ");
            } else {
                System.out.print(" " + a[i] + " ");
            }
        }
        System.out.println();
        sort(a);
    }
}
```

结果：

    lo  j   hi  0 1 2 3 4 5 6 7 8 9 10 11 
                E A S Y Q U E S T I  O  N 
    0   2   11  E A E Y Q U S S T I  O  N 
    0   1   1   A E E Y Q U S S T I  O  N 
    0       0   A E E Y Q U S S T I  O  N 
    2       1   A E E Y Q U S S T I  O  N 
    3   11  11  A E E N Q U S S T I  O  Y 
    3   4   10  A E E I N U S S T Q  O  Y 
    3       3   A E E I N U S S T Q  O  Y 
    5   10  10  A E E I N O S S T Q  U  Y 
    5   5   9   A E E I N O S S T Q  U  Y 
    5       4   A E E I N O S S T Q  U  Y 
    6   7   9   A E E I N O Q S T S  U  Y 
    6       6   A E E I N O Q S T S  U  Y 
    8   9   9   A E E I N O Q S S T  U  Y 
    8       8   A E E I N O Q S S T  U  Y 
    10      9   A E E I N O Q S S T  U  Y 
    11      10  A E E I N O Q S S T  U  Y 
    12      11  A E E I N O Q S S T  U  Y 

***
**2.3.3 对于长度为N的数组，在Quick.sort()执行时，其最大的元素最多会被交换多少次。**

*Answer：*

***
**2.3.4 假如跳过开头打乱数组的操作，给出六个含有10个元素的数组，使得Quick.sort()所需的比较次数达到最坏情况。**

*Answer：*

倒序数组：10 9 8 7 6 5 4 3 2 1

***
**2.3.5 给出一段代码将已知只有两种主键值的数组排序。**

*Answer：*

使用三向切分快速排序。

```java
public class Ex233Quick3way {

    // This class should not be instantiated.
    private Ex233Quick3way() {
    }

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    // quicksort the subarray a[lo .. hi] using 3-way partitioning
    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int lt = lo, gt = hi;
        T v = a[lo];
        int i = lo + 1;
        while (i <= gt) {
            int cmp = a[i].compareTo(v);
            if (cmp < 0) {
                exch(a, lt++, i++);
            } else if (cmp > 0) {
                exch(a, i, gt--);
            } else {
                i++;
            }
        }

        // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi].
        sort(a, lo, lt - 1);
        sort(a, gt + 1, hi);
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    public static void main(String[] args) {
        Integer[] a = { 5, 5, 1, 5, 1, 5 };
        sort(a);
        for (Integer integer : a) {
            System.out.print(integer + " ");
        }
    }
}
```

结果：

    1 1 5 5 5 5 

***
**2.3.6 编写一度按代码来计算$C_N$的准确值，在$N=100、1000、10000$的情况下比较准确值和估计值$2NlnN$的差距。**

*Answer：*

```java
class QuickTEx236 {

    private static int cnt; // 记录比较次数

    public static int getCnt() {
        return cnt;
    }

    public static void resetCnt() {
        cnt = 0;
    }

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                cnt++;
                if (i == hi) {
                    cnt--;
                    break;
                }
            }
            cnt++;
            while (less(v, a[--j])) {
                cnt++;
                if (j == lo) {
                    cnt--;
                    break;
                }
            }
            cnt++;
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }
        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}

public class Ex236CalCn {
    
    public static void main(String[] args) {

        for (int N = 100; N <= 10000; N *= 10) {
            
            Double[] a = new Double[N];
            for (int i = 0; i < a.length; i++) {
                a[i] = StdRandom.uniform();
            }

            QuickTEx236.sort(a);
            int cnt = QuickTEx236.getCnt();
            System.out.println("Cn = " + cnt + "， 估计值2NlnN = " + 2 * N * Math.log(N));
            QuickTEx236.resetCnt();
        }
    }
}
```

结果：

    Cn = 846， 估计值2NlnN = 921.0340371976183
    Cn = 11333， 估计值2NlnN = 13815.510557964273
    Cn = 158959， 估计值2NlnN = 184206.80743952366

**2.3.7 在使用快速排序将N个不重复的元素排序时，计算大小为0、1、2的子数组的数量。如果你喜欢数学，请推导；如果你不喜欢，请做一些实验并提出猜想。**

*Answer：*

```java
class QuickTEx237 {

    // 统计长度为0、1、2的子数组的个数
    private static int cnt0;
    private static int cnt1;
    private static int cnt2;

    public static int getCnt0() {
        return cnt0;
    }

    public static int getCnt1() {
        return cnt1;
    }

    public static int getCnt2() {
        return cnt2;
    }
    
    public static void resetCnt() {
        cnt0 = 0;
        cnt1 = 0;
        cnt2 = 0;
    }

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分

        // 判断左边子数组
        int subArr1length = j - lo;
        if (subArr1length == 0) {
            cnt0++;
        } else if (subArr1length == 1) {
            cnt1++;
        } else if (subArr1length == 2) {
            cnt2++;
        }

        // 判断右边子数组
        int subArr2length = hi - j;
        if (subArr2length == 0) {
            cnt0++;
        } else if (subArr2length == 1) {
            cnt1++;
        } else if (subArr2length == 2) {
            cnt2++;
        }

        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        Integer[] a = { 5, 2, 1, 3, 4, 0 };
        sort(a);
        for (Integer integer : a) {
            System.out.print(integer + " ");
        }
    }
}

public class Ex237CountSubArray {
    public static void main(String[] args) {

        for (int N = 100; N <= 1000000; N *= 10) {
            // 创建N个不重复的元素
            Integer[] arr = new Integer[N];
            for (int i = 0; i < arr.length; i++) {
                arr[i] = i;
            }
            StdRandom.shuffle(arr);

            QuickTEx237.sort(arr);
            System.out.println("数组长度为" + N + "大小为0的子数数量：" + QuickTEx237.getCnt0());
            System.out.println("数组长度为" + N + "大小为1的子数数量：" + QuickTEx237.getCnt1());
            System.out.println("数组长度为" + N + "大小为2的子数数量：" + QuickTEx237.getCnt2());

            QuickTEx237.resetCnt();
            System.out.println();
        }
    }
}
```

结果：

    数组长度为100大小为0的子数数量：37
    数组长度为100大小为1的子数数量：32
    数组长度为100大小为2的子数数量：18

    数组长度为1000大小为0的子数数量：319
    数组长度为1000大小为1的子数数量：341
    数组长度为1000大小为2的子数数量：168

    数组长度为10000大小为0的子数数量：3353
    数组长度为10000大小为1的子数数量：3324
    数组长度为10000大小为2的子数数量：1650

    数组长度为100000大小为0的子数数量：33319
    数组长度为100000大小为1的子数数量：33341
    数组长度为100000大小为2的子数数量：16650

    数组长度为1000000大小为0的子数数量：333505
    数组长度为1000000大小为1的子数数量：333248
    数组长度为1000000大小为2的子数数量：166805

***
**2.3.8 Quick.sort()在处理N个全部重复的元素时大约需要多少次比较。**

```java
class QuickTEx238 {

    private static int cnt; // 记录比较次数

    public static int getCnt() {
        return cnt;
    }

    public static void resetCnt() {
        cnt = 0;
    }

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                cnt++;
                if (i == hi) {
                    cnt--;
                    break;
                }
            }
            cnt++;
            while (less(v, a[--j])) {
                cnt++;
                if (j == lo) {
                    cnt--;
                    break;
                }
            }
            cnt++;
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }
        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}

public class Ex238SameElm {
    public static void main(String[] args) {
        int N = StdIn.readInt();
        Integer[] arr = new Integer[N];

        for (int i = 0; i < arr.length; i++) {
            arr[i] = 0;
        }

        QuickTEx238.sort(arr);
        int cnt = QuickTEx238.getCnt();
        System.out.println("Cn = " + cnt);
        QuickTEx238.resetCnt();
    }
}
```

***
**2.3.9 请说明Quick.sort()在处理只有两种主键值的数组时的行为，以及处理只有三种主键值的数组时的行为。**

*Answer：*

两种主键值：

    lo  j   hi  0 1 2 3 4 5 6 7 8 9 10 11 
                E E S E S E E S E S  S  E 
    0   4   11  E E E E E S E S S S  S  E 
    0   2   3   E E E E E S E S S S  S  E 
    0   1   1   E E E E E S E S S S  S  E 
    0       0   E E E E E S E S S S  S  E 
    2       1   E E E E E S E S S S  S  E 
    3       3   E E E E E S E S S S  S  E 
    5   9   11  E E E E E S E E S S  S  S 
    5   8   8   E E E E E S E E S S  S  S 
    5   7   7   E E E E E E E S S S  S  S 
    5   6   6   E E E E E E E S S S  S  S 
    5       5   E E E E E E E S S S  S  S 
    7       6   E E E E E E E S S S  S  S 
    8       7   E E E E E E E S S S  S  S 
    9       8   E E E E E E E S S S  S  S 
    10  11  11  E E E E E E E S S S  S  S 
    10      10  E E E E E E E S S S  S  S 
    12      11  E E E E E E E S S S  S  S 

三种主键值：

    lo  j   hi  0 1 2 3 4 5 6 7 8 9 10 11 
                E E S A S E A S A S  S  A 
    0   3   11  A A A A S S E E S S  S  E 
    0   1   2   A A A A S S E E S S  S  E 
    0       0   A A A A S S E E S S  S  E 
    2       2   A A A A S S E E S S  S  E 
    4   9   11  A A A A S E E E S S  S  S 
    4   8   8   A A A A S E E E S S  S  S 
    4   7   7   A A A A E E E S S S  S  S 
    4   5   6   A A A A E E E S S S  S  S 
    4       4   A A A A E E E S S S  S  S 
    6       6   A A A A E E E S S S  S  S 
    8       7   A A A A E E E S S S  S  S 
    9       8   A A A A E E E S S S  S  S 
    10  11  11  A A A A E E E S S S  S  S 
    10      10  A A A A E E E S S S  S  S 
    12      11  A A A A E E E S S S  S  S 

***
**2.3.10 Chebyshev不等式表明，一个随机变量的标准差距离均值大于k的概率小于$1/k^2$。对于N=100万，用Chebyshev不等式计算快速排序所使用的比较次数大于1000亿次的概率。**

*Answer：*

***
**2.3.11 加入在遇到和切分元素重复的元素时我们继续扫描数组而不是停下来，证明使用这种方法的快速排序在处理只有若干种元素值的数组时得运行时间是平方级别的。**

*Answer：*

***
**2.3.12 按照代码所示轨迹的格式给出信息量最佳的快速排序第一次是如何切分数组B A B A B A B A C A D A B R A的。**

*Answer：*

使用三向切分的快速排序：

```java
class Quick3wayTEx2312 {

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    // quicksort the subarray a[lo .. hi] using 3-way partitioning
    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int lt = lo, gt = hi;
        T v = a[lo];
        int i = lo + 1;
        while (i <= gt) {
            int cmp = a[i].compareTo(v);
            if (cmp < 0) {
                exch(a, lt++, i++);
                show(a, lt, i, gt);
            } else if (cmp > 0) {
                exch(a, i, gt--);
                show(a, lt, i, gt);
            } else {
                i++;
            }
        }

        // a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi].
//      sort(a, lo, lt - 1);
//      sort(a, gt + 1, hi);
    }
    
    private static <T> void show(T[] a, Integer lt, Integer i, Integer gt) {
        System.out.print(lt + "\t" + i + "\t" + gt + "\t");
        for (int k = 0; k < a.length; k++) {
            if (k < 10) {
                System.out.print(a[k] + " ");
            } else {
                System.out.print(" " + a[k] + " ");
            }
        }
        System.out.println();
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}

public class Ex2312 {

    public static void main(String[] args) {
        String[] a = new String("B A B A B A B A C A D A B R A").split(" ");
        System.out.print("lt\ti\tgt\t");
        for (int i = 0; i < a.length; i++) {
            System.out.print(i + " ");
        }
        System.out.println();
        System.out.print(" \t \t \t");
        for (int i = 0; i < a.length; i++) {
            if (i < 10) {
                System.out.print(a[i] + " ");
            } else {
                System.out.print(" " + a[i] + " ");
            }
        }
        System.out.println();
        Quick3wayTEx2312.sort(a);
    }
}
```

结果：

    lt  i   gt  0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 
                B A B A B A B A C A  D  A  B  R  A 
    1   2   14  A B B A B A B A C A  D  A  B  R  A 
    2   4   14  A A B B B A B A C A  D  A  B  R  A 
    3   6   14  A A A B B B B A C A  D  A  B  R  A 
    4   8   14  A A A A B B B B C A  D  A  B  R  A 
    4   8   13  A A A A B B B B A A  D  A  B  R  C 
    5   9   13  A A A A A B B B B A  D  A  B  R  C 
    6   10  13  A A A A A A B B B B  D  A  B  R  C 
    6   10  12  A A A A A A B B B B  R  A  B  D  C 
    6   10  11  A A A A A A B B B B  B  A  R  D  C 
    7   12  11  A A A A A A A B B B  B  B  R  D  C 

***
**2.3.13 在最佳、平均和最坏情况下，快速排序的递归深度分别是多少？这决定了系统为了追踪递归调用所需的栈的大小。在最坏情况下保证递归深度为数组大小的对数级的方法请见练习2.3.20。**

*Answer：*

***
**2.3.14 证明在用快速排序处理大小为N的不重复数组时，比较第i大和第j大元素的概率为$2/(j-i)$，并用该结论证明命题K。**

*Answer：*

***

#### 提高题

***
**2.3.15 螺丝和螺帽。（G.J.E.Rawlins）假设有N个螺丝和N个螺帽混在一堆，你需要快速将它们配对。一个螺丝只会匹配一个螺帽，一个螺帽也只会匹配一个螺丝。你可以试着把一个螺丝和一个螺帽拧在一起看看谁大了，但不能直接比较两个螺丝或者两个螺帽。给出一个解决这个问题的有效方法。**

*Answer：*

条件抽象为有一个元素类型为T的数组和一个元素类型为K的数组，两个数组等长，且元素一一对应。并且T和K均没有实现Comparable接口，即无法将其中一个元素与同类的另一个元素进行比较。但是可以比较一组(T, K)，来得到实际与T匹配的K大于或小于当前比较的K。

- 方法一：对每一个T，用遍历数组的方法查看每一个K，直至二者匹配成功。这个算法是平方级别的。
- 方法二：使用快速排序的类似方法。
    1. 取一个元素T。
    2. 逐一将K元素与这个T元素匹配，找到实际匹配的元素K，并将全体K元素分为两堆，一堆比实际与T匹配的K小（堆A），另一堆比实际与T匹配的K大（堆B）。
    3. 用上一步得到与T匹配的K元素，反过来将T元素也分为两堆。一堆比第一步取出的T元素小（堆a），一堆比第一步取出的T元素大（堆b）。
    4. 现在我们得到了一组已经匹配的(T, K)，和两堆T元素、两堆K元素。重复步骤1~4，但是现在在比较时只需要比较对应的堆中元素了（堆a中的T元素只需要与堆A中的K元素比较），直至全部比配。

***
**2.3.16 最佳情况。编写一段程序来生成使算法2.5中的sort()方法表现最佳的数组（无重复元素）：数组大小为N且不包含重复元素，每次切分后两个子数组的大小最多差1。（对于这道练习，我们不需要在排序开始时打乱数组。）**

*Answer：*

```java
class QuickTEx2316 {
    
    public static <T extends Comparable<? super T>> boolean sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        // StdRandom.shuffle(a);
        return sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> boolean sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            // 正常情况，返回true
            return true;
        }
        int j = partition(a, lo, hi); // 将数组切分
        // 如果两个子数组长度差超过1，返回false
        if (Math.abs(hi + lo - 2 * j) > 1) {
            return false;
        }
        return sort(a, lo, j - 1) && sort(a, j + 1, hi);
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}

public class Ex2316 {

    public static void main(String[] args) {

        // 以N = 20为例
        Integer[] arr = new Integer[20];
        Integer[] backup = new Integer[20];
        
        while (true) {
            // 对arr赋值，并确保无重复元素
            for (int i = 0; i < arr.length; i++) {
                arr[i] = i;
            }
            // 打乱arr
            StdRandom.shuffle(arr);
            // 将arr复制到backup
            System.arraycopy(arr, 0, backup, 0, arr.length);
            // 如果对arr的排序返回true，则说明此时arr数组满足要求
            if (QuickTEx2316.sort(arr)) {
                break;
            }
        }
        
        // 此时打印出的backup数组即为满足要求的数组
        for (Integer i : backup) {
            System.out.print(i + " ");
        }
    }
}
```

一个满足条件的数组：

    10 9 2 16 8 11 1 6 17 14 4 15 0 13 19 12 18 5 7 3 

***
**2.3.17 哨兵。修改算法2.5，去掉内循环while中的边界检查。由于切分元素本身就是一个哨兵（v不可能小于a[lo]），左侧边界的检查是多余的。要去掉另一个检查，可以在打乱数组后将数组的最大元素放在a[length-1]中。该元素永远不会移动（除非和相等的元素交换），可以在所有包含它的子数组中称为哨兵。**

*Answer：*

```java
public class QuickTSentinel {
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sortWithSentinel(a);
    }

    private static <T extends Comparable<? super T>> void sortWithSentinel(T[] a) {
        // 查找数组的最大元素
        int max = 0;
        for (int i = 0; i < a.length; i++) {
            if (less(a[max], a[i])) {
                max = i;
            }
        }
        // 将最大元素放在数组末尾
        exch(a, max, a.length - 1);
        sortWithSentinel(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sortWithSentinel(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partitionWithSentinel(a, lo, hi); // 将数组切分
        sortWithSentinel(a, lo, j - 1); // 排序左半边
        sortWithSentinel(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partitionWithSentinel(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;

        T v = a[lo];

        while (true) {
            while (less(a[++i], v)) {
                // if (i == hi) break;
            }
            while (less(v, a[--j])) {
                // if (j == lo) break;
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j);
        return j;
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
    
    public static void main(String[] args) {
        
        Integer[] arr = new Integer[20];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
        StdRandom.shuffle(arr);
        sort(arr);
        for (Integer i : arr) {
            System.out.print(i + " ");
        }
    }
}
```

***
**2.3.18 三取样切分。为快速排序实现正文所述的三取样切分（参见2.3.3.2节）。运行双倍测试来确认这项改动的效果。**

*Answer：*

三取样切分：

```java
public class QuickTMedian3 {
    
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {

        int n = hi - lo + 1;
        int m = median3(a, lo, lo + n / 2, hi); // 计算中位数
        exch(a, m, lo); // 将中位数上的元素与lo交换

        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                // 对于左边界的检查实际是冗余的
                // 因为v不可能小于a[lo]
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> int median3(T[] a, int i, int j, int k) {
        return (less(a[i], a[j]) ? (less(a[j], a[k]) ? j : less(a[i], a[k]) ? k : i)
                : (less(a[k], a[j]) ? j : less(a[k], a[i]) ? k : i));
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

SortCompare：

```java
public class SortCompareEx2318 {
    
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("QuickT")) {
            QuickT.sort(a);
        }
        if (alg.equals("QuickTMedian3")) {
            QuickTMedian3.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, Double[] a) {
        return time(alg, a);
    }

    public static void main(String[] args) {

        String alg1 = "QuickTMedian3";
        String alg2 = "QuickT";

        Double[] a = null;
        Double[] b = null;
        
        for (int i = 1000; i <= 1000000; i *= 10) {
            
            double t1 = 0d;
            double t2 = 0d;
            for (int k = 0; k < 20; k++) {
                
                a = new Double[i];
                b = new Double[i];
                
                for (int j = 0; j < i; j++) {
                    a[j] = StdRandom.uniform();
                }
                System.arraycopy(a, 0, b, 0, i);
                
                t1 += timeRandomInput(alg1, a);
                t2 += timeRandomInput(alg2, b);
            }
            StdOut.printf("For %d random Doubles\n    %s is", i, alg1);
            StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
        }
    }
}
```

结果：

    For 1000 random Doubles
        QuickTMedian3 is 1.3 times faster than QuickT
    For 10000 random Doubles
        QuickTMedian3 is 1.5 times faster than QuickT
    For 100000 random Doubles
        QuickTMedian3 is 1.0 times faster than QuickT
    For 1000000 random Doubles
        QuickTMedian3 is 1.1 times faster than QuickT

***
**2.3.19 五取样切分。实现一种基于随机抽取子数组中5个元素病区中位数进行切分的快速排序。将取样元素放在数组的一侧以保证只有中位数元素参与了切分。运行双倍测试来确定这项改动的效果，并和标准的快速排序以及三取样切分的快速排序及逆行比较。附加题：找到一种对于任意输入都只需要少于7次比较的五取样算法。**

*Answer：*

***
**2.3.20 非递归的快速排序。实现一个非递归的快速排序，使用一个循环来将弹出栈的子数组切分并将结果子数组重新压入栈。**

*Answer：*

```java
public class QuickTLoop {
    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        Stack<Integer> stack = new Stack<>();
        if (lo < hi) {
            stack.push(hi);
            stack.push(lo);
            while (!stack.isEmpty()) {
                int m = stack.pop();
                int n = stack.pop();
                int idx = partition(a, m, n);
                if (m < idx - 1) {
                    stack.push(idx - 1);
                    stack.push(m);
                }
                if (n > idx + 1) {
                    stack.push(n);
                    stack.push(idx + 1);
                }
            }
        }
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        T pivot = a[lo];
        while (lo < hi) {
            while (lo < hi && !less(a[hi], pivot)) {
                hi--;
            }
            a[lo] = a[hi];
            while (lo < hi && !less(pivot, a[lo])) {
                lo++;
            }
            a[hi] = a[lo];
        }
        a[lo] = pivot;
        return lo;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}
```

***
**2.3.21 重复元素排序的比较次数的下界。完成命题M的证明的第一部分。参考命题I的证明并注意当有k个主键值时所有元素存在$N!/f_1!f_2!...f_k!$种不同的排列，其中第i个主键值出现的频率为f_i（即$Np_i$，按照，命题M的记法），且$f_1+f_2+...+f_k=N$。**

*Answer：*

***
**2.3.22 快速三向切分。用将重复元素放置于子数组两端的方式实现一个信息量最优的排序算法。使用两个索引p和q，是的a[lo..p-1]和a[q+1..hi]的元素都和a[lo]相等。使用另外两个索引i和j，是的a[p..i-1]小于a[lo]，a[j+i..q]大于a[lo]。在内循环种加入代码，在a[i]和v相当时将其与a[p]交换（并将p加1），在a[j]和v相等且a[i]和a[j]尚未和v进行比较之前将其与a[q]交换。**

*Answer：*

使用官方实现的快速三向切分：

```java
public class QuickBentleyMcIlroy {

    // cutoff to insertion sort, must be >= 1
    private static final int INSERTION_SORT_CUTOFF = 8;

    // cutoff to median-of-3 partitioning
    private static final int MEDIAN_OF_3_CUTOFF = 40;

    // This class should not be instantiated.
    private QuickBentleyMcIlroy() { }

    /**
     * Rearranges the array in ascending order, using the natural order.
     * @param a the array to be sorted
     */
    public static void sort(Comparable[] a) {
        sort(a, 0, a.length - 1);
    }

    private static void sort(Comparable[] a, int lo, int hi) { 
        int n = hi - lo + 1;

        // cutoff to insertion sort
        if (n <= INSERTION_SORT_CUTOFF) {
            insertionSort(a, lo, hi);
            return;
        }

        // use median-of-3 as partitioning element
        else if (n <= MEDIAN_OF_3_CUTOFF) {
            int m = median3(a, lo, lo + n/2, hi);
            exch(a, m, lo);
        }

        // use Tukey ninther as partitioning element
        else  {
            int eps = n/8;
            int mid = lo + n/2;
            int m1 = median3(a, lo, lo + eps, lo + eps + eps);
            int m2 = median3(a, mid - eps, mid, mid + eps);
            int m3 = median3(a, hi - eps - eps, hi - eps, hi); 
            int ninther = median3(a, m1, m2, m3);
            exch(a, ninther, lo);
        }

        // Bentley-McIlroy 3-way partitioning
        int i = lo, j = hi+1;
        int p = lo, q = hi+1;
        Comparable v = a[lo];
        while (true) {
            while (less(a[++i], v))
                if (i == hi) break;
            while (less(v, a[--j]))
                if (j == lo) break;

            // pointers cross
            if (i == j && eq(a[i], v))
                exch(a, ++p, i);
            if (i >= j) break;

            exch(a, i, j);
            if (eq(a[i], v)) exch(a, ++p, i);
            if (eq(a[j], v)) exch(a, --q, j);
        }


        i = j + 1;
        for (int k = lo; k <= p; k++)
            exch(a, k, j--);
        for (int k = hi; k >= q; k--)
            exch(a, k, i++);

        sort(a, lo, j);
        sort(a, i, hi);
    }


    // sort from a[lo] to a[hi] using insertion sort
    private static void insertionSort(Comparable[] a, int lo, int hi) {
        for (int i = lo; i <= hi; i++)
            for (int j = i; j > lo && less(a[j], a[j-1]); j--)
                exch(a, j, j-1);
    }


    // return the index of the median element among a[i], a[j], and a[k]
    private static int median3(Comparable[] a, int i, int j, int k) {
        return (less(a[i], a[j]) ?
               (less(a[j], a[k]) ? j : less(a[i], a[k]) ? k : i) :
               (less(a[k], a[j]) ? j : less(a[k], a[i]) ? k : i));
    }

   /***************************************************************************
    *  Helper sorting functions.
    ***************************************************************************/
    
    // is v < w ?
    private static boolean less(Comparable v, Comparable w) {
        if (v == w) return false;    // optimization when reference equal
        return v.compareTo(w) < 0;
    }

    // does v == w ?
    private static boolean eq(Comparable v, Comparable w) {
        if (v == w) return true;    // optimization when reference equal
        return v.compareTo(w) == 0;
    }
        
    // exchange a[i] and a[j]
    private static void exch(Object[] a, int i, int j) {
        Object swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }


   /***************************************************************************
    *  Check if array is sorted - useful for debugging.
    ***************************************************************************/
    private static boolean isSorted(Comparable[] a) {
        for (int i = 1; i < a.length; i++)
            if (less(a[i], a[i-1])) return false;
        return true;
    }

    // print array to standard output
    private static void show(Comparable[] a) {
        for (int i = 0; i < a.length; i++) {
            StdOut.println(a[i]);
        }
    }

    /**
     * Reads in a sequence of strings from standard input; quicksorts them
     * (using an optimized version of quicksort); 
     * and prints them to standard output in ascending order. 
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {
        String[] a = StdIn.readAllStrings();
        QuickBentleyMcIlroy.sort(a);
        assert isSorted(a);
        show(a);
    }
}
```

***
**2.3.23 Java的排序库函数。在练习2.3.22的代码种使用Turkey's ninther方法来找出切分元素---选择三组，每组三个元素，分别取三组元素的中为主，然后取三个中位数的中位数作为切分元素，且在排序小数组时切换到插入排序。**

*Answer：*

***
**2.3.24 取样排序。（W.Frazer, A.McKellar）实现一个快速排序，取样大小为$2^k-1$。首先将取样得到的元素排序，然后在递归函数种使用样品的中位数切分。分为两部分的其余样品元素无需再次排序并可以分别应用于原数组的两个子数组。这种算法被称为取样排序。**

*Answer：*

***

#### 实验题

***
**2.3.25 切换到插入排序。实现一个快速排序，在子数组元素少于M时切换到插入排序。用快速排序处理大小N分别为、$10^4$、$10^5$、$10^6$的随机数组，根据经验给出使其在你的计算环境种运行速度最快的M值。将M从0变化到30的每个值所得到的平均运行时间绘成曲线。**

*Answer：*

```java
public class QuickTWithInsertion {

    private static final int INSERTION_SORT_CUTOFF = 8;

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }

        // 判断是否为小数组
        int n = hi - lo + 1;
        if (n <= INSERTION_SORT_CUTOFF) {
            insertionSort(a, lo, hi + 1);
            return;
        }

        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    // 对小数组使用插入排序，排序[lo...hi)
    private static <T extends Comparable<? super T>> void insertionSort(T[] a, int lo, int hi) {
        for (int i = lo; i < hi; i++) {
            for (int j = i; j > lo && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
            }
        }
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                // 对于左边界的检查实际是冗余的
                // 因为v不可能小于a[lo]
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        Integer[] arr = new Integer[20];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
        StdRandom.shuffle(arr);
        sort(arr);
        for (Integer i : arr) {
            System.out.print(i + " ");
        }
    }
}
```

***
**2.3.26 子数组的大小。编写一个程序，在快速排序处理大小为N的数组的过程中，当子数组的大小小于M时，排序方法需要切换为插入排序。将子数组的大小绘制成直方图。用$N=10^5$、$M=10、20、50$进行测试。**

*Answer：*

略。

***
**2.3.27 忽略小数组。用实验对比一下处理小数组的方法和练习2.3.25的处理方法的效果：在快速排序中直接忽略小数组，仅在快速排序结束后运行一次插入排序。**

*Answer：*

```java
class QuickTIgnoreSubarray {

    private static final int INSERTION_SORT_CUTOFF = 8;

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        // 打乱数组，消除对输入的依赖
        StdRandom.shuffle(a);
        sort(a, 0, a.length - 1);
        insertionSort(a); // 快排之后运行一次插入排序
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }

        // 判断是否为小数组
        int n = hi - lo + 1;
        if (n <= INSERTION_SORT_CUTOFF) {
            // 忽略小数组
            return;
        }

        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    // 插入排序，排序[lo...hi)
    private static <T extends Comparable<? super T>> void insertionSort(T[] a) {
        int N = a.length;
        for (int i = 1; i < N; i++) {
            // 将a[i]插入到a[i - 1]、a[i - 2]、a[i - 3]...之中
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--) {
                exch(a, j, j - 1);
            }
        }
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;
        T v = a[lo];
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                // 对于左边界的检查实际是冗余的
                // 因为v不可能小于a[lo]
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}

public class SortCompareEx2327 {
    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("QuickTIgnoreSubarray")) {
            QuickTIgnoreSubarray.sort(a);
        }
        if (alg.equals("QuickTWithInsertion")) {
            QuickTWithInsertion.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, Double[] a) {
        return time(alg, a);
    }

    public static void main(String[] args) {

        String alg1 = "QuickTWithInsertion";
        String alg2 = "QuickTIgnoreSubarray";

        Double[] a = null;
        Double[] b = null;

        for (int i = 1000; i <= 1000000; i *= 10) {

            double t1 = 0d;
            double t2 = 0d;
            for (int k = 0; k < 20; k++) {

                a = new Double[i];
                b = new Double[i];

                for (int j = 0; j < i; j++) {
                    a[j] = StdRandom.uniform();
                }
                System.arraycopy(a, 0, b, 0, i);

                t1 += timeRandomInput(alg1, a);
                t2 += timeRandomInput(alg2, b);
            }
            StdOut.printf("For %d random Doubles\n    %s is", i, alg1);
            StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
        }
    }
}
```

结果：

    For 1000 random Doubles
        QuickTWithInsertion is 1.5 times faster than QuickTIgnoreSubarray
    For 10000 random Doubles
        QuickTWithInsertion is 0.8 times faster than QuickTIgnoreSubarray
    For 100000 random Doubles
        QuickTWithInsertion is 1.1 times faster than QuickTIgnoreSubarray
    For 1000000 random Doubles
        QuickTWithInsertion is 1.1 times faster than QuickTIgnoreSubarray

***
**2.3.28 递归深度。用经验性的研究估计切换阈值为M的快速排序在将大小为N的不重复数组排序时的平均递归深度，其中$M=10、20、50$，$N=10^3、10^4、10^5、10^6$。**

*Answer：*

***
**2.3.29 随机化。用经验性的研究对比随机选择切分元素和正文所述的一开始就将数组随机化这两种策略的效果。在子数组大小为M时进行切换，将大小为N的不重复数组排序，其中$M=10、20、50$，$N=10^3、10^4、10^5、10^6$。**

*Answer：*

```java
class QuickTRandomPartition {

    private static final int CUTOFF = 10; // 设置M

    public static <T extends Comparable<? super T>> void sort(T[] a) {
        sort(a, 0, a.length - 1);
    }

    private static <T extends Comparable<? super T>> void sort(T[] a, int lo, int hi) {
        if (hi <= lo) {
            return;
        }
        int j = partition(a, lo, hi); // 将数组切分
        sort(a, lo, j - 1); // 排序左半边
        sort(a, j + 1, hi); // 排序右半边
    }

    private static <T extends Comparable<? super T>> int partition(T[] a, int lo, int hi) {
        // 将数组分为a[lo...i-1]，a[i]，a[i+1...hi]
        int i = lo, j = hi + 1;

        // 如果是小数组
        if (j - i <= CUTOFF) {
            // 随机挑选一个元素与lo进行交换
            exch(a, lo, StdRandom.uniform(i, j));
        }
        T v = a[lo]; // 将lo作为切分元素
        
        while (true) {
            // 扫描左右，检查扫描是否结束并交换元素
            while (less(a[++i], v)) {
                if (i == hi) {
                    break;
                }
            }
            while (less(v, a[--j])) {
                // 对于左边界的检查实际是冗余的
                // 因为v不可能小于a[lo]
                if (j == lo) {
                    break;
                }
            }
            if (i >= j) {
                break;
            }
            exch(a, i, j);
        }

        exch(a, lo, j); // 将v = a[j]放入正确的位置
        return j; // 达成 a[lo..j-1] <= a[j] <= a[j+1..hi]
    }

    private static <T extends Comparable<? super T>> void exch(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }

    private static <T extends Comparable<? super T>> boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }
}

public class SortCompareEx2329 {

    public static double time(String alg, Double[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("QuickT")) {
            QuickT.sort(a);
        }
        if (alg.equals("QuickTRandomPartition")) {
            QuickTRandomPartition.sort(a);
        }
        return timer.elapsedTime();
    }

    public static double timeRandomInput(String alg, Double[] a) {
        return time(alg, a);
    }

    public static void main(String[] args) {

        String alg1 = "QuickT";
        String alg2 = "QuickTRandomPartition";

        Double[] a = null;
        Double[] b = null;

        for (int i = 1000; i <= 1000000; i *= 10) {

            double t1 = 0d;
            double t2 = 0d;
            for (int k = 0; k < 20; k++) {

                a = new Double[i];
                b = new Double[i];

                for (int j = 0; j < i; j++) {
                    a[j] = StdRandom.uniform();
                }
                StdRandom.shuffle(a);
                System.arraycopy(a, 0, b, 0, i);

                t1 += timeRandomInput(alg1, a);
                t2 += timeRandomInput(alg2, b);
            }
            StdOut.printf("For %d random Doubles\n    %s is", i, alg1);
            StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
        }
    }
}
```

M = 10：

    For 1000 random Doubles
        QuickT is 0.9 times faster than QuickTRandomPartition
    For 10000 random Doubles
        QuickT is 1.1 times faster than QuickTRandomPartition
    For 100000 random Doubles
        QuickT is 0.8 times faster than QuickTRandomPartition
    For 1000000 random Doubles
        QuickT is 0.7 times faster than QuickTRandomPartition

M = 20：

    For 1000 random Doubles
        QuickT is 0.9 times faster than QuickTRandomPartition
    For 10000 random Doubles
        QuickT is 1.5 times faster than QuickTRandomPartition
    For 100000 random Doubles
        QuickT is 0.9 times faster than QuickTRandomPartition
    For 1000000 random Doubles
        QuickT is 0.8 times faster than QuickTRandomPartition

M = 50：

    For 1000 random Doubles
        QuickT is 0.6 times faster than QuickTRandomPartition
    For 10000 random Doubles
        QuickT is 1.2 times faster than QuickTRandomPartition
    For 100000 random Doubles
        QuickT is 0.8 times faster than QuickTRandomPartition
    For 1000000 random Doubles
        QuickT is 0.8 times faster than QuickTRandomPartition

***
**2.3.30 极端情况。用初始随机化和非初始随机化的快速排序测试练习2.1.35和练习2.1.36中描述的大型非随机数据。在将这些大数组排序时，乱序对快速排序性能有何影响？**

*Answer：*

***
**2.3.31 运行时间直方图。编写一个程序，接受命令行参数N和T，用快速排序对大小为N的随机浮点数数组进行T次排序。并将所有运行时间绘制成直方图。令$N=10^3、10^4、10^5、10^6$，为了使曲线更平滑，T值越大越好。**

*Answer：*

```java
public class Ex2331 {
    
    private static <T> void show(T[] a) {
        StdDraw.clear();
        int n = a.length;
        for (int i = 0; i < n; i++) {
            double x = 1.0 * i / n;
            double y = (double) a[i] / 2.0;
            double rw = 0.5 / n;
            double rh = (double) a[i] / 2.0;
            StdDraw.filledRectangle(x, y, rw, rh);
        }
    }
    
    public static void main(String[] args) {
        Double[] times = new Double[100]; // T
        for (int i = 0; i < times.length; i++) {
            Double[] arr = new Double[100000]; // N
            for (int j = 0; j < arr.length; j++) {
                arr[j] = StdRandom.uniform();
            }
            
            Stopwatch timer = new Stopwatch();
            QuickT.sort(arr);
            times[i] = timer.elapsedTime();
        }
        show(times);
    }
}
```
    
### 2.4 优先队列

***

#### 练习题

***
**2.4.1 用序列P R I O \* R \* \* I \* T \* Y \* \* \*  Q U E \* \* \* U \* E（字母表示插入元素，星号表示删除最大元素操作一个初始为空的优先队列。）给出每次删除最大元素是返回的字符。**

*Answer：*

    R R P O T Y I I U Q E U

***
**2.4.2 分析以下说法：要实现在常数时间找到最大元素，为何不用一个栈或队列，然后记录已插入的最大元素并在找出最大元素时返回它的值。**

*Answer：*

根据书中2.4.2.3的说法，“在所有初级实现中，插入元素和删除最大元素这两个操作之一在最坏情况下需要线性时间来完成。”

***
**2.4.3 用以下数据结构实现优先队列，支持插入元素和删除最大元素的操作：无序数组、有序数组、无序链表和链表。**

*Answer：*

无序数组实现：

```java
public class PQUnsortedArray<T extends Comparable<? super T>> {
    
    private int N; // 元素个数
    private T[] arr; // 用于保存元素的数组
    private static final int DEFAULT_SIZE = 10; // 默认数组长度
    
//  public PQUnsortedArray(Class<T> type) {
//      this(type, DEFAULT_SIZE);
//  }
//  
//  @SuppressWarnings("unchecked")
//  public PQUnsortedArray(Class<T> type, int size) {
//      arr = (T[]) Array.newInstance(type, size);
//  }
    
    public PQUnsortedArray() {
        this(DEFAULT_SIZE);
    }
    
    @SuppressWarnings("unchecked")
    public PQUnsortedArray(int capacity) {
        arr = (T[]) new Comparable[capacity];
    }
    
    // 插入一个元素
    // 时间复杂度O(1)
    public void insert(T t) {
        if (N == arr.length) {
            throw new UnsupportedOperationException("Array Overflow..");
        }
        
        arr[N++] = t;
    }
    
    // 删除并返回最大元素
    // 由于getMaxIndex()方法，时间复杂度O(N)
    public T delMax() {
        if (N == 0) {
            throw new UnsupportedOperationException("Array Underflow..");
        }
        
        int idx = getMaxIndex();
        
        T t = arr[idx]; // 得到最大元素
        exch(idx, --N); // 将最大元素与末尾元素交换
        arr[N] = null; // 防止对象游离
        
        return t;
    }
    
    // 获取最大元素的角标
    // 时间复杂度O(N)
    private int getMaxIndex() {
        
        if (N == 1) {
            return 0;
        }
        
        int maxIdx = 0;
        
        for (int i = 1; i < N; i++) {
            if (less(maxIdx, i)) {
                maxIdx = i;
            }
        }
        
        return maxIdx;
    }
    
    private boolean less(int i, int j) {
        return arr[i].compareTo(arr[j]) < 0;
    }
    
    private void exch(int i, int j) {
        T t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
    
    public T[] getArr() {
        return arr;
    }
}
```

有序数组实现：

```java
public class PQSortedArray<T extends Comparable<? super T>> {
    
    private int N; // 元素个数
    private T[] arr; // 用于保存元素的数组
    private static final int DEFAULT_SIZE = 10; // 默认数组长度
    
//  public PQSortedArray(Class<T> type) {
//      this(type, DEFAULT_SIZE);
//  }
//  
//  @SuppressWarnings("unchecked")
//  public PQSortedArray(Class<T> type, int size) {
//      arr = (T[]) Array.newInstance(type, size);
//  }
    
    public PQSortedArray() {
        this(DEFAULT_SIZE);
    }
    
    @SuppressWarnings("unchecked")
    public PQSortedArray(int capacity) {
        arr = (T[]) new Comparable[capacity];
    }
    
    // 插入一个元素
    // 由于每次插入元素都进行插入排序，所以插入排序的复杂度与最好情况类似，为O(N)
    public void insert(T t) {
        if (N == arr.length) {
            throw new UnsupportedOperationException("Array Overflow..");
        }
        
        arr[N++] = t;
        
        // 插入元素后对数组进行排序
        Insertion.sort(arr, 0, N);
    }
    
    // 删除并返回最大元素 
    // 复杂度O(1)
    public T delMax() {
        if (N == 0) {
            throw new UnsupportedOperationException("Array Underflow..");
        }
        
        T t = arr[N - 1]; // 得到最大元素
        arr[--N] = null; // 防止对象游离
        return t;
    }
}
```

无序链表实现：

```java
public class PQUnSortedLinkedList<T extends Comparable<? super T>> {

    private int N; // 链表包含的元素数量
    private Node<T> beginMarker; // 定义链表起始标记
    private Node<T> endMarker; // 定义链表结束标志

    // 定义结点
    private static class Node<T> {
        T value;
        Node<T> prev;
        Node<T> next;

        public Node(T value) {
            this(value, null, null);
        }

        public Node(T value, Node<T> prev, Node<T> next) {
            this.value = value;
            this.prev = prev;
            this.next = next;
        }
    }

    public PQUnSortedLinkedList() {
        beginMarker = new Node<>(null);
        endMarker = new Node<>(null, beginMarker, null);
        beginMarker.next = endMarker;
        N = 0;
    }

    // 插入元素到链表尾，复杂度为O(1)
    public void insert(T t) {
        Node<T> node = new Node<>(t);
        node.next = endMarker;
        node.prev = endMarker.prev;
        endMarker.prev.next = node;
        endMarker.prev = node;
        N++;
    }

    // 删除并返回最大元素
    // 由于getMaxNode()方法，算法复杂度为O(N)
    public T delMax() {

        if (N == 0) {
            throw new UnsupportedOperationException("List Underflow...");
        }

        Node<T> node = getMaxNode(); // 获取最大元素的node
        T val = node.value;
        Node<T> prevNode = node.prev;
        Node<T> nextNode = node.next;

        prevNode.next = nextNode;
        nextNode.prev = prevNode;

        node = null;
        N--;
        return val;
    }

    // 复杂度为O(N)
    private Node<T> getMaxNode() {
        if (N == 1) {
            return beginMarker.next;
        }

        Node<T> maxNode = beginMarker.next;
        T maxValue = maxNode.value;

        for (Node<T> i = maxNode.next; i != endMarker; i = i.next) {
            T val = i.value;
            if (less(maxValue, val)) {
                maxNode = i;
                maxValue = val;
            }
        }

        return maxNode;
    }

    private boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    @Override
    public String toString() {
        if (N == 0) {
            return "[]";
        }

        StringBuilder sb = new StringBuilder();
        sb.append("[");
        Node<T> p = beginMarker.next;
        for (int i = 0; i < N; i++) {
            sb.append(p.value + ", ");
            p = p.next;
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    public static void main(String[] args) {
    }
}
```

有序链表实现：

```java
public class PQSortedLinkedList<T extends Comparable<? super T>> {

    private int N; // 链表包含的元素数量
    private Node<T> beginMarker; // 定义链表起始标记
    private Node<T> endMarker; // 定义链表结束标志

    // 定义结点
    private static class Node<T> {
        T value;
        Node<T> prev;
        Node<T> next;

        public Node(T value) {
            this(value, null, null);
        }

        public Node(T value, Node<T> prev, Node<T> next) {
            this.value = value;
            this.prev = prev;
            this.next = next;
        }
    }

    public PQSortedLinkedList() {
        beginMarker = new Node<>(null);
        endMarker = new Node<>(null, beginMarker, null);
        beginMarker.next = endMarker;
        N = 0;
    }

    // 基于向有序数组中插入元素，复杂度为O(N)
    public void insert(T t) {
        Node<T> newNode = new Node<T>(t);
        if (++N == 1) {
            beginMarker.next = newNode;
            newNode.next = endMarker;
            endMarker.prev = newNode;
            newNode.prev = beginMarker;
            return;
        }
        
        Node<T> node = beginMarker.next;
        while (node != endMarker) {
            if (less(newNode.value, node.value)) {
                // 对链表进行操作，将新元素放入正确的位置。
                node.prev.next = newNode;
                newNode.prev = node.prev;
                node.prev = newNode;
                newNode.next = node;
                return;
            }
            node = node.next;
        }
        // 链表中元素均小于等于新元素，将新元素添加到链表尾
        endMarker.prev.next = newNode;
        newNode.next = endMarker;
        newNode.prev = endMarker.prev;
        endMarker.prev = newNode;
    }

    // 删除并返回最大元素，复杂度O(1)
    public T delMax() {
        if (N == 0) {
            throw new UnsupportedOperationException("List Underflow...");
        }
        Node<T> node = endMarker.prev;
        T value = node.value;
        endMarker.prev = node.prev;
        node.prev.next = endMarker;
        node = null;
        N--;
        return value;
    }

    private boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    @Override
    public String toString() {
        if (N == 0) {
            return "[]";
        }

        StringBuilder sb = new StringBuilder();
        sb.append("[");
        Node<T> p = beginMarker.next;
        for (int i = 0; i < N; i++) {
            sb.append(p.value + ", ");
            p = p.next;
        }
        sb.replace(sb.length() - 2, sb.length(), "]");
        return sb.toString();
    }

    public static void main(String[] args) {
        
    }
}
```

实现 | 插入元素 | 删除最大元素 
-----|-----|-----
无序数组 | O(1) | O(N)
有序数组 | O(N) | O(1) 
无序链表 | O(1) | O(N)
有序链表 | O(N) | O(1)

***
**2.4.4 一个按降序排序的数组也是一个面向最大元素的堆吗？**

*Answer：*

是。
由于是降序排列，满足堆顶元素最大；由于在每一个子堆中，堆顶的角标一定小于堆底两个元素的角标，所以每一个子堆都是一个有序堆；由于每一个子堆都是有序的，所以整个堆也是有序的。

***
**2.4.5 将E A S Y Q U E S T I O N顺序插入一个面向最大元素的堆中，给出结果。**

*Answer：*

```java
public class Ex245 {
    public static void main(String[] args) {
        String[] strings = "E A S Y Q U E S T I O N".split(" ");
        MaxPQ<String> pq = new MaxPQ<>(strings.length);
        for (int i = 0; i < strings.length; i++) {
            pq.insert(strings[i]);
        }
        pq.show();
    }
}
```

结果：

    Y T U S Q N E A S I O E 

                Y
          T            U
       S      Q    N       E
      A S    I O  E  


***
**2.4.6 按照练习2.4.1的规则，用序列P R I O \* R \* \* I \* T \* Y \* \* \* Q U E \* \* \* U \* E操作一个初始为空的面向最大元素的堆，给出每次操作后堆的内容。**

*Answer：*

```java
public class Ex246 {
    public static void main(String[] args) {
        String[] strings = "P R I O * R * * I * T * Y * * * Q U E * * * U * E".split(" ");
        MaxPQ<String> pq = new MaxPQ<>(10);
        for (int i = 0; i < strings.length; i++) {
            if (strings[i].equals("*")) {
                pq.delMax();
            } else {
                pq.insert(strings[i]);
            }
            System.out.print("---- " + strings[i] + "\t");
            pq.show();
        }
    }
}
```

结果：

    ---- P  P 
    ---- R  R P 
    ---- I  R P I 
    ---- O  R P I O 
    ---- *  P O I 
    ---- R  R P I O 
    ---- *  P O I 
    ---- *  O I 
    ---- I  O I I 
    ---- *  I I 
    ---- T  T I I 
    ---- *  I I 
    ---- Y  Y I I 
    ---- *  I I 
    ---- *  I 
    ---- *  
    ---- Q  Q 
    ---- U  U Q 
    ---- E  U Q E 
    ---- *  Q E 
    ---- *  E 
    ---- *  
    ---- U  U 
    ---- *  
    ---- E  E 

***
**2.4.7 在堆中，最大的元素一定在位置1上，第二大的元素一定在位置2或位置3上。对于一个大小为31的堆，给出第k大的元素可能出现的位置和不可能出现的位置，其中$k=2、3、4$（设元素不重复）。**

*Answer：*

***
**2.4.8 回答上一道练习中第k小元素的可能和不可能的位置。**

*Answer：*

***
**2.4.9 给出A B C D E五个元素可能构造出来的所有堆，然后给出A A A B B这五个元素可能构造出来的所有堆。**

*Answer：*

A B C D E：

    E D C A B 
    E D C B A 
    E D B A C 
    E D B C A
    E D A B C 
    E D A C B 
    E C D A B
    E C D B A

A A A B B：

    B B A A A 
    B A B A A 

***
**2.4.10 假设我们不想浪费堆有序的数组pq[]中的那个位置，将最大的元素放在pq[0]，它的子节点放在pq[1]和pq[2]，以此类推。pq[k]的父结点和子节点在哪里。**

*Answer：*

pq[k]的两个子节点分别为pq[2*k+1]和pq[2k+2]；父结点为pq[(k-1)/2]。

***
**2.4.11 如果你的应用中有大量的插入元素操作，但只有若干删除最大元素操作，哪种优先队列的实现方法更有效：堆、无序数组、有序数组。**

*Answer：*

插入元素操作为主要操作，所以应该选择插入元素为廉价操作的实现。
无序数组实现插入元素的时间复杂度为$O(1)$，删除最大元素操作的时间复杂度为$O(N)$；有序数组实现正好相反；而堆实现均为$O(logN)$。
所以应该选择无序数组实现（插入元素时不排序，直接插入数组末端；删除最大元素时遍历数组并与末尾元素交换并删除返回）。

***
**2.4.12 如果你的应用场景中存在大量找出最大元素的操作，但插入元素和删除最大元素操作相对较少，哪种优先队列的实现方法更有效：堆、无序数组、有序数组。**

*Answer：*

选择有序数组进行实现。

***
**2.4.13 想办法在sink()中避免检查j<N。**

*Answer：*

该判断是用来检查一个堆是否包含两个子元素的，即如果j<N判断成功则说明该堆包含两个子元素：j和j+1；如果判断失败，则说明该堆只包含一个子元素j。

当N为偶数时，此时一定会有一个堆只有一个子元素。
当N为奇数时，此时所有的堆都有两个子元素。

所以可以通过提前对N进行判断来避免在循环中重复检查j<N。

***
**2.4.14 对于没有重复元素的大小为N的堆，一次删除最大元素的操作中最少要交换几个元素？构造一个能够达到这个交换次数的大小为15的堆。连续两次删除最大元素呢？三次呢？**

*Answer：*

两次。

                     100
           99                   90
       9       8            89      88
    7    6   5   4        87   86   85   84

***
**2.4.15 设计一个程序，在线性时间内检测数组pq[]是否是一个面向最小元素的堆。**

*Answer：*

使用MinPQ.java中实现的isMinHeap()方法。

```java
// is pq[1..N] a min heap?
private boolean isMinHeap() {
    return isMinHeap(1);
}

// is subtree of pq[1..n] rooted at k a min heap?
private boolean isMinHeap(int k) {
    if (k > n) return true;
    int left = 2*k;
    int right = 2*k + 1;
    if (left  <= n && greater(k, left))  return false;
    if (right <= n && greater(k, right)) return false;
    return isMinHeap(left) && isMinHeap(right);
}

private boolean greater(int i, int j) {
    if (comparator == null) {
        return ((Comparable<Key>) pq[i]).compareTo(pq[j]) > 0;
    }
    else {
        return comparator.compare(pq[i], pq[j]) > 0;
    }
}
```

***
**2.4.16 对于N=32，构造数组使得堆排序使用的比较次数最多以及最少。**

*Answer：*

***
**2.4.17 证明：构造大小为k得面向最小元素得优先队列，然后进行N-k次替换最小元素操作（删除最小元素后再插入元素）后，N个元素中得前k大元素均会留在优先队列中。**

*Answer：*

***
**2.4.18 在MaxPQ中，如果一个用例使用insert()插入了一个比队列中所有元素都大的新元素，随后调用delMax()。假设没有重复元素，此时的堆和进行这些操作之前的堆完全相同吗？进行两次insert()（第一次插入一个比队列所有元素都大的元素，第二次插入一个更大的元素）操作接两次delMax()操作呢？**

*Answer：*

- （1）相同
- （2）相同

在上述两种情况下，insert后的swim路径和delMax后的sink路径相同，所以最终堆相同。

***
**2.4.19 实现MaxPQ的一个构造函数，接受一个数组作为参数。使用正文2.4.5.1节中所述自底向上的方法构造堆。**

*Answer：*

即接受一个数组，使用swim()方法来构造堆。

```java
@SuppressWarnings("unchecked")
public MaxPQ(Key[] arr) {
    pq = (Key[]) new Comparable[arr.length + 1];
    for (int i = 0; i < arr.length; i++) {
        pq[i + 1] = arr[i];
        swim(i + 1);
    }
    N = arr.length;
}
private void exch(int i, int j) {
    Key t = pq[i];
    pq[i] = pq[j];
    pq[j] = t;
}

private void swim(int k) {
    while (k > 1 && less(k / 2, k)) {
        exch(k / 2, k);
        k = k / 2;
    }
}
```

***
**2.4.20 证明：基于下沉的堆构造方法使用的比较次数小于2N，交换次数小于N。**

*Answer：*

官方的证明：

![ex2420](images\chapter02\ex2420.PNG)

***

#### 提高题

***
**2.4.21 基础数据结构。说明如何使用优先队列实现第一章中的栈、队列和随机队列这几种数据结构。**

*Answer：*

这里进行比较的是优先级。优先队列由堆实现。

- 栈：后进先出结构，后进入栈的元素具有更高的优先级。优先级由小到大次序：0、1、2、3、4...，使用MaxPQ
    + push：向优先队列中添加一个优先级更大的元素，swim后上浮到堆顶。
    + pop：返回堆顶元素，再通过sink维护堆有序
- 队列：先进先出结构，先进入队列的元素具有更高的优先级。优先级由大到小次序：0、1、2、3、4...，使用MinPQ
    + enqueue：向优先队列中添加一个优先级更小的元素，放在堆底
    + dequeue：返回堆顶元素，再通过sink维护堆有序
- 随机队列：随机取出元素的结构，无优先级次序，使用无序数组实现。
    + add：向优先队列的尾部添加一个元素
    + take：随机将数组中的一个元素与队列尾部元素交换并返回

***
**2.4.22 调整数组大小。在MaxPQ中加入调整数组大小的代码，并和命题Q一样证明对于一般性长度为N的队列其数组访问的上限。**

*Answer：*

```java
public void insert(Key v) {
    
    if (N == pq.length - 1) {
        resize(pq.length * 2);
    }
    
    pq[++N] = v;
    swim(N);
}

public Key delMax() {
    Key max = pq[1]; // 从根结点得到最大元素
    exch(1, N--); // 将其和最后一个结点交换
    pq[N + 1] = null; // 防止对象游离
    sink(1); // 恢复堆的有序性
    if (N < pq.length / 4) {
        resize(pq.length / 2);
    }
    return max;
}


// 调整pq的长度到size
@SuppressWarnings("unchecked")
private void resize(int size) {
    Key[] tmp = (Key[]) new Comparable[size];
    for (int i = 1; i <= N; i++) {
        tmp[i] = pq[i];
    }
    pq = tmp;
}
```

***
**2.4.23 Multiway的堆。只考虑比较的成本且假设找到t个元素中的最大者需要t次比较，在堆排序中使用t向堆的清苦昂下找出使比较次数$NlgN$的系数最小的t值。首先，假设使用的是一个简单通用的sink()方法；其次，假设Floyd方法在内循环中每轮可以节省一次比较。**

*Answer：*


**2.4.44 使用链接的优先队列。用堆有序的二叉树实现一个优先队列，但是用链表结构代替数组。每个结点都需要三个链接：两个向下，一个向上。你的实现即使在无法预知队列大小的情况下也能保证优先队列的基本操作所需的时间为对数级别。**

*Answer：*

没有估计操作耗时是否为对数级别。

```java
public class TreeSortedHeap<T extends Comparable<? super T>> {

    private int N; // 记录结点个数
    private TreeNode<T> beginMarker; // 开始节点

    private static class TreeNode<T> {
        // 三个指针
        TreeNode<T> prev; // 指向上一个结点
        TreeNode<T> left; // 指向左下结点
        TreeNode<T> right; // 指向右下结点
        T value; // 记录结点的值

        public TreeNode(T value) {
            this(value, null, null, null);
        }

        public TreeNode(T value, TreeNode<T> prev, TreeNode<T> left, TreeNode<T> right) {
            this.prev = prev;
            this.left = left;
            this.right = right;
            this.value = value;
        }
    }

    public TreeSortedHeap() {
        beginMarker = new TreeNode<>(null);
        N = 0;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void insert(T val) {

        // 创建新结点
        TreeNode<T> node = new TreeNode<>(val);

        if (N == 0) {
            // N = 0时直接将新结点放在beginMarker的left上
            beginMarker.left = node;
            node.prev = beginMarker;
        } else {
            // 寻找一个合适的位置添加该新结点
            TreeNode<T> firstNode = beginMarker.left;
            while (true) {
                if (firstNode.left == null) {
                    // 结点左端为null，则添加新结点到左端
                    firstNode.left = node;
                    break;
                } else if (firstNode.right == null) {
                    // 结点有端为null，则添加新结点到右端
                    firstNode.right = node;
                    break;
                } else if (StdRandom.bernoulli()) { // 左右两端都不为null，则随机向左右移动
                    firstNode = firstNode.left;
                } else {
                    firstNode = firstNode.right;
                }
            }
            node.prev = firstNode; // 设置node结点的prev指针
        }
        
        // 上浮该结点
        swim(node);
        N++;
    }

    public T delMax() {
        if (N == 0) {
            throw new UnsupportedOperationException("Tree Underflow...");
        }

        T maxValue = beginMarker.left.value; // 最大值为堆顶结点的值

        TreeNode<T> node = beginMarker.left;
        // 寻找一个合适的叶结点，用于与最大元素结点交换值
        while (!(node.left == null && node.right == null)) {
            // 左右结点至少有一个不为null
            if (node.left == null) {
                // 左节点为null，则向右移动
                node = node.right;
            } else if (node.right == null) {
                // 右节点为null，则向左移动
                node = node.left;
            } else if (less(node.left.value, node.right.value)) {
                // 左右均不为null，比较它们的value，并向小的一端移动
                node = node.left;
            } else {
                node = node.right;
            }
        }

        // 此时node是一个叶结点，其left和right均为null
        exch(node, beginMarker.left);
        
        // 由于exch交换的不是node并操作其三个指针，而是node的value 
        // 所以需要调整指针，使指向该node的指针为null
        if (node.prev.left == node) {
            node.prev.left = null;
        } else {
            node.prev.right = null;
        }
        node = null;
        
        // N = 0 时不需要sink，因为beginMarker.left为null
        if (--N != 0) {
            sink(beginMarker.left);
        }
        return maxValue;
    }

    // 下沉结点
    private void sink(TreeNode<T> node) {
        while (true) {
            // 左右均不为null时，选择沉向较大的一端
            if (node.left != null && node.right != null) {
                if (less(node.left.value, node.right.value)) {
                    exch(node, node.right);
                    node = node.right;
                } else {
                    exch(node, node.left);
                    node = node.left;
                }
            } else if (node.left == null && node.right == null) {
                // 当左右均为null时，此时node已经沉到底部，结束循环
                break;
            } else {
                // 结点的左右一端为null
                TreeNode<T> tmp = node.left == null ? node.right : node.left;
                // 比较此时node的value是否比不为null的结点value小
                if (less(node.value, tmp.value)) {
                    // 如果小，则交换两个node值，并下沉
                    exch(node, tmp);
                    node = tmp;
                } else {
                    // 如果不小，此时node已下沉至合适的位置，结束循环
                    break;
                }
            }
        }
    }

    // 上浮该结点到合适的位置
    private void swim(TreeNode<T> node) {
        while (node.prev != beginMarker && less(node.prev.value, node.value)) {
            exch(node, node.prev);
            node = node.prev;
        }
    }

    // 交换两个node的value，而不是交换两个node
    private void exch(TreeNode<T> v, TreeNode<T> w) {
        T val = v.value;
        v.value = w.value;
        w.value = val;
    }

    private boolean less(T v, T w) {
        return v.compareTo(w) < 0;
    }

    public static void main(String[] args) {
        
    }
}
```

***
**2.4.25 计算数论。编写程序CubeSum.java在不使用额外空间的条件下，按大小顺序打印所有$a^3+b^3$的结果，其中a和b为0至N之间的整数。也就是说，不要全部计算$N^2$个和然后排序，而是创建一个最小有限队列，初始状态为$(0^3,0,0)$,$(1^3,1,0)$,$(2^3,2,0)$,...$(N^3,N,0)$。这样只要优先队列非空，删除并打印最小元素$(i^3+j^3,i,j)$。然后如果j<N，插入元素$(i^3+(j+1)^3,i,j+1)$。用这段程序找出$0$到$10^6$之间所有满足$a^3+b^3=c^3+d^3$的不同整数a，b，c，d。**

*Answer：*

```java
public class CubeSum implements Comparable<CubeSum> {

    private final int sum;
    private final int i;
    private final int j;

    public CubeSum(int i, int j) {
        this.sum = i * i * i + j * j * j;
        this.i = i;
        this.j = j;
    }

    public int compareTo(CubeSum that) {
        if (this.sum < that.sum)
            return -1;
        if (this.sum > that.sum)
            return +1;
        return 0;
    }

    public String toString() {
        return sum + " = " + i + "^3" + " + " + j + "^3";
    }

    public static void main(String[] args) {

        int n = Integer.parseInt(args[0]);

        // initialize priority queue
        MinPQ<CubeSum> pq = new MinPQ<>();
        for (int i = 0; i <= n; i++) {
            pq.insert(new CubeSum(i, i));
        }

        // find smallest sum, print it out, and update
        while (!pq.isEmpty()) {
            CubeSum s = pq.delMin();
            StdOut.println(s);
            if (s.j < n)
                pq.insert(new CubeSum(s.i, s.j + 1));
        }
    }
}
```

***
**2.4.26 无需交换的堆。因为sink()和swim()都用到了初级函数exch()，所以所有元素都被都加载并存储了一次。回避这种低效方式，用插入排序给出新的实现。**

*Answer：*


***
**2.4.27 找出最小元素。在MaxPQ中加入一个min()方法。你的实现所需的时间和空间都应该是常数。**

*Answer：*

添加一个指针用于记录最小元素，并在每次insert()后进行调整。当优先队列没有元素时设置指针为null。

```java
//...

private Key min; // 该优先队列的最小元素

@SuppressWarnings("unchecked")
public MaxPQ(int maxN) {
    pq = (Key[]) new Comparable[maxN + 1];
}

@SuppressWarnings("unchecked")
public MaxPQ(Key[] arr) {
    pq = (Key[]) new Comparable[arr.length + 1];
    min = arr[0];
    for (int i = 0; i < arr.length; i++) {
        min = arr[i].compareTo(min) < 0 ? arr[i] : min;
        pq[i + 1] = arr[i];
        swim(i + 1);
    }
    N = arr.length;
}

public Key min() {
    return min;
}

public void insert(Key v) {

    if (N == pq.length - 1) {
        resize(pq.length * 2);
    }
    
    min = (min == null ? v : (min.compareTo(v) < 0 ? min : v));

    pq[++N] = v;
    swim(N);
}

public Key delMax() {
    
    if (N == 0) {
        throw new UnsupportedOperationException("MaxPQ Underflow...");
    }
    
    Key max = pq[1]; // 从根结点得到最大元素
    exch(1, N--); // 将其和最后一个结点交换
    pq[N + 1] = null; // 防止对象游离
    sink(1); // 恢复堆的有序性
    if (N < pq.length / 4) {
        resize(pq.length / 2);
    }
    if (N == 0) {
        min = null;
    }
    return max;
}
```

***
**2.4.28 选择过滤。编写一个TopM的用例，从标准输入读入坐标$(x, y, z)$，从命令行得到值M，然后打印出距离原点的欧几里得距离最小的M个点。在$N=10^8$且$M=10^4$时，预计程序的运行时间。**

*Answer：*

```java
public class TopM {
    public static void main(String[] args) {
        
        int M = StdIn.readInt();
        int N = StdIn.readInt();
        MaxPQ<Point3D> pq = new MaxPQ<>(M + 1);
        
        Stopwatch timer = new Stopwatch();
        for (int i = 0; i < N; i++) {
            pq.insert(new Point3D(StdRandom.uniform(), StdRandom.uniform(), StdRandom.uniform()));
            if (pq.size() > M) {
                pq.delMax();
            }
        }
        
        double time = timer.elapsedTime();

        for (Point3D point3d : pq) {
            System.out.println(point3d);
        }
        System.out.println("Elapsed Time : " + time);
    }
}

// 定义点(x, y, z)
class Point3D implements Comparable<Point3D> {
    private double x;
    private double y;
    private double z;

    public Point3D(double x, double y, double z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    public double dist() {
        return Math.sqrt(x * x + y * y + z * z);
    }

    @Override
    public int compareTo(Point3D o) {
        return Double.compare(this.dist(), o.dist());
    }

    @Override
    public String toString() {
        return "Point3D [" + dist() + "] [x=" + x + ", y=" + y + ", z=" + z + "]";
    }
}
```

结果：

    ...
    Point3D [0.10933943027847799] [x=0.03411978493896106, y=0.10186315036262727, z=0.02036786408885294]
    Point3D [0.10449391536828184] [x=0.04186358525000122, y=0.01092741117471907, z=0.09511577295080009]
    Point3D [0.10092184305693476] [x=0.015210771922626432, y=0.04584516767562896, z=0.08861191468600527]
    Point3D [0.09801123602100627] [x=0.02662402516294915, y=0.016316435090130033, z=0.09290391604468362]
    Point3D [0.09327818901992099] [x=0.08621096012456397, y=0.006325118864661028, z=0.03504973284612678]
    Point3D [0.06584414268277008] [x=0.04621579430742151, y=0.04000757828513235, z=0.02447335616383861]
    Point3D [0.05949561409982146] [x=0.05718042992409744, y=0.001345608804127374, z=0.016380350049877146]
    Elapsed Time : 0.006

***
**2.4.29 同时面向最大和最小元素的优先队列。设计一个数据类型，支持如下操作：插入元素、删除最大元素、删除最小元素（所需时间均为对数级别），以及找到最大元素、找到最小元素（所需时间均为常数级别）。**

*Answer：*


***
**2.4.30 动态中位数查找。设计一个数据类型，支持在对数时间内插入元素，常数时间内找到中位数并在对数时间内删除中位数。**

*Answer：*

- 使用两个堆，面向最大元素的堆maxHeap和面向最小元素的堆minHeap以及一个用来保存中位数的引用v。
- 第一次插入元素时，若N_tot = N_max + N_min = 0，则将中位数直接赋值给引用v。
- 第一次插入元素时，若总元素个数不等于0，则先比较该元素与中位数的大小：
    + 若该元素小于等于v，则将该元素添加到maxHeap
    + 若该元素大于v，则将该元素添加到minHeap
- 添加元素后，若|N_max - N_min| > 1，此时需要对堆进行操作并重新赋值中位数：
    + 如果N_max > N_min，则将当前中位数v添加到minHeap，然后令新的中位数等于delMax
    + 如果N_min > N_max，则将当前中位数v添加到maxHeap，然后令新的中位数等于delMin
- 删除中位数时，记录当前v的值，并重新获取中位数：
    + 如果N_max > N_min，则令新的中位数为delMax
    + 如果N_min > N_max，则令新的中位数为delMin
    + 如果N_max = N_min，则令新的中位数为delMax

***
**2.4.31 快速插入。用基于比较的方式实现MinPQ的API，使得插入元素需要~$loglogN$次比较，删除最小元素需要~$2logN$次比较。**

*Answer：*

在swim()中使用二分查找来寻找祖先结点。

***
**2.4.32 下界。请证明，不存在一个基于比较的对MinPQ的API的实现能够使得插入元素和删除最小元素的操作都保证只使用~$NloglogN$次比较。**

*Answer：*

    This would yield an nloglogn compare-based sorting algorithm (insert the n items, then repeatedly remove the minimum), violating the proposition of Section 2.3.

***
**2.4.33 索引优先队列的实现。按照2.4.4.6节的描述修改算法2.6来实现索引优先队列API中的基本操作：使用pq[]保存索引，添加一个数组keys[]来保存元素，在天极一个数组qp[]来保存pq[]的逆序---qp[i]的值是i在pq[]中的位置（即索引j，pq[j]=i）。修改算法2.6的代码来维护这些数据结构。若i不再队列之中，则总是令qp[i]=-1并添加一个方法contains()来检测这种情况。你需要修改辅助函数exch()和less()，但不需要修改sink()和swim()。**

*Answer：*

使用官方实现的IndexMinPQ.java：

```java
public class IndexMinPQ<Key extends Comparable<Key>> implements Iterable<Integer> {
    private int maxN;        // maximum number of elements on PQ
    private int n;           // number of elements on PQ
    private int[] pq;        // binary heap using 1-based indexing
    private int[] qp;        // inverse of pq - qp[pq[i]] = pq[qp[i]] = i
    private Key[] keys;      // keys[i] = priority of i

    /**
     * Initializes an empty indexed priority queue with indices between {@code 0}
     * and {@code maxN - 1}.
     * @param  maxN the keys on this priority queue are index from {@code 0}
     *         {@code maxN - 1}
     * @throws IllegalArgumentException if {@code maxN < 0}
     */
    @SuppressWarnings("unchecked")
    public IndexMinPQ(int maxN) {
        if (maxN < 0) throw new IllegalArgumentException();
        this.maxN = maxN;
        n = 0;
        keys = (Key[]) new Comparable[maxN + 1];    // make this of length maxN??
        pq   = new int[maxN + 1];
        qp   = new int[maxN + 1];                   // make this of length maxN??
        for (int i = 0; i <= maxN; i++)
            qp[i] = -1;
    }

    /**
     * Returns true if this priority queue is empty.
     *
     * @return {@code true} if this priority queue is empty;
     *         {@code false} otherwise
     */
    public boolean isEmpty() {
        return n == 0;
    }

    /**
     * Is {@code i} an index on this priority queue?
     *
     * @param  i an index
     * @return {@code true} if {@code i} is an index on this priority queue;
     *         {@code false} otherwise
     * @throws IllegalArgumentException unless {@code 0 <= i < maxN}
     */
    public boolean contains(int i) {
        if (i < 0 || i >= maxN) throw new IllegalArgumentException();
        return qp[i] != -1;
    }

    /**
     * Returns the number of keys on this priority queue.
     *
     * @return the number of keys on this priority queue
     */
    public int size() {
        return n;
    }

    /**
     * Associates key with index {@code i}.
     *
     * @param  i an index
     * @param  key the key to associate with index {@code i}
     * @throws IllegalArgumentException unless {@code 0 <= i < maxN}
     * @throws IllegalArgumentException if there already is an item associated
     *         with index {@code i}
     */
    public void insert(int i, Key key) {
        if (i < 0 || i >= maxN) throw new IllegalArgumentException();
        if (contains(i)) throw new IllegalArgumentException("index is already in the priority queue");
        n++;
        qp[i] = n;
        pq[n] = i;
        keys[i] = key;
        swim(n);
    }

    /**
     * Returns an index associated with a minimum key.
     *
     * @return an index associated with a minimum key
     * @throws NoSuchElementException if this priority queue is empty
     */
    public int minIndex() {
        if (n == 0) throw new NoSuchElementException("Priority queue underflow");
        return pq[1];
    }

    /**
     * Returns a minimum key.
     *
     * @return a minimum key
     * @throws NoSuchElementException if this priority queue is empty
     */
    public Key minKey() {
        if (n == 0) throw new NoSuchElementException("Priority queue underflow");
        return keys[pq[1]];
    }

    /**
     * Removes a minimum key and returns its associated index.
     * @return an index associated with a minimum key
     * @throws NoSuchElementException if this priority queue is empty
     */
    public int delMin() {
        if (n == 0) throw new NoSuchElementException("Priority queue underflow");
        int min = pq[1];
        exch(1, n--);
        sink(1);
        assert min == pq[n+1];
        qp[min] = -1;        // delete
        keys[min] = null;    // to help with garbage collection
        pq[n+1] = -1;        // not needed
        return min;
    }



   /***************************************************************************
    * General helper functions.
    ***************************************************************************/
    private boolean greater(int i, int j) {
        return keys[pq[i]].compareTo(keys[pq[j]]) > 0;
    }

    private void exch(int i, int j) {
        int swap = pq[i];
        pq[i] = pq[j];
        pq[j] = swap;
        qp[pq[i]] = i;
        qp[pq[j]] = j;
    }


   /***************************************************************************
    * Heap helper functions.
    ***************************************************************************/
    private void swim(int k) {
        while (k > 1 && greater(k/2, k)) {
            exch(k, k/2);
            k = k/2;
        }
    }

    private void sink(int k) {
        while (2*k <= n) {
            int j = 2*k;
            if (j < n && greater(j, j+1)) j++;
            if (!greater(k, j)) break;
            exch(k, j);
            k = j;
        }
    }


   /***************************************************************************
    * Iterators.
    ***************************************************************************/

    /**
     * Returns an iterator that iterates over the keys on the
     * priority queue in ascending order.
     * The iterator doesn't implement {@code remove()} since it's optional.
     *
     * @return an iterator that iterates over the keys in ascending order
     */
    public Iterator<Integer> iterator() { return new HeapIterator(); }

    private class HeapIterator implements Iterator<Integer> {
        // create a new pq
        private IndexMinPQ<Key> copy;

        // add all elements to copy of heap
        // takes linear time since already in heap order so no keys move
        public HeapIterator() {
            copy = new IndexMinPQ<Key>(pq.length - 1);
            for (int i = 1; i <= n; i++)
                copy.insert(pq[i], keys[pq[i]]);
        }

        public boolean hasNext()  { return !copy.isEmpty();                     }
        public void remove()      { throw new UnsupportedOperationException();  }

        public Integer next() {
            if (!hasNext()) throw new NoSuchElementException();
            return copy.delMin();
        }
    }


    /**
     * Unit tests the {@code IndexMinPQ} data type.
     *
     * @param args the command-line arguments
     */
    public static void main(String[] args) {

    }
}
```

***
**2.4.34 索引优先队列的实现（附加操作）。向练习2.4.33的实现中添加minIndex()、change()和delte()方法。**

*Answer：*

```java
public int minIndex() {
    if (n == 0) throw new NoSuchElementException("Priority queue underflow");
    return pq[1];
}

public void changeKey(int i, Key key) {
    if (i < 0 || i >= maxN) throw new IllegalArgumentException();
    if (!contains(i)) throw new NoSuchElementException("index is not in the priority queue");
    keys[i] = key;
    swim(qp[i]);
    sink(qp[i]);
}

public void delete(int i) {
    if (i < 0 || i >= maxN) throw new IllegalArgumentException();
    if (!contains(i)) throw new NoSuchElementException("index is not in the priority queue");
    int index = qp[i];
    exch(index, n--);
    swim(index);
    sink(index);
    keys[i] = null;
    qp[i] = -1;
}
```

***
**2.4.35 离散概率分布取样。编写一个Sample类，其构造函数接受一个double类型的数组p[]作为参数并支持以下操作：random()---返回任意索引i及其概率p[i]/T（T是p[]中所有元素之和）；change(i, v)---将p[i]的值修改为v。**

*Answer：*

***

#### 实验题

***
**2.4.36 性能测试I。编写一个测试用例，用插入元素操作填满一个优先队列，然后用删除最大元素操作删去一般元素，再用插入元素操作填满优先队列，再用删除最大元素操作删除所有元素。用一列随机的长短不同的元素多次重复以上过程，测量每次运行的用时，打印平均用时或是将其绘成图表。**

*Answer：*

```java
public class Ex2436 {
    
    public static void main(String[] args) {
        
        int T = StdIn.readInt(); // 重复实验次数
        int N = StdIn.readInt();

        double time = 0d;

        for (int i = 0; i < T; i++) {
            
            int size = N + StdRandom.uniform(-N / 2, N / 2); // 队列的大小随机
            MaxPQ<Double> pq = new MaxPQ<>(size); // 创建队列
            
            Stopwatch timer = new Stopwatch();

            // 填满队列
            while(pq.size() != size) {
                pq.insert(StdRandom.uniform());
            }
            
            // 删除一半
            while (pq.size() != size / 2) {
                pq.delMax();
            }
            
            // 再次填满
            while (pq.size() != size) {
                pq.insert(StdRandom.uniform());
            }
            
            // 全部删除
            while (pq.size() != 0) {
                pq.delMax();
            }

            time += timer.elapsedTime();
        }

        System.out.println("For " + T + " experiments, N = " + N + ", Average time = " + time / T);
    }
}
```

结果：

    For 1000 experiments, N = 10000, average time = 0.0019889999999999695

***
**2.4.37 性能测试II。编写一个性能测试用例，用插入元素操作填满一个优先队列，然后再一秒种之内尽可能多地连续反复调用删除最大元素和插入元素的操作。用一列随机的长度不同的元素多次重复以上过程，将程序能够完成的删除最大元素操作的平均次数打印出来或是绘成图表。**

*Answer：*

```java
public class Ex2437 {
    
    public static void main(String[] args) {

        int T = StdIn.readInt();
        int N = StdIn.readInt();

        int totTimes = 0;

        for (int i = 0; i < T; i++) {

            int size = N + StdRandom.uniform(-N / 2, N / 2);
            MaxPQ<Double> pq = new MaxPQ<>(size);
            while (pq.size() != size) {
                pq.insert(StdRandom.uniform());
            }
            
            int times = 0;

            Stopwatch timer = new Stopwatch();

            while (timer.elapsedTime() < 1) {
                pq.delMax();
                pq.insert(StdRandom.uniform());
                times++;
            }
            
            totTimes += times;
        }
        
        System.out.println("For " + T + " experiments, N = " + N + ", Average times = " + totTimes / T);
    }
}
```

结果：

    For 100 experiments, N = 1000, Average times = 13947683

***
**2.4.38 练习测试。编写一个练习用例，用算法2.6中实现的优先队列接口方法处理实际应用中可能出现的高难度极端情况。例如，元素已经有序、元素全部逆序、元素全部相同或是所有元素只有两个值。**

*Answer：*

略。

***
**2.4.39 构造函数的代价。对于$N=10^3$、$N=10^6$、$N=10^9$，根据经验判断堆排序时构造堆占总耗时的比例。**

*Answer：*

- 堆的构造耗时：
    + 比较次数：2N
    + 交换次数：N
- 总耗时：
    + 比较次数：2NlgN+2N
    + 交换次数：NlgN+N

所以比例为：$\frac{1}{lgN+1}$

***
**2.4.40 Floyd方法。根据正文中Floyd的先沉后浮的思想实现堆排序。对于$N=10^3$、$N=10^6$、$N=10^9$大小的随机不重复数组，记录你的程序所使用的比较次数和标准实现所使用的比较次数。**

*Answer：*

***
**2.4.41 Multiway堆。根据正文中描述实现基于完全对有序的三叉树和四叉树的堆排序。对于$N=10^3$、$N=10^6$、$N=10^9$大小的随机不重复数组，记录你的程序所使用的比较次数和标准实现所使用的比较次数。**

*Answer：*

***
**2.4.42 堆的前序表示。用前序法而非级别表示一颗堆有序的树，并基于此实现堆排序。于$N=10^3$、$N=10^6$、$N=10^9$大小的随机不重复数组，记录你的程序所使用的比较次数和标准实现所使用的比较次数。**

*Answer：*


### 2.5 应用

***

#### 练习题

***
**2.5.1 在下面这段String类型的compareTo()方法的实现中，第三行对提高运行效率有何帮助？**

```java
public int compareTo(String that) {
    if (this == that) return 0; // 这一行
    int n = Math.min(this.length(), that.length());
    for (int i = 0; i < n; i++) 
    {
        if (this.charAt(i) < that.charAt(i)) return -1;
        else if (this.charAt(i) > that.charAt(i)) return +1;
    }
    return this.length() - that.length;
}
```

*Answer：*

第三行代码如下：

```java
if (this == that) {
    return 0;
}
```

如果两个字符串的地址是相同的，那么它们一定是相同的，此时则不需要再比较字符串中的每一个字符。

***
**2.5.2 编写一段程序，从标准输入读入一列单词并打印其中所有由两个单词所组成的组合词。例如，如果输入的单词为after、thought和afterthought，那么afterthought就是一个组合词。**

*Answer：*

```java
public class Ex252 {

    // 字符串二分查找
    public static int indexOf(String[] arr, String key) {
        int lo = 0;
        int hi = arr.length - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (key.compareTo(arr[mid]) < 0) {
                hi = mid - 1;
            } else if (key.compareTo(arr[mid]) > 0) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return -1;
    }

    public static void main(String[] args) {

        String[] arr = new In(new File(args[0])).readAllStrings();
        Arrays.sort(arr);

        int N = arr.length;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                if (indexOf(arr, arr[i] + arr[j]) != -1) {
                    System.out.println("Found : " + arr[i] + " " + arr[j] + " " + arr[i] + arr[j]);
                } else if (indexOf(arr, arr[j] + arr[i]) != -1) {
                    System.out.println("Found : " + arr[j] + " " + arr[i] + " " + arr[j] + arr[i]);
                }
            }
        }
    }
}
```

输入字符串：

    after
    documentsqueue
    all
    target
    problems
    documents
    queue
    apple
    tree
    summary
    appletree
    afterall
    problemstree
    bird
    singing
    singingbird

结果：

    Found : after all afterall
    Found : apple tree appletree
    Found : singing bird singingbird
    Found : documents queue documentsqueue
    Found : problems tree problemstree

***
**2.5.3 找出下面这段账户余额Balance类实现代码的错误。为什么compareTo()方法对Comparable接口的实现有缺陷？**

```java
public class Balance implements Comparable<Balance> {
    ...
    private double amount;
    public int compareTo(Balance that) {
        if (this.amount < that.amount - 0.005)
            return -1;
        if (this.amount > that.amount + 0.005)
            return +1;
        return 0;
    }
    ...
}
```

*Answer：*

首先，用于商业计算用途的类，底层应该使用java.math.BigDecimal，而不是double类型。
其次，在使用了BigDecimal之后，compareTo中应该使用BigDecimal的compareTo方法。

即使使用double类型，这个compareTo实现也违反了传递性：即可能会出现a.compareTo(b)和b.compareTo(c)均为0，但是a.compareTo(c)却不为0的情况。

***
**2.5.4 实现一个方法String[] dedup(String[] a)，返回一个有序的a[]，并删去其中的重复元素。**

*Answer：*

```java
public static String[] dedup(String[] a) {

    // 创建一个数组a的拷贝
    String[] arr = new String[a.length];
    System.arraycopy(a, 0, arr, 0, a.length);

    // 先对数组进行排序
    Arrays.sort(arr);

    List<String> list = new ArrayList<>();
    list.add(arr[0]);
    String tmp = arr[0];
    for (int i = 1; i < arr.length; i++) {
        if (arr[i].equals(tmp)) {
            continue;
        }
        list.add(arr[i]);
        tmp = arr[i];
    }

    return list.toArray(new String[0]);
}
```

***
**2.5.5 说明为何选择排序是不稳定的。**

*Answer：*

举例：

序列5 8 5 2 9，第一遍选择第1个元素5会和2交换，那么原序列中2个5的相对前后顺序就被破坏了，所以选择排序不是一个稳定的排序算法。

***
**2.5.6 用递归实现select()。**

原select()方法：

```java
public static <T> Comparable<T> select(Comparable<T>[] a, int k) {
    StdRandom.shuffle(a);
    int lo = 0, hi = a.length - 1;
    while (hi > lo) {
        int j = partition(a, lo, hi);
        if (j == k)
            return a[k];
        else if (j > k)
            hi = j - 1;
        else if (j < k)
            lo = j + 1;
    }
    return a[k];
}
```

*Answer：*

```java
public static <T> Comparable<T> select_2(Comparable<T>[] a, int k) {
    StdRandom.shuffle(a);
    return select_2(a, k, 0, a.length - 1);
}

private static <T> Comparable<T> select_2(Comparable<T>[] a, int k, int lo, int hi) {
    if (hi <= lo) {
        return a[k];
    } else {
        int j = partition(a, lo, hi);
        if (j > k) {
            return select_2(a, k, lo, j - 1);
        } else if (j < k) {
            return select_2(a, k, j + 1, hi);
        } else {
            return a[k];
        }
    }
}
```

***
**2.5.7 用select()找出N个元素中的最小值平均大约需要多少次比较？**

*Answer：*

参见p221。比较次数为~$2N+2kln(N/k)+2(N-k)ln(N/(N-k))$。带入$k=0$，得~$2N$。

***
**2.5.8 编写一段程序Frequency，从标准输入读取一列字符串并按照字符串出现频率由高到低得顺序打印出每个字符串及其出现次数。**

*Answer：*

```java
public class Ex258 {

    public static List<Map.Entry<String, Integer>> frequency(String[] arr) {

        Map<String, Integer> map = new HashMap<>();

        // 将数组中得数据存入map
        for (String s : arr) {
            if (map.containsKey(s)) {
                map.put(s, map.get(s) + 1);
            } else {
                map.put(s, 1);
            }
        }

        List<Map.Entry<String, Integer>> list = new ArrayList<>(map.entrySet());

        // 按值对Entry排序
        Collections.sort(list, new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Entry<String, Integer> o1, Entry<String, Integer> o2) {
                return o2.getValue().compareTo(o1.getValue());
            }
        });

        return list;
    }

    public static void main(String[] args) {
        String[] arr = { "d", "d", "n", "a", "c", "d", "b", "a", "c", "c", "d", "b", "n" };
        List<Map.Entry<String, Integer>> list = frequency(arr);
        for (Map.Entry<String, Integer> m : list) {
            System.out.println(m.getKey() + " " + m.getValue());
        }
    }
}
```

结果：

    d 4
    c 3
    a 2
    b 2
    n 2

***
**2.5.9 为将右侧所示得数据排序编写一个新的数据类型。**

输入：

    1-Oct-28    3500000
    2-Oct-28    3850000
    3-Oct-28    4060000
    4-Oct-28    4330000
    5-Oct-28    4360000
    ...
    30-Dec-99   554680000
    31-Dec-99   374049984
    3-Jan-00    931800000
    4-Jan-00    1009000000
    5-Jan-00    1085500000

输出：

    19-Aug-40   130000
    26-Aug-40   160000
    24-Jul-40   200000
    10-Aug-42   210000
    23-Jun-42   210000
    ...
    23-Jul-02   2441019904
    17-Jul-02   2566500096
    15-Jul-02   2574799872
    19-Jul-02   2654099968
    24-Jul-02   2775559936

*Answer：*

排序方法中先按照交易量排序，交易量相同再按照开头的两位数字排序。

```java
class DJIATranscation implements Comparable<DJIATranscation> {
    private Integer data_mem1;
    private String data_mem2;
    private String data_mem3;
    private Integer trans_amount;

    public DJIATranscation(String trans) {
        String[] s = trans.split("-");
        this.data_mem1 = Integer.parseInt(s[0]);
        this.data_mem2 = s[1];
        this.data_mem3 = s[2];
        this.trans_amount = Integer.parseInt(s[3]);
    }

    @Override
    public String toString() {
        return data_mem1 + "-" + data_mem2 + "-" + data_mem3 + "\t" + trans_amount;
    }

    @Override
    public int compareTo(DJIATranscation o) {
        return this.trans_amount != o.trans_amount ? this.trans_amount.compareTo(o.trans_amount)
                : this.data_mem1.compareTo(o.data_mem1);
    }
}

public class Ex259 {
    public static void main(String[] args) {
        String[] arr = { "1-Oct-28-3500000", "2-Oct-28-3850000", "3-Oct-28-4060000", "4-Oct-28-4330000",
                "5-Oct-28-4360000", "30-Dec-99-554680000", "31-Dec-99-374049984", "3-Jan-00-931800000",
                "4-Jan-00-1009000000", "5-Jan-00-1085500000" };

        Set<DJIATranscation> set = new TreeSet<>();

        for (String s : arr) {
            set.add(new DJIATranscation(s));
        }
        
        for (DJIATranscation s : set) {
            System.out.println(s);
        }
    }
}
```

***
**2.5.10 创建一个数据类型Version来表示软件的版本，例如115.1.1、115.10.1、115.10.2.为它实现Comparable接口，其中115.1.1的版本低于115.10.1。**

*Answer：*

```java
public class Version implements Comparable<Version> {
    private int main_ver;
    private int sec_ver;
    private int thr_ver;

    public Version(String version) {
        String[] strings = version.split("\\.");
        this.main_ver = Integer.parseInt(strings[0]);
        this.sec_ver = Integer.parseInt(strings[1]);
        this.thr_ver = Integer.parseInt(strings[2]);
    }

    @Override
    public String toString() {
        return main_ver + "." + sec_ver + "." + thr_ver;
    }

    @Override
    public int compareTo(Version o) {

        return this.main_ver != o.main_ver ? this.main_ver - o.main_ver
                : (this.sec_ver != o.sec_ver ? this.sec_ver - o.sec_ver
                        : (this.thr_ver != o.thr_ver ? this.thr_ver - o.thr_ver : 0));
    }
}
```

***
**2.5.11 描述排序结果的一种方法是创建一个保存0到a.length-1的排列p[]，使得p[i]的值为a[i]元素的最终位置。用这种方法描述插入排序、选择排序、希尔排序、归并排序和堆排序对一个含有7个相同元素的数组的排序结果。**

*Answer：*

不使用数组记录位置，定义一个新的类：

```java
public class Element implements Comparable<Element> {
    private String main_key;
    private int vice_key;

    public Element(String m, int v) {
        this.main_key = m;
        this.vice_key = v;
    }

    // 按照主键进行排序
    @Override
    public int compareTo(Element o) {
        return this.main_key.compareTo(o.main_key);
    }

    @Override
    public String toString() {
        return "[" + main_key + ", " + vice_key + "]";
    }

    public static void main(String[] args) {
        Element[] elements = { new Element("A", 0), new Element("A", 1), new Element("A", 2), new Element("A", 3),
                new Element("A", 4), new Element("A", 5), new Element("A", 6) };
        
        Quick.sort(elements);
        
        for (int i = 0; i < elements.length; i++) {
            System.out.println(elements[i]);
        }
    }
}
```

以QuickSort为例，结果：

    [A, 5]
    [A, 4]
    [A, 2]
    [A, 0]
    [A, 6]
    [A, 3]
    [A, 1]

***

#### 提高题

***
**2.5.12 调度。编写一段程序SPT.java，从标准输入中读取任务的名称和所需的运行时间，用2.5.4.3节所述的最短处理时间优先的原则打印出一份调度计划，使得任务完成得平均时间最小。**

*Answer：*

```java
class Task implements Comparable<Task> {
    private String taskName;
    private double costTime;
    public Task(String name, double time) {
        taskName = name;
        costTime = time;
    }
    
    @Override
    public int compareTo(Task o) {
        return Double.compare(this.costTime, o.costTime);
    }
    
    @Override
    public String toString() {
        return "[" + taskName + ", " + costTime + "]";
    }
}

public class SPT {
    public static void main(String[] args) {
        int n = StdIn.readInt();
        Task[] tasks = new Task[n];
        for (int i = 0; i < tasks.length; i++) {
            String name = StdIn.readString();
            double time = StdIn.readDouble();
            tasks[i] = new Task(name, time);
        }
        
        Arrays.sort(tasks);
        
        for (Task task : tasks) {
            System.out.println(task);
        }
    }
}
```

***
**2.5.13 负载均衡。编写一段程序LPT.java，接受一个整数M作为命令行参数，从标准输入中读取任务的名称和所需的运行时间，用2.5.4.3节所述的最长处理时间优先原则打印出一份调度计划，将所有任务分配给M个处理器并使得所有任务完成所需总时间最少。**

*Answer：*

```java
public class LPT {

    public static void main(String[] args) {
        int m = Integer.parseInt(args[0]);   // number of machines

        int n = StdIn.readInt();
        Job[] jobs = new Job[n];
        for (int i = 0; i < n; i++) {
            String name = StdIn.readString();
            double time = StdIn.readDouble();
            jobs[i] = new Job(name, time);
        }

        // sort jobs in ascending order of processing time 
        Arrays.sort(jobs);

        // generate m empty processors and put on priority queue
        MinPQ<Processor> pq = new MinPQ<Processor>(m);
        for (int i = 0; i < m; i++)
            pq.insert(new Processor());
        
        // assign each job (in decreasing order of time) to processor that is least busy
        for (int j = n-1; j >= 0; j--) {
            Processor min = pq.delMin();
            min.add(jobs[j]);
            pq.insert(min);
        }
        
        // print out contents of each processor
        while (!pq.isEmpty())
            StdOut.println(pq.delMin());
    }
}
```

***
**2.5.14 逆域名排序。为域名编写一个数据类型Domain并为它实现一个从ompareTo()方法，使之能够按照你想域名排序。例如域名cs.princeton.edu的逆是edu.princeton.cs。这在网络日志处理时很有用。**

*Answer：*

```java
public class Domain implements Comparable<Domain> {

    private String domain;

    public Domain(String domain) {
        this.domain = domain;
    }

    @Override
    public int compareTo(Domain o) {

        return this.reverse().compareTo(o.reverse());
    }

    private String reverse() {

        String[] fields = domain.split("\\.");
        StringBuilder sb = new StringBuilder();

        for (int i = fields.length - 1; i >= 0; i--) {
            if (i != 0) {
                sb.append(fields[i] + ".");
            } else {
                sb.append(fields[i]);
            }
        }
        return sb.toString();
    }
}
```

***
**2.5.15 垃圾邮件大战。在非法垃圾邮件之战的伊始，你有一大串来自各个域名的电子邮件地址。为了更好地伪造回信地址，你应该总是从相同的域中向目标用户发送邮件。例如从 wayne@cs.princeton.edu 向 rs@cs.princeton.edu 发送垃圾邮件就很不错。你会如何处理这份电子邮件列表来高效地完成这个任务呢？**

*Answer：*

将这一大串电子邮件地址存入Address（存放完整的邮件地址：user+@+domain，并实现Comparable）数组，然后进行排序。

当收到一个电子邮件后，分析其完整地址得到域名（split("@")，取arr[1]）。对Address数组进行二分查找，查找与该域名匹配的Address对象。

使用这个查找到的Address发送垃圾回复。

***
**2.5.16 公正的选举。为了避免对名字排在字母表靠后的候选人的偏见，加州在2003年的州长选举中将所有候选人按照以下字母顺序排序： 
R W Q O J M V A H B S G Z X N T C I E K U P D Y F L 
创建一个遵守这种顺序的数据类型并编写一个用例California，在它的静态方法main()中将字符串按照这种方式排序。假设所有字符串全部都是大写的。**

*Answer：*

```java
public class California {

    public static void main(String[] args) {

        String[] a = { "WILL", "TAVISH", "TACO", "HARRY", "AZUNA", "IMOTHPEN", "RUTH" };

        Arrays.sort(a, new Comparator<String>() {

            private final String table = "RWQOJMVAHBSGZXNTCIEKUPDYFL";

            // 取首字母进行比较，如果相同，则比较全名；如果不相同则查表比较。
            @Override
            public int compare(String o1, String o2) {

                String name1 = o1.substring(0, 1);
                String name2 = o2.substring(0, 1);

                return name1.equals(name2) ? o1.compareTo(o2) : table.indexOf(name1) - table.indexOf(name2);
            }

        });

        for (String string : a) {
            System.out.println(string);
        }
    }
}
```

结果：

    RUTH
    WILL
    AZUNA
    HARRY
    TACO
    TAVISH
    IMOTHPEN

***
**2.5.17 检测稳定性。扩展2.1.16中的check()方法，对指定数组调用sort()，如果排序结果是稳定的则返回true，否则返回false。不要假设sort()只会使用exch()移动数据。**

*Answer：*

```java
public class SortStabilityCheck {

    // 检测排序是否为稳定排序
    @SuppressWarnings("unchecked")
    public static <T extends Comparable<? super T>> boolean sort(T[] arr, String method) {

        int N = arr.length;
        T[] backup = (T[]) Array.newInstance(arr.getClass().getComponentType(), N);
        System.arraycopy(arr, 0, backup, 0, N);

        // backup使用Arrays.sort()排序，该排序是稳定排序
        Arrays.sort(backup);

        // 对arr数组进行指定方法的排序
        chooseMethod(arr, method);

        // 对比backup数组和arr数组
        for (int i = 0; i < N; i++) {
            if (backup[i] != arr[i]) {
                return false;
            }
        }
        return true;
    }

    private static <T extends Comparable<? super T>> void chooseMethod(T[] arr, String method) {
        if (method.equals("Insertion")) {
            Insertion.sort(arr);
        }
        if (method.equals("Selection")) {
            Selection.sort(arr);
        }
        if (method.equals("Shell")) {
            Shell.sort(arr);
        }
        if (method.equals("Merge")) {
            Merge.sort(arr);
        }
        if (method.equals("Quick")) {
            Quick.sort(arr);
        }
        if (method.equals("Heap")) {
            Heap.sort(arr);
        }
    }

    public static void main(String[] args) {
        Integer[] integers = new Integer[] { new Integer(5), new Integer(8), new Integer(5), new Integer(2),
                new Integer(9) };
        System.out.println(sort(integers, "Merge"));
    }
}
```

***
**2.5.18 强制稳定。编写一段能够将任意排序方法变得稳定的封装代码，创建一种新的数据类型作为键，将键的原始索引保存在其中，并在调用sort()之后再回复原始的键。**

*Answer：*

***
**2.5.19 Kendall tau距离。编写一段程序KendallTau.java，在线性对数时间内计算两组排列之间的Kendall tau距离。**

*Answer：*

```java
public class KendallTau {

    // return Kendall tau distance between two permutations
    public static long distance(int[] a, int[] b) {
        if (a.length != b.length) {
            throw new IllegalArgumentException("Array dimensions disagree");
        }
        int n = a.length;

        int[] ainv = new int[n];
        for (int i = 0; i < n; i++)
            ainv[a[i]] = i;

        Integer[] bnew = new Integer[n];
        for (int i = 0; i < n; i++)
            bnew[i] = ainv[b[i]];

        return Inversions.count(bnew);
    }

    // return a random permutation of size n
    public static int[] permutation(int n) {
        int[] a = new int[n];
        for (int i = 0; i < n; i++)
            a[i] = i;
        StdRandom.shuffle(a);
        return a;
    }

    public static void main(String[] args) {

        // two random permutation of size n
        int n = Integer.parseInt(args[0]);
        int[] a = KendallTau.permutation(n);
        int[] b = KendallTau.permutation(n);

        // print initial permutation
        for (int i = 0; i < n; i++)
            StdOut.println(a[i] + " " + b[i]);
        StdOut.println();

        StdOut.println("inversions = " + KendallTau.distance(a, b));
    }
}
```

***
**2.5.20 空闲时间。假设有一台计算机能够并行处理N个任务。编写一段程序并给定一系列任务的起始时间和结束时间，找出这台及其最长的空闲时间和最长的繁忙时间。**

*Answer：*

***
**2.5.21 多维排序。编写一个Vector数据类型并将d维整型向量排序。排序方法是线按照一维数字排序，一维数字相同的向量则按照二维数字排序，再相同的向量则按照三维数字排序，如此这般。**

*Answer：*

```java
public class Vector implements Comparable<Vector> {
    private int d; // 保存维数
    private int[] arr; // 保存每一维度的值

    public Vector(int d, int[] a) {
        if (d != a.length) {
            throw new IllegalArgumentException();
        }
        this.d = d;
        this.arr = new int[d];
        for (int i = 0; i < d; i++) {
            arr[i] = a[i];
        }
    }

    @Override
    public int compareTo(Vector o) {

        if (this.d != o.d) {
            throw new IllegalArgumentException();
        }

        for (int i = 0; i < d; i++) {
            if (this.arr[i] > o.arr[i]) {
                return 1;
            } else if (this.arr[i] < o.arr[i]) {
                return -1;
            }
        }
        return 0;
    }

    public static void main(String[] args) {
        Vector vector1 = new Vector(4, new int[] { 0, 1, 2, 4 });
        Vector vector2 = new Vector(4, new int[] { 0, 1, 2, 3 });
        System.out.println(vector1.compareTo(vector2));
    }
}
```

***
**2.5.22 股票交易。投资者对一只股票的买卖交易都发布在电子交易市场中。他们会指定最高买入价和最低卖出价，以及在该价位买卖的笔数。编写一段程序，用优先队列来匹配买家和卖家并用模拟数据进行测试。可以使用两个优先队列，一个用于买家一个用于卖家，当一方的报价和另一方的一份或多份报价匹配时就进行交易。**

*Answer：*

```java
class Deal implements Comparable<Deal> {

    protected double price;
    protected double amount;

    public Deal(double price, double amount) {
        this.price = price;
        this.amount = amount;
    }

    public double getPrice() {
        return price;
    }

    @Override
    public int compareTo(Deal o) {
        return Double.compare(price, o.price);
    }
}

class BuyDeal extends Deal {

    public BuyDeal(double price, double amount) {
        super(price, amount);
    }

    @Override
    public String toString() {
        return "BuyDeal [price=" + price + ", amount=" + amount + "]";
    }
}

class SellDeal extends Deal {

    public SellDeal(double price, double amount) {
        super(price, amount);
    }

    @Override
    public String toString() {
        return "SellDeal [price=" + price + ", amount=" + amount + "]";
    }
}

public class Ex2522 {

    public static boolean makeDeal(Deal a, Deal b) {
        
        if (!(a instanceof BuyDeal) || !(b instanceof SellDeal)) {
            throw new IllegalArgumentException();
        }
        
        if (a.getPrice() <= b.getPrice()) {
            return true;
        }
        return false;
    }

    public static void main(String[] args) {
        MaxPQ<Deal> buyDeal = new MaxPQ<>();
        MinPQ<Deal> sellDeal = new MinPQ<>();

        while (!buyDeal.isEmpty() && !sellDeal.isEmpty()) {
            Deal buy = buyDeal.delMax();
            Deal sell = sellDeal.delMin();
            if (!makeDeal(buy, sell)) {
                buyDeal.insert(buy);
                sellDeal.insert(sell);
            }
        }
    }
}
```

***
**2.5.23 选择的取样：实验使用取样来改进select()函数的想法。**

*Answer：*

***
**2.5.24 稳定的优先队列。实现一个稳定的优先队列（将重复的元素按照他们被插入的顺序返回）。**

*Answer：*

官方实现的StableMinPQ.java：

```java
public class StableMinPQ<Key extends Comparable<? super Key>> {
    
    private Key[] pq; // store element at indices 1 to N
    private long[] time; // timestamp
    private int n; // number of elements on priority queue
    private long timestamp = 1; // timestamp for when item was inserted

    // create an empty priority queue with given initial capacity
    @SuppressWarnings("unchecked")
    public StableMinPQ(int initCapacity) {
        pq = (Key[]) new Comparable[initCapacity + 1];
        time = new long[initCapacity + 1];
        n = 0;
    }

    // create an empty priority queue
    public StableMinPQ() {
        this(1);
    }

    // Is the priority queue empty?
    public boolean isEmpty() {
        return n == 0;
    }

    // Return the number of elements on the priority queue.
    public int size() {
        return n;
    }

    // Return the smallest key on the priority queue.
    public Key min() {
        if (isEmpty())
            throw new RuntimeException("Priority queue underflow");
        return pq[1];
    }

    // helper function to double the size of the heap array
    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        assert capacity > n;
        Key[] tempPQ = (Key[]) new Comparable[capacity];
        long[] tempTime = new long[capacity];
        for (int i = 1; i <= n; i++)
            tempPQ[i] = pq[i];
        for (int i = 1; i <= n; i++)
            tempTime[i] = time[i];
        pq = tempPQ;
        time = tempTime;
    }

    // add a new key to the priority queue
    public void insert(Key x) {
        // double size of array if necessary
        if (n == pq.length - 1)
            resize(2 * pq.length);

        // add x, and percolate it up to maintain heap invariant
        n++;
        pq[n] = x;
        time[n] = ++timestamp;
        swim(n);
    }

    // Delete and return the smallest key on the priority queue.
    public Key delMin() {
        if (n == 0)
            throw new RuntimeException("Priority queue underflow");
        exch(1, n);
        Key min = pq[n--];
        sink(1);
        pq[n + 1] = null; // avoid loitering and help with garbage collection
        time[n + 1] = 0;
        if ((n > 0) && (n == (pq.length - 1) / 4))
            resize(pq.length / 2);
        return min;
    }

    /***************************************************************************
     * Helper functions to restore the heap invariant.
     ***************************************************************************/
    private void swim(int k) {
        while (k > 1 && greater(k / 2, k)) {
            exch(k, k / 2);
            k = k / 2;
        }
    }

    private void sink(int k) {
        while (2 * k <= n) {
            int j = 2 * k;
            if (j < n && greater(j, j + 1))
                j++;
            if (!greater(k, j))
                break;
            exch(k, j);
            k = j;
        }
    }

    /***************************************************************************
     * Helper functions for compares and swaps.
     ***************************************************************************/
    private boolean greater(int i, int j) {
        int cmp = pq[i].compareTo(pq[j]);
        if (cmp > 0)
            return true;
        if (cmp < 0)
            return false;
        return time[i] > time[j];
    }

    private void exch(int i, int j) {
        Key temp = pq[i];
        pq[i] = pq[j];
        pq[j] = temp;
        long tempTime = time[i];
        time[i] = time[j];
        time[j] = tempTime;
    }

    private static final class Tuple implements Comparable<Tuple> {
        private String name;
        private int id;

        private Tuple(String name, int id) {
            this.name = name;
            this.id = id;
        }

        public int compareTo(Tuple that) {
            return this.name.compareTo(that.name);
        }

        public String toString() {
            return name + " " + id;
        }
    }

    public static void main(String[] args) {
        StableMinPQ<Tuple> pq = new StableMinPQ<Tuple>();

        // insert a bunch of strings
        String text = "it was the best of times it was the worst of times it was the "
                + "age of wisdom it was the age of foolishness it was the epoch "
                + "belief it was the epoch of incredulity it was the season of light "
                + "it was the season of darkness it was the spring of hope it was the " + "winter of despair";
        String[] strings = text.split(" ");
        for (int i = 0; i < strings.length; i++) {
            pq.insert(new Tuple(strings[i], i));
        }

        // delete and print each key
        while (!pq.isEmpty()) {
            StdOut.println(pq.delMin());
        }
        StdOut.println();
    }
}
```

***
**2.5.26 平面身上的点。为表1.2.3的Point2D类型编写三个静态的比较器，一个按照x坐标比较，一个按照y坐标比较，一个按照点到远点的距离进行比较。编写两个非静态的比较器，一个按照两点到第三点的距离比较，一个按照两点相对于第三点的幅角比较。**

*Answer：*

参见edu.princeton.cs.algs4.Point2D。

***
**2.5.26 简单多边形。给定平面上的N个点，用它们画出一个多边形。**

*Answer：*

```java
public class Ex2526 {
    public static void drawPolygon(Point2D[] arr) {

        int N = arr.length;
        if (N < 3) {
            throw new IllegalArgumentException();
        }

        // 保护性拷贝
        Point2D[] backup = new Point2D[N];
        System.arraycopy(arr, 0, backup, 0, N);

        // 寻找y坐标最小的点
        Point2D ymin = arr[0];
        for (int i = 1; i < N; i++) {
            if (arr[i].y() < ymin.y()) {
                ymin = arr[i];
                continue;
            }
            if (arr[i].y() == ymin.y() && arr[i].x() < ymin.x()) {
                ymin = arr[i];
            }
        }

        // 此时点ymin为y坐标最小的点（y相同时，x最小）
        // 在此基础上，以ymin为基准按照角度进行排序
        Arrays.sort(arr, ymin.polarOrder());

        StdDraw.setPenColor(StdDraw.BOOK_RED);
        StdDraw.setPenRadius();
        // 这里实际上需要查找这些点的最大、最小横坐标以及最大、最小纵坐标来确定X、Y的大小
        StdDraw.setXscale(0, 5);
        StdDraw.setYscale(0, 5);
        for (int i = 0; i < N; i++) {
            StdDraw.line(arr[i].x(), arr[i].y(), arr[(i + 1) % N].x(), arr[(i + 1) % N].y());
        }

        // 恢复arr数组
        arr = backup;
    }

    public static void main(String[] args) {
        Point2D[] arr = new Point2D[] { new Point2D(1, 2), new Point2D(1, 1), new Point2D(2, 1), new Point2D(2, 3),
                new Point2D(3, 3), new Point2D(3, 2) };
        drawPolygon(arr);
    }
}
```

***
**2.5.27 平行数组的排序。在将平行数组排序时，可以将索引排序并返回一个index[]数组。为Insertion添加一个indirectSort()方法，接受一个Comparable的对象数组a[]作为参数，但它不会将a[]中的元素重新排列，而是返回一个整型数组index[]使得a[index[0]]到a[index[N-1]]正好是升序的。**

*Answer：*

```java
public class Ex2527 {
    
    public static <T extends Comparable<? super T>> int[] indirectSort(T[] arr) {
        
        int[] a = new int[arr.length];
        
        Map<Integer, T> map = new HashMap<>();
        for (int i = 0; i < arr.length; i++) {
            map.put(i, arr[i]);
        }
        
        // 对map进行按值排序
        List<Map.Entry<Integer, T>> list = new ArrayList<>(map.entrySet());
        Collections.sort(list, new Comparator<Map.Entry<Integer, T>>() {
            @Override
            public int compare(Entry<Integer, T> o1, Entry<Integer, T> o2) {
                return o1.getValue().compareTo(o2.getValue());
            }
        });
        
        int n = 0;
        for (Entry<Integer, T> entry : list) {
            a[n++] = entry.getKey();
        }
        return a;
    }
    
    public static void main(String[] args) {
        String[] arr = "C D A E F B".split(" ");
        int[] a = indirectSort(arr);
        for (int i : a) {
            System.out.print(arr[i] + " ");
        }
    }
}
```

***
**2.5.28 按文件名排序。编写一个FileSorter程序，从命令行接受一个目录名并打印按照文件名排序后的所有文件。**

*Answer：*

```java
public class FileSorter {
    public static void main(String[] args) {
        File dict = new File(args[0]);
        
        File[] files = dict.listFiles();
        
        Arrays.sort(files, new Comparator<File>() {
            @Override
            public int compare(File o1, File o2) {
                return o1.getName().compareTo(o2.getName());
            }
        });
        
        for (File file : files) {
            System.out.println(file.getName());
        }
    }
}
```

***
**2.5.29 按大小和最后修改日期将文件排序。为File数据类型编写比较器，使之能够将文件按照大小、文件名或最后修改日期将文件升序或降序排列。在程序LS中使用这些比较器，他接受一个命令行参数并根据指定的顺序列出目录的内容。例如，“-t”指按照时间戳排序。支持多个选项以消除排序位次相同者，同时必须确保排序的稳定性。**

*Answer：*

略。

***
**2.5.30 Boerner定理。真假判断：如果你先将一个矩阵的每一列排序，再将矩阵的每一行排序，所有的列仍然是有序的。证明你的结论。**

*Answer：*

该结论是正确的，但不会证明...

***

#### 实验题

***
**2.5.31 重复元素。编写一段程序，接受命令行参数M、N和T，然后使用正文中的代码进行T遍实验：生成N个0到M-1间的int值并不计算重复值的个数。令$T=10$，$N=10^3、10^4、10^5$和$10^6$以及$M=N/2、N$和$2N$。根据概率论，重复值的个数应该约为$M(1-e^{-\alpha})$，其中$\alpha=N/M$。打印一张表格来确认你的实验验证了这个公式。**

*Answer：*

```java
public class Ex2531 {
    
    public static void main(String[] args) {

        int M = 1000;
        int N = 1000;
        int T = 10;

        int times = 0;
        for (int i = 0; i < T; i++) {
            // N个数的数组，数组元素均在0到M-1之间
            int[] arr = new int[N];
            for (int j = 0; j < N; j++) {
                arr[j] = StdRandom.uniform(0, M);
            }

            times += checkRepeatElements(arr);
        }

        System.out.println("M = " + M + ", " + "N = " + N);
        System.out.println("理论值：" + M * (1 - Math.exp(-(double) N / M)));
        System.out.println("实际值：" + (double) times / T);
    }

    // 使用Set来排除重复的值
    private static int checkRepeatElements(int[] arr) {

        Set<Integer> set = new HashSet<>();

        for (int i = 0; i < arr.length; i++) {
            set.add(arr[i]);
        }

        return set.size();
    }

    // 排序后遍历数组计算不重复的值
//  private static <T extends Comparable<? super T>> int distinct(T[] a) {
//      if (a.length == 0)
//          return 0;
//      Arrays.sort(a);
//      int distinct = 1;
//      for (int i = 1; i < a.length; i++)
//          if (a[i].compareTo(a[i - 1]) != 0)
//              distinct++;
//      return distinct;
//  }
}
```

结果：

M = 1000, N = 1000
理论值：632.1205588285577
实际值：632.1

***
**2.5.32 8字谜题。8字谜题是S.Loyd与19世纪70年代发明的一个游戏。游戏需要一个三乘三的九宫格，其中八格中填上了1到8者8个数字，一格空着。你的目标就是将所有的格子排序。可以将一个格子向上下或者左右移动（但不能是对角线方向）到空白的格子。编写一个程序用A\*算法解决这个问题。尝试用其它函数代替错位的各自数量，比如每个各自距离它的正确位置的曼哈顿距离，或是这些距离的平方和。**

*Answer：*

***
**2.5.33 随机交易。开发一个接受参数N的生成器，根据你能想到的任意假设条件生成N个随机的Transaction对象。对于$N=10^3、10^4、10^5$和$10^6$，比较用希尔排序，归并排序、快速排序和堆排序将N个交易排序的性能。**

*Answer：*

SortCompare问题。程序略。

## 第三章 查找

### 3.1 符号表

***

#### 练习题

***
**3.1.1 编写一段程序，创建一张符号表并建立字母成绩和数值分数的对应关系，如下表所示。从标准输入读取一系列字母成绩，计算并打印GPA（字母成绩对应的分数的平均值）。**

A+|A|A-|B+|B|B-|C+|C|C-|D|F
-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----
4.33|4.00|3.67|3.33|3.00|2.67|2.33|2.00|1.67|1.00|0.00

*Answer：*

```java
public class Ex311 {
    public static void main(String[] args) {
        ST<String, Double> st = new ST<>();
        st.put("A+", 4.33);
        st.put("A", 4.00);
        st.put("A-", 3.67);
        st.put("B+", 3.33);
        st.put("B", 3.00);
        st.put("B-", 2.67);
        st.put("C+", 2.33);
        st.put("C", 2.00);
        st.put("C-", 1.67);
        st.put("D", 1.00);
        st.put("F", 0.00);

        int n = 0;
        double total = 0;
        while (!StdIn.isEmpty()) {
            total += st.get(StdIn.readString());
            n++;
        }
        System.out.println("平均分为：" + total / n);
    }
}
```

***
**3.1.2 开发一个符号表的实现ArrayST，使用（无序）数组来实现我们的基本API。**

*Answer：*

只实现了基本的put/get操作以及调整数组大小的方法。

```java
public class ArrayST<Key extends Comparable<? super Key>, Value> {

    private Key keys[];
    private Value vals[];
    private int N;

    @SuppressWarnings("unchecked")
    public ArrayST(int capacity) {
        keys = (Key[]) new Comparable[capacity];
        vals = (Value[]) new Object[capacity];
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public Value get(Key key) {
        if (isEmpty()) {
            return null;
        }
        for (int i = 0; i < N; i++) {
            if (keys[i].compareTo(key) == 0) {
                return vals[i];
            }
        }
        return null;
    }

    public void put(Key key, Value val) {
        for (int i = 0; i < N; i++) {
            if (keys[i].compareTo(key) == 0) {
                vals[i] = val;
                return;
            }
        }
        if (N == keys.length) {
            resize(2 * keys.length);
        }
        keys[N] = key;
        vals[N] = val;
        N++;
    }

    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        Key[] tempk = (Key[]) new Comparable[capacity];
        Value[] tempv = (Value[]) new Object[capacity];
        for (int i = 0; i < N; i++) {
            tempk[i] = keys[i];
            tempv[i] = vals[i];
        }
        vals = tempv;
        keys = tempk;
    }
}
```

***
**3.1.3 开发一个符号表的实现OrderedSequentialSearchST，使用有序链表来实现我们的有序符号表API。**

*Answer：*

只实现了基本的put/get操作。

```java
public class OrderedSequentialSearchST<Key extends Comparable<? super Key>, Value> {

    private Node<Key, Value> first; // 链表首结点
    private int N;

    private static class Node<Key, Value> {
        Key key;
        Value val;
        Node<Key, Value> next;

        public Node(Key key, Value val, Node<Key, Value> next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public Value get(Key key) {
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return x.val;
            }
        }
        return null;
    }

    public void put(Key key, Value value) {
        Node<Key, Value> pre = first;
        for (Node<Key, Value> x = first; x != null && x.key.compareTo(key) < 0; pre = x, x = x.next) {
            if (x.key.compareTo(key) == 0) {
                x.val = value;
                return;
            }
        }
        if (pre == first) {
            first = new Node<>(key, value, first);
        } else {
            pre.next = new Node<>(key, value, pre.next);
        }
        N++;
    }
}
```

***
**3.1.4 开发抽象数据类型Time和Event来处理表3.1.5中的例子中的数据。**

*Answer：*

略。

***
**3.1.5 实现SequentialSearchST中的size()、delete()和keys()方法。**

*Answer：*

```java
// ...
private int N;

public int size() {
    return N;
}

public Value delete(Key key) {
    if (size() == 0) {
        throw new UnsupportedOperationException("ST underflow...");
    }

    Value value = null;
    Node<Key, Value> pre = first;
    for (Node<Key, Value> x = first; x != null; pre = x, x = x.next) {
        if (x.key.equals(key)) {
            value = x.val;
            pre.next = x.next;
            x = null;
            break;
        }
    }
    N--;
    return value;
}

public Iterable<Key> keys() {
    Queue<Key> queue = new Queue<>();
    for (Node<Key, Value> x = first; x != null; x = x.next) {
        queue.enqueue(x.key);
    }
    return queue;
}
```

官方实现的delete()方法是void的：

```java
/**
 * Removes the specified key and its associated value from this symbol table     
 * (if the key is in this symbol table).    
 *
 * @param  key the key
 * @throws IllegalArgumentException if {@code key} is {@code null}
 */
public void delete(Key key) {
    if (key == null) throw new IllegalArgumentException("argument to delete() is null"); 
    first = delete(first, key);
}

// delete key in linked list beginning at Node x
// warning: function call stack too large if table is large
private Node delete(Node x, Key key) {
    if (x == null) return null;
    if (key.equals(x.key)) {
        n--;
        return x.next;
    }
    x.next = delete(x.next, key);
    return x;
}
```


***
**3.1.6 用输入中的单词总数W和不同单词总数D的函数给出FrequencyCounter调用put()和get()方法的次数。**

*Answer：*

会调用put()和get()的代码如下：

```java
if (!st.contains(word)) {
    st.put(word, 1);
} else {
    st.put(word, st.get(word) + 1);
}
```

- put()：W次
- get()：W-D次

***
**3.1.7 对于$N=10、10^2、10^3、10^4、10^5、10^6$，在N个小于1000的随机非负整数中FrequencyCounter平均能找到多少个不同的键？**

*Answer：*

```java
public class Ex317 {
    public static void main(String[] args) {

        ST<Integer, Integer> st = null;
        for (int N = 10; N <= 1000000; N *= 10) {
            int total = 0;
            for (int T = 0; T < 10; T++) { // 10次平均
                st = new ST<>();
                for (int i = 0; i < N; i++) {
                    st.put(StdRandom.uniform(0, 1000), 0);
                }
                total += st.size();
            }
            System.out.println("N = " + N + ", 平均有不同键：" + (double) total / 10);
        }
    }
}
```

结果：

    N = 10, 平均有不同键：10.0
    N = 100, 平均有不同键：95.5
    N = 1000, 平均有不同键：633.1
    N = 10000, 平均有不同键：1000.0
    N = 100000, 平均有不同键：1000.0
    N = 1000000, 平均有不同键：1000.0

***
**3.1.8 在《双城记》中，使用频率最高的长度大于等于10的单词是什么？**

*Answer：*

没有使用ST.java，直接对Map按值降序排序。

```java
public class Ex318 {
    
    public static void main(String[] args) {
        
        Map<String, Integer> map = new HashMap<>();
        while (!StdIn.isEmpty()) {
            String word = StdIn.readString();
            if (!map.containsKey(word)) {
                map.put(word, 1);
            } else {
                map.put(word, map.get(word) + 1);
            }
        }
        
        List<Map.Entry<String,Integer>> list = new ArrayList<>(map.entrySet());
        Collections.sort(list, new Comparator<Map.Entry<String,Integer>>() {
            @Override
            public int compare(Entry<String, Integer> o1, Entry<String, Integer> o2) {
                return o2.getValue() - o1.getValue();
            }
        });
        
        String word = "";
        for (Entry<String, Integer> entry : list) {
            word = entry.getKey();
            if (word.length() >= 10) {
                System.out.println(word + " " + entry.getValue());
                break;
            }
        }
    }
}
```

结果：

    monseigneur 101

***
**3.1.9 在FrequencyCounter中添加追踪put()方法最后一次调用的代码。打印出最后插入的那个单词以及在此之前总共从输入中处理了多少个单词。用你的程序处理tale.txt中长度分别大于等于1、8和10的单词。**

*Answer：*

```java
public class Ex319 {
    public static void main(String[] args) {
        int threshold = Integer.parseInt(args[0]);

        ST<String, Integer> st = new ST<>();

        String lastWord = "";
        int putCount = 0;

        while (!StdIn.isEmpty()) {
            String word = StdIn.readString();
            // 忽略较短单词
            if (word.length() < threshold) {
                continue;
            }
            if (!st.contains(word)) {
                st.put(word, 1);
            } else {
                st.put(word, st.get(word) + 1);
            }
            lastWord = word;
            putCount++;
        }
        System.out.println("======" + "threshold = " + threshold + "======");
        System.out.println("最后处理的单词：" + lastWord);
        System.out.println("共处理单词数：" + putCount);
    }
}
```

结果：

    ======threshold = 1======
    最后处理的单词：known
    共处理单词数：135643
    ======threshold = 8======
    最后处理的单词：faltering
    共处理单词数：14346
    ======threshold = 10======
    最后处理的单词：disfigurement
    共处理单词数：4579

***
**3.1.10 给出SequentialSearchST将键E A S Y Q U E S T I O N插入一个空符号表的轨迹。一共进行了多少次比较？**

*Answer：*

```java
class SequentialSearchSTEx3110<Key, Value> {
    private Node<Key, Value> first; // 链表首结点

    private int N;
    
    private int count; // 记录比较次数
    
    public int getCount() { // 返回count的方法
        return count;
    }

    private static class Node<Key, Value> {
        Key key;
        Value val;
        Node<Key, Value> next;

        public Node(Key key, Value val, Node<Key, Value> next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }

    public int size() {
        return N;
    }

    // 查找指定的键，返回相关联的值
    public Value get(Key key) {
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return x.val;
            }
        }
        return null;
    }

    // 查找给定的键，找到则更新其值，否则在表中新建结点插入表头
    public void put(Key key, Value val) {
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            count++;
            if (key.equals(x.key)) {
                x.val = val; // 命中，更新
                return;
            }
        }
        first = new Node<>(key, val, first); // 未命中，新建结点
        N++;
        show();
    }

    // 打印当前所有key的方法
    private void show() {
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            System.out.print(x.key + " ");
        }
        System.out.println();
    }

    // 根据键删除指定的键值对
    public Value delete(Key key) {
        if (size() == 0) {
            throw new UnsupportedOperationException("ST underflow...");
        }

        Value value = null;
        Node<Key, Value> pre = first;
        for (Node<Key, Value> x = first; x != null; pre = x, x = x.next) {
            if (x.key.equals(key)) {
                value = x.val;
                pre.next = x.next;
                x = null;
                break;
            }
        }
        N--;
        return value;
    }
    
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            queue.enqueue(x.key);
        }
        return queue;
    }
}


public class Ex3110 {
    
    public static void main(String[] args) {
        SequentialSearchSTEx3110<String, Integer> st = new SequentialSearchSTEx3110<>();
        String string = "E A S Y Q U E S T I O N";
        for (String s : string.split(" ")) {
            st.put(s, 0);
        }
        System.out.println(st.getCount());
    }
}
```

结果：

    E 
    A E 
    S A E 
    Y S A E 
    Q Y S A E 
    U Q Y S A E 
    T U Q Y S A E 
    I T U Q Y S A E 
    O I T U Q Y S A E 
    N O I T U Q Y S A E 
    55

***
**3.1.11 给出BinarySearchST将键E A S Y Q U E S T I O N插入一个空符号表的轨迹。一共进行了多少次比较？**

*Answer：*

删除了一些不必要的方法。

```java
class BinarySearchSTEx3111<Key extends Comparable<? super Key>, Value> {

    private Key[] keys;
    private Value[] vals;
    private int N;

    private int count;

    public int getCount() {
        return count;
    }

    @SuppressWarnings("unchecked")
    public BinarySearchSTEx3111(int capacity) {
        keys = (Key[]) new Comparable[capacity];
        vals = (Value[]) new Object[capacity];
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public Value get(Key key) {
        if (isEmpty()) {
            return null;
        }
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            return vals[i];
        }
        return null;
    }

    public int rank(Key key) {
        int lo = 0, hi = N - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int cmp = key.compareTo(keys[mid]);
            count++;
            if (cmp < 0) {
                hi = mid - 1;
            } else if (cmp > 0) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return lo;
    }

    public void put(Key key, Value val) {
        int i = rank(key);
        count++;
        if (i < N && keys[i].compareTo(key) == 0) {
            vals[i] = val;
            return;
        }

        if (N == keys.length) {
            resize(2 * keys.length);
        }

        for (int j = N; j > i; j--) {
            keys[j] = keys[j - 1];
            vals[j] = vals[j - 1];
        }
        keys[i] = key;
        vals[i] = val;
        N++;
        show();
    }

    public void show() {
        for (int i = 0; i < N; i++) {
            System.out.print(keys[i] + " ");
        }
        System.out.println();
    }

    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        Key[] tempk = (Key[]) new Comparable[capacity];
        Value[] tempv = (Value[]) new Object[capacity];
        for (int i = 0; i < N; i++) {
            tempk[i] = keys[i];
            tempv[i] = vals[i];
        }
        vals = tempv;
        keys = tempk;
    }
}

public class Ex3111 {
    
    public static void main(String[] args) {
        BinarySearchSTEx3111<String, Integer> st = new BinarySearchSTEx3111<>(20);
        String string = "E A S Y Q U E S T I O N";
        for (String s : string.split(" ")) {
            st.put(s, 0);
        }
        System.out.println(st.getCount());
    }
}
```

结果：

    E 
    A E 
    A E S 
    A E S Y 
    A E Q S Y 
    A E Q S U Y 
    A E Q S T U Y 
    A E I Q S T U Y 
    A E I O Q S T U Y 
    A E I N O Q S T U Y 
    41

***
**3.1.12 修改BinarySearchST，用一个Item对象的数组而非两个平行数组来保存键和值。添加一个构造函数，接受一个Item的数组为参数并将其归并排序。**

*Answer：*

```java
public class BinarySearchSTItem<Key extends Comparable<? super Key>, Value> {
    
    private Item<Key, Value>[] entry;

    private int N;

    private static class Item<Key extends Comparable<? super Key>, Value> implements Comparable<Item<Key, Value>> {

        Key key;
        Value value;

        public Item(Key key, Value value) {
            this.key = key;
            this.value = value;
        }

        @Override
        public String toString() {
            return "[" + key + ", " + value + "]";
        }

        @Override
        public int compareTo(Item<Key, Value> o) {
            return key.compareTo(o.key);
        }
    }

    @SuppressWarnings("unchecked")
    public BinarySearchSTItem(int capacity) {
        this.entry = new Item[capacity];
    }

    public BinarySearchSTItem(Item<Key, Value>[] arr) {
        Merge.sort(arr);
        entry = arr;
        N = arr.length;
    }

    public void show() {
        for (Item<Key, Value> item : entry) {
            if (item != null) {
                System.out.print(item + " ");
            }
        }
        System.out.println();
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public Value get(Key key) {
        if (isEmpty()) {
            return null;
        }
        int i = rank(key);
        if (i < N && entry[i].key.compareTo(key) == 0) {
            return entry[i].value;
        }
        return null;
    }

    public void put(Key key, Value val) {
        int i = rank(key);
        if (i < N && entry[i].key.compareTo(key) == 0) {
            entry[i].value = val;
            return;
        }

        if (N == entry.length) {
            resize(2 * entry.length);
        }

        for (int j = N; j > i; j--) {
            entry[j] = entry[j - 1];
        }
        entry[i] = new Item<>(key, val);
        N++;
    }

    public int rank(Key key) {
        int lo = 0, hi = N - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int cmp = key.compareTo(entry[mid].key);
            if (cmp < 0) {
                hi = mid - 1;
            } else if (cmp > 0) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return lo;
    }

    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        Item<Key, Value>[] temp = new Item[capacity];
        for (int i = 0; i < N; i++) {
            temp[i] = entry[i];
        }
        entry = temp;
    }

    public static void main(String[] args) {
        @SuppressWarnings("unchecked")
        Item<String, Integer>[] items = new Item[] {
                new Item<>("D", 1),
                new Item<>("E", 1),
                new Item<>("C", 1),
                new Item<>("B", 1),
                new Item<>("F", 1),
                };
        
        BinarySearchSTItem<String, Integer> bssti = new BinarySearchSTItem<>(items);
        bssti.show();
        bssti.put("A", 2);
        bssti.show();
        bssti.put("F", 100);
        bssti.show();
    }
}
```

测试结果：

    [B, 1] [C, 1] [D, 1] [E, 1] [F, 1] 
    [A, 2] [B, 1] [C, 1] [D, 1] [E, 1] [F, 1] 
    [A, 2] [B, 1] [C, 1] [D, 1] [E, 1] [F, 100] 

***
**3.1.13 对于一个会随机混合进行$10^3$次put()和$10^6$次get()操作的应用程序，你会使用本节中的哪种符号表的实现？说明理由。**

*Answer：*

根据题意，get()操作占多数，所以应该选择get()相对廉价的实现。所以选择BinarySearchST，这个实现的get()操作为$logN$。

***
**3.1.14 对于一个会随机混合进行$10^6$次put()和$10^3$次get()操作的应用程序，你会使用本节中的哪种符号表的实现？说明理由。**

*Answer：*

根据题意，put()操作占多数，所以应该选择put()操作相对廉价的实现。所以选择SequentialSearchST，这个实现的put()操作为$N$。

***
**3.1.15 假设在一个BinarySearchST的用例程序中，查找操作的次数是插入操作的1000倍。当分别进行$10^3、10^6、10^9$次查找时，请估计插入操作在总耗时中的比例。**

*Answer：*

- 查找操作次数为$10^3$时，插入操作的次数为$1$，最多有$1$个元素。此时比例为：$\frac{1\times1}{1\times1 + 10^3\times log1}=1$。
- 查找操作次数为$10^6$时，插入操作的次数为$10^3$，最多有$10^3$个元素。此时比例为：$\frac{10^3\times10^3}{10^3\times10^3 + 10^6\times log10^3}=0.091$。
- 查找操作次数为$10^9$时，插入操作的次数为$10^6$，最多有$10^6$个元素。此时比例为：$\frac{10^6\times10^6}{10^6\times10^6 + 10^9\times log10^6}=0.980$。

***
**3.1.16 为BinarySearchST实现Delete()方法。**

*Answer：*

```java
public void delete(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("argument to delete() is null");
    }
    if (isEmpty()) {
        return;
    }

    // compute rank
    int i = rank(key);

    // key not in table
    if (i == N || keys[i].compareTo(key) != 0) {
        return;
    }

    for (int j = i; j < N - 1; j++) {
        keys[j] = keys[j + 1];
        vals[j] = vals[j + 1];
    }

    N--;
    keys[N] = null; // to avoid loitering
    vals[N] = null;
    
    // resize if 1/4 full
    if (N > 0 && N == keys.length / 4) {
        resize(keys.length / 2);
    }
}
```

***
**3.1.17 为BinarySearchST实现floor()方法。**

*Answer：*

```java
public Key floor(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("argument to floor() is null");
    }
    int i = rank(key);
    if (i < N && key.compareTo(keys[i]) == 0) {
        return keys[i];
    }
    if (i == 0) {
        return null;
    } else {
        return keys[i - 1];
    }
}
```

***
**3.1.18 证明BinarySearchST中rank()方法的实现的正确性。**

*Answer：*

参见p241。

***
**3.1.19 修改FrequencyCounter，打印出现频率最高的所有单词，而非其中之一。**

*Answer：*

```java
public class FrequencyCounterEx3119 {
    
    public static void main(String[] args) {
        
        int minlen = Integer.parseInt(args[0]);
        ST<String, Integer> st = new ST<>();
        while (!StdIn.isEmpty()) {
            String word = StdIn.readString();
            // 忽略较短单词
            if (word.length() < minlen) {
                continue;
            }
            if (!st.contains(word)) {
                st.put(word, 1);
            } else {
                st.put(word, st.get(word) + 1);
            }
        }

        // 找出出现频率最高的单词
        String max = " ";
        st.put(max, 0);

        for (String word : st.keys()) {
            if (st.get(word) > st.get(max)) {
                max = word;
            }
        }
        
        // 经过遍历此时max为频率最高的单词
        // freq为最高频率
        Integer freq = st.get(max);
        
        for (String word : st.keys()) {
            if (st.get(word) == freq) {
                StdOut.println(word + " " + freq);
            }
        }
    }
}
```
















***
**3.1.20 补全命题B的证明（证明N的一般情况）。**

*Answer：*

***

#### 提高题

***

**3.1.21 内存使用。基于1.4节的假设，对于N对键值比较BinarySearchST和SequentialSearchST的内存使用情况。不需要记录键值本身占用的内存，只统计它们的引用。对于BinarySearchST，假设数组大小可以动态调整，数组中被占用的空间比例为25%~100%。**

*Answer：*

***
**3.1.22 自组织查找。自组织查找指的是一种能够将数组元素重新排序使得被访问频率较高的元素更容易被找到的查找算法。请修改你为练习3.1.2给出的答案，在每次查找命中时：将被找到的键值对移动到数组的开头，将所有中间的键值对向右移动一格。这个启发式的过程被称为前移编码。**

*Answer：*

修改get()方法：

```java
public Value get(Key key) {
    if (isEmpty()) {
        return null;
    }
    for (int i = 0; i < N; i++) {
        if (keys[i].compareTo(key) == 0) {
            
            // 将命中的键值对移动到数组开头
            Key k = keys[i];
            Value v = vals[i];
            for (int j = i; j > 0; j--) {
                keys[j] = keys[j - 1];
                vals[j] = vals[j - 1];
            }
            keys[0] = k;
            vals[0] = v;
            
            return v;
        }
    }
    return null;
}
```

***
**3.1.23 二分查找的分析。请证明对于大小为N的符号表，一次二分查找所需的最大比较次数正好是N的二进制表示的位数，因为右移一位会将二进制的N变位二进制的[N/2]。**

*Answer：*

***
**3.1.24 插值法查找。假设符号表的键支持算术操作（例如，它们可能是Double或者是Integer类型的值）。编写一个二分查找来模拟查字典的行为，例如当单词的首字母在字母表的开头时我们也会在字典的前半部分进行查找。具体来说，设$k_{lo}$为符号表的第一个键，$k_{hi}$为符号表的最后一个键，当要查找$k_x$时，先和$\lfloor(k_x-k_{lo})/(k_{hi}-k_{lo})\rfloor$进行比较，而非取中间元素。用SearchCompare调用FrequencyCounter来比较你的实现和BinarySearchST的性能。**

*Answer：*

键的类型直接使用Integer并修改rank()方法：

```java
public int rank(Integer key) {

    int lo = 0, hi = N - 1;

    while (lo <= hi) {

        // 如果lo==hi，直接令mid=lo
        int mid = (lo == hi ? lo : lo + (key - keys[lo]) / (keys[hi] - keys[lo]) * (hi - lo));

        // 如果mid经计算不在lo~hi范围，则取mid为lo和hi的中间值
        // 同时防止数组角标越界
        if (mid < lo || mid > hi) {
            System.out.println(mid);
            mid = lo + (hi - lo) / 2;
        }

        int cmp = key.compareTo(keys[mid]);
        if (cmp < 0) {
            hi = mid - 1;
        } else if (cmp > 0) {
            lo = mid + 1;
        } else {
            return mid;
        }
    }
    return lo;
}
```

对插值查找的分析：
>
从时间复杂度上来看，它也是$o(n)$，但是对于表长较大，而关键字分布又比较均匀的查找表来说，插值查找算法的平均性能比折半查找要好的多。反之，数组中如果分布非常不均匀，那么差值查找未必是很合适的选择。

性能比较略。

***
**3.1.25 缓存。因为默认的contains()实现中调用了get()，所以FrequencyCounter的内循环会将同一个键查找两三遍：**

```java
if (!st.contains(word)) st.put(word, 1);
else                    st.put(word, st.get(word) + 1);
```

**为了能够提高这样的用例代码的效率，我们可以用一种叫缓存的技术手段，即将访问最频繁的键的位置保存在一个变量中。修改SequentialSearchST和BinarySearchST来实现这个点子。**

*Answer：*

以BinarySearchTS为例：

```java
private Map<Key, Value> cache = new HashMap<>();

// ...

public Value get(Key key) {
    if (isEmpty()) {
        return null;
    }
    
    Value val = null;
    
    if ((val = cache.get(key)) != null) {
        // 从缓存中取出值
        return val;
    }
    
    int i = rank(key);
    if (i < N && keys[i].compareTo(key) == 0) {
        val =  vals[i];
        cache.put(key, val); // 取出的值存入缓存
    }
    
    return val;
}

public void put(Key key, Value val) {
    int i = rank(key);
    if (i < N && keys[i].compareTo(key) == 0) {
        vals[i] = val;
        if (cache.containsKey(key)) {
            // 对值的修改加入缓存
            cache.put(key, val);
        } 
        return;
    }

    if (N == keys.length) {
        resize(2 * keys.length);
    }

    for (int j = N; j > i; j--) {
        keys[j] = keys[j - 1];
        vals[j] = vals[j - 1];
    }
    keys[i] = key;
    vals[i] = val;
    N++;
}
```

***
**3.1.26 基于字典的频率统计。修改FrequencyCounter，接受一个字典文件作为参数，统计标准输入中出现在字典中的单词的频率，并将单词和频率打印为两张表格，一张按照频率高低排序，一张按照字典顺序排序。**

*Answer：*

```java
public class FrequencyCounterEx3126 {
    
    public static void main(String[] args) {

        // 读取字典：words3.txt
        String[] dictionary = new In(args[0]).readAllStrings();
        ST<String, Integer> st = new ST<>();
        // 存入符号表
        for (String string : dictionary) {
            st.put(string, 0);
        }

        // 默认按照字典顺序排序
        Map<String, Integer> map = new TreeMap<>(new Comparator<String>() {

            private int getIdx(String s) {
                for (int i = 0; i < dictionary.length; i++) {
                    if (s.equals(dictionary[i])) {
                        return i;
                    }
                }
                return -1;
            }
            
            // 按字典顺序排序
            @Override
            public int compare(String o1, String o2) {
                return getIdx(o1) - getIdx(o2);
            }
        });
        
        // 读取标准输入，记录频率
        while (!StdIn.isEmpty()) {
            String word = StdIn.readString();
            // 符号表中存在该key
            if (st.contains(word)) {
                // map中存在该key
                if (map.containsKey(word)) { 
                    map.put(word, map.get(word) + 1);
                } else {
                    map.put(word, 1);
                }
            }
            // 不存在于字典的单词不进行统计
        }

        // 输出按照字典顺序排序
        System.out.println(map);
        
        // 按频率排序
        List<Map.Entry<String, Integer>> list = new ArrayList<>(map.entrySet());
        Collections.sort(list, new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Entry<String, Integer> o1, Entry<String, Integer> o2) {
                return o2.getValue() - o1.getValue();
            }
        });
        
        // 输出按照频率顺序排序
        System.out.println(list);
    }
}
```

字典，words3.txt：

    bed bug dad yes zoo
    now for tip ilk dim 
    tag jot sob nob sky
    hut men egg few jay
    owl joy rap gig wee
    was wad fee tap tar
    dug jam all bad yet

输入：

    dug
    all
    yet
    yet
    yet
    now
    dad
    zoo
    zoo
    owl
    yet

结果：

    {dad=1, zoo=2, now=1, owl=1, dug=1, all=1, yet=4}
    [yet=4, zoo=2, dad=1, now=1, owl=1, dug=1, all=1]

***
**3.1.27 小符号表。假设一段BinarySearchST的用例插入了N个不同的键并会进行S次查找。当构造表的成本和所有查找的总成本相同时，给出S的增长数量级。**

*Answer：*

***
**3.1.28 有序的插入。修改BinarySearchST，使得插入一个比当前所有键都大的键只需要常数时间。**

*Answer：*

修改put()方法：

```java
public void put(Key key, Value val) {
    
    // 如果最大键比key小，此时直接将key-val插入到位置N
    if (keys[N - 1].compareTo(key) < 0) {
        // 判断是否需要resize
        if (N == keys.length) {
            resize(2 * keys.length);
        }
        keys[N] = key;
        vals[N] = val;
        N++;
        return;
    }
    
    int i = rank(key);
    if (i < N && keys[i].compareTo(key) == 0) {
        vals[i] = val;
        return;
    }

    if (N == keys.length) {
        resize(2 * keys.length);
    }

    for (int j = N; j > i; j--) {
        keys[j] = keys[j - 1];
        vals[j] = vals[j - 1];
    }
    keys[i] = key;
    vals[i] = val;
    N++;
}
```

***
**3.1.29 测试用例。编写一段测试代码TestBinarySearch.java用来测试正文中min()、max()、floor()、ceiling()、select()、rank()、deleteMin()、deleteMax()、和keys()的实现。**

*Answer：*

测试程序：

```java
public class TestBinarySearch {
    public static void main(String[] args) {
        BinarySearchST<Integer, String> st = new BinarySearchST<>(4);
        st.put(4, "A");
        st.put(2, "B");
        st.put(1, "D");
        st.put(3, "W");
        st.put(6, "T");
        System.out.println(st); // [1, D] [2, B] [3, W] [4, A] [6, T] [null, null] [null, null] [null, null] 

        System.out.println(st.min()); // 1
        System.out.println(st.max()); // 6
        
        System.out.println(st.floor(0)); // null
        System.out.println(st.floor(1)); // 1
        System.out.println(st.floor(2)); // 2
        System.out.println(st.floor(5)); // 4
        
        System.out.println(st.ceiling(3)); // 3
        System.out.println(st.ceiling(4)); // 4
        System.out.println(st.ceiling(5)); // 6
        System.out.println(st.ceiling(8)); // null
        
        System.out.println(st.select(-1)); // null
        System.out.println(st.select(3)); // 4
        System.out.println(st.select(6)); // null
        
        System.out.println(st.rank(0)); // 0
        System.out.println(st.rank(3)); // 2
        System.out.println(st.rank(6)); // 4
        System.out.println(st.rank(8)); // 5
        
        st.deleteMin();
        st.deleteMax();
        System.out.println(st); // [2, B] [3, W] [4, A] [null, null] [null, null] [null, null] [null, null] [null, null]
        
        for (Integer i : st.keys()) {
            System.out.print(i + " "); // 2 3 4 
        }
    }
}
```

BinarySearchST实现：

```java
public class BinarySearchST<Key extends Comparable<? super Key>, Value> {

    private Key[] keys;
    private Value[] vals;
    private int N;

    @SuppressWarnings("unchecked")
    public BinarySearchST(int capacity) {
        keys = (Key[]) new Comparable[capacity];
        vals = (Value[]) new Object[capacity];
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public Value get(Key key) {
        if (isEmpty()) {
            return null;
        }
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            return vals[i];
        }
        return null;
    }

    public int rank(Key key) {
        int lo = 0, hi = N - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int cmp = key.compareTo(keys[mid]);
            if (cmp < 0) {
                hi = mid - 1;
            } else if (cmp > 0) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return lo;
    }

    public void put(Key key, Value val) {
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            vals[i] = val;
            return;
        }

        if (N == keys.length) {
            resize(2 * keys.length);
        }

        for (int j = N; j > i; j--) {
            keys[j] = keys[j - 1];
            vals[j] = vals[j - 1];
        }
        keys[i] = key;
        vals[i] = val;
        N++;
    }

    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        Key[] tempk = (Key[]) new Comparable[capacity];
        Value[] tempv = (Value[]) new Object[capacity];
        for (int i = 0; i < N; i++) {
            tempk[i] = keys[i];
            tempv[i] = vals[i];
        }
        vals = tempv;
        keys = tempk;
    }

    public Key min() {
        return keys[0];
    }

    public Key max() {
        return keys[N - 1];
    }

    public Key select(int k) {
        return k < 0 || k > N - 1 ? null : keys[k];
    }

    public Key ceiling(Key key) {
        int i = rank(key);
        return i == N ? null : keys[i];
    }

    // 练习3.1.17
    public Key floor(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to floor() is null");
        }
        int i = rank(key);
        if (i < N && key.compareTo(keys[i]) == 0) {
            return keys[i];
        }
        return i == 0 ? null : keys[i - 1];
    }

    // 练习3.1.16
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        if (isEmpty()) {
            return;
        }

        // compute rank
        int i = rank(key);

        // key not in table
        if (i == N || keys[i].compareTo(key) != 0) {
            return;
        }

        for (int j = i; j < N - 1; j++) {
            keys[j] = keys[j + 1];
            vals[j] = vals[j + 1];
        }

        N--;
        keys[N] = null; // to avoid loitering
        vals[N] = null;

        // resize if 1/4 full
        if (N > 0 && N == keys.length / 4) {
            resize(keys.length / 2);
        }
    }

    public void deleteMin() {
        delete(min());
    }

    public void deleteMax() {
        delete(max());
    }

    public boolean contains(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to contains() is null");
        return get(key) != null;
    }

    public Iterable<Key> keys() {
        return keys(keys[0], keys[N - 1]);
    }

    public Iterable<Key> keys(Key lo, Key hi) {
        Queue<Key> q = new Queue<>();
        for (int i = rank(lo); i < rank(hi); i++) {
            q.enqueue(keys[i]);
        }
        if (contains(hi)) {
            q.enqueue(keys[rank(hi)]);
        }
        return q;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < keys.length; i++) {
            sb.append("[" + keys[i] + ", " + vals[i] + "] ");
        }
        return sb.toString();
    }
}
```

***
**3.1.30 验证。向BinarySearch中加入断言（assert）语句，在每次插入和删除数据后检查算法的有效性和数据结构的完整性。例如，对于每个索引必有`i==rank(select(i))`且数组应该总是有序。**

*Answer：*

略。

***

#### 实验题

***

本章实验题均为对性能的测试，暂略。

### 3.2 二叉查找树

***

#### 练习题

***
**3.2.1 将 E A S Y Q U E S T I O N 作为键按顺序插入一棵初始为空的儿茶查找树中（方便起见设第i个键对应的值为i），画出生成的二叉查找树。构造这棵树需要多少次比较。**

*Answer：*

![ex321](images\chapter03\ex321.png)

比较次数为（只统计了key之间的比较次数）：

    E : 0
    A : 1
    S : 1
    Y : 2
    Q : 2
    U : 3
    E : 1 
    S : 2
    T : 5 
    I : 4
    O : 5
    N : 6

总计：32

***
**3.2.2 将 A X C S E R H 作为键按顺序插入将会构造一颗最坏情况下的二叉查找树结构，最下方的结点的两个链接全部为空，其它结点都含有一个空链接。用这些键给出构造最坏情况下的树的其他5种排列。**

*Answer：*

1. A C E H R S X 
2. X S R H E C A 
3. X A S C R E H
4. A C E H R X S
5. X S R H E A C

***
**3.2.3 给出 A X C S E R H 的5种能够造出最优二叉查找树的排列。**

*Answer：*

1. H C S A E R X
2. H S R X C A E
3. H C A E S R X
4. H C E S R A X
5. H S R C E A X

***
**3.2.4 假设某棵二叉查找树的所有键均为1至10的整数，而我们要查找5。那么以下哪个不可能是键的检查序列。**

a. 10,9,8,7,6,5
b. 4,10,8,7,5,3
c. 1,10,2,9,3,8,4,7,6,5
d. 2,7,3,8,4,5
e. 1,2,10,4,8,5

*Answer：*

b. 检查到5停止，不可能再向下检查3。
d. 检查3后不可能再去检查8，因为3和8分别为7的左右结点。

***
**3.2.5 假设已知某棵二叉查找树中的每个结点的查找频率，且我们可以以任意顺序用它们构造一棵树。我们是应该按照查找频率的顺序由高到低或是由低到高将它们插入，还是用其他某种顺序？证明你的结论。**

*Answer：*

***
**3.2.6 为二叉查找树添加一个方法height()来计算树的高度。实现两种方案：一种使用递归，一种模仿size()在每个结点中添加一个变量。**

*Answer：*

递归实现：

```java
public int height() {
    return height(root);
}

private int height(Node<Key, Value> x) {
    return x == null ? -1 : 1 + Math.max(height(x.left), height(x.right));
}
```

添加变量实现：

略。

***
**3.2.7 为二叉查找树添加一个方法avgCompares()来计算一颗给定的树中的一次随机命中查找平均所需的比较次数（即树的内部路径长度除以树的大小再加1）。实现两种方案：一种使用递归，一种模仿size()在每个结点中添加一个变量。**

*Answer：*

***
**编写一个静态方法optCompares()接受一个整形参数N并计算一棵最优（完美平衡的）二叉查找树中的一次随机查找命中所需的比较次数，如果树中的链接数量为2的幂，那么所有的空连接都应该在同一层，否则则分布在最底部的两层中。**

*Answer：*

***
**3.2.9 对于$N=2、3、4、5$和$6$，画出用N个键可能构造出的所有不同形状的二叉查找树。**

*Answer：*

只考虑形状。

N = 2

        E           E
    E                   E

N = 3

            E       E                   E               E       E
        E               E           E       E       E               E
    E                       E                           E       E

...

实际上N个不同结点可以构造的不同二叉查找树数量为：

$$
h(N)=\frac{C_{2N}^{N}}{N+1}
$$

所以对于$N=2、3、4、5$和$6$，可以构造出的二叉查找树数量分别为：2、5、14、42、132。

***
**3.2.10 编写一个测试用例TestBST.java来测试正文中min()、max()、floor()、ceiling()、select()、rank()、delete()、deleteMin()、deleteMax()和keys()方法的实现。**

*Answer：*

```java
public class TestBST {
    public static void main(String[] args) {
        BST<Integer, String> bst = new BST<>();
        bst.put(8, "A");
        bst.put(5, "B");
        bst.put(12, "C");
        bst.put(3, "D");
        bst.put(7, "E");
        bst.put(10, "F");
        bst.put(14, "G");
        
        /*              8
         *      5               12
         *  3       7       10      14
         */
        
        Iterable<Integer> queue = bst.keys(); 
        for (Integer i : queue) {
            System.out.print(i + " "); // 3 5 7 8 10 12 14 
        }
        
        System.out.println(bst.min()); // 3
        System.out.println(bst.max()); // 14
        System.out.println(bst.floor(6)); // 5
        System.out.println(bst.floor(11)); // 10
        System.out.println(bst.floor(9)); // 8
        System.out.println(bst.floor(20)); // 14
        System.out.println(bst.floor(0)); // null
        System.out.println(bst.select(6)); // 14
        System.out.println(bst.select(1)); // 5
//      System.out.println(bst.select(-1)); // throw exception
//      System.out.println(bst.select(7)); // throw exception
        System.out.println(bst.rank(8)); // 3
        System.out.println(bst.rank(0)); // 0
        System.out.println(bst.rank(16)); // 7
        
        bst.delete(7);
        bst.delete(bst.min());
        bst.delete(bst.max());
        queue = bst.keys();
        for (Integer i : queue) {
            System.out.print(i + " "); //  5 8 10 12  
        }
        
        queue = bst.keys(7, 11);
        for (Integer i : queue) {
            System.out.print(i + " "); //  8 10   
        }
    }
}
```

***
**3.2.11 高度为N且含有N个结点的二叉树能有多少种形状？使用N个不同的键能有多少种不同的方式构造一棵高度为N的二叉查找树。**

*Answer：*

1. $2^{N-1}$。*N个结点构造出的树的最大高度应该是N-1。例如：1、2、3三个元素所能构造的最高的树的高度为2。*
2. $\frac{C_{2N}^{N}}{N+1}$

***
**3.2.12 实现一种二叉树，舍弃rank()和select()方法并且不在Node对象中使用计数器。**

*Answer：*

***
**3.2.13 为二叉查找树实现非递归的put()和get()方法。**

*Answer：*

```java
public void put(Key key, Value val) {
    if (root == null) {
        root = new Node<>(key, val);
        return;
    }
    
    Node<Key, Value> x = root;
    Node<Key, Value> parent = null;
    while (x != null) {
        int cmp = key.compareTo(x.key);
        parent = x;
        if (cmp > 0) {
            x = x.right;
        } else if (cmp < 0) {
            x = x.left;
        } else {
            x.val = val;
            return;
        }
    }
    
    int cmp = key.compareTo(parent.key);
    
    if (cmp < 0) {
        parent.left = new Node<Key, Value>(key, val);
    } else {
        parent.right = new Node<Key, Value>(key, val);
    }
}

public Value get(Key key) {
    Node<Key, Value> x = root;
    while (x != null) {
        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x = x.left;
        } else if (cmp > 0) {
            x = x.right;
        } else {
            return x.val;
        }
    }
    
    return null;
}
```

***
**3.2.14 实现非递归的min()、max()、floor()、ceiling()、rank()和select()。**

*Answer：*

```java
public Key min() {
    Node<Key, Value> x = root;
    while (x.left != null) {
        x = x.left;
    }
    return x.key;
}

public Key max() {
    Node<Key, Value> x = root;
    while (x.right != null) {
        x = x.right;
    }
    return x.key;
}

public Key floor(Key key) {
    Node<Key, Value> x = root;
    Node<Key, Value> t = null;
    while (x != null) {
        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x = x.left;
            continue;
        }
        if (cmp == 0 || x.right == null) {
            return x.key;
        }
        t = x;
        x = x.right;
    }
    return t == null ? null : t.key;
}

public Key ceiling(Key key) {
    Node<Key, Value> x = root;
    Node<Key, Value> t = null;
    while (x != null) {
        int cmp = key.compareTo(x.key);
        if (cmp > 0) {
            x = x.right;
            continue;
        }
        if (cmp == 0 || x.left == null) {
            return x.key;
        }
        t = x;
        x = x.left;
    }
    return t == null ? null : t.key;
}
```

rank()和select()暂略。

***
**3.2.15 对于右下方的二叉查找树，给出计算下列方法的过程中结点的访问序列。**

![ex3215](images\chapter03\ex3215.png)

- a. floor("Q")
- b. select(5)
- c. ceiling("Q")
- d. rank("J")
- e. size("D", "T")
- f. keys("D", "T")

*Answer：*

a. E - Q
b. E - D - Q - J
c. E - Q
d. E - D - Q - J
e. E - D - T - E - D - Q - J - T - S - E - D - A
f. E - D - D - E - Q - J - J - M - M - Q - T - S - S - T 

***
**3.2.16 设一颗树的外部路径长度为从根结点到空连接的所有路径上的结点总数。证明对于大小为N的任意二叉树，其外部路径长度和内部路径长度之差为$2N$。**

*Answer：*

内部路径长度：所有结点的深度之和；根结点到每一个结点的路径长度和。

示例：

![ex3216](images\chapter03\ex3216.png)

内部结点长度$I=0 + 1 + 1 + 2 = 4$
外部结点长度$E=2 + 2 + 3 + 3 + 2 = 12$
$E-I=12-4=8=2N$。

证明略。

***
**3.2.17 从练习3.2.1构造的二叉树中将所有键按照插入顺序逐个删除并画出每次删除所得到的树。**

*Answer：*

练习3.2.1构造的二叉树：

![ex231](images\chapter03\ex321.png)

略。

***
**3.2.18 从练习3.2.1构造的二叉树中将所有键按照字母顺序逐个删除并画出每次删除所得到的树。**

*Answer：*

略。

***
**3.2.19 从练习3.2.1构造的二叉树中次个删除根节点并画出每次删除所得到的树。**

*Answer：*

略。

***
**3.2.20 请证明：对于含有N个结点的二叉查找树，接受两个参数的size()方法所需的运行时间最多为树高的倍数加上查找范围内的键的数量。**

*Answer：*

***
**3.2.21 为二叉查找树添加一个randomKey()方法在和树高成正比的时间内从符号表中随机返回一个键。**

*Answer：*

```java
public Key randomKey() {
    return select(StdRandom.uniform(0, size() - 1));
}
```

***
**3.2.22 请证明：若一颗二叉查找树中的一个结点有两个子结点，那么它的后继结点不会有左子结点，前趋结点不会有右结点。**

*Answer：*

- 结点t的前趋结点：t的左子数（t.left）中值最大的结点
- 结点t的后继节点：t的右子树（t.right）中值最小的结点

假设后继结点具有左子结点，那么此时它将不再满足成为后继结点的条件，即值不再最小。产生矛盾，所以后继结点不存在左结点。
前趋结点同理。

***
**3.2.23 delete()方法符合交换律吗？（先删除x后删除y和先删除y后删除x能够得到相同的结果吗）**

*Answer：*

不符合。

反例：

![ex3223](images\chapter03\ex3223.png)

***
**3.2.24 请证明：使用基于比较的算法构造一颗二叉查找树所需的最小比较次数为$lg(N!)$~$NlgN$。**

*Answer：*

***

#### 提高题

***
**3.2.25 完美平衡。编写一段程序，用一组键构造一个和二分查找等价的二叉查找树。也就是或，在这棵树中查找任意键所产生的比较序列和在这组键中使用二分查找所产生的比较序列完全相同。**

*Answer：*

```java
public class PerfectBalance {

    // precondition: a[] has no duplicates
    private static <T extends Comparable<? super T>> void perfect(BST<T, Integer> bst, T[] a) {
        Arrays.sort(a); // 对数组进行排序
        perfect(bst, a, 0, a.length - 1);
    }

    // precondition: a[lo..hi] is sorted
    private static <T extends Comparable<? super T>> void perfect(BST<T, Integer> bst, T[] a, int lo, int hi) {
        if (hi < lo) {
            return;
        }
        Integer mid = lo + (hi - lo) / 2;
        bst.put(a[mid], mid);
        perfect(bst, a, lo, mid - 1);
        perfect(bst, a, mid + 1, hi);
    }

    public static void main(String[] args) {
        String[] words = StdIn.readAllStrings();
        BST<String, Integer> bst = new BST<>();
        perfect(bst, words);
    }
}
```

***
**3.2.26 准确的概率。计算用N个随机的互补相同的键构造出练习3.2.9中的每一棵树的概率。**

*Answer：*

***
**3.2.27 内存使用。基于1.4节的假设，对于N对键值比较二叉查找树和BinarySearchST以及SequentialSearch的内存使用情况。不需要记录键值本身占用的内存，只统计它们的引用。**

*Answer：*

***
**3.2.28 缓存。修改二叉查找树的实现，将最近访问的结点Node保存在一个变量中，这样get()或put()再次访问同一个键时就只需要常数时间了。**

*Answer：*

增加成员变量lastAcc来记录上一次被put()、get()访问的结点。修改了put()、get()、delete()、deleteMin()、deleteMax()以及contains()方法：

```java
// 成员变量
private Node<Key, Value> lastAcc;

// contains
public boolean contains(Key key) {
    
    if (key == null) {
        throw new IllegalArgumentException("argument to contains() is null");
    }
    
    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        return true;
    }
    
    return get(key) != null;
}

// get
public Value get(Key key) {
    // 如果缓存命中，则从缓存读取
    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        return lastAcc.val;
    }
    return get(root, key);
}

// 递归查找key对应的值
private Value get(Node<Key, Value> x, Key key) {

    if (x == null) {
        return null;
    }

    lastAcc = x;

    int cmp = key.compareTo(x.key);

    if (cmp < 0) {
        return get(x.left, key);
    } else if (cmp > 0) {
        return get(x.right, key);
    } else {
        return x.val;
    }
}

// put
public void put(Key key, Value val) {
    
    // 如果缓存命中，则直接存入缓存
    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        lastAcc.val = val;
        return;
    }
    
    root = put(root, key, val);
}

private Node<Key, Value> put(Node<Key, Value> x, Key key, Value val) {

    // 如果结点为null，则创建一个新结点
    if (x == null) {
        Node<Key, Value> node = new Node<>(key, val, 1);
        lastAcc = node;
        return node;
    }

    lastAcc = x;

    int cmp = key.compareTo(x.key);

    if (cmp < 0) {
        x.left = put(x.left, key, val);
    } else if (cmp > 0) {
        x.right = put(x.right, key, val);
    } else {
        x.val = val;
    }

    // 每次插入或更新后更新当前结点的结束器
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}

// deleteMin
public void deleteMin() {
    if (isEmpty()) {
        throw new NoSuchElementException("Symbol table underflow");
    }
    root = deleteMin(root);
}

private Node<Key, Value> deleteMin(Node<Key, Value> x) {
    if (x.left == null) {
        if (x == lastAcc) {
            lastAcc = null;
        }
        return x.right;
    }
    x.left = deleteMin(x.left);
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}

// deleteMax
public void deleteMax() {
    if (isEmpty()) {
        throw new NoSuchElementException("Symbol table underflow");
    }
    root = deleteMax(root);
}

private Node<Key, Value> deleteMax(Node<Key, Value> x) {
    if (x.right == null) {
        if (x == lastAcc) {
            lastAcc = null;
        }
        return x.left;
    }
    x.right = deleteMax(x.right);
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}

// delete
public void delete(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("calls delete() with a null key");
    }

    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        lastAcc = null;
    }

    root = delete(root, key);
}

private Node<Key, Value> delete(Node<Key, Value> x, Key key) {
    if (x == null) {
        return null;
    }

    int cmp = key.compareTo(x.key);
    if (cmp < 0) {
        x.left = delete(x.left, key);
    } else if (cmp > 0) {
        x.right = delete(x.right, key);
    } else {
        if (x.right == null) {
            return x.left;
        }
        if (x.left == null) {
            return x.right;
        }
        Node<Key, Value> t = x;
        x = min(t.right);
        x.right = deleteMin(t.right);
        x.left = t.left;
    }
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

***
**3.2.29 二叉树检查。编写一个递归的方法isBinaryTree()，接受一个结点Node作为参数。如果以该结点为根的子树中的结点总数和计数器的值N相符则返回true，否则返回false。注意：这项检查也能保证数据结构中不存在环，因此这的确是一颗二叉树。**

*Answer：*

```java
private boolean isBinaryTree(Node<Key, Value> x) {
    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }

    if (x.right == null && x.left == null) { // 如果x没有左右结点
        return x.N == 1;
    } else if (x.right != null && x.left != null) { // 如果x同时具有左右结点，则结点数量为1+左子树结点数+右子数结点数
        return x.N != x.left.N + x.right.N + 1 ? false : isBinaryTree(x.left) && isBinaryTree(x.right);
    } else { // x结点只有一个子树
        Node<Key, Value> t = x.left == null ? x.right : x.left;
        return x.N != t.N + 1 ? false : isBinaryTree(t);
    }
}
```

***
**3.2.30 有序性检查。编写一个递归的方法isOrdered()，接受一个结点Node和min、max两个键作为参数。如果以该节点为根的子树种的所有结点都在min、max之间，min和max的确分别时树中的最小和最大结点且二叉查找树的有序性对树中所有键都成立，返回true，否则返回false。**

*Answer：*

```java
private boolean isOrdered(Node<Key, Value> x, Key min, Key max) {

    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }
    if (min.compareTo(max) > 0) {
        throw new IllegalArgumentException("min can not be greater than max...");
    }

    // 根结点不在[min, max]范围，直接返回false
    if (min.compareTo(x.key) > 0 || max.compareTo(x.key) < 0) {
        return false;
    }

    if (x.left == null && x.right == null) { // 如果x的左右结点均为null
        return true;
    } else if (x.left != null && x.right != null) { // 如果x的左右结点均不为null
        if (x.left.key.compareTo(x.key) > 0 || x.right.key.compareTo(x.key) < 0) { // 有序性检查
            return false;
        }
        return isOrdered(x.left, min, max) && isOrdered(x.right, min, max);
    } else { // x的左右结点其中之一为null
        if (x.left == null) { // 如果左结点为空
            if (x.right.key.compareTo(x.key) < 0) { // 如果右结点key小于x.key
                return false;
            }
            x = x.right;
        } else { // 如果右结点为空
            if (x.left.key.compareTo(x.key) > 0) { // 如果右结点key大于x.key
                return false;
            }
            x = x.left;
        }
        return isOrdered(x, min, max);
    }
}
```

***
**3.2.31 等值键检查。编写一个方法hasNoDuplicates()，接受一个结点Node为参数。如果以该结点为根的二叉查找树不含有等值的键则返回true，否则返回false。假设数已经通过了前几到练习的检查。**

*Answer：*

```java
private boolean hasNoDuplicates(Node<Key, Value> x) {

    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }
    
    return hasNoDuplicates(x, new TreeSet<Key>());
}

private boolean hasNoDuplicates(Node<Key, Value> x, Set<Key> set) {
    
    // 判断set中是否存在x.key
    if (set.contains(x.key)) {
        return false; // 如果存在，说明有重复键
    }
    set.add(x.key); // 如果不存在，将x.key添加进set
    
    if (x.left == null && x.right == null) { // 如果x结点没有左右结点，返回true
        return true;
    } else if (x.left != null && x.right != null) { // 如果x结点具有左右两个子结点
        return hasNoDuplicates(x.left, set) && hasNoDuplicates(x.right, set); 
    } else {
        // 结点x只有一个子结点
        x = x.left == null ? x.right : x.left;
        return hasNoDuplicates(x, set); 
    }
}
```

***
**3.2.32 验证。编写一个方法isBST()，接受一个结点Node作为参数。若该结点是一个二叉树的根节点则返回true，否则返回false。**

```java
private boolean isBST() {
    if (!isBinaryTree(root)) {
        return false;
    }
    if (!isOrdered(root, min(), max())) {
        return false;
    }
    if (!hasNoDuplicates(root)) {
        return false;
    }
    return true;
}
```

进行一定修改后的测试示例：

```java
public class TestisBST<Key extends Comparable<? super Key>, Value> {

    // Node类
    private static class Node<Key, Value> {
        private Key key; // 键
        @SuppressWarnings("unused")
        private Value val; // 值
        private Node<Key, Value> left, right; // 指向子树的链接
        private int N; // 以该结点为根的子树中的结点总数

        public Node(Key key, Value val, int n) {
            this.key = key;
            this.val = val;
            N = n;
        }
    }
    
    /****************************************************
     ******************** check *************************
     ****************************************************/

    // 二叉树检查
    private boolean isBinaryTree(Node<Key, Value> x) {
        if (x == null) {
            throw new IllegalArgumentException("Node x can not be null...");
        }

        if (x.right == null && x.left == null) { // 如果x没有左右结点
            return x.N == 1;
        } else if (x.right != null && x.left != null) { // 如果x同时具有左右结点，则结点数量为1+左子树结点数+右子数结点数
            return x.N != x.left.N + x.right.N + 1 ? false : isBinaryTree(x.left) && isBinaryTree(x.right);
        } else { // x结点只有一个子树
            Node<Key, Value> t = x.left == null ? x.right : x.left;
            return x.N != t.N + 1 ? false : isBinaryTree(t);
        }
    }

    // 有序性检查
    private boolean isOrdered(Node<Key, Value> x, Key min, Key max) {

        if (x == null) {
            throw new IllegalArgumentException("Node x can not be null...");
        }
        if (min.compareTo(max) > 0) {
            throw new IllegalArgumentException("min can not be greater than max...");
        }

        // 根结点不在[min, max]范围，直接返回false
        if (min.compareTo(x.key) > 0 || max.compareTo(x.key) < 0) {
            return false;
        }

        if (x.left == null && x.right == null) { // 如果x的左右结点均为null
            return true;
        } else if (x.left != null && x.right != null) { // 如果x的左右结点均不为null
            if (x.left.key.compareTo(x.key) > 0 || x.right.key.compareTo(x.key) < 0) { // 有序性检查
                return false;
            }
            return isOrdered(x.left, min, max) && isOrdered(x.right, min, max);
        } else { // x的左右结点其中之一为null
            if (x.left == null) { // 如果左结点为空
                if (x.right.key.compareTo(x.key) < 0) { // 如果右结点key小于x.key
                    return false;
                }
                x = x.right;
            } else { // 如果右结点为空
                if (x.left.key.compareTo(x.key) > 0) { // 如果右结点key大于x.key
                    return false;
                }
                x = x.left;
            }
            return isOrdered(x, min, max);
        }
    }

    // 等值键检查
    private boolean hasNoDuplicates(Node<Key, Value> x) {

        if (x == null) {
            throw new IllegalArgumentException("Node x can not be null...");
        }

        return hasNoDuplicates(x, new TreeSet<Key>());
    }

    private boolean hasNoDuplicates(Node<Key, Value> x, Set<Key> set) {

        // 判断set中是否存在x.key
        if (set.contains(x.key)) {
            return false; // 如果存在，说明有重复键
        }
        set.add(x.key); // 如果不存在，将x.key添加进set

        if (x.left == null && x.right == null) { // 如果x结点没有左右结点，返回true
            return true;
        } else if (x.left != null && x.right != null) { // 如果x结点具有左右两个子结点
            return hasNoDuplicates(x.left, set) && hasNoDuplicates(x.right, set);
        } else {
            // 结点x只有一个子结点
            x = x.left == null ? x.right : x.left;
            return hasNoDuplicates(x, set);
        }
    }

    // 验证
    private boolean isBST(Node<Key, Value> x, Key min, Key max) {
        if (!isBinaryTree(x)) {
            System.out.println("二叉树检查未通过...");
            return false;
        }
        if (!isOrdered(x, min, max)) {
            System.out.println("有序性检查未通过...");
            return false;
        }
        if (!hasNoDuplicates(x)) {
            System.out.println("等值键检查未通过...");
            return false;
        }
        System.out.println("测试通过...");
        return true;
    }

    public static void main(String[] args) {

        /****************************************************
         ******************** 测 试 ***************************
         ****************************************************/

        /*
         * 创建结点
         */
        Node<Integer, Character> root = new Node<>(20, '*', 10);
        Node<Integer, Character> nodeA = new Node<>(10, 'A', 5);
        Node<Integer, Character> nodeB = new Node<>(30, 'B', 4);
        Node<Integer, Character> nodeC = new Node<>(7, 'C', 1);
        Node<Integer, Character> nodeD = new Node<>(15, 'D', 3);
        Node<Integer, Character> nodeE = new Node<>(25, 'E', 1);
        Node<Integer, Character> nodeF = new Node<>(40, 'F', 2);
        Node<Integer, Character> nodeG = new Node<>(12, 'G', 1);
        Node<Integer, Character> nodeH = new Node<>(17, 'H', 1);
        Node<Integer, Character> nodeI = new Node<>(45, 'I', 1);

        /*
         * 创建链接
         */
        root.left = nodeA;
        root.right = nodeB;

        nodeA.left = nodeC;
        nodeA.right = nodeD;

        nodeB.left = nodeE;
        nodeB.right = nodeF;

        nodeD.left = nodeG;
        nodeD.right = nodeH;

        nodeF.right = nodeI;

        /*
         * 调整结点root~nodeI的key和计数值来进行测试
         */

        new TestisBST<Integer, Character>().isBST(root, 0, 50);
    }
}
```

***
**3.2.33 选择/排名检查。编写一个方法，对于0到size()-1之间所有的i检查i和rank(select(i))是否相等，并检查二叉查找树中的任意键key和select(rank(key))是否相等。**

*Answer：*

```java
public boolean selectRankCheck() {
    
    // select检查：检查i和rank(select(i))是否相等
    int N = size();
    for (int i = 0; i < N; i++) {
        if (i != rank(select(i))) {
            System.out.println("select()检查失败...");
            return false;
        }
    }
    
    // rank检查：检查任意key和select(rank(key))是否相等
    for (Key elem : keys()) {
        if (elem.compareTo(select(rank(elem))) != 0) {
            System.out.println("rank()检查失败...");
            return false;
        }
    }
    
    return true;
}
```

***
**3.2.34 线性符号表。你的目标是实现一个扩展的符号表ThreadedST，支持以下两个运行时间为常数的操作：**

- Key next(Key key)，key的下一个键（若key为最大键则返回空）
- Key prev(Key key)，key的上一个键（若key为最小键则返回空）

**要做到这一点需要在结点中增加pred和succ两个变量来保存结点的前趋和后继结点，并相应修改put()、deleteMin()、deleteMax()和delete()方法来维护这两个变量。**

*Answer：*

***
**3.2.35 改进的分析。为了更好地解释正文表格中的试验结果请改进它的数学模型。证明随着N的增大，在一棵随机构造的二叉查找树中，一次命中查找所需的平均比较次数会趋近于$2lnN+2\gamma-3\approx 1.39lgN-1.85$，其中$\gamma=0.57721...$，即欧拉常数。**

*Answer：*

***
**3.2.36 迭代器。能否实现一个非递归版本的keys()方法，其使用额外空间和树的高度成正比（和查找范围内的键的多少无关）？**

*Answer：*

```java
public Iterable<Key> keys() {
    
    Stack<Node<Key, Value>> stack = new Stack<>();
    Queue<Key> queue = new Queue<>();
    
    Node<Key, Value> x = root;
    while (x != null || !stack.isEmpty()) {
        while (x != null) {
            stack.push(x);
            x = x.left;
        }
        if (!stack.isEmpty()) {
            x = stack.pop();
            queue.enqueue(x.key);
            x = x.right;
        }
    }
    return queue;
}
```

***
**3.2.37 按层遍历。编写一个方法printLevel()，接受一个结点Node作为参数，按照层级顺序打印以该结点为根的子树（即按每个结点到根结点的距离的顺序，同一层的结点应该按从左至右的顺序）。**

*Answer：*

```java
public void printLevel() {
    printLevel(root);
}

public void printLevel(Node<Key, Value> x) {
    if (x == null) {
        throw new IllegalArgumentException("As a root Node, x can not be null...");
    } 
    Queue<Node<Key, Value>> queue = new Queue<>();
    queue.enqueue(x);
    while (!queue.isEmpty()) {
        Node<Key, Value> t = queue.dequeue();
        System.out.print(t.key + " ");
        if (t.left != null) {
            queue.enqueue(t.left);
        }
        if (t.right != null) {
            queue.enqueue(t.right);
        }
    }
}
```

***
**3.2.28 绘图。为二叉查找树添加一个方法draw()，按照正文的样式将树绘制出来。提示：将结点中用变量保存坐标并用递归的方法设置这些变量。**

*Answer：*

***

#### 实验题

***
**3.2.39 平均情况.用经验数据评估在一棵由N个随机结点构造的二叉查找树中，一次命中查找和未命中查找中所需的比较次数的平均差和标准差，其中$N=10^4、10^5、10^6$，重复实验100遍。将你的实验结果和练习3.2.35给出的平均比较次数的公式进行对比。**

*Answer：*

***
**3.2.40 树的高度。用经验数据评估一棵由N个随机结点构造的二叉查找树的平均高度，其中$N=10^4、10^5$和$10^6$，重复实验100遍。将你的结果和正文中给出的估计值$2.99lgN$进行对比。**

*Answer：*

（这个$2.99lgN$应该是以2为底的对数，即$2.99log_{2}N$）

```java
public class Ex3240 {
    
    public static void main(String[] args) {

        BST<Integer, Integer> bst = null;

        int height = 0;

        // N分别为10^4、10^5、10^6
        for (int N = 10000; N <= 1000000; N *= 10) {
            
            // 100次实验
            for (int t = 0; t < 100; t++) {
                bst = new BST<>();
                while (bst.size() != N) { // bst含有N个结点
                    int i = StdRandom.uniform(3 * N);
                    bst.put(i, i);
                }
                height += bst.height();
            }

            System.out.println("For N = " + N);
            System.out.println("Average BST height = " + (double) height / 100);
            System.out.println("2.99logN = " + 2.99 * Math.log(N) / Math.log(2) + "\n");
        }
    }
}
```

*结论匹配的并不好。*

***
**3.2.41 数组表示。开发一个二叉查找树的实现，用三个数组表示一棵树（预先分配为构造函数中所指定的最大长度）：一个数组用来保存键，一个数组用来保存做链接的索引，一个数组用来保存右链接的索引。比较你的程序和标准实现的性能。**

*Answer：*

只是先了put()和get()方法：

```java
public class ThreeArraysBST<Key extends Comparable<? super Key>, Value> {
    
    private Tuple<Key, Value>[] tuples;
    private Integer[] lefts;
    private Integer[] rights;

    private int N; // 记录键的数量
    
    // 定义一个元组来保存键值对
    private static class Tuple<Key, Value> {
        private Key key;
        private Value val;
        
        public Tuple(Key key, Value val) {
            this.key = key;
            this.val = val;
        }
        
        @Override
        public String toString() {
            return "[" + key + ", " + val + "]";
        }
    }
    
    @SuppressWarnings("unchecked")
    public ThreeArraysBST(int capacity) {
        if (capacity <= 0) {
            throw new IllegalArgumentException("Illegal capacity...");
        }
        tuples = new Tuple[capacity];
        lefts = new Integer[capacity];
        rights = new Integer[capacity];
        N = 0;
    }

    public void put(Key key, Value val) {

        if (N == tuples.length) {
            throw new UnsupportedOperationException("BST Overflow...");
        }

        if (N == 0) {
            tuples[N++] = new Tuple<>(key, val);
            return;
        }

        int i = 0;
        while (true) {
            int cmp = key.compareTo(tuples[i].key);
            if (cmp < 0) {
                if (lefts[i] == null) {
                    lefts[i] = N;
                    break;
                }
                i = lefts[i];
            } else if (cmp > 0) {
                if (rights[i] == null) {
                    rights[i] = N;
                    break;
                }
                i = rights[i];
            } else {
                tuples[i].val = val;
                return;
            }
        }
        tuples[N++] = new Tuple<>(key, val);
    }
    
    public Value get(Key key) {
        // 遍历keys数组
        for (int i = 0; i < N; i++) {
            if (key.compareTo(tuples[i].key) == 0) {
                return tuples[i].val;
            }
        }
        return null;
    }
    
    public static void main(String[] args) {
        ThreeArraysBST<Integer, Character> tabst = new ThreeArraysBST<>(10);
        tabst.put(5, 'A');
        tabst.put(3, 'B');
        tabst.put(6, 'C');
        tabst.put(1, 'D');
        tabst.put(9, 'E');
        tabst.put(7, 'F');
        tabst.put(4, 'G');
        tabst.put(3, 'Z');

        for (Tuple<Integer, Character> i : tabst.tuples) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (Integer i : tabst.lefts) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (Integer i : tabst.rights) {
            System.out.print(i + " ");
        }
    }
}
```

测试结果：

树的结构：

                5
        3           6
    1       4               9
                       7

各个数组值如下：

    idx:       0      1      2      3      4      5     6     7    8    9
    tuples: [5, A] [3, Z] [6, C] [1, D] [9, E] [7, F] [4, G] null null null 
    lefts:     1      3    null   null     5    null   null  null null null 
    rigths:    2      6      4    null   null   null   null  null null null 

***
**3.2.42 Hibbard删除方法的性能问题。编写一个程序，从命令行接受一个参数N并构造一棵由N个随机键生成的二叉查找树，然后进入一个循环。在循环中它先删除一个随机键（`delete(select(StdRandom.uniform(N)))`），然后再插入一个随机键，如此循环$N^2$次。循环结束后，计算并打印树的内部平均路径长度（内部路径长度除以N再加一）。对于$N=10^2、10^3$和$10^4$，运行你的程序来验证一个有些违反直觉的假设：这个过程会增加树的平均路径长度，增加的长度和N的平方根成正比。使用能够随机选择前趋或后继结点的delete()方法重复这个实验。**

*Answer：*

实现能够随机选择前趋或后继结点的delete()方法：

```java
public void delete(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("calls delete() with a null key");
    }
    root = delete(root, key);
}

private Node<Key, Value> delete(Node<Key, Value> x, Key key) {
    if (x == null) {
        return null;
    }

    int cmp = key.compareTo(x.key);
    if (cmp < 0) {
        x.left = delete(x.left, key);
    } else if (cmp > 0) {
        x.right = delete(x.right, key);
    } else {
        if (x.right == null) {
            return x.left;
        }
        if (x.left == null) {
            return x.right;
        }

        Node<Key, Value> t = x;
        // 进行随机选择
        if (StdRandom.bernoulli()) {
            x = min(t.right); // 选择后继结点
            x.right = deleteMin(t.right);
            x.left = t.left;
        } else {
            x = max(t.left); // 选择前趋结点
            x.left = deleteMax(t.left);
            x.right = t.right;
        }

    }
    x.N = size(x.left) + size(x.right) + 1;
    return x;
}
```

计算内部路径长度的方法：

```java
public int internalPathLength() {
    if (root == null) {
        throw new IllegalArgumentException();
    }
    return internalPathLength(root, 0);
}

private int internalPathLength(Node<Key, Value> x, int i) {
    if (x == null) {
        return 0;
    }
    int path = i;
    path += internalPathLength(x.left, i + 1);
    path += internalPathLength(x.right, i + 1);
    return path;
}
```

实验：

```java
public static void main(String[] args) {
    BSTEx3242<Integer, Integer> bst = new BSTEx3242<>();

    int N = 1000;
    int T = N * N;
    int i = 0;
    
    // 插入N个随机元素
    while (bst.size() != N) {
        i = StdRandom.uniform(T);
        bst.put(i, i);
    }
    
    // N^2次删除插入前的内部平均路径长度
    System.out.println((double) bst.internalPathLength() / N + 1);

    // 进行N^2次删除插入
    for (int t = 0; t < T; t++) {
        bst.delete(bst.select(StdRandom.uniform(N)));
        while (bst.size() != N) {
            i = StdRandom.uniform(T);
            bst.put(i, i);
        }
    }
    
    // N^2次删除插入后的内部平均路径长度
    System.out.println((double) bst.internalPathLength() / N + 1);
}
```

*结果并不理想，删除插入的过程减小了平均路径长度。*

***
**3.2.43 put()/get()方法比例。用经验数据评估当使用FrequencyCounter来统计100万个随机整数中每个数的出现频率时，二叉查找树put()方法和get()方法所消耗的时间的比例。**

*Answer：*

***
**3.2.44 绘制成本图。改造二叉查找树的实现来绘制本节所示的哪种能够显示计算中每次put()操作成本的图。**

*Answer：*

略。

***
**3.2.45 实际耗时。改造FrequencyCounter，使用Stopwatch和StdDraw绘图，其中x轴表示get()和put()调用的总数，y轴为总运行时间，每次调用之后即再当前运行时间处绘制一个点。使用SequentialSearchST和你的程序处理《双城记》，再用BinarySearchST处理一遍，最后用二叉查找树处理一边，然后讨论运行的结果。**

*Answer：*

略。

***
**3.2.46 二叉查找树的临界点。使用随机double值作为键，分别找出使得二叉查找树的符号表比二分查找要快10、100倍和1000倍的N值。分析并预测N的大小并通过实验验证它。**

*Answer：*

略。

***
**3.2.47 平均查找耗时。用实验研究和计算在一棵由N个随机结点构造的二叉查找树中到达任意结点的平均路径长度（内部路径长度除以N再加1）的平均差和标准差，对于100到10000之间的每个N重复实验1000遍。将结果绘制成和图3.2.14相似的一张Tufte图（p268），并画上函数$1.39lgN-1.85$的曲线。**

*Answer：*

*略*


### 3.3 平衡查找树

***

#### 练习题

***
**3.3.1 将键E A S Y Q U T I O N按顺序插入一棵空2-3树并画出结果。**

*Answer：*

![ex331](images\chapter03\ex331.png)

***
**3.3.2 将键Y L P M X H C R A E S按顺序插入一棵空2-3树并画出结果。**

*Answer：*

![ex332](images\chapter03\ex332.png)

***
**3.3.3 使用什么顺序插入键S E A C H X M能够得到一棵高度为1的2-3树。**

*Answer：*

高度为1的2-3树：

![ex333](images\chapter03\ex333.png)

插入顺序：M H X S A E C 

***
**3.3.4 证明含有$N$个键的2-3树的高度在~$\lfloor log_3N\rfloor$即$0.63lgN$（树完全由三结点组成）和~$\lfloor lgN\rfloor$（树完全由2结点组成）之间。**

*Answer：*

略。

***
**3.3.5 右图显示了$N=1$到$6$之间大小为N的素偶不同的2-3树（无先后次序）。请画出$N=7、8、9$和$10$的大小为N的所有不同2-3树。**

*Answer：*

以$N=7$为例，此时树的高度在1-2之间。

所以图如下：

![ex335](images\chapter03\ex335.png)

其余略。

***
**3.3.6 计算用N个随机键构造练习3.3.5中每棵2-3树的概率。**

*Answer：*

***
**3.3.7 以图3.3.9为例为图3.3.8中的其他5种情况画出相应的示意图。**

*Answer：*

略。

***
**3.3.8 画出使用三个2-结点和红连接一起表示一个4-结点的所有可能方法（不一定只能使用红色左链接）。**

*Answer：*

略。

***
**3.3.9 下图中哪些是红黑树。**

虚线表示红连接。

![ex339](images\chapter03\ex339.png)

只有（iii）和（iv）是红黑树。

***
**3.3.10 将含有键E A S Y Q U T I O N的结点按照顺序插入一棵红黑树并画出结果。**

*Answer：*

虚线表示红连接。

![ex3310](images\chapter03\ex3310.png)

***
**3.3.11 将含有键Y L P M X H C R A E S的结点按照顺序插入一棵红黑树并画出结果。**

*Answer：*

![ex3311](images\chapter03\ex3311.png)

***
**3.3.12 在我们的标准索引测试用例中插入键P并画出插入过程中每次变换（颜色转换或是旋转）后的红黑树。**

标准测试用例生成的红黑树（插入键P之前）：

![ex3312_std](images\chapter03\ex3312_std.png)

*Answer：*

![ex3312_insertP](images\chapter03\ex3312_insertP.png)

***
**3.3.13 真假判断：如果你按照升序将键顺序插入一棵红黑树，树的高度是单调递增的。**

*Answer：*

```java
public class Ex3313 {
    public static void main(String[] args) {
        RedBlackBST<Integer, Integer> rbbst = new RedBlackBST<>();
        Integer[] ints = new Integer[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
        for (Integer i : ints) {
            rbbst.put(i, 0);
            System.out.print(rbbst.height() + " ");
        }
    }
}
```

结果：

    0 1 1 2 2 2 2 3 3 3 

***
**3.3.14 用字母A到K按顺序构造一棵红黑树并画出结果，然后大致说明在按照升序插入键来构造一棵红黑树的过程中发生了什么。**

*Answer：*

插入结果：

![ex3314](images\chapter03\ex3314.png)

- 当红黑树包含结点且加点个数为奇数时，再进行插入操作会进行rotateLeft()操作
- 当红黑树包含结点且加点个数为偶数时，再进行插入操作会进行flipColors()、rotateLeft()操作

***
**3.3.15 再键按照降序插入红黑树的情况下重新回答上面两道练习。**

*Answer：*

略。

***
**3.3.16 向右图所示的红黑树中插入n并画出结果（图中只显示了插入时的查找路径）。**

![ex3315QUE](images\chapter03\ex3315QUE.png)

*Answer：*

![ex3315ANS](images\chapter03\ex3315ANS.png)

***
**3.3.17 随机生成两棵均含有16个结点的红黑树。画出它们并将它们和使用同一组键构造的二叉查找树进行比较。**

*Answer：*

略。

***
**3.3.18 对于2到10之间的N，画出所有大小为N的不同红黑树（请参考练习3.3.5）。**

*Answer：*

略。

***
**3.3.19 每个结点只需要1位来保存结点的颜色即可表示2-结点、3-结点和4-结点。使用二叉树，我们在每个结点需要几位信息才能表示5-结点、6-结点、7结点和8-结点？**

*Answer：*

***
**3.3.20 计算一棵大小位N且完美平衡的二叉树的内部路径长度，其中N为2的幂减1。**

*Answer：*

N为2的幂减1；N结点的满二叉树，高度$h=log_2^{N+1}-1$。

示例：

- $2^1 - 1 = N = 1$，此时$h=0$，内部路径长度$L=0$。
- $2^2 - 1 = N = 3$，此时$h=1$，内部路径长度$L=2$。
- $2^3 - 1 = N = 7$，此时$h=2$，内部路径长度$L=10$。
- $2^4 - 1 = N = 15$，此时$h=3$，内部路径长度$L=34$。
- ...
- $2^k - 1 = N$，此时$h=k-1$，内部路径长度$L=\sum_{i=k-1}^{1} (i·2^i)=(k-2)2^k+2$。

最终得：$L=(log_2^{N+1}-2)(N+1)+2$

***
**3.3.21 基于你为练习3.2.10给出得答案编写一个测试用例TestRB.java。**

*Answer：*

测试min()、max()、floor()、ceiling()、select()、rank()、delete()、deleteMin()、deleteMax()和keys()方法。

使用练习3.3.11得到的红黑树：顺序插入Y L P M X H C R A E S

![ex3311](images\chapter03\ex3311.png)

```java
public class TestRB {
    public static void main(String[] args) {
        RedBlackBST<String, Integer> rbbst = new RedBlackBST<>();
        String[] strings = "Y L P M X H C R A E S".split(" ");
        for (String s : strings) {
            rbbst.put(s, 0);
        }
        
        Iterable<String> queue = rbbst.keys(); 
        for (String s : queue) {
            System.out.print(s + " "); // A C E H L M P R S X Y 
        }
        System.out.println();
        
        System.out.println(rbbst.min()); // A
        System.out.println(rbbst.max()); // Y
        System.out.println(rbbst.floor("Z")); // Y
        System.out.println(rbbst.floor("B")); // A
        System.out.println(rbbst.ceiling("D")); // E
        System.out.println(rbbst.ceiling("Q")); // R
        System.out.println(rbbst.select(3)); // H
        System.out.println(rbbst.rank("T")); // 9
        
        rbbst.delete("M");
        queue = rbbst.keys(); 
        for (String s : queue) {
            System.out.print(s + " "); // A C E H L P R S X Y 
        }
        System.out.println();
        
        rbbst.deleteMin();
        queue = rbbst.keys(); 
        for (String s : queue) {
            System.out.print(s + " "); // C E H L P R S X Y 
        }
        System.out.println();
        
        rbbst.deleteMax();
        queue = rbbst.keys(); 
        for (String s : queue) {
            System.out.print(s + " "); // C E H L P R S X
        }
        System.out.println();
    }
}
```

***
**3.3.22 找出一组键的序列使得用它顺序构造的二叉查找树比用它构造的红黑树的高度更低，或者证明这样的序列不存在。**

*Answer：*

这样的序列应该不存在。

***

#### 提高题

***
**3.3.23 没有平衡限制的2-3树。使用2-3树（不一定平衡）作为数据结构实现符号表的基本API。树中的三结点中的红链接可以左斜也可以右斜。树底部的3-结点和新结点通过黑色链接相连。实验并估计随机构造的这样一棵大小为N的的树的平均路径长度。**

*Answer：*

在红黑树的基础上改造，只实现了put()方法。

由于这里“三结点中的红链接可以左斜也可以右斜”说明此时红黑树允许右链接出现红色。
“树底部的3-结点和新结点通过黑色链接相连”，即需要判断树底部的结点是2-结点还是3-结点。

不再需要旋转操作，因为根据上述描述，不会出现某个结点具有两条红链接。但此时树不再平衡

```java
public class RedBlackBSTEx3323<Key extends Comparable<? super Key>, Value> {

    private static final boolean RED = true;
    private static final boolean BLACK = false;

    private Node<Key, Value> root; // 红黑树的根结点

    private static class Node<Key, Value> {

        private Key key; // 键
        private Value val; // 值
        private Node<Key, Value> left, right; // 左、右链接
        private boolean color; // 父结点的指向这个结点的链接的颜色
        private int size; // 这颗子树的结点总数

        public Node(Key key, Value val, boolean color, int size) {
            this.key = key;
            this.val = val;
            this.color = color;
            this.size = size;
        }

        // 测试用toString()
        @Override
        public String toString() {
            return "Node [key=" + key + ", val=" + val + ", color=" + color + ", size=" + size + "]";
        }
    }

    /*
     * 存入键值对key-val
     */
    public void put(Key key, Value val) {

        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        root = put(root, key, val, null);
        root.color = BLACK; // root结点的颜色一定是黑色
    }

    private Node<Key, Value> put(Node<Key, Value> h, Key key, Value val, Node<Key, Value> parent) {

        if (h == null) {

            // 针对特殊情况root结点
            if (parent == null) {
                return new Node<>(key, val, RED, 1);
            }

            // parent是三结点的其中一个结点，用黑链接连接新结点
            if (isRed(parent.left) || isRed(parent.right) || isRed(parent)) {
                return new Node<>(key, val, BLACK, 1);
            } else {
                return new Node<>(key, val, RED, 1);
            }
        }

        int cmp = key.compareTo(h.key);
        if (cmp < 0) {
            h.left = put(h.left, key, val, h);
        } else if (cmp > 0) {
            h.right = put(h.right, key, val, h);
        } else {
            h.val = val;
        }

        h.size = size(h.left) + size(h.right) + 1;

        return h;
    }

    /*
     * 判断结点x的颜色
     */
    private boolean isRed(Node<Key, Value> x) {
        return x == null ? false : x.color == RED;
    }

    /*****************************************************************
     *********** 以下方法不涉及结点的颜色，与二叉查找树实现相同***************
     ****************************************************************/

    /*
     * 返回这颗红黑树的结点总数
     */
    public int size() {
        return size(root);
    }

    private int size(Node<Key, Value> x) {
        return x == null ? 0 : x.size;
    }

    /*
     * 计算内部路径长度
     */
    public int internalPathLength() {
        if (root == null) {
            throw new IllegalArgumentException();
        }
        return internalPathLength(root, 0);
    }

    private int internalPathLength(Node<Key, Value> x, int i) {
        if (x == null) {
            return 0;
        }
        int path = i;
        path += internalPathLength(x.left, i + 1);
        path += internalPathLength(x.right, i + 1);
        return path;
    }

    public static void main(String[] args) {
        RedBlackBSTEx3323<Integer, Integer> rbbst = null;
        int N = 0;
        int L = 0;
        
        for (int i = 10; i <= 100000; i *= 10) { 
            rbbst = new RedBlackBSTEx3323<>();
            
            // 存入随机键
            while (rbbst.size() != i) {
                int r = StdRandom.uniform(100000);
                rbbst.put(r, r);
            }
            
            N = rbbst.size();
            L = rbbst.internalPathLength();
            System.out.println("N = " + N + ", L = " + L + ", avg = " + (double)L / N + 1);
        }
    }
}
```

结果：

    N = 10, L = 21, avg = 2.11
    N = 100, L = 636, avg = 6.361
    N = 1000, L = 11585, avg = 11.5851
    N = 10000, L = 150421, avg = 15.04211
    N = 100000, L = 1965999, avg = 19.659991

***
**3.3.24 红黑树的最坏情况。找出如何构造一棵大小为N的最差红黑树，其中从根结点到几乎所有空链接的路径长度均为$2lgN$**。

*Answer：*

红黑树的最坏情况是它所对应的2-3树中构成最左边的路径结点全部都是3-结点，而其余均为2-结点。

![ex3324](images\chapter03\ex3324.png)

插入顺序：20 10 30 5 15 3 

***
**3.3.25 自顶向下的2-3-4树。使用平衡2-3-4树作为数据结构实现符号表的基本API。在数的表示种使用红黑连接并实现正文所述的插入算法，其中在沿查找路径向下的过程中分解4-结点并进行颜色转换，在回溯向上的过程中将4-结点配平。**

*Answer：*

***
**3.3.26 自顶向下一遍完成。修改你为练习3.3.25给出的答案，不使用递归。在沿查找路径向下的过程中分解并平衡4-结点（以及3-结点），最后在树底插入新键即可。**

*Answer：*

***
**3.3.27 允许红色右链接。修改你为练习3.3.25给出的答案，允许红色右链接的存在。**

*Answer：*

***
**3.3.28 自底向上的2-3-4树，使用平衡2-3-4树作为数据结构实现符号表的基本API。在树的表示中使用红黑链接并用和算法3.4相同的递归方式实现自底向上的插入。你的插入方式应该只需要分解查找路径底部的4-结点（如果有的话）。**

*Answer：*

***
**3.3.29 最优存储。修改RedBlackBST的实现，用下面的技巧实现无序为结点颜色的存储使用额外的空间：要将结点标记为红色，只需要交换它的左右链接。要检测一个结点是否是红色，检测它的左子结点是否大于它的右子结点。你需要修改一些比较语句来适应链接的交换。这个技巧将变量的比较变成了键的比较，显然成本会更高，但它说明在需要的情况下这个变量是可以被删掉的。**

*Answer：*

***
**3.3.30 缓存。修改RedBlackBST的实现，将最近访问的结点Node保存在一个变量中，这样get()或put()再次访问同一个键时就只需要常数时间了。**

*Answer：*

只对put和get()方法进行了修改。**实际应该再修改delete的相关方法，在要删除的键与缓存中的键相同时，清空缓存。**

*由于contains()会调用get()，如果contains()返回true即get()命中，此时缓存会被修改。*

```java
// 成员变量
private Node<Key, Value> lastAcc; 

// put
public void put(Key key, Value val) {

    if (key == null) {
        throw new IllegalArgumentException("first argument to put() is null");
    }

    if (val == null) {
        delete(key);
        return;
    }
    
    // 如果key与缓存中的key相同
    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        lastAcc.val = val;
        return;
    }

    root = put(root, key, val);
    root.color = BLACK; // root结点的颜色一定是黑色
}

private Node<Key, Value> put(Node<Key, Value> h, Key key, Value val) {

    if (h == null) {
        Node<Key, Value> node = new Node<>(key, val, RED, 1);
        lastAcc = node; // 使用缓存记录新存入的键
        return node;
    }

    int cmp = key.compareTo(h.key);
    if (cmp < 0) {
        h.left = put(h.left, key, val);
    } else if (cmp > 0) {
        h.right = put(h.right, key, val);
    } else {
        // 发生更新，使用缓存记录被更新的结点
        lastAcc = h;
        h.val = val;
    }

    // 如果右子结点是红色而左子结点是黑色，进行左旋转
    if (isRed(h.right) && !isRed(h.left)) {
        h = rotateLeft(h);
    }
    // 如果左子结点是红色的且它的左子结点也是红色的，进行右旋转
    if (isRed(h.left) && isRed(h.left.left)) {
        h = rotateRight(h);
    }
    // 如果左右子结点均为红色，进行颜色转换
    if (isRed(h.left) && isRed(h.right)) {
        flipColors(h);
    }

    h.size = size(h.left) + size(h.right) + 1;

    return h;
}

// get
public Value get(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("argument to get() is null");
    }
    
    // 如果key与lastAcc的key相同
    if (lastAcc != null && key.compareTo(lastAcc.key) == 0) {
        return lastAcc.val;
    }

    return get(root, key);
}

private Value get(Node<Key, Value> x, Key key) {
    while (x != null) {
        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x = x.left;
        } else if (cmp > 0) {
            x = x.right;
        } else {
            // 使用缓存记录被访问的结点
            lastAcc = x;
            return x.val;
        }
    }
    return null;
}
```

***
**3.3.31 树的绘制。为RedBlackBST添加一个draw()方法，像正文一样绘制出红黑树。**

*Answer：*

***
**3.3.32 AVL树。AVL树时一种二叉查找树，其中任意结点的两个子树的高度最多相差1。证明将其中由高度为偶数的结点指向高度为奇数的结点的链接设为红色就可以得到一颗（完美平衡的）2-3-4树，其中红色链接可以是右链接。附加题：使用AVL树作为数据结构实现符号表的API。一种方法是在每个结点中保存它的高度并在递归调用后使用旋转来根据需要调整这个高度；另一种方法是在树的表示中使用红黑链接并使用类似练习3.3.39和练习3.3.40的moveRedLeft()和moveRedRight()的方法。**

*Answer：*

AVL树的实现：

```java
public class AVLTreeST<Key extends Comparable<Key>, Value> {

    private Node<Key, Value> root;

    private static class Node<Key, Value> {

        private Key key; // 键
        private Value val; // 值
        private int height; // 以该结点为根的树的高度
        private int size; // 以该结点为根的结点总数
        private Node<Key, Value> left, right; // 左右链接

        public Node(Key key, Value val, int height, int size) {
            this.key = key;
            this.val = val;
            this.size = size;
            this.height = height;
        }
    }

    /*
     * 判断符号表是否为空
     */
    public boolean isEmpty() {
        return root == null;
    }

    /*
     * 返回树的结点总数
     */
    public int size() {
        return size(root);
    }

    /*
     * 返回以x为根的树的结点总数
     */
    private int size(Node<Key, Value> x) {
        if (x == null)
            return 0;
        return x.size;
    }

    /*
     * 返回树的高度
     */
    public int height() {
        return height(root);
    }

    /*
     * 返回x的高度
     */
    private int height(Node<Key, Value> x) {
        if (x == null)
            return -1;
        return x.height;
    }

    /*
     * get方法
     */
    public Value get(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to get() is null");
        Node<Key, Value> x = get(root, key);
        if (x == null)
            return null;
        return x.val;
    }

    private Node<Key, Value> get(Node<Key, Value> x, Key key) {
        if (x == null)
            return null;
        int cmp = key.compareTo(x.key);
        if (cmp < 0)
            return get(x.left, key);
        else if (cmp > 0)
            return get(x.right, key);
        else
            return x;
    }

    /*
     * 是否包含key
     */
    public boolean contains(Key key) {
        return get(key) != null;
    }

    /*
     * put方法
     */
    public void put(Key key, Value val) {
        if (key == null)
            throw new IllegalArgumentException("first argument to put() is null");
        if (val == null) {
            delete(key);
            return;
        }
        root = put(root, key, val);
    }

    private Node<Key, Value> put(Node<Key, Value> x, Key key, Value val) {
        if (x == null)
            return new Node<>(key, val, 0, 1);
        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x.left = put(x.left, key, val);
        } else if (cmp > 0) {
            x.right = put(x.right, key, val);
        } else {
            x.val = val;
            return x;
        }
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        return balance(x);
    }

    /*
     * 重新按照AVL树的条件平衡x
     */
    private Node<Key, Value> balance(Node<Key, Value> x) {
        if (balanceFactor(x) < -1) {
            if (balanceFactor(x.right) > 0) {
                x.right = rotateRight(x.right);
            }
            x = rotateLeft(x);
        } else if (balanceFactor(x) > 1) {
            if (balanceFactor(x.left) < 0) {
                x.left = rotateLeft(x.left);
            }
            x = rotateRight(x);
        }
        return x;
    }

    /*
     * 返回x的平衡因子
     */
    private int balanceFactor(Node<Key, Value> x) {
        return height(x.left) - height(x.right);
    }

    /*
     * 右旋转x
     */
    private Node<Key, Value> rotateRight(Node<Key, Value> x) {
        Node<Key, Value> y = x.left;
        x.left = y.right;
        y.right = x;
        y.size = x.size;
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        y.height = 1 + Math.max(height(y.left), height(y.right));
        return y;
    }

    /*
     * 左旋转x
     */
    private Node<Key, Value> rotateLeft(Node<Key, Value> x) {
        Node<Key, Value> y = x.right;
        x.right = y.left;
        y.left = x;
        y.size = x.size;
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        y.height = 1 + Math.max(height(y.left), height(y.right));
        return y;
    }

    /*
     * 删除key
     */
    public void delete(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to delete() is null");
        if (!contains(key))
            return;
        root = delete(root, key);
    }

    private Node<Key, Value> delete(Node<Key, Value> x, Key key) {
        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x.left = delete(x.left, key);
        } else if (cmp > 0) {
            x.right = delete(x.right, key);
        } else {
            if (x.left == null) {
                return x.right;
            } else if (x.right == null) {
                return x.left;
            } else {
                Node<Key, Value> y = x;
                x = min(y.right);
                x.right = deleteMin(y.right);
                x.left = y.left;
            }
        }
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        return balance(x);
    }

    /*
     * 删除最小key
     */
    public void deleteMin() {
        if (isEmpty())
            throw new NoSuchElementException("called deleteMin() with empty symbol table");
        root = deleteMin(root);
    }

    private Node<Key, Value> deleteMin(Node<Key, Value> x) {
        if (x.left == null)
            return x.right;
        x.left = deleteMin(x.left);
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        return balance(x);
    }

    /*
     * 删除最大key
     */
    public void deleteMax() {
        if (isEmpty())
            throw new NoSuchElementException("called deleteMax() with empty symbol table");
        root = deleteMax(root);
    }

    private Node<Key, Value> deleteMax(Node<Key, Value> x) {
        if (x.right == null)
            return x.left;
        x.right = deleteMax(x.right);
        x.size = 1 + size(x.left) + size(x.right);
        x.height = 1 + Math.max(height(x.left), height(x.right));
        return balance(x);
    }

    /*
     * 返回最小key
     */
    public Key min() {
        if (isEmpty())
            throw new NoSuchElementException("called min() with empty symbol table");
        return min(root).key;
    }

    private Node<Key, Value> min(Node<Key, Value> x) {
        if (x.left == null)
            return x;
        return min(x.left);
    }

    /*
     * 返回最大key
     */
    public Key max() {
        if (isEmpty())
            throw new NoSuchElementException("called max() with empty symbol table");
        return max(root).key;
    }

    private Node<Key, Value> max(Node<Key, Value> x) {
        if (x.right == null)
            return x;
        return max(x.right);
    }

    /*
     * 向下取整
     */
    public Key floor(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to floor() is null");
        if (isEmpty())
            throw new NoSuchElementException("called floor() with empty symbol table");
        Node<Key, Value> x = floor(root, key);
        if (x == null)
            return null;
        else
            return x.key;
    }

    private Node<Key, Value> floor(Node<Key, Value> x, Key key) {
        if (x == null)
            return null;
        int cmp = key.compareTo(x.key);
        if (cmp == 0)
            return x;
        if (cmp < 0)
            return floor(x.left, key);
        Node<Key, Value> y = floor(x.right, key);
        if (y != null)
            return y;
        else
            return x;
    }

    /*
     * 向上取整
     */
    public Key ceiling(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to ceiling() is null");
        if (isEmpty())
            throw new NoSuchElementException("called ceiling() with empty symbol table");
        Node<Key, Value> x = ceiling(root, key);
        if (x == null)
            return null;
        else
            return x.key;
    }

    private Node<Key, Value> ceiling(Node<Key, Value> x, Key key) {
        if (x == null)
            return null;
        int cmp = key.compareTo(x.key);
        if (cmp == 0)
            return x;
        if (cmp > 0)
            return ceiling(x.right, key);
        Node<Key, Value> y = ceiling(x.left, key);
        if (y != null)
            return y;
        else
            return x;
    }

    /*
     * 返回第k小的key
     */
    public Key select(int k) {
        if (k < 0 || k >= size())
            throw new IllegalArgumentException("k is not in range 0-" + (size() - 1));
        Node<Key, Value> x = select(root, k);
        return x.key;
    }

    private Node<Key, Value> select(Node<Key, Value> x, int k) {
        if (x == null)
            return null;
        int t = size(x.left);
        if (t > k)
            return select(x.left, k);
        else if (t < k)
            return select(x.right, k - t - 1);
        else
            return x;
    }

    /*
     * 返回比key小的键的数量
     */
    public int rank(Key key) {
        if (key == null)
            throw new IllegalArgumentException("argument to rank() is null");
        return rank(key, root);
    }

    private int rank(Key key, Node<Key, Value> x) {
        if (x == null)
            return 0;
        int cmp = key.compareTo(x.key);
        if (cmp < 0)
            return rank(key, x.left);
        else if (cmp > 0)
            return 1 + size(x.left) + rank(key, x.right);
        else
            return size(x.left);
    }

    /*
     * 按顺序遍历
     */
    public Iterable<Key> keys() {
        return keysInOrder();
    }

    public Iterable<Key> keysInOrder() {
        Queue<Key> queue = new Queue<Key>();
        keysInOrder(root, queue);
        return queue;
    }

    private void keysInOrder(Node<Key, Value> x, Queue<Key> queue) {
        if (x == null)
            return;
        keysInOrder(x.left, queue);
        queue.enqueue(x.key);
        keysInOrder(x.right, queue);
    }

    /*
     * 按层遍历
     */
    public Iterable<Key> keysLevelOrder() {
        Queue<Key> queue = new Queue<>();
        if (!isEmpty()) {
            Queue<Node<Key, Value>> queue2 = new Queue<>();
            queue2.enqueue(root);
            while (!queue2.isEmpty()) {
                Node<Key, Value> x = queue2.dequeue();
                queue.enqueue(x.key);
                if (x.left != null) {
                    queue2.enqueue(x.left);
                }
                if (x.right != null) {
                    queue2.enqueue(x.right);
                }
            }
        }
        return queue;
    }

    /*
     * 按顺序遍历lo和hi之间的key
     */
    public Iterable<Key> keys(Key lo, Key hi) {
        if (lo == null)
            throw new IllegalArgumentException("first argument to keys() is null");
        if (hi == null)
            throw new IllegalArgumentException("second argument to keys() is null");
        Queue<Key> queue = new Queue<Key>();
        keys(root, queue, lo, hi);
        return queue;
    }

    private void keys(Node<Key, Value> x, Queue<Key> queue, Key lo, Key hi) {
        if (x == null)
            return;
        int cmplo = lo.compareTo(x.key);
        int cmphi = hi.compareTo(x.key);
        if (cmplo < 0)
            keys(x.left, queue, lo, hi);
        if (cmplo <= 0 && cmphi >= 0)
            queue.enqueue(x.key);
        if (cmphi > 0)
            keys(x.right, queue, lo, hi);
    }

    /*
     * 返回lo和hi之间结点的数量
     */
    public int size(Key lo, Key hi) {
        if (lo == null)
            throw new IllegalArgumentException("first argument to size() is null");
        if (hi == null)
            throw new IllegalArgumentException("second argument to size() is null");
        if (lo.compareTo(hi) > 0)
            return 0;
        if (contains(hi))
            return rank(hi) - rank(lo) + 1;
        else
            return rank(hi) - rank(lo);
    }

    public static void main(String[] args) {

    }
}
```

***
**3.3.33 验证。为RedBlackBST实现一个is23()方法来检查是否存在同时和两条红链接相连的结点和红色右链接，以及一个isBalanced()方法来检查从根结点到所有空链接的路径上的黑链接的数量是否相同。将这两个方法和练习3.2.32的isBST()方法结合起来实现一个isRedBlackBST()来检查一棵树是否是红黑树。**

*Answer：*

```java
/*
 * 检查是否存在同时和两条红链接相连的结点和红色右链接
 */
private boolean is23() {
    return is23(root);
}

private boolean is23(Node<Key, Value> x) {
    if (x == null) {
        return true;
    }

    // 检查是否有红色右链接
    if (isRed(x.right)) {
        return false;
    }

    // 检查是否存在和两条红链接相连的结点
    if (x != root && isRed(x) && isRed(x.left)) {
        return false;
    }
    return is23(x.left) && is23(x.right);
}

/*
 * 检查从根结点到所有空链接的路径上的黑链接的数量是否相同
 */
private boolean isBalanced() {
    // root到min的黑链接数量
    int black = 0;
    Node<Key, Value> x = root;
    while (x != null) {
        if (!isRed(x)) {
            black++;
        }
        x = x.left;
    }
    return isBalanced(root, black);
}

private boolean isBalanced(Node<Key, Value> x, int black) {
    if (x == null) {
        return black == 0;
    }
    if (!isRed(x)) {
        black--;
    }
    return isBalanced(x.left, black) && isBalanced(x.right, black);
}

/*
 * 二叉树检查
 */
private boolean isBinaryTree(Node<Key, Value> x) {
    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }

    if (x.right == null && x.left == null) { // 如果x没有左右结点
        return x.size == 1;
    } else if (x.right != null && x.left != null) { // 如果x同时具有左右结点，则结点数量为1+左子树结点数+右子数结点数
        return x.size != x.left.size + x.right.size + 1 ? false : isBinaryTree(x.left) && isBinaryTree(x.right);
    } else { // x结点只有一个子树
        Node<Key, Value> t = x.left == null ? x.right : x.left;
        return x.size != t.size + 1 ? false : isBinaryTree(t);
    }
}

/*
 * 有序性检查
 */
private boolean isOrdered(Node<Key, Value> x, Key min, Key max) {

    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }
    if (min.compareTo(max) > 0) {
        throw new IllegalArgumentException("min can not be greater than max...");
    }

    // 根结点不在[min, max]范围，直接返回false
    if (min.compareTo(x.key) > 0 || max.compareTo(x.key) < 0) {
        return false;
    }

    if (x.left == null && x.left == null) { // 如果x的左右结点均为null
        return true;
    } else if (x.left != null && x.right != null) { // 如果x的左右结点均不为null
        if (x.left.key.compareTo(x.key) > 0 || x.right.key.compareTo(x.key) < 0) { // 有序性检查
            return false;
        }
        return isOrdered(x.left, min, max) && isOrdered(x.right, min, max);
    } else { // x的左右结点其中之一为null
        if (x.left == null) { // 如果左结点为空
            if (x.right.key.compareTo(x.key) < 0) { // 如果右结点key小于x.key
                return false;
            }
            x = x.right;
        } else { // 如果右结点为空
            if (x.left.key.compareTo(x.key) > 0) { // 如果右结点key大于x.key
                return false;
            }
            x = x.left;
        }
        return isOrdered(x, min, max);
    }
}

/*
 * 等值键检查
 */
private boolean hasNoDuplicates(Node<Key, Value> x) {

    if (x == null) {
        throw new IllegalArgumentException("Node x can not be null...");
    }

    return hasNoDuplicates(x, new TreeSet<Key>());
}

private boolean hasNoDuplicates(Node<Key, Value> x, Set<Key> set) {

    // 判断set中是否存在x.key
    if (set.contains(x.key)) {
        return false; // 如果存在，说明有重复键
    }
    set.add(x.key); // 如果不存在，将x.key添加进set

    if (x.left == null && x.right == null) { // 如果x结点没有左右结点，返回true
        return true;
    } else if (x.left != null && x.right != null) { // 如果x结点具有左右两个子结点
        return hasNoDuplicates(x.left, set) && hasNoDuplicates(x.right, set);
    } else {
        // 结点x只有一个子结点
        x = x.left == null ? x.right : x.left;
        return hasNoDuplicates(x, set);
    }
}

/*
 * isRedBlackBST()方法
 */
public boolean isRedBlackBst() {
    if (!isBinaryTree(root)) {
        System.out.println("isBinaryTree check failed...");
        return false;
    }
    if (!isOrdered(root, min(), max())) {
        System.out.println("isOrdered check failed...");
        return false;
    }
    if (!hasNoDuplicates(root)) {
        System.out.println("hasNoDuplicates check failed...");
        return false;
    }
    // 此时已root为根的树已是一棵二叉树
    if (!is23()) {
        System.out.println("is23 check failed...");
        return false;
    }
    if (!isBalanced()) {
        System.out.println("isBalanced check failed...");
        return false;
    }
    return true;
}
```

***
**3.3.34 所有的2-3树。编写一段代码来生成高度为2、3和4的所有结构不同的2-3树，分别共有2、7和112种。**

*Answer：*

***
**3.3.35 2-3树。编写一段程序TwoThreeST.java，使用两种结点类型来直接表示和实现2-3查找树。**

*Anwer：*

***
**3.3.36 2-3-4-5-6-7-8树。说明平衡的2-3-4-5-6-7-8树中的查找和插入算法。**

*Answer：*

相当于B树，参见B树的查找和插入。

***
**3.3.37 无记忆性。请证明红黑树不是没有记忆的。例如，如果你向树中插入一个小于所有键的新键，然后立即删除树的最小键。你可能得到一棵不同的树。**

*Answer：* 

***
**3.3.38 旋转的基础定理。请证明，使用一系列左旋转或右旋转可以将一棵二叉查找树转化为由同一组键生成的其他任意一棵二叉查找树。**

*Answer：*

>
rotate the smallest key in the first BST to the root along the leftward spine; then recur with the resulting right subtree until you have a tree of height N (with every left link null). Do the same with the second BST. Remark: it is unknown whether there exists a polynomial-time algorithm for determining the minimum number of rotations needed to transform one BST into the other (even though the rotation distance is at most 2N - 6 for BSTs with at least 11 nodes).

***
**3.3.39 删除最小键。实现红黑树的deleteMin()方法，在沿着树的最左路径向下的过程中实现正文所述的变换，保证当前结点不是2-结点。**

*Answer：*

```java
public void deleteMin() {
    if (isEmpty()) {
        throw new NoSuchElementException("BST underflow");
    }

    // 如果根结点的两个子结点的颜色都是黑色，将根结点设置为红色
    if (!isRed(root.left) && !isRed(root.right)) {
        root.color = RED;
    }

    root = deleteMin(root);

    if (!isEmpty()) {
        root.color = BLACK;
    }
}

private Node<Key, Value> deleteMin(Node<Key, Value> h) {
    if (h.left == null) {
        return null;
    }

    if (!isRed(h.left) && !isRed(h.left.left)) {
        h = moveRedLeft(h);
    }

    h.left = deleteMin(h.left);
    return balance(h);
}

private Node<Key, Value> moveRedLeft(Node<Key, Value> h) {

    flipColors(h);
    if (isRed(h.right.left)) {
        h.right = rotateRight(h.right);
        h = rotateLeft(h);
        flipColors(h);
    }
    return h;
}

private Node<Key, Value> balance(Node<Key, Value> h) {

    if (isRed(h.right)) {
        h = rotateLeft(h);
    }
    if (isRed(h.left) && isRed(h.left.left)) {
        h = rotateRight(h);
    }
    if (isRed(h.left) && isRed(h.right)) {
        flipColors(h);
    }

    h.size = size(h.left) + size(h.right) + 1;

    return h;
}
```

***
**3.3.40 删除最大键。实现红黑树的deleteMax()方法。需要注意的是因为红链接都是左连接，所以这里用到的变换和上一道练习中的稍有不同。**

*Answer：*

```java
public void deleteMax() {
    if (isEmpty()) {
        throw new NoSuchElementException("BST underflow");
    }

    // 如果根结点的两个子结点的颜色都是黑色，将根结点设置为红色
    if (!isRed(root.left) && !isRed(root.right)) {
        root.color = RED;
    }

    root = deleteMax(root);

    if (!isEmpty()) {
        root.color = BLACK;
    }
}

private Node<Key, Value> deleteMax(Node<Key, Value> h) {
    if (isRed(h.left)) {
        h = rotateRight(h);
    }

    if (h.right == null) {
        return null;
    }

    if (!isRed(h.right) && !isRed(h.right.left)) {
        h = moveRedRight(h);
    }

    h.right = deleteMax(h.right);

    return balance(h);
}

private Node<Key, Value> moveRedRight(Node<Key, Value> h) {
    flipColors(h);
    if (isRed(h.left.left)) {
        h = rotateRight(h);
        flipColors(h);
    }
    return h;
}

private Node<Key, Value> balance(Node<Key, Value> h) {

    if (isRed(h.right)) {
        h = rotateLeft(h);
    }
    if (isRed(h.left) && isRed(h.left.left)) {
        h = rotateRight(h);
    }
    if (isRed(h.left) && isRed(h.right)) {
        flipColors(h);
    }

    h.size = size(h.left) + size(h.right) + 1;

    return h;
}
```

***
**3.3.41 删除操作。将上两题中的方法和二叉查找树的delete()方法结合起来，实现红黑树的删除操作。**

*Answer：*

```java
public void delete(Key key) {

    if (key == null) {
        throw new IllegalArgumentException("argument to delete() is null");
    }

    // 不含有该键，直接返回，避免改变红黑树的结构
    if (!contains(key)) {
        return;
    }

    // 如果根节点的两个子结点都是黑色，设置根节点颜色为红色
    if (!isRed(root.left) && !isRed(root.right)) {
        root.color = RED;
    }

    root = delete(root, key);

    if (!isEmpty()) {
        root.color = BLACK;
    }
}

private Node<Key, Value> delete(Node<Key, Value> h, Key key) {

    if (key.compareTo(h.key) < 0) {
        if (!isRed(h.left) && !isRed(h.left.left)) {
            h = moveRedLeft(h);
        }
        h.left = delete(h.left, key);
    } else {
        if (isRed(h.left)) {
            h = rotateRight(h);
        }
        if (key.compareTo(h.key) == 0 && (h.right == null)) {
            return null;
        }
        if (!isRed(h.right) && !isRed(h.right.left)) {
            h = moveRedRight(h);
        }
        if (key.compareTo(h.key) == 0) {
            Node<Key, Value> x = min(h.right);
            h.key = x.key;
            h.val = x.val;
            h.right = deleteMin(h.right);
        } else {
            h.right = delete(h.right, key);
        }
    }
    return balance(h);
}
```

***

#### 实验题

***
**3.3.42 统计红色结点。编写一段程序，统计给定的红黑树中红色结点所占的比例。对于$N=10^4、10^5$和$10^6$，用你的程序统计至少100棵随机构造的大小为N的红黑树并得出一个猜想。**

*Answer：*

```java
// 计算红色结点的比率
public double countRedNodeRatio() {
    return (double) countRedNode(root, 0) / size();
}

private int countRedNode(Node<Key, Value> x, int n) {

    if (x == null) {
        return n;
    }

    if (x.color) {
        n++;
    }

    n = countRedNode(x.left, n);
    n = countRedNode(x.right, n);

    return n;
}

// 测试
public static void main(String[] args) {
    
    RedBlackBSTEx3442<Integer, Integer> rbbst = null;

    for (int N = 1000; N <= 1000000; N *= 10) {
        
        rbbst = new RedBlackBSTEx3442<>();
        double ratio = 0d;
        
        for (int i = 0; i < 100; i++) {
            while (rbbst.size()!= N) {
                int r = StdRandom.uniform(0, Integer.MAX_VALUE - 1);
                rbbst.put(r, r);
            }
            ratio += rbbst.countRedNodeRatio();
        }
        
        System.out.println("For N = " + N + ", Red Ratio = " + ratio / 100);
    }
}
```

结果：

For N = 1000, Red Ratio = 0.25300000000000006
For N = 10000, Red Ratio = 0.25270000000000054
For N = 100000, Red Ratio = 0.2558000000000004
For N = 1000000, Red Ratio = 0.25421899999999936

猜测：在N足够大时，红色结点占红黑树中总结点的1/4。

***
**3.3.43 成本图。改造RedBlackBST的实现来绘制本节中能够显示计算中每次put()操作的成本的图（请参考练习3.1.38）。**

*Answer：*

略。

***
**3.3.44 平均查找用时。用实验研究和计算在一棵由N个随机结点构造的红黑树中到达一个随机结点的平均路径长度（内部路径长度除以N再加1）的平均差和标准差，对于1到10000之间的每个N至少重复实验1000遍。将结果绘制成和图3.3.30相似的Tufte图，并画上函数$lgN-0.5$的曲线。**

*Answer：*

略。

***
**3.3.45 统计旋转。改进你为练习3.3.43给出的程序，用图像绘制出在构造红黑树的过程中旋转和分解结点的次数并讨论结果。**

*Answer：*

略。

***
**3.3.46 红黑树的高度。改进你为练习3.3.43给出的程序，用图像绘制出所有红黑树的高度并讨论结果。**

*Answer：*

略。

### 3.4 散列表

***

#### 练习题

***
**3.4.1 将键E A S Y Q U T I O N依次插入一张初始为空且含有$M=5$条链表的基于拉链法的散列表中。使用散列函数`11 * k % m`将第k个字母散列到某个数组索引上。**

*Answer：*

```java
class SeparateChainingHashSTEx341<Key, Value> {

    private static final int INIT_CAPACITY = 4; // 初始数组长度

    private int n; // 键值对的数量
    private int m; // 散列表的大小

    private SequentialSearchST<Key, Value>[] st;

    /*
     * 构造函数
     */
    public SeparateChainingHashSTEx341() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public SeparateChainingHashSTEx341(int m) {
        this.m = m;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[m];
        for (int i = 0; i < m; i++) {
            st[i] = new SequentialSearchST<>();
        }
    }

    /*
     * 返回散列表中键值对的数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否是空的
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据key返回Value
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }
        int i = hash(key);
        return st[i].get(key);
    }

    /*
     * 存入键值对key-Value
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 如果链表的平均长度大于10，对散列表进行调整扩大
        if (n >= 10 * m) {
            resize(2 * m);
        }

        int i = hash(key);
        if (!st[i].contains(key)) {
            n++;
        }
        st[i].put(key, val);
    }

    /*
     * 判断散列表是否包含指定的key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据给定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        int i = hash(key);
        if (st[i].contains(key)) {
            n--;
        }
        st[i].delete(key);

        // 如果链表的平均长度小于2，对散列表进行缩小
        if (m > INIT_CAPACITY && n <= 2 * m) {
            resize(m / 2);
        }
    }

    // 将key散列到[0, m-1]
    private int hash(Key key) {
        return 11 * n % m;
    }

    // 对散列表扩大并重新散列所有键值对
    private void resize(int chains) {
        SeparateChainingHashSTEx341<Key, Value> temp = new SeparateChainingHashSTEx341<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m = temp.m;
        this.n = temp.n;
        this.st = temp.st;
    }

    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                queue.enqueue(key);
            }
        }
        return queue;
    }
}

public class Ex341 {
    public static void main(String[] args) {
        char[] chars = "EASYQUTION".toCharArray();
        SeparateChainingHashSTEx341<Character, Integer> schst = new SeparateChainingHashSTEx341<>(5);
        for (char c : chars) {
            schst.put(c, 0);
        }
    }
}
```

***
**3.4.2 重新实现SeparateChainingHashST，直接使用SequentialSearchST中链表部分的代码。**

*Answer：*

```java
public class SeparateChainHashSTEx342<Key, Value> {

    private Node<Key, Value>[] chains;
    private int m; // 链表的条数
    private int n; // 散列表的键值对数

    private static class Node<Key, Value> {
        Key key;
        Value val;
        Node<Key, Value> next;

        public Node(Key key, Value val, Node<Key, Value> next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }

    @SuppressWarnings("unchecked")
    public SeparateChainHashSTEx342(int m) {
        if (m <= 0) {
            throw new IllegalArgumentException();
        }
        chains = new Node[m];
        this.m = m;
    }

    public int size() {
        return n;
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException();
        }

        if (val == null) {
            delete(key);
            return;
        }

        int h = hash(key);

        for (Node<Key, Value> x = chains[h]; x != null; x = x.next) {
            if (key.equals(x.key)) {
                x.val = val;
                return;
            }
        }
        
        chains[h] = new Node<>(key, val, chains[h]);
        n++;
        
        // 如果n/m > 10，增加链表的数量
        if (n >= 10 * m) {
            resize(2 * m);
        }
    }

    // 调整链表的数目
    private void resize(int newM) {
        SeparateChainHashSTEx342<Key, Value> tmp = new SeparateChainHashSTEx342<>(newM);
        for (Key key : keys()) {
            tmp.put(key, get(key));
        }

        this.m = tmp.m;
        this.n = tmp.n;
        this.chains = tmp.chains;
    }

    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        int h = hash(key);

        for (Node<Key, Value> x = chains[h]; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return x.val;
            }
        }
        return null;
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        int h = hash(key);

        if (chains[h] == null) {
            return;
        }

        chains[h] = delete(chains[h], key);

        // 如果链表条数过多，缩小链表数目
        if (m > 4 && n <= 2 * m) {
            resize(m / 2);
        }
    }

    private Node<Key, Value> delete(Node<Key, Value> x, Key key) {

        if (x == null) {
            return null;
        }

        if (key.equals(x.key)) {
            n--;
            return x.next;
        }
        x.next = delete(x.next, key);
        return x;
    }

    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    public Iterable<Key> keys() {

        Queue<Key> queue = new Queue<>();

        for (int i = 0; i < chains.length; i++) {
            if (chains[i] != null) {
                for (Node<Key, Value> x = chains[i]; x != null; x = x.next) {
                    queue.enqueue(x.key);
                }
            }
        }
        return queue;
    }

    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    public static void main(String[] args) {

    }
}
```

***
**3.4.3 修改你为上一道练习给出的实现，为每个键值对添加一个整型变量，将其值设为插入该键值对时的散列表中元素的数量。实现一个方法，将该变量的值大于给定整数k的键（及其相应的值）全部删除。**

*Answer：*

```java
public class SeparateChainHashSTEx343<Key, Value> {

    private Node<Key, Value>[] chains;
    private int m; // 链表的条数
    private int n; // 散列表的键值对数

    private static class Node<Key, Value> {
        Key key;
        Value val;
        Node<Key, Value> next;
        int count;

        public Node(Key key, Value val, Node<Key, Value> next, int count) {
            this.key = key;
            this.val = val;
            this.next = next;
            this.count = count; // 练习3.4.3 添加一个整型变量保存插入该键值对时散列表的元素数量
        }
    }

    @SuppressWarnings("unchecked")
    public SeparateChainHashSTEx343(int m) {
        if (m <= 0) {
            throw new IllegalArgumentException();
        }
        chains = new Node[m];
        this.m = m;
    }

    public int size() {
        return n;
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException();
        }

        if (val == null) {
            delete(key);
            return;
        }

        int h = hash(key);

        for (Node<Key, Value> x = chains[h]; x != null; x = x.next) {
            if (key.equals(x.key)) {
                x.val = val;
                return;
            }
        }
        chains[h] = new Node<>(key, val, chains[h], n++);

        // 如果n/m > 10，增加链表的数量
        if (n >= 10 * m) {
            resize(2 * m);
        }
    }

    // 调整链表的数目
    private void resize(int newM) {
        SeparateChainHashSTEx343<Key, Value> tmp = new SeparateChainHashSTEx343<>(newM);
        for (Key key : keys()) {
            tmp.put(key, get(key));
        }

        this.m = tmp.m;
        this.n = tmp.n;
        this.chains = tmp.chains;
    }

    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        int h = hash(key);

        for (Node<Key, Value> x = chains[h]; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return x.val;
            }
        }
        return null;
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        int h = hash(key);

        if (chains[h] == null) {
            return;
        }

        chains[h] = delete(chains[h], key);

        // 如果链表条数过多，缩小链表数目
        if (m > 4 && n <= 2 * m) {
            resize(m / 2);
        }
    }

    private Node<Key, Value> delete(Node<Key, Value> x, Key key) {

        if (x == null) {
            return null;
        }

        if (key.equals(x.key)) {
            n--;
            return x.next;
        }
        x.next = delete(x.next, key);
        return x;
    }

    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    public Iterable<Key> keys() {

        Queue<Key> queue = new Queue<>();

        for (int i = 0; i < chains.length; i++) {
            if (chains[i] != null) {
                for (Node<Key, Value> x = chains[i]; x != null; x = x.next) {
                    queue.enqueue(x.key);
                }
            }
        }
        return queue;
    }

    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    // 练习题 3.4.3
    public void delete(int k) {
        
        for (Key key : keys()) {
            if (getCount(key) > k) {
                delete(key);
            }
        }
    }

    // 调用这个方法时，参数key一定存在于某个链表中
    private int getCount(Key key) {

        int h = hash(key);

        Node<Key, Value> x = null;
        for (x = chains[h]; x != null; x = x.next) {
            if (key.equals(x.key)) {
                break;
            }
        }

        return x.count;
    }

    public static void main(String[] args) {
        
    }
}
```

***
**3.4.4 使用散列函数`(a * k) % M`将S E A R C H X M P L中第k个键散列为一个数组索引。编写一段程序找出a和最小的M，使得该散列函数得到的每个索引都不相同（没有碰撞）。这样的函数也被称为完美散列函数。**

*Answer：*

???

显而易见，a = 1, M = 10就可以。

***
**3.4.5 下面这段hashCode()的实现合法吗？**

```java
public int hashCode() {
    return 17;
}
```

*Answer：*

合法。但是很差。进行散列时会不断的发生碰撞，性能很低。

***
**3.4.6 假设键为t位的整数。对于一个使用素数M的除留余数法的散列函数，请证明对于键的每一位，都存不同的两个键，它们的散列值只有该位不同。**

*Answer：*

***
**3.4.7 考虑对于整型的键将除留余数法的散列函数实现为`(a * k) % M`，其中a为一个任意的固定素数。这样是否足以利用键的所有位使得我们可以使用一个非素数M了呢？**

*Answer：*

不行。
假设，a = 3，M = 10，则散列函数变为`(3k) % 10`，此时hash函数只利用了k的最后一位，而高位不起任何作用。

***
**3.4.8 对于$N=10、10^2、10^3、10^4、10^5$和$10^6$，请估计将N个键插入一张SeparateChainingHashST的散列表后还剩多少空链表？**

*Answer：*

在散列表的实现中添加两个方法：

```java
// 统计散列表中空链表的数目
public int countEmptyList() {
    int count = 0;
    for (int i = 0; i < st.length; i++) {
        if (st[i].size() == 0) {
            count++;
        }
    }
    return count;
}

// 获取总链表数量的方法
public int getM() {
    return m;
}
```

测试：

```java
public class Ex348 {
    public static void main(String[] args) {

        SeparateChainingHashSTEx348<Integer, Integer> schst = null;

        // 键可能有重复
        for (int N = 10; N <= 1000000; N *= 10) {
            schst = new SeparateChainingHashSTEx348<>();
            for (int i = 0; i < N; i++) {
                int r = StdRandom.uniform(Integer.MAX_VALUE);
                schst.put(r, r);
            }
            System.out.println("for N = " + N + ", 链表总数 :" + schst.getM() + ", 空链表数：" + schst.countEmptyList());
        }
    }
}
```

结果：

    for N = 10, 链表总数 :4, 空链表数：0
    for N = 100, 链表总数 :16, 空链表数：0
    for N = 1000, 链表总数 :128, 空链表数：0
    for N = 10000, 链表总数 :1024, 空链表数：0
    for N = 100000, 链表总数 :16384, 空链表数：41
    for N = 1000000, 链表总数 :131072, 空链表数：56

***
**3.4.9 为SeparateChainingHashST实现一个即时的delete()方法。**

*Answer：*

```java
public void delete(Key key) {
    if (key == null) {
        throw new IllegalArgumentException("argument to delete() is null");
    }

    int i = hash(key);
    if (st[i].contains(key)) {
        n--;
    }
    st[i].delete(key);

    // 如果链表的平均长度小于2，对散列表进行缩小
    if (m > INIT_CAPACITY && n <= 2 * m) {
        resize(m / 2);
    }
}
```

***
**3.4.10 将键E A S Y Q U T I O N 依次插入一张初始为空且大小为$M=16$的基于线性探测法的散列表中。使用散列函数`11 k % M`将第K个字母散列到某个数组索引上。对于$M=10$将本题重新完成一遍。**

*Answer：*

```java
class LinearProbingHashSTEx3410<Key, Value> {
    private static final int INIT_CAPACITY = 4;

    private int n; // 散列表中键值对的数量
    private int m; // 散列表的大小
    private Key[] keys; // 保存键的数组
    private Value[] vals; // 保存值得数组

    /*
     * 构造函数
     */
    public LinearProbingHashSTEx3410() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public LinearProbingHashSTEx3410(int capacity) {
        m = capacity;
        n = 0;
        keys = (Key[]) new Object[m];
        vals = (Value[]) new Object[m];
    }

    /*
     * 返回散列表中的键值对数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 存入键值对
     */
    public void put(Key key, Value val) {
        if (key == null)
            throw new IllegalArgumentException("first argument to put() is null");

        if (val == null) {
            delete(key);
            return;
        }

        // 键值对数量超过散列表大小的一半，扩大散列表
        if (n >= m / 2)
            resize(2 * m);

        int i;
        for (i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                vals[i] = val;
                return;
            }
        }
        keys[i] = key;
        vals[i] = val;
        n++;
    }

    /*
     * 根据特定的键返回值
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        for (int i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                return vals[i];
            }
        }

        return null;
    }

    /*
     * 判断给定的key是否存在于散列表中
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据特定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        if (!contains(key)) {
            return;
        }

        int i = hash(key);
        while (!key.equals(keys[i])) {
            i = (i + 1) % m;
        }

        // 删除对应的键值
        keys[i] = null;
        vals[i] = null;

        // 重新散列被删除键右侧的所有键并插入散列表
        i = (i + 1) % m;
        while (keys[i] != null) {
            Key keyToRehash = keys[i];
            Value valToRehash = vals[i];
            keys[i] = null;
            vals[i] = null;
            n--;
            put(keyToRehash, valToRehash);
            i = (i + 1) % m;
        }

        n--;

        // 如果键值对数量小于散列表大小的1/8，缩小散列表
        if (n > 0 && n <= m / 8) {
            resize(m / 2);
        }
    }

    /*
     * 以一个Iterable集合的形式返回所有key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (int i = 0; i < m; i++) {
            if (keys[i] != null)
                queue.enqueue(keys[i]);
        }
        return queue;
    }

    // 练习题 3.4.10指定的散列函数
    private int hash(Key key) {
        return (11 * n) % m;
    }

    // 调整散列表的大小
    private void resize(int capacity) {
        LinearProbingHashSTEx3410<Key, Value> temp = new LinearProbingHashSTEx3410<>(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != null) {
                temp.put(keys[i], vals[i]);
            }
        }
        keys = temp.keys;
        vals = temp.vals;
        m = temp.m;
    }
}

public class Ex3410 {
    public static void main(String[] args) {
        String[] strings = "E A S Y Q U T I O N".split(" ");
        LinearProbingHashSTEx3410<String, Integer> lphst = new LinearProbingHashSTEx3410<>(16);
        for (String string : strings) {
            lphst.put(string, 0);
        }
    }
}
```

***
**3.4.11 将键E A S Y Q U T I O N依次插入一张初始为空的大小为$M=4$的基于线性探测法的散列表中，数组只要达到半满即自动将长度加倍。使用散列函数`11 k % M`将第k个字母散列到某个数组索引上。给出得到的散列表的内容。**

*Answer：*

```java
public class LinearProbingHashSTEx3411<Key, Value> {
    private static final int INIT_CAPACITY = 4;

    private int n; // 散列表中键值对的数量
    private int m; // 散列表的大小
    private Key[] keys; // 保存键的数组
    private Value[] vals; // 保存值得数组

    /*
     * 构造函数
     */
    public LinearProbingHashSTEx3411() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public LinearProbingHashSTEx3411(int capacity) {
        m = capacity;
        n = 0;
        keys = (Key[]) new Object[m];
        vals = (Value[]) new Object[m];
    }

    /*
     * 返回散列表中的键值对数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 存入键值对
     */
    public void put(Key key, Value val) {
        if (key == null)
            throw new IllegalArgumentException("first argument to put() is null");

        if (val == null) {
            delete(key);
            return;
        }

        // 键值对数量超过散列表大小的一半，扩大散列表
        if (n >= m / 2)
            resize(2 * m);

        int i;
        for (i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                vals[i] = val;
                return;
            }
        }
        keys[i] = key;
        vals[i] = val;
        n++;
    }

    /*
     * 根据特定的键返回值
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        for (int i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                return vals[i];
            }
        }

        return null;
    }
    
    /*
     * 判断给定的key是否存在于散列表中
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据特定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        if (!contains(key)) {
            return;
        }

        int i = hash(key);
        while (!key.equals(keys[i])) {
            i = (i + 1) % m;
        }

        // 删除对应的键值
        keys[i] = null;
        vals[i] = null;

        // 重新散列被删除键右侧的所有键并插入散列表
        i = (i + 1) % m;
        while (keys[i] != null) {
            Key keyToRehash = keys[i];
            Value valToRehash = vals[i];
            keys[i] = null;
            vals[i] = null;
            n--;
            put(keyToRehash, valToRehash);
            i = (i + 1) % m;
        }

        n--;

        // 如果键值对数量小于散列表大小的1/8，缩小散列表
        if (n > 0 && n <= m / 8) {
            resize(m / 2);
        }
    }

    /*
     * 以一个Iterable集合的形式返回所有key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (int i = 0; i < m; i++) {
            if (keys[i] != null)
                queue.enqueue(keys[i]);
        }
        return queue;
    }

    // 练习3.4.11指定的hash函数
    private int hash(Key key) {
        return 11 * n % m;
    }

    // 调整散列表的大小
    private void resize(int capacity) {
        LinearProbingHashSTEx3411<Key, Value> temp = new LinearProbingHashSTEx3411<>(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != null) {
                temp.put(keys[i], vals[i]);
            }
        }
        keys = temp.keys;
        vals = temp.vals;
        m = temp.m;
    }
    
    public static void main(String[] args) {
        String[] strings = "E A S Y Q U T I O N".split(" ");
        LinearProbingHashSTEx3411<String, Integer> lphst = new LinearProbingHashSTEx3411<>(4);
        for (String string : strings) {
            lphst.put(string, 0);
        }
        
        Object[] objects = lphst.keys;
        
        for (int i = 0; i < objects.length; i++) {
            System.out.print(objects[i] + " ");
        }
    }
}
```

结果：

    E A Q N null null null null null null null S U I null null null null null null null null T Y O null null null null null null null 

***
**3.4.12 设有键A到G，散列值如下所示。将它们按照一定顺序插入到一张初始为空大小为7的基于线性探测的散列表中（这里数组的大小不会动态调整）。下面哪个选项是不可能由插入这些键产生的？给出这些键在构造散列表时可能所需的最大和最小探测次数，并给出相应的插入顺序来证明你的答案。**

- a. E F G A C B D
- b. C E B G F D A
- c. B D F A C E G
- d. C G B A D E F
- e. F G B D A C E
- f. G E C A D B F

键|A|B|C|D|E|F|G
-----|-----|-----|-----|-----|-----|-----|-----
散列值（M=7）|2|0|0|4|4|4|2

*Answer：*

均不可能。

***
**3.4.13 在下面哪些情况中基于线性探测的散列表中的一次随机的命中查找所需的时间是线性的。**

- a. 所有键均被散列到同一个索引上
- b. 所有键均被散列到不同的索引上
- c. 所有键均被散列到同一个偶数索引上
- d. 所有键均被散列到不同的偶数索引上

*Answer：*

a，c。

***
**3.4.14 对于未命中的查找回答上一道练习的问题，假设被查找的键被散列到表中任意位置的可能性均等。**

*Answer：*

a，c。

***
**3.4.15 在最坏情况下，向一张初始为空、基于线性探测法并能动够动态调整数组大小的散列表中插入N个键需要多少次比较。**

*Answer：*

***
**3.4.16 假设有一张大小为$10^6$的基于线性探测的散列表已经半满了，被占用的元素随机分布。请估计所有索引值中能够倍100整除的位置都被占用的概率。**

*Answer：*

```java
public static void main(String[] args) {

    int cnt = 0;
    int M = 1000000;
    LinearProbingHashSTEx3416<Integer, Integer> lphst = null;
    loop1: for (int T = 0; T < 1000; T++) { // 1000次实验
        lphst = new LinearProbingHashSTEx3416<>(M);
        while (lphst.size() != M / 2) {
            int r = StdRandom.uniform(Integer.MAX_VALUE);
            lphst.put(r, 0);
        }
        Object[] keys = lphst.keys;
        for (int i = 100; i <= M; i += 100) {
            if (keys[i] == null) {
                continue loop1;
            }
        }
        cnt++;
    }
    System.out.println("能被100整除的位置均被占用的概率：" + (double) cnt / 1000);
}
```

结果：

    能被100整除的位置均被占用的概率：0.0

***
**3.4.17 使用3.4.3.1节的delelet()方法从标准索引测试用例使用的LinearProbingHashST中删除键C并给出结果散列表的内容。**

*Answer：*

    idx    0  1  2  3  4  5  6  7  8  9  10  11  12  13  14  15
    key    P  M        A  H  S  L        E               R   X 
    val    10 9        8  5  0  11       12              3   7

***
**3.4.18 为SeparateChainHashST添加一个构造函数，使用例能够指定查找操作可以接受的在链表中进行的平均探测次数。动态调整数组的大小以保证链表的平均长度小于该值，并使用答疑中所述的方法来保证hash()方法的系数总是素数。**

*Answer：*

```java
public class SeparateChainingHashSTEx3418<Key, Value> {

    // 用于hash函数
    private static final int[] PRIMES = { 31, 61, 127, 251, 509, 1021, 2039, 4093, 9191, 16381, 32749, 65521, 131071,
            262139, 524287, 1048573, 2097143, 4194301, 8388593, 16777213, 33554393, 67108859, 134217689, 268435399,
            536870909, 1073741789, 2147483647 };

    private static final int INIT_CAPACITY = 4; // 初始数组长度

    private int n; // 键值对的数量
    private int m; // 散列表的大小

    private SequentialSearchST<Key, Value>[] st;

    private int alpha = 5; // 默认的可以接受的最大平均探测次数

    /*
     * 构造函数
     */
    public SeparateChainingHashSTEx3418() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public SeparateChainingHashSTEx3418(int capacity) {
        this.m = capacity;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[m];
        for (int i = 0; i < m; i++) {
            st[i] = new SequentialSearchST<>();
        }
    }

    // 练习3.4.18
    public SeparateChainingHashSTEx3418(int alpha, int capacity) {

        this(capacity);

        if (alpha < 1) {
            throw new IllegalArgumentException();
        }
        this.alpha = alpha;
    }

    /*
     * 返回散列表中键值对的数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否是空的
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据key返回Value
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }
        int i = hash(key);
        return st[i].get(key);
    }

    /*
     * 存入键值对key-Value
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 如果链表平均长度大于alpha，链表数量扩大2倍
        if (n >= alpha * m) {
            resize(2 * m);
        }

        int i = hash(key);
        if (!st[i].contains(key)) {
            n++;
        }
        st[i].put(key, val);
    }

    /*
     * 判断散列表是否包含指定的key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据给定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        int i = hash(key);
        if (st[i].contains(key)) {
            n--;
        }
        st[i].delete(key);

        // 如果链表的平均长度小于2，对散列表进行缩小
        if (m > INIT_CAPACITY && n <= 2 * m) {
            resize(m / 2);
        }
    }

    // 将key散列到[0, m-1]
    // 练习3.4.18
    private int hash(Key key) {

        int t = key.hashCode() & 0x7fffffff;

        int lgM = (int) Math.log10(m);

        if (lgM < 26) {
            t %= PRIMES[lgM + 5];
        }
        return t % m;
    }

    // 对散列表扩大并重新散列所有键值对
    private void resize(int chains) {
        SeparateChainingHashSTEx3418<Key, Value> temp = new SeparateChainingHashSTEx3418<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m = temp.m;
        this.n = temp.n;
        this.st = temp.st;
    }

    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                queue.enqueue(key);
            }
        }
        return queue;
    }
}
```

***
**3.4.19 为SeparateChainingHashST和LinearProbingHashST实现keys()方法。**

*Answer：*

```java
// SeparateChainingHashST
public Iterable<Key> keys() {
    Queue<Key> queue = new Queue<Key>();
    for (int i = 0; i < m; i++) {
        for (Key key : st[i].keys()) {
            queue.enqueue(key);
        }
    }
    return queue;
}

// LinearProbingHashST
public Iterable<Key> keys() {
    Queue<Key> queue = new Queue<>();
    for (int i = 0; i < m; i++) {
        if (keys[i] != null)
            queue.enqueue(keys[i]);
    }
    return queue;
}
```

***
**3.4.20 为LinearProbingHashST添加一个方法来计算一次命中查找的平均成本，假设表中每个键被查找的可能性相同。**

*Answer：*

***
**3.4.21 为LinearProbingHashST添加一个方法来计算一次为命中查找的平均成本，假设使用了一个随机的散列函数。请注意：要解决这个问题并不一定要计算所有的散列函数。**

*Answer：*

***
**3.4.22 为下列数据类型实现hashCode()方法：Point2D、Interval1D、Interval2D和Date。**

*Answer：*

```java
// Point2D
public int hashCode() {
    int hashX = ((Double) x).hashCode();
    int hashY = ((Double) y).hashCode();
    return 31*hashX + hashY;
}

// Interval1D
public int hashCode() {
    int hash1 = ((Double) min).hashCode();
    int hash2 = ((Double) max).hashCode();
    return 31*hash1 + hash2;
}

// Interval2D
public int hashCode() {
    int hash1 = x.hashCode();
    int hash2 = y.hashCode();
    return 31*hash1 + hash2;
}

// Date
public int hashCode() {
    int hash = 17;
    hash = 31*hash + month;
    hash = 31*hash + day;
    hash = 31*hash + year;
    return hash;
}
```

***
**3.4.23 对于字符串类型的键，考虑R = 256和M = 255的除留余数法。请证明这是一个糟糕的选择，因为任意排列的字母所得字符串的散列值均相同。**

*Answer：*

测试：

```java
public class Ex3423 {
    public static void main(String[] args) {

        
        System.out.println(hash("abcde"));
        System.out.println(hash("abdce"));
        System.out.println(hash("badce"));
        System.out.println(hash("adbce"));
        System.out.println(hash("abecd"));
    }

    private static int hash(String s) {
        int hash = 0;
        for (int i = 0; i < s.length(); i++) {
            hash = ((256 * hash) + s.charAt(i)) % 255;
        }
        return hash;
    }
}
```

结果：

    240
    240
    240
    240
    240

***
**3.4.24 对于double类型，分析拉链法、线性探测法和二叉查找树的内存使用情况。**

*Answer：*

略。

***

#### 提高题

***
**3.4.25 散列值的缓存。修改3.4.1.8节的Transaction类并维护一个变量hash，在hashCode()方法第一次为一个对象计算散列值后将值保存在hash中，这样随后的调用就不必重新计算了。**

*Answer：*

```java
public class Transaction implements Comparable<Transaction> {

    private final String who; // customer
    private final Date when; // date
    private final double amount; // amount

    private Integer hash;

    public Transaction(String who, Date when, double amount) {
        if (Double.isNaN(amount) || Double.isInfinite(amount))
            throw new IllegalArgumentException("Amount cannot be NaN or infinite");
        this.who = who;
        this.when = when;
        this.amount = amount;
    }

    public Transaction(String transaction) {
        String[] a = transaction.split("\\s+");
        who = a[0];
        when = new Date(a[1]);
        amount = Double.parseDouble(a[2]);
        if (Double.isNaN(amount) || Double.isInfinite(amount))
            throw new IllegalArgumentException("Amount cannot be NaN or infinite");
    }

    public String who() {
        return who;
    }

    public Date when() {
        return when;
    }

    public double amount() {
        return amount;
    }

    @Override
    public String toString() {
        return String.format("%-10s %10s %8.2f", who, when, amount);
    }

    public int compareTo(Transaction that) {
        return Double.compare(this.amount, that.amount);
    }

    @Override
    public boolean equals(Object other) {
        if (other == this)
            return true;
        if (other == null)
            return false;
        if (other.getClass() != this.getClass())
            return false;
        Transaction that = (Transaction) other;
        return (this.amount == that.amount) && (this.who.equals(that.who)) && (this.when.equals(that.when));
    }

    public int hashCode() {
        if (this.hash == null) {
            int hash = 1;
            hash = 31 * hash + who.hashCode();
            hash = 31 * hash + when.hashCode();
            hash = 31 * hash + ((Double) amount).hashCode();
            this.hash = hash;
            return hash;
        } else {
            return this.hash;
        }
    }

    public static class WhoOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.who.compareTo(w.who);
        }
    }

    public static class WhenOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return v.when.compareTo(w.when);
        }
    }

    public static class HowMuchOrder implements Comparator<Transaction> {

        @Override
        public int compare(Transaction v, Transaction w) {
            return Double.compare(v.amount, w.amount);
        }
    }
}
```

***
**3.4.26 线性探测法中的延时删除。为LinearProbingHashST添加一个delete()方法，在删除一个键值时将其设置为null，并在调用resize()方法时将键值对从表中删除。这种方法主要难点在于决定何时应该调用resize()方法。**

*Answer：*

***
**3.4.27 二次探测。修改SeparateChainingHashST，进行二次散列并选择两条链表中的较短者。将键E A S Y Q U T I O N 依次插入一张初始为空且大小为$M=3$的基于拉链法的散列表中，以`11 k % M`作为第一个散列函数，`17 k % M`作为第二个散列函数来将第k个字母散列到某个数组索引上。给出插入过程的轨迹以及随机的命中和未命中查找在该符号表中所需的平均探测次数。**

*Answer：*

```java
public void put(Key key, Value val) {
    if (key == null) {
        throw new IllegalArgumentException("first argument to put() is null");
    }

    if (val == null) {
        delete(key);
        return;
    }

    // 如果链表的平均长度大于10，对散列表进行调整扩大
    if (n >= 10 * m) {
        resize(2 * m);
    }

    int h1 = hash_1(key);
    int h2 = hash_2(key);

    // 选择长度较小的链表
    int i = st[h1].size() <= st[h2].size() ? h1 : h2;

    if (!st[i].contains(key)) {
        n++;
    }
    st[i].put(key, val);
}

private int hash_1(Key key) {
    return 11 * n % m;
}

private int hash_2(Key key) {
    return 17 * n % m;
}
```

插入轨迹以及平均探测次数略。

***
**3.4.28 二次散列。修改LinearProbingHashST，进行二次散列以得到探测起始点。确切地说，是将（所有的）$(i+1)\%M$替换为$(i + k)\%M$，其中k是一个非零、和M互质且和键相关的整数。**

*Answer：*

***
**3.4.29 删除操作。分别为前两题中所述的散列表实现即时的delete()方法。**

*Answer：*

***
**3.4.30 卡方值（chi-square statistic）。为SeparateChainingHashST添加一个方法来计算散列表的$\chi ^2$。对于大小为M并含有N个元素的散列表，这个值的定义为：**

$$
\chi^2 = (M/N)((f_0-N/M)^2+(f_1-N/M)^2 + ... + (f_{M-1}-N/M)^2)
$$

**其中，$f_i$为散列值为i的键的数量。这个统计是检测我们的散列函数产生的随机值是否满足假设的一种方法。如果满足，对于$N>cM$，这个值落在$M-\sqrt{M}$和$M+\sqrt{M}$之间的概率为$1-1/c$。**

*Answer：*

```java
public double calculateCHI() {
    double chi = 0d;
    for (int i = 0; i < st.length; i++) {
        chi += Math.pow(st[i].size() - (double) n / m, 2);
    }
    chi *= ((double) m / n);
    return chi;
}
```

***
**3.4.31 Cuckoo散列函数。实现一个符号表，在其中维护两张散列表和两个散列函数。一个给定的键只能存在于一张散列表之中。在插入一个新键时，在其中一张散列表中插入该键。如果这张表中该键的位置已经被占用了，就用新键替代老键并将老键插入到另一张散列表中（如果在这张表中该键的位置也被占用了，那么就将这个占用着重新插入第一张散列表，把位置腾给被插入的键），如此循环往复。动态调整数组大小以保持两张表都不到半满。这种实现中查找所需的比较次数在最坏情况下是一个常数，插入操作所需的时间在均摊后也是常数。**

*Answer：*

参见布谷鸟散列。

***
**3.4.32 散列攻击。找出$2^N$个hashCode()方法返回值均相同且长度均为$2^N$的字符串。假设String类型的hashCode()方法实现如下：**

```java
public int hashCode() {
    int hash = 0;
    for (int i = 0; i < length(); i++) {
        hash = (hash * 31) + charAt(i);
    }
    return hash;
}
```

*Answer：*

It is easy to verify that "Aa" and "BB" hash to the same hashCode() value (2112). Now, any string of length 2N that is formed by concatenating these two strings together in any order (e.g., BBBBBB, AaAaAa, BBAaBB, AaBBBB) will hash to the same value. Here is a [list of 10000 strings with the same hash value](https://algs4.cs.princeton.edu/34hash/hash-attack.txt){:target="_blank"}.

测试：

```java
public class Ex3432 {

    public static void main(String[] args) {
        String s1 = "aoffckzdaoffckzdhfibxuiu";
        String s2 = "aoffckzdaoffckzdhkdcmtby";
        String s3 = "aoffckzdaoffckzdmfdwlmfy";
        System.out.print(hash(s1) + " ");
        System.out.print(hash(s2) + " ");
        System.out.print(hash(s3) + " ");
    }

    public static int hash(String s) {
        int hash = 0;
        for (int i = 0; i < s.length(); i++) {
            hash = (hash * 31) + s.charAt(i);
        }
        return hash;
    }
}
```

结果：0 0 0 

***
**3.4.33 糟糕的散列函数。考虑Java早期版本中String类型的hashCode()方法的实现，如下所示：**

```java
public int hashCode() {
   int hash = 0;
   int skip = Math.max(1, length() / 8);
   for (int i = 0; i < length(); i += skip)
      hash = (hash * 37) + charAt(i);
   return hash;
}
```

**说明你认为为什么设计者选择这种实现的原因以及为什么它被替换成了上一道练习中的实现。**

*Answer：*

This was done in the hopes of computing the hash function more quickly. Indeed, the hash values were computed more quickly, but it became more likely that many strings hashed to the same values. This resulted in a significant degradation in performance on many real-world inputs (e.g., long URLs) which all hashed to the same value, e.g., 
http://www.cs.princeton.edu/algs4/34hash/*****java.html.

***

#### 实验题

***
**3.4.34 散列的成本。用各种常见数据类型进行实验以得到hash()方法阿赫compareTo()方法的耗时比的经验数据。**

*Answer：*

略。

***
**3.4.35 卡方检验。使用你为练习3.4.30给出的答案验证常用数据类型的散列函数产生的值是否随机。**

```java
public class SeparateChainingHashSTEx3435<Key, Value> {

    private static final int INIT_CAPACITY = 4; // 初始数组长度

    private int n; // 键值对的数量
    private int m; // 散列表的大小

    private SequentialSearchST<Key, Value>[] st;

    /*
     * 构造函数
     */
    public SeparateChainingHashSTEx3435() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public SeparateChainingHashSTEx3435(int m) {
        this.m = m;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[m];
        for (int i = 0; i < m; i++) {
            st[i] = new SequentialSearchST<>();
        }
    }

    /*
     * 返回散列表中键值对的数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否是空的
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据key返回Value
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }
        int i = hash(key);
        return st[i].get(key);
    }

    /*
     * 存入键值对key-Value
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 如果链表的平均长度大于10，对散列表进行调整扩大
        if (n >= 10 * m) {
            resize(2 * m);
        }

        int i = hash(key);
        if (!st[i].contains(key)) {
            n++;
        }
        st[i].put(key, val);
    }

    /*
     * 判断散列表是否包含指定的key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据给定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        int i = hash(key);
        if (st[i].contains(key)) {
            n--;
        }
        st[i].delete(key);

        // 如果链表的平均长度小于2，对散列表进行缩小
        if (m > INIT_CAPACITY && n <= 2 * m) {
            resize(m / 2);
        }
    }

    // 将key散列到[0, m-1]
    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    // 对散列表扩大并重新散列所有键值对
    private void resize(int chains) {
        SeparateChainingHashSTEx3435<Key, Value> temp = new SeparateChainingHashSTEx3435<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m = temp.m;
        this.n = temp.n;
        this.st = temp.st;
    }

    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                queue.enqueue(key);
            }
        }
        return queue;
    }

    public double calculateCHI() {
        double chi = 0d;
        for (int i = 0; i < st.length; i++) {
            chi += Math.pow(st[i].size() - (double) n / m, 2);
        }
        chi *= ((double) m / n);
        return chi;
    }

    public static void main(String[] args) {
        SeparateChainingHashSTEx3435<String, Integer> schst =  new SeparateChainingHashSTEx3435<>();
        // 读取《双城记》
        for (int i = 0; !StdIn.isEmpty(); i++) {
            String key = StdIn.readString();
            schst.put(key, i);
        }
        
        System.out.println(schst.calculateCHI());
    }
}
```

只对$\chi ^2$进行了计算，没有进行检验。

***
**3.4.36 链表长度的范围。编写一段程序，向一张长度为$N/100$的基于拉链法的散列表中插入N个随机的int键，找出表中最长和短链表的长度，其中$N=10^3、10^4、10^5$和$10^6$。**

*Answer：*

put()过程没有进行resize()。

```java
public class SeparateChainingHashSTEx3436<Key, Value> {

    private static final int INIT_CAPACITY = 4; // 初始数组长度

    private int n; // 键值对的数量
    private int m; // 散列表的大小

    private SequentialSearchST<Key, Value>[] st;

    /*
     * 构造函数
     */
    public SeparateChainingHashSTEx3436() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public SeparateChainingHashSTEx3436(int m) {
        this.m = m;
        st = (SequentialSearchST<Key, Value>[]) new SequentialSearchST[m];
        for (int i = 0; i < m; i++) {
            st[i] = new SequentialSearchST<>();
        }
    }

    /*
     * 返回散列表中键值对的数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否是空的
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据key返回Value
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }
        int i = hash(key);
        return st[i].get(key);
    }

    /*
     * 存入键值对key-Value
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 如果链表的平均长度大于10，对散列表进行调整扩大
        // if (n >= 10 * m) {
        // resize(2 * m);
        // }

        int i = hash(key);
        if (!st[i].contains(key)) {
            n++;
        }
        st[i].put(key, val);
    }

    /*
     * 判断散列表是否包含指定的key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据给定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        int i = hash(key);
        if (st[i].contains(key)) {
            n--;
        }
        st[i].delete(key);

        // 如果链表的平均长度小于2，对散列表进行缩小
        if (m > INIT_CAPACITY && n <= 2 * m) {
            resize(m / 2);
        }
    }

    // 将key散列到[0, m-1]
    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    // 对散列表扩大并重新散列所有键值对
    private void resize(int chains) {
        SeparateChainingHashSTEx3436<Key, Value> temp = new SeparateChainingHashSTEx3436<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m = temp.m;
        this.n = temp.n;
        this.st = temp.st;
    }

    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                queue.enqueue(key);
            }
        }
        return queue;
    }

    public static void main(String[] args) {
        SeparateChainingHashSTEx3436<Integer, Integer> schst = null;

        for (int N = 1000; N <= 1000000; N *= 10) {
            schst = new SeparateChainingHashSTEx3436<Integer, Integer>(N / 100);
            for (int i = 0; i < N; i++) {
                int rand = StdRandom.uniform(Integer.MAX_VALUE);
                schst.put(rand, 0);
            }

            int maxLength = Integer.MIN_VALUE;
            int minLength = Integer.MAX_VALUE;

            @SuppressWarnings("rawtypes")
            SequentialSearchST[] sts = schst.st;
            for (int i = 0; i < sts.length; i++) {
                int size = sts[i].size();
                if (size > maxLength) {
                    maxLength = size;
                }
                if (size < maxLength) {
                    minLength = size;
                }
            }
            
            System.out.println("对于N = " + N);
            System.out.println("链表最大长度：" + maxLength);
            System.out.println("链表最小长度：" + minLength + "\n");
        }

    }
}
```

结果：

对于N = 1000
链表最大长度：114
链表最小长度：104

对于N = 10000
链表最大长度：123
链表最小长度：100

对于N = 100000
链表最大长度：132
链表最小长度：112

对于N = 1000000
链表最大长度：141
链表最小长度：121

***
**3.4.37 混合使用。用实验研究在SeparateChainingHashST中使用RedBlackST代替SequentialSearchST来处理碰撞的性能。这种方案的优点是即使散列函数很糟糕它仍然能够保证对数级别的性能。缺点是需要维护两种不同的符号表实现。实际效果如何呢？**

*Answer：*

这个时候要求Key满足Key extends Comparable<? super Key>，以便使用红黑树。
测试略。

***
**3.4.38 拉链法的分布（*书中BUG*）。编写一段程序，向一张大小为$10^5$的基于线性探测法的散列表中插入$10^5$个小于$10^6$的随机非负整数并在每$10^3$次插入后打印当前探测的总次数。讨论你的结果在何种程度上验证了命题K。**

*Answer：*

```java
public class LinearProbingHashSTEx3438<Key, Value> {
    private static final int INIT_CAPACITY = 4;

    private int n; // 散列表中键值对的数量
    private int m; // 散列表的大小
    private Key[] keys; // 保存键的数组
    private Value[] vals; // 保存值得数组

    private int detectTimes; // 探测次数

    /*
     * 构造函数
     */
    public LinearProbingHashSTEx3438() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public LinearProbingHashSTEx3438(int capacity) {
        m = capacity;
        n = 0;
        keys = (Key[]) new Object[m];
        vals = (Value[]) new Object[m];
    }

    /*
     * 返回散列表中的键值对数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 存入键值对
     */
    public void put(Key key, Value val) {
        if (key == null)
            throw new IllegalArgumentException("first argument to put() is null");

        if (val == null) {
            delete(key);
            return;
        }

        // 键值对数量超过散列表大小的一半，扩大散列表
        if (n >= m / 2) {
            resize(2 * m);
        }

        int i;
        for (i = hash(key); keys[i] != null; i = (i + 1) % m) {
            detectTimes += 2; // 判断是否为null；调用equals方法
            if (keys[i].equals(key)) {
                vals[i] = val;
                return;
            }
        }
        detectTimes++;
        keys[i] = key;
        vals[i] = val;
        n++;
    }

    /*
     * 根据特定的键返回值
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        for (int i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                return vals[i];
            }
        }

        return null;
    }

    /*
     * 判断给定的key是否存在于散列表中
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return get(key) != null;
    }

    /*
     * 根据特定的键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        if (!contains(key)) {
            return;
        }

        int i = hash(key);
        while (!key.equals(keys[i])) {
            i = (i + 1) % m;
        }

        // 删除对应的键值
        keys[i] = null;
        vals[i] = null;

        // 重新散列被删除键右侧的所有键并插入散列表
        i = (i + 1) % m;
        while (keys[i] != null) {
            Key keyToRehash = keys[i];
            Value valToRehash = vals[i];
            keys[i] = null;
            vals[i] = null;
            n--;
            put(keyToRehash, valToRehash);
            i = (i + 1) % m;
        }

        n--;

        // 如果键值对数量小于散列表大小的1/8，缩小散列表
        if (n > 0 && n <= m / 8) {
            resize(m / 2);
        }
    }

    /*
     * 以一个Iterable集合的形式返回所有key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (int i = 0; i < m; i++) {
            if (keys[i] != null)
                queue.enqueue(keys[i]);
        }
        return queue;
    }

    // 散列算法，将键散列到[0, m-1]
    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    // 调整散列表的大小
    private void resize(int capacity) {
        LinearProbingHashSTEx3438<Key, Value> temp = new LinearProbingHashSTEx3438<>(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != null) {
                temp.put(keys[i], vals[i]);
            }
        }
        keys = temp.keys;
        vals = temp.vals;
        m = temp.m;
    }

    // 实验题3.4.38
    // 计算当前总的探测次数
    public int getDetectTimes() {
        return detectTimes;
    }

    public void resetDetectTimes() {
        detectTimes = 0;
    }

    public static void main(String[] args) {
        LinearProbingHashSTEx3438<Integer, Integer> lphst = new LinearProbingHashSTEx3438<>(100000);

        int putCnt = 0;
        while (lphst.size() != 100000) {
            lphst.put(StdRandom.uniform(0, 1000000), 0);
            putCnt++;
            if (putCnt % 1000 == 0) {
                System.out.println("当前插入次数：" + putCnt + "， 探测次数：" + lphst.getDetectTimes());
            }
        }
    }
}
```

结果：

当前插入次数：1000， 探测次数：1012
当前插入次数：2000， 探测次数：2039
当前插入次数：3000， 探测次数：3088
当前插入次数：4000， 探测次数：4154
当前插入次数：5000， 探测次数：5253
当前插入次数：6000， 探测次数：6336
当前插入次数：7000， 探测次数：7490
当前插入次数：8000， 探测次数：8635
...
当前插入次数：99000， 探测次数：204347
当前插入次数：100000， 探测次数：207301
当前插入次数：101000， 探测次数：210544
当前插入次数：102000， 探测次数：213857
当前插入次数：103000， 探测次数：216861
当前插入次数：104000， 探测次数：220197
当前插入次数：105000， 探测次数：223222

***
**3.4.39 线性分步法的分布。向一张大小为N的基于线性探测法的散列表中插入$N/2$个随机非负整数并根据表中的键簇计算依次未命中查找的平均成本，其中$N=10^3、10^4、10^5$和$10^6$。讨论你的结果在何种程度上验证了命题M。**

*Answer：*

***
**3.4.40 绘图。改进LinearProbingHashST和SeparateChainingHashST的实现，使之绘出和正文中类似的图表。**

*Answer：*

略。

***
**3.4.41 二次探测。用实验研究来评估二次探测法的效果。**

*Answer：*

***
**3.4.42 二次散列。用实验研究来评估二次散列法的效果。**

*Answer：*

***
**3.4.43 停车问题（D.Knuth）。用实验研究来验证一个猜想：向一张大小为M的基于线性探测法的散列表中插入M个随机键需要的比较次数为~$cM^{3/2}$，其中$c=\sqrt{\pi/2}$。**

*Answer：*

```java
public class Ex3443 {

    public static void main(String[] args) {

        int M = 10000;

        LinearProbingHashSTEx3438<Integer, Integer> lphst = new LinearProbingHashSTEx3438<>(M);

        while (lphst.size() != M) {
            lphst.put(StdRandom.uniform(Integer.MAX_VALUE), 0);
        }
        System.out.println("M = " + M + "，理论比较次数：" + Math.sqrt(Math.PI / 2) * Math.pow(M, (double) 3 / 2) + "，实际比较次数："
                + lphst.getDetectTimes());
    }
}
```

结果不理想。

### 3.5 应用

***

#### 练习题

***
**3.5.1 分别使用ST和HashST来实现SET和HashSET（为键关联虚拟值并忽略它们）。**

*Answer：*

书中ST指代RedBlackST，HashST指代LinearProbingHashST。SET具有有序性操作，HashSET不具有有序性操作。

```java
// SET
public class SET<Key extends Comparable<? super Key>> implements Iterable<Key> {

    private static final int DUMMY = 0;

    private RedBlackBST<Key, Integer> rbbst;

    public SET() {
        rbbst = new RedBlackBST<>();
    }

    public void add(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called add() with a null key");
        }
        rbbst.put(key, DUMMY);
    }

    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called contains() with a null key");
        }
        return rbbst.contains(key);
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called delete() with a null key");
        }
        rbbst.delete(key);
    }
    
    public void deleteMax() {
        rbbst.deleteMax();
    }
    
    public void deleteMin() {
        rbbst.deleteMin();
    }

    public int size() {
        return rbbst.size();
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public Iterator<Key> iterator() {
        return rbbst.keys().iterator();
    }

    public Key max() {
        if (isEmpty()) {
            throw new NoSuchElementException("called max() with empty set");
        }
        return rbbst.max();
    }

    public Key min() {
        if (isEmpty()) {
            throw new NoSuchElementException("called min() with empty set");
        }
        return rbbst.min();
    }

    public Key ceiling(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called ceiling() with a null key");
        }
        Key k = rbbst.ceiling(key);
        if (k == null) {
            throw new NoSuchElementException("all keys are less than " + key);
        }
        return k;
    }

    public Key floor(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called floor() with a null key");
        }
        Key k = rbbst.floor(key);
        if (k == null) {
            throw new NoSuchElementException("all keys are greater than " + key);
        }
        return k;
    }

    /*
     * 并集操作
     */
    public SET<Key> union(SET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        SET<Key> c = new SET<>();
        for (Key x : this) {
            c.add(x);
        }
        for (Key x : that) {
            c.add(x);
        }
        return c;
    }

    /*
     * 交集操作
     */
    public SET<Key> intersects(SET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        SET<Key> c = new SET<>();
        if (this.size() < that.size()) {
            for (Key x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (Key x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    /*
     * 不支持hashCode()方法
     */
    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @SuppressWarnings("unchecked")
    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        SET<Key> other = (SET<Key>) obj;
        if (rbbst == null) {
            if (other.rbbst != null) {
                return false;
            }
        } else if (!rbbst.equals(other.rbbst)) {
            return false;
        }
        return true;
    }
}

// HashSET
public class HashSET<Key> implements Iterable<Key> {

    private static final int DUMMY = 0;

    private LinearProbingHashST<Key, Integer> lphst;

    public HashSET() {
        lphst = new LinearProbingHashST<>();
    }

    public void add(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called add() with a null key");
        }
        lphst.put(key, DUMMY);
    }

    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called contains() with a null key");
        }
        return lphst.contains(key);
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called delete() with a null key");
        }
        lphst.delete(key);
    }

    public int size() {
        return lphst.size();
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public Iterator<Key> iterator() {
        return lphst.keys().iterator();
    }

    /*
     * 并集操作
     */
    public HashSET<Key> union(HashSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        HashSET<Key> c = new HashSET<>();
        for (Key x : this) {
            c.add(x);
        }
        for (Key x : that) {
            c.add(x);
        }
        return c;
    }

    /*
     * 交集操作
     */
    public HashSET<Key> intersects(HashSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        HashSET<Key> c = new HashSET<>();
        if (this.size() < that.size()) {
            for (Key x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (Key x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @SuppressWarnings("unchecked")
    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        HashSET<Key> other = (HashSET<Key>) obj;
        if (lphst == null) {
            if (other.lphst != null) {
                return false;
            }
        } else if (!lphst.equals(other.lphst)) {
            return false;
        }
        return true;
    }
}
```

***
**3.5.2 删除SequentialSearchST中和值有关的所有代码来实现SequentialSearchSET。**

*Answer：*

```java
public class SequentialSearchSET<Key> implements Iterable<Key> {

    private Node<Key> first; // 链表首结点

    private int N;

    private static class Node<Key> {

        Key key;
        Node<Key> next;

        public Node(Key key, Node<Key> next) {
            this.key = key;
            this.next = next;
        }
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        for (Node<Key> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return true; // 命中，返回
            }
        }
        return false;
    }

    public void add(Key key) {

        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (contains(key)) {
            return;
        }

        first = new Node<>(key, first); // 未命中，新建结点
        N++;
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        first = delete(first, key);
    }

    private Node<Key> delete(Node<Key> x, Key key) {
        if (x == null) {
            return null;
        }
        if (key.equals(x.key)) {
            N--;
            return x.next;
        }
        x.next = delete(x.next, key);
        return x;
    }

    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (Node<Key> x = first; x != null; x = x.next) {
            queue.enqueue(x.key);
        }
        return queue;
    }

    @Override
    public Iterator<Key> iterator() {
        return keys().iterator();
    }

    /*
     * 并集操作
     */
    public SequentialSearchSET<Key> union(SequentialSearchSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        SequentialSearchSET<Key> c = new SequentialSearchSET<>();
        for (Key x : this) {
            c.add(x);
        }
        for (Key x : that) {
            c.add(x);
        }
        return c;
    }

    /*
     * 交集操作
     */
    public SequentialSearchSET<Key> intersects(SequentialSearchSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        SequentialSearchSET<Key> c = new SequentialSearchSET<>();
        if (this.size() < that.size()) {
            for (Key x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (Key x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @SuppressWarnings("unchecked")
    @Override
    public boolean equals(Object obj) {
        if (this == obj) {

        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        SequentialSearchSET<Key> other = (SequentialSearchSET<Key>) obj;
        if (N != other.N) {
            return false;
        }
        if (first == null) {
            if (other.first != null) {
                return false;
            }
        } else if (!first.equals(other.first)) {
            return false;
        }
        return true;
    }
}
```

***
**3.5.3 删除BinarySearchST中和值相关的所有代码来实现BinarySearchSET。**

*Answer：*

```java
public class BinarySearchSET<Key extends Comparable<? super Key>> implements Iterable<Key> {

    private static final int DEFAULT_CAPACITY = 10;
    private Key[] keys;
    private int N;
    
    @SuppressWarnings("unchecked")
    public BinarySearchSET(int capacity) {
        keys = (Key[]) new Comparable[capacity];
    }
    
    public BinarySearchSET() {
        this(DEFAULT_CAPACITY);
    }
    
    public int size() {
        return N;
    }
    
    public boolean isEmpty() {
        return N == 0;
    }
    
    public void add(Key key) {
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            return;
        }

        if (N == keys.length) {
            resize(2 * keys.length);
        }

        for (int j = N; j > i; j--) {
            keys[j] = keys[j - 1];
        }
        keys[i] = key;
        N++;
    }
    
    public int rank(Key key) {
        int lo = 0, hi = N - 1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int cmp = key.compareTo(keys[mid]);
            if (cmp < 0) {
                hi = mid - 1;
            } else if (cmp > 0) {
                lo = mid + 1;
            } else {
                return mid;
            }
        }
        return lo;
    }
    
    @SuppressWarnings("unchecked")
    private void resize(int capacity) {
        Key[] tempk = (Key[]) new Comparable[capacity];
        for (int i = 0; i < N; i++) {
            tempk[i] = keys[i];
        }
        keys = tempk;
    }
    
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        if (isEmpty()) {
            return false;
        }
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            return true;
        }
        return false;
    }
    
    public Key min() {
        return keys[0];
    }

    public Key max() {
        return keys[N - 1];
    }

    public Key select(int k) {
        return k < 0 || k > N - 1 ? null : keys[k];
    }

    public Key ceiling(Key key) {
        int i = rank(key);
        return i == N ? null : keys[i];
    }
    
    public Key floor(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to floor() is null");
        }
        int i = rank(key);
        if (i < N && key.compareTo(keys[i]) == 0) {
            return keys[i];
        }
        return i == 0 ? null : keys[i - 1];
    }
    
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        if (isEmpty()) {
            return;
        }

        // compute rank
        int i = rank(key);

        // key not in table
        if (i == N || keys[i].compareTo(key) != 0) {
            return;
        }

        for (int j = i; j < N - 1; j++) {
            keys[j] = keys[j + 1];
        }

        N--;
        keys[N] = null; // to avoid loitering

        // resize if 1/4 full
        if (N > 0 && N == keys.length / 4) {
            resize(keys.length / 2);
        }
    }
    
    public void deleteMin() {
        delete(min());
    }

    public void deleteMax() {
        delete(max());
    }
    
    public Iterable<Key> keys() {
        
        Key lo = keys[0];
        Key hi = keys[N - 1];
        
        Queue<Key> q = new Queue<>();
        for (int i = rank(lo); i < rank(hi); i++) {
            q.enqueue(keys[i]);
        }
        if (contains(hi)) {
            q.enqueue(keys[rank(hi)]);
        }
        return q;
    }
    
    @Override
    public Iterator<Key> iterator() {
        return keys().iterator();
    }
    
    /*
     * 并集操作
     */
    public BinarySearchSET<Key> union(SET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        BinarySearchSET<Key> c = new BinarySearchSET<Key>();
        for (Key x : this) {
            c.add(x);
        }
        for (Key x : that) {
            c.add(x);
        }
        return c;
    }
    
    /*
     * 交集操作
     */
    public BinarySearchSET<Key> intersects(BinarySearchSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        BinarySearchSET<Key> c = new BinarySearchSET<>();
        if (this.size() < that.size()) {
            for (Key x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (Key x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        BinarySearchSET<Key> other = (BinarySearchSET<Key>) obj;
        if (N != other.N) {
            return false;
        }
        if (!Arrays.equals(keys, other.keys)) {
            return false;
        }
        return true;
    }
}
```

***
**3.5.4 分别为int和double两种原始数据类型的键实现HashSTint类和HashSTdouble类（将LinearProbingHashST中的泛型改为原始数据类型）。**

*Answer：*

int类型的实现：这个实现中，底层数组的默认值是Integer.MAX_VALUE（泛型实现中是null）并且删除了值的相关代码。

```java
public class LinearProbingHashSTint {

    private static final int DEFAULT_VALUE = Integer.MAX_VALUE;

    private static final int INIT_CAPACITY = 4;
    private int n; // 散列表中键的数量
    private int m; // 散列表的大小
    private int[] keys; // 保存键的数组

    /*
     * 构造函数
     */
    public LinearProbingHashSTint() {
        this(INIT_CAPACITY);
    }

    public LinearProbingHashSTint(int capacity) {
        m = capacity;
        n = 0;
        keys = new int[m];
        for (int i = 0; i < keys.length; i++) {
            keys[i] = DEFAULT_VALUE;
        }
    }

    /*
     * 返回散列表中的键值对数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    public void put(int key) {

        // 键值对数量超过散列表大小的一半，扩大散列表
        if (n >= m / 2) {
            resize(2 * m);
        }
        if (contains(key)) {
            return;
        }

        int i = hash(key);
        keys[i] = key;
        n++;
    }

    public boolean contains(int key) {
        for (int i = hash(key); keys[i] != DEFAULT_VALUE; i = (i + 1) % m) {
            if (keys[i] == key) {
                return true;
            }
        }
        return false;
    }

    public void delete(int key) {

        if (!contains(key)) {
            return;
        }

        int i = hash(key);

        while (key != keys[i]) {
            i = (i + 1) % m;
        }

        // 删除对应的键值
        keys[i] = 0;

        // 重新散列被删除键右侧的所有键并插入散列表
        i = (i + 1) % m;
        while (keys[i] != DEFAULT_VALUE) {
            int keyToRehash = keys[i];
            keys[i] = DEFAULT_VALUE;
            n--;
            put(keyToRehash);
            i = (i + 1) % m;
        }

        n--;

        // 如果键值对数量小于散列表大小的1/8，缩小散列表
        if (n > 0 && n <= m / 8) {
            resize(m / 2);
        }
    }

    public Iterable<Integer> keys() {
        Queue<Integer> queue = new Queue<>();
        for (int i = 0; i < m; i++) {
            if (keys[i] != DEFAULT_VALUE)
                queue.enqueue(keys[i]);
        }
        return queue;
    }

    private int hash(int key) {
        return (key & 0x7fffffff) % m;
    }

    // 调整散列表的大小
    private void resize(int capacity) {
        LinearProbingHashSTint temp = new LinearProbingHashSTint(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != DEFAULT_VALUE) {
                temp.put(keys[i]);
            }
        }
        keys = temp.keys;
        m = temp.m;
    }
}
```

double实现略。

***
**3.5.5 分别为int和double两种原始数据类型的键实现STint类和STdouble类（将RedBlackBST中的泛型改为原始数据类型）。**

*Answer：*

int类型为键的红黑树，该实现中已删除所有关于值的代码。

```java
public class RedBlackBSTint {

    private static final boolean RED = true;
    private static final boolean BLACK = false;

    private Node root; // 红黑树的根结点

    private static class Node {

        private int key; // 键
        private Node left, right; // 左、右链接
        private boolean color; // 父结点的指向这个结点的链接的颜色
        private int size; // 这棵子树的结点总数

        public Node(int key, boolean color, int size) {
            this.key = key;
            this.color = color;
            this.size = size;
        }
    }

    /*
     * 存入键值对key-val
     */
    public void put(int key) {

        root = put(root, key);
        root.color = BLACK; // root结点的颜色一定是黑色
    }

    private Node put(Node h, int key) {

        if (h == null) {
            return new Node(key, RED, 1);
        }

        int cmp = key - h.key;
        if (cmp < 0) {
            h.left = put(h.left, key);
        } else if (cmp > 0) {
            h.right = put(h.right, key);
        } else {
            // 空闲
        }

        // 如果右子结点是红色而左子结点是黑色，进行左旋转
        if (isRed(h.right) && !isRed(h.left)) {
            h = rotateLeft(h);
        }
        // 如果左子结点是红色的且它的左子结点也是红色的，进行右旋转
        if (isRed(h.left) && isRed(h.left.left)) {
            h = rotateRight(h);
        }
        // 如果左右子结点均为红色，进行颜色转换
        if (isRed(h.left) && isRed(h.right)) {
            flipColors(h);
        }

        h.size = size(h.left) + size(h.right) + 1;

        return h;
    }

    // 转换Node h与它的两个子结点的颜色
    private void flipColors(Node h) {
        h.color = !h.color;
        h.left.color = !h.left.color;
        h.right.color = !h.right.color;
    }

    /*
     * 左旋转Node h的右链接
     */
    private Node rotateLeft(Node h) {
        Node x = h.right;
        h.right = x.left;
        x.left = h;
        x.color = x.left.color;
        x.left.color = RED;
        x.size = h.size;
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }

    /*
     * 右旋转Node h的左链接
     */
    private Node rotateRight(Node h) {
        Node x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = x.right.color;
        x.right.color = RED;
        x.size = h.size;
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }

    /*
     * 判断结点x的颜色
     */
    private boolean isRed(Node x) {
        return x == null ? false : x.color == RED;
    }

    /*
     * 删除键值对
     */
    public void delete(int key) {

        // 不含有该键，直接返回，避免改变红黑树的结构
        if (!contains(key)) {
            return;
        }

        // 如果根节点的两个子结点都是黑色，设置根节点颜色为红色
        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = delete(root, key);

        if (!isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node delete(Node h, int key) {

        int cmp = key - h.key;

        if (cmp < 0) {
            if (!isRed(h.left) && !isRed(h.left.left)) {
                h = moveRedLeft(h);
            }
            h.left = delete(h.left, key);
        } else {
            if (isRed(h.left)) {
                h = rotateRight(h);
            }
            if (cmp == 0 && (h.right == null)) {
                return null;
            }
            if (!isRed(h.right) && !isRed(h.right.left)) {
                h = moveRedRight(h);
            }
            if (cmp == 0) {
                Node x = min(h.right);
                h.key = x.key;
                h.right = deleteMin(h.right);
            } else {
                h.right = delete(h.right, key);
            }
        }
        return balance(h);
    }

    /*
     * 删除最大键
     */
    public void deleteMax() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow");
        }

        // 如果根结点的两个子结点的颜色都是黑色，将根结点设置为红色
        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = deleteMax(root);

        if (!isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node deleteMax(Node h) {
        if (isRed(h.left)) {
            h = rotateRight(h);
        }

        if (h.right == null) {
            return null;
        }

        if (!isRed(h.right) && !isRed(h.right.left)) {
            h = moveRedRight(h);
        }

        h.right = deleteMax(h.right);

        return balance(h);
    }

    /*
     * 删除最小键
     */
    public void deleteMin() {
        if (isEmpty()) {
            throw new NoSuchElementException("BST underflow");
        }

        // 如果根结点的两个子结点的颜色都是黑色，将根结点设置为红色
        if (!isRed(root.left) && !isRed(root.right)) {
            root.color = RED;
        }

        root = deleteMin(root);

        if (!isEmpty()) {
            root.color = BLACK;
        }
    }

    private Node deleteMin(Node h) {
        if (h.left == null) {
            return null;
        }

        if (!isRed(h.left) && !isRed(h.left.left)) {
            h = moveRedLeft(h);
        }

        h.left = deleteMin(h.left);
        return balance(h);
    }

    // 假定结点h是红色，并且h.right和h.right.left是黑色，将h.right或它的一个子结点转为红色
    private Node moveRedRight(Node h) {
        flipColors(h);
        if (isRed(h.left.left)) {
            h = rotateRight(h);
            flipColors(h);
        }
        return h;
    }

    // 假定结点h是红色并且h.left和h.left.left是黑色，将h.left或它的一个子结点转为红色
    private Node moveRedLeft(Node h) {

        flipColors(h);
        if (isRed(h.right.left)) {
            h.right = rotateRight(h.right);
            h = rotateLeft(h);
            flipColors(h);
        }
        return h;
    }

    // 重新平衡以h为根的子树
    private Node balance(Node h) {

        if (isRed(h.right)) {
            h = rotateLeft(h);
        }
        if (isRed(h.left) && isRed(h.left.left)) {
            h = rotateRight(h);
        }
        if (isRed(h.left) && isRed(h.right)) {
            flipColors(h);
        }

        h.size = size(h.left) + size(h.right) + 1;

        return h;
    }

    /*****************************************************************
     *********** 以下方法不涉及结点的颜色，与二叉查找树实现相同***************
     ****************************************************************/

    /*
     * 判断红黑树是否为空树
     */
    public boolean isEmpty() {
        return root == null;
    }

    /*
     * 返回这颗红黑树的结点总数
     */
    public int size() {
        return size(root);
    }

    private int size(Node x) {
        return x == null ? 0 : x.size;
    }

    /*
     * 测试树中是否包含指定的key
     */
    public boolean contains(int key) {
        Node x = root;
        while (x != null) {
            int cmp = key - x.key;
            if (cmp < 0) {
                x = x.left;
            } else if (cmp > 0) {
                x = x.right;
            } else {
                return true;
            }
        }
        return false;
    }

    /*
     * 返回最小的key
     */
    public int min() {
        if (isEmpty()) {
            throw new NoSuchElementException("calls min() with empty symbol table");
        }
        return min(root).key;
    }

    private Node min(Node x) {
        return x.left == null ? x : min(x.left);
    }

    /*
     * 返回最大的key
     */
    public int max() {
        if (isEmpty()) {
            throw new NoSuchElementException("calls max() with empty symbol table");
        }
        return max(root).key;
    }

    private Node max(Node x) {
        return x.right == null ? x : max(x.right);
    }

    /*
     * 向下取整
     */
    public int floor(int key) {
        if (isEmpty()) {
            throw new NoSuchElementException("calls floor() with empty symbol table");
        }

        Node x = floor(root, key);

        return x == null ? null : x.key;
    }

    private Node floor(Node x, int key) {
        if (x == null) {
            return null;
        }
        int cmp = key - x.key;
        if (cmp == 0) {
            return x;
        }
        if (cmp < 0) {
            return floor(x.left, key);
        }

        Node t = floor(x.right, key);

        return t == null ? x : t;
    }

    /*
     * 向上取整
     */
    public int ceiling(int key) {
        if (isEmpty()) {
            throw new NoSuchElementException("calls ceiling() with empty symbol table");
        }

        Node x = ceiling(root, key);

        return x == null ? null : x.key;
    }

    private Node ceiling(Node x, int key) {
        if (x == null) {
            return null;
        }
        int cmp = key - x.key;
        if (cmp == 0) {
            return x;
        }
        if (cmp > 0) {
            return ceiling(x.right, key);
        }

        Node t = ceiling(x.left, key);

        return t == null ? x : t;
    }

    /*
     * 返回给定键的排名
     */
    public int rank(int key) {
        return rank(key, root);
    }

    private int rank(int key, Node x) {
        if (x == null) {
            return 0;
        }
        int cmp = key - x.key;
        if (cmp < 0) {
            return rank(key, x.left);
        } else if (cmp > 0) {
            return 1 + size(x.left) + rank(key, x.right);
        } else {
            return size(x.left);
        }
    }

    /*
     * 返回排名第k的键
     */
    public int select(int k) {
        if (k < 0 || k >= size()) {
            throw new IllegalArgumentException("argument to select() is invalid: " + k);
        }
        return select(root, k).key;
    }

    private Node select(Node x, int k) {
        if (x == null) {
            return null;
        }
        int t = size(x.left);
        if (t > k) {
            return select(x.left, k);
        } else if (t < k) {
            return select(x.right, k - t - 1);
        } else {
            return x;
        }
    }

    /*
     * 返回树的高度
     */
    public int height() {
        return height(root);
    }

    private int height(Node x) {
        return x == null ? -1 : 1 + Math.max(height(x.left), height(x.right));
    }

    /*
     * 返回所有key
     */
    public Iterable<Integer> keys() {
        if (isEmpty()) {
            return new Queue<Integer>();
        }
        return keys(min(), max());
    }

    /*
     * 返回lo...hi之间的key
     */
    public Iterable<Integer> keys(int lo, int hi) {

        Queue<Integer> queue = new Queue<>();

        if (isEmpty() || lo - hi > 0) {
            return queue;
        }

        keys(root, queue, lo, hi);
        return queue;
    }

    private void keys(Node x, Queue<Integer> queue, int lo, int hi) {
        if (x == null) {
            return;
        }
        int cmplo = lo - x.key;
        int cmphi = hi - x.key;
        if (cmplo < 0) {
            keys(x.left, queue, lo, hi);
        }
        if (cmplo <= 0 && cmphi >= 0) {
            queue.enqueue(x.key);
        }
        if (cmphi > 0) {
            keys(x.right, queue, lo, hi);
        }
    }
}
```

double实现略。

***
**3.5.6 分别为int和double两种原始数据类型的键实现HashSETint类和HashSETdouble类。**

*Answer：*

```java
public class HashSETint implements Iterable<Integer> {

    private LinearProbingHashSTint lphstint;

    public HashSETint(int capacity) {
        lphstint = new LinearProbingHashSTint(capacity);
    }

    public HashSETint() {
        lphstint = new LinearProbingHashSTint();
    }

    public void add(int key) {
        lphstint.put(key);
    }

    public boolean contains(int key) {
        return lphstint.contains(key);
    }

    public void delete(int key) {
        lphstint.delete(key);
    }

    public int size() {
        return lphstint.size();
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public Iterator<Integer> iterator() {
        return lphstint.keys().iterator();
    }

    /*
     * 并集操作
     */
    public HashSETint union(HashSETint that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        HashSETint c = new HashSETint();
        for (int x : this) {
            c.add(x);
        }
        for (int x : that) {
            c.add(x);
        }
        return c;
    }

    /*
     * 交集操作
     */
    public HashSETint intersects(HashSETint that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        HashSETint c = new HashSETint();
        if (this.size() < that.size()) {
            for (int x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (int x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        HashSETint other = (HashSETint) obj;
        if (lphstint == null) {
            if (other.lphstint != null) {
                return false;
            }
        } else if (!lphstint.equals(other.lphstint)) {
            return false;
        }
        return true;
    }
}
```

double类型略。

***
**3.5.7 分别为int和double两种原始数据类型的键实现SETint类和SETdouble类。**

*Answer：*

```java
public class SETint implements Iterable<Integer> {

    private RedBlackBSTint rbbstint;

    public SETint() {
        rbbstint = new RedBlackBSTint();
    }

    public void add(int key) {
        rbbstint.put(key);
    }

    public boolean contains(int key) {
        return rbbstint.contains(key);
    }

    public void delete(int key) {
        rbbstint.delete(key);
    }

    public void deleteMax() {
        rbbstint.deleteMax();
    }

    public void deleteMin() {
        rbbstint.deleteMin();
    }

    public int size() {
        return rbbstint.size();
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public Iterator<Integer> iterator() {
        return rbbstint.keys().iterator();
    }

    public int max() {
        if (isEmpty()) {
            throw new NoSuchElementException("called max() with empty set");
        }
        return rbbstint.max();
    }

    public int min() {
        if (isEmpty()) {
            throw new NoSuchElementException("called min() with empty set");
        }
        return rbbstint.min();
    }

    public int ceiling(int key) {
        int k = rbbstint.ceiling(key);
        if (k == Integer.MAX_VALUE) {
            throw new NoSuchElementException("all keys are less than " + key);
        }
        return k;
    }

    public int floor(int key) {
        int k = rbbstint.floor(key);
        if (k == Integer.MIN_VALUE) {
            throw new NoSuchElementException("all keys are greater than " + key);
        }
        return k;
    }

    /*
     * 并集操作
     */
    public SETint union(SETint that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        SETint c = new SETint();
        for (int x : this) {
            c.add(x);
        }
        for (int x : that) {
            c.add(x);
        }
        return c;
    }

    /*
     * 交集操作
     */
    public SETint intersects(SETint that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        SETint c = new SETint();
        if (this.size() < that.size()) {
            for (int x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (int x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }

    @Override
    public int hashCode() {
        throw new UnsupportedOperationException("hashCode() is not supported because sets are mutable");
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        }
        if (obj == null) {
            return false;
        }
        if (getClass() != obj.getClass()) {
            return false;
        }
        SETint other = (SETint) obj;
        if (rbbstint == null) {
            if (other.rbbstint != null) {
                return false;
            }
        } else if (!rbbstint.equals(other.rbbstint)) {
            return false;
        }
        return true;
    }
}
```

double实现略。

***
**3.5.8 修改LinearProbingHashST，允许在表中保存重复的键。对于get()方法，返回给定键所关联的任意值；对于delete()方法，删除树中所有给定键相等的键值对。**

*Answer：*

```java
public class LinearProbingHashSTEx358<Key, Value> {
    
    private static final int INIT_CAPACITY = 4;

    private int n; // 散列表中键值对的数量
    private int m; // 散列表的大小
    private Key[] keys; // 保存键的数组
    private Value[] vals; // 保存值得数组

    public LinearProbingHashSTEx358() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public LinearProbingHashSTEx358(int capacity) {
        m = capacity;
        n = 0;
        keys = (Key[]) new Object[m];
        vals = (Value[]) new Object[m];
    }

    /*
     * 返回散列表中的键值对数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 存入键值对，允许重复的键
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 键值对数量超过散列表大小的一半，扩大散列表
        if (n >= m / 2)
            resize(2 * m);

        int i = hash(key);
        while (keys[i] != null) {
            i = (i + 1) % m;
        }
        keys[i] = key;
        vals[i] = val;
        n++;
    }

    /*
     * 根据特定的键返回值，返回该键的一个随机值
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        List<Value> list = new ArrayList<>();

        int i;
        for (i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                list.add(vals[i]);
            }
        }

        i = list.size();

        return i == 0 ? null : list.get(StdRandom.uniform(i));
    }

    /*
     * 判断给定的key是否存在于散列表中，不依赖get()方法的实现
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        for (int i = hash(key); keys[i] != null; i = (i + 1) % m) {
            if (keys[i].equals(key)) {
                return true;
            }
        }

        return false;
    }

    /*
     * 删除表中所有和给定键相等的键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        // 循环判断当前散列表是否还包含该键
        while (contains(key)) {
            int i = hash(key);
            while (!key.equals(keys[i])) {
                i = (i + 1) % m;
            }

            // 删除对应的键值
            keys[i] = null;
            vals[i] = null;

            // 重新散列被删除键右侧的所有键并插入散列表
            i = (i + 1) % m;
            while (keys[i] != null) {
                Key keyToRehash = keys[i];
                Value valToRehash = vals[i];
                keys[i] = null;
                vals[i] = null;
                n--;
                put(keyToRehash, valToRehash);
                i = (i + 1) % m;
            }
            n--;
        }

        // 如果键值对数量小于散列表大小的1/8，缩小散列表
        if (n > 0 && n <= m / 8) {
            resize(m / 2);
        }
    }

    /*
     * 以一个Iterable集合的形式返回所有key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (int i = 0; i < m; i++) {
            if (keys[i] != null)
                queue.enqueue(keys[i]);
        }
        return queue;
    }

    // 散列算法，将键散列到[0, m-1]
    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }

    // 调整散列表的大小
    private void resize(int capacity) {
        LinearProbingHashSTEx358<Key, Value> temp = new LinearProbingHashSTEx358<>(capacity);
        for (int i = 0; i < m; i++) {
            if (keys[i] != null) {
                temp.put(keys[i], vals[i]);
            }
        }
        keys = temp.keys;
        vals = temp.vals;
        m = temp.m;
    }

    public static void main(String[] args) {
        LinearProbingHashSTEx358<String, Integer> lphst = new LinearProbingHashSTEx358<>();
        lphst.put("Ad", 23);
        lphst.put("1D", 5);
        lphst.put("0B", 7);
        lphst.put("tC", 22);
        lphst.put("12D", 13);
        lphst.put("1D", 33);
        lphst.put("0B", 0);
        lphst.put("12D", 313);

        // lphst.delete("0B");

        System.out.println(lphst.get("0B")); // 随机返回0、7

        for (Object key : lphst.keys) {
            System.out.print(key + "\t"); // null null 0B 1D Ad 12D 1D 0B 12D null null null null null null tC
        }
        System.out.println();
        for (Object val : lphst.vals) {
            System.out.print(val + "\t"); // null null 7 5 23 13 33 0 313 null null null null null null 22
        }
    }
}
```

***
**3.5.9 修改二叉查找树BST，允许在树中保存重复的键。对于get()方法，返回给定键所关联的任意值；对于delete()方法，删除树中所有和给定键相等的结点。**

```java
public class BSTEx359<Key extends Comparable<? super Key>, Value> {

    private Node<Key, Value> root; // 二叉查找树的根结点

    private static class Node<Key, Value> {
        private Key key; // 键
        private Value val; // 值
        private Node<Key, Value> left, right; // 指向子树的链接
        private int N; // 以该结点为根的子树中的结点总数

        public Node(Key key, Value val, int n) {
            this.key = key;
            this.val = val;
            N = n;
        }
    }

    /*
     * 判断二叉查找树是否为空
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 返回二叉查找树的结点总数
     */
    public int size() {
        return size(root);
    }

    // 返回x的结点总数
    private int size(Node<Key, Value> x) {
        return x == null ? 0 : x.N;
    }

    /*
     * 返回指定区间[lo, hi]内的结点数量
     */
    public int size(Key lo, Key hi) {
        if (lo == null) {
            throw new IllegalArgumentException("first argument to size() is null");
        }
        if (hi == null) {
            throw new IllegalArgumentException("second argument to size() is null");
        }

        if (lo.compareTo(hi) > 0) {
            return 0;
        }
        if (contains(hi)) {
            return rank(hi) - rank(lo) + 1;
        } else {
            return rank(hi) - rank(lo);
        }
    }

    /*
     * 判断二叉查找树是否包含指定的key，不依赖于get()方法
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        return contains(root, key);
    }
    
    private boolean contains(Node<Key, Value> x, Key key) {
        if (x == null) {
            return false;
        }
        int cmp = key.compareTo(x.key);

        if (cmp < 0) {
            return contains(x.left, key);
        } else if (cmp > 0) {
            return contains(x.right, key);
        } else {
            return true;
        }
    }

    /*
     * 返回key对应的值，由于允许重复键，返回一个随机的值
     */
    public Value get(Key key) {

        List<Value> list = get(root, key, new ArrayList<Value>());

        int i = list.size();
        
        return i == 0 ? null : list.get(StdRandom.uniform(i));
    }

    // 递归查找key对应的值
    private List<Value> get(Node<Key, Value> x, Key key, List<Value> list) {

        if (x == null) {
            return list;
        }

        int cmp = key.compareTo(x.key);

        if (cmp < 0) {
            return get(x.left, key, list);
        } else if (cmp > 0) {
            return get(x.right, key, list);
        } else {
            list.add(x.val);
            return get(x.left, key, list); // 向左子树继续查找重复键
        }
    }

    /*
     * 向二叉查找树中插入键值对
     */
    public void put(Key key, Value val) {
        root = put(root, key, val);
    }

    private Node<Key, Value> put(Node<Key, Value> x, Key key, Value val) {

        // 如果结点为null，则创建一个新结点
        if (x == null) {
            Node<Key, Value> node = new Node<>(key, val, 1);
            return node;
        }

        int cmp = key.compareTo(x.key);

        // 两个键相等（插入重复键）时，转向左子树
        if (cmp <= 0) {
            x.left = put(x.left, key, val);
        } else {
            x.right = put(x.right, key, val);
        }

        // 每次插入或更新后更新当前结点的结束器
        x.N = size(x.left) + size(x.right) + 1;
        return x;
    }

    /*
     * 返回最小的key
     */
    public Key min() {
        if (isEmpty()) {
            throw new NoSuchElementException("calls min() with empty symbol table");
        }
        return min(root).key;
    }

    private Node<Key, Value> min(Node<Key, Value> x) {
        return x.left == null ? x : min(x.left);
    }

    /*
     * 返回最大的key
     */
    public Key max() {
        if (isEmpty()) {
            throw new NoSuchElementException("calls max() with empty symbol table");
        }
        return max(root).key;
    }

    private Node<Key, Value> max(Node<Key, Value> x) {
        return x.right == null ? x : max(x.right);
    }

    /*
     * 向下取整
     */
    public Key floor(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to floor() is null");
        }
        if (isEmpty()) {
            throw new NoSuchElementException("calls floor() with empty symbol table");
        }
        Node<Key, Value> x = floor(root, key);

        return x == null ? null : x.key;
    }

    private Node<Key, Value> floor(Node<Key, Value> x, Key key) {
        if (x == null) {
            return null;
        }
        int cmp = key.compareTo(x.key);
        if (cmp == 0) {
            return x;
        }
        if (cmp < 0) {
            return floor(x.left, key);
        }
        Node<Key, Value> t = floor(x.right, key);

        return t == null ? x : t;
    }

    /*
     * 向上取整
     */
    public Key ceiling(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to ceiling() is null");
        }
        if (isEmpty()) {
            throw new NoSuchElementException("calls ceiling() with empty symbol table");
        }
        Node<Key, Value> x = ceiling(root, key);

        return x == null ? null : x.key;
    }

    private Node<Key, Value> ceiling(Node<Key, Value> x, Key key) {
        if (x == null) {
            return null;
        }
        int cmp = key.compareTo(x.key);
        if (cmp == 0) {
            return x;
        }
        if (cmp > 0) {
            return ceiling(x.right, key);
        }
        Node<Key, Value> t = ceiling(x.left, key);

        return t == null ? x : t;
    }

    /*
     * 返回给定键的排名
     */
    public int rank(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to rank() is null");
        }
        return rank(key, root);
    }

    // Number of keys in the subtree less than key.
    private int rank(Key key, Node<Key, Value> x) {
        if (x == null) {
            return 0;
        }
        int cmp = key.compareTo(x.key);
        if (cmp <= 0) {
            return rank(key, x.left);
        } else if (cmp > 0) {
            return 1 + size(x.left) + rank(key, x.right);
        } else {
            return size(x.left);
        }
    }

    /*
     * 返回排名第k的key
     */
    public Key select(int k) {
        if (k < 0 || k >= size()) {
            throw new IllegalArgumentException("argument to select() is invalid: " + k);
        }
        return select(root, k).key;
    }

    // Return key of rank k.
    private Node<Key, Value> select(Node<Key, Value> x, int k) {
        if (x == null) {
            return null;
        }
        int t = size(x.left);
        if (t > k) {
            return select(x.left, k);
        } else if (t < k) {
            return select(x.right, k - t - 1);
        } else {
            return x;
        }
    }

    /*
     * 删除一个最小key（没有删除其重复键）
     */
    public void deleteMin() {
        if (isEmpty()) {
            throw new NoSuchElementException("Symbol table underflow");
        }
        root = deleteMin(root);
    }

    private Node<Key, Value> deleteMin(Node<Key, Value> x) {
        if (x.left == null) {
            return x.right;
        }
        x.left = deleteMin(x.left);
        x.N = size(x.left) + size(x.right) + 1;
        return x;
    }

    /*
     * 删除一个最大key（没有删除其重复键）
     */
    public void deleteMax() {
        if (isEmpty()) {
            throw new NoSuchElementException("Symbol table underflow");
        }
        root = deleteMax(root);
    }

    private Node<Key, Value> deleteMax(Node<Key, Value> x) {
        if (x.right == null) {
            return x.left;
        }
        x.right = deleteMax(x.right);
        x.N = size(x.left) + size(x.right) + 1;
        return x;
    }

    /*
     * 删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("calls delete() with a null key");
        }

        // 加入循环，删除所有与key相等的键
        while (contains(key)) {
            root = delete(root, key);
        }
    }

    private Node<Key, Value> delete(Node<Key, Value> x, Key key) {
        if (x == null) {
            return null;
        }

        int cmp = key.compareTo(x.key);
        if (cmp < 0) {
            x.left = delete(x.left, key);
        } else if (cmp > 0) {
            x.right = delete(x.right, key);
        } else {
            if (x.right == null) {
                return x.left;
            }
            if (x.left == null) {
                return x.right;
            }
            Node<Key, Value> t = x;
            x = min(t.right);
            x.right = deleteMin(t.right);
            x.left = t.left;
        }
        x.N = size(x.left) + size(x.right) + 1;
        return x;
    }

    /*
     * 返回树的高度
     */
    public int height() {
        return height(root);
    }

    private int height(Node<Key, Value> x) {
        return x == null ? -1 : 1 + Math.max(height(x.left), height(x.right));
    }

    /*
     * 范围查找操作
     */
    public Iterable<Key> keys() {
        if (isEmpty()) {
            return new Queue<Key>();
        }
        return keys(min(), max());
    }

    public Iterable<Key> keys(Key lo, Key hi) {
        if (lo == null) {
            throw new IllegalArgumentException("first argument to keys() is null");
        }
        if (hi == null) {
            throw new IllegalArgumentException("second argument to keys() is null");
        }

        Queue<Key> queue = new Queue<>();

        if (isEmpty() || lo.compareTo(hi) > 0) {
            return queue;
        }

        keys(root, queue, lo, hi);
        return queue;
    }

    private void keys(Node<Key, Value> x, Queue<Key> queue, Key lo, Key hi) {
        if (x == null) {
            return;
        }
        int cmplo = lo.compareTo(x.key);
        int cmphi = hi.compareTo(x.key);
        if (cmplo <= 0) { // 条件由“<”改为“<=”
            keys(x.left, queue, lo, hi);
        }
        if (cmplo <= 0 && cmphi >= 0) {
            queue.enqueue(x.key);
        }
        if (cmphi > 0) {
            keys(x.right, queue, lo, hi);
        }
    }

    public static void main(String[] args) {
        BSTEx359<String, Integer> bst = new BSTEx359<>();
        bst.put("A", 1);
        bst.put("A", 2);
        bst.put("A", 3);
        bst.put("A", 7);
        bst.put("B", 2);
        bst.put("B", 2);
        bst.put("C", 2);
        bst.put("D", 2);
        bst.put("D", 2);

        System.out.println(bst.get("A")); // 此时会随机返回1、2、3、7

        for (String string : bst.keys()) {
            System.out.print(string + " "); // A A A A B B C D D 
        }
        System.out.println();

        bst.delete("A"); // 删除所有"A"
        bst.deleteMin(); // 删除一个"B"
        for (String string : bst.keys()) {
            System.out.print(string + " "); // B C D D 
        }
        System.out.println();
        
        System.out.println(bst.size()); // 4
        System.out.println(bst.get("A")); // null
    }
}
```

***
**3.5.10 修改红黑树RedBlackBST，允许在树中保存重复的键。对于get()方法，返回给定键所关联的任意值；对于delete()方法，删除树中所有和给定键相等的结点。**

*Answer：*

***
**3.5.11 开发一个和SET相似的类MultiSET，允许出现相等的键，也就时实现了数学上的多重集合。**

*Answer：*

内部封装一个允许重复键的二叉查找树（没有使用红黑树）。

```java
public class MulitSET<Key extends Comparable<? super Key>> implements Iterable<Key>{
    
    private static final int DUMMY = 0;
    
    private BSTEx359<Key, Integer> bst;
    

    public MulitSET() {
        bst = new BSTEx359<>();
    }
    
    public void add(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called add() with a null key");
        }
        bst.put(key, DUMMY);
    }
    
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called contains() with a null key");
        }
        return bst.contains(key);
    }

    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called delete() with a null key");
        }
        bst.delete(key);
    }
    
    public void deleteMax() {
        bst.deleteMax();
    }
    
    public void deleteMin() {
        bst.deleteMin();
    }
    
    public int size() {
        return bst.size();
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    @Override
    public Iterator<Key> iterator() {
        return bst.keys().iterator();
    }
    
    public Key max() {
        if (isEmpty()) {
            throw new NoSuchElementException("called max() with empty set");
        }
        return bst.max();
    }

    public Key min() {
        if (isEmpty()) {
            throw new NoSuchElementException("called min() with empty set");
        }
        return bst.min();
    }

    public Key ceiling(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called ceiling() with a null key");
        }
        Key k = bst.ceiling(key);
        if (k == null) {
            throw new NoSuchElementException("all keys are less than " + key);
        }
        return k;
    }
    
    public Key floor(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("called floor() with a null key");
        }
        Key k = bst.floor(key);
        if (k == null) {
            throw new NoSuchElementException("all keys are greater than " + key);
        }
        return k;
    }
    
    /*
     * 并集操作
     */
    public MulitSET<Key> union(MulitSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        MulitSET<Key> c = new MulitSET<>();
        for (Key x : this) {
            c.add(x);
        }
        for (Key x : that) {
            c.add(x);
        }
        return c;
    }
    
    /*
     * 交集操作
     */
    public MulitSET<Key> intersects(MulitSET<Key> that) {
        if (that == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }
        MulitSET<Key> c = new MulitSET<>();
        if (this.size() < that.size()) {
            for (Key x : this) {
                if (that.contains(x)) {
                    c.add(x);
                }
            }
        } else {
            for (Key x : that) {
                if (this.contains(x)) {
                    c.add(x);
                }
            }
        }
        return c;
    }
}
```

***
**3.5.12 修改LookupCSV，将每个键和输入中与该键对应的所有值相关联。**

*Answer：*

原LookupCSV内部封装的是一个RedBlackBST，这里改成可以接受重复键的红黑树实现，并修改get()方法使其返回所有的对应值就可以了。

***
**3.5.13 修改LookupCSV为RangeLookupCSV，从标准输入接受两个键并打印.csv文件中所有在该范围之间的键值对。**

*Answer：*

```java
public class RangeLookupCSV {
    
    public static void main(String[] args) {
        In in = new In(args[0]);
        int keyField = Integer.parseInt(args[1]);
        int valField = Integer.parseInt(args[2]);
        RedBlackBST<String, String> rbbst = new RedBlackBST<>();

        while (in.hasNextLine()) {
            String line = in.readLine();
            String[] tokens = line.split(",");
            String key = tokens[keyField];
            String val = tokens[valField];
            rbbst.put(key, val);
        }

        while (!StdIn.isEmpty()) {
            String query_1 = StdIn.readString(); // 接受参数1
            String query_2 = StdIn.readString(); // 接受参数2
            
            // 如果参数1大于参数2
            if(query_1.compareTo(query_2) > 0) {
                throw new IllegalArgumentException();
            }
            
            for (String string : rbbst.keys(query_1, query_2)) {
                System.out.println(rbbst.get(string));
            }
            System.out.println();
        }
    }
}
```

***
**3.5.14 编写并测试方法invert()，它接受参数ST<String, Bag<String>>并返回给定符号表的反向索引（一个相同类型的符号表）。**

*Answer：*

```java
public class Invert {

    public static void main(String[] args) {
        Bag<String> bag1 = new Bag<>();
        bag1.add("A");
        bag1.add("B");
        bag1.add("C");
        
        Bag<String> bag2 = new Bag<>();
        bag2.add("X");
        bag2.add("Y");
        bag2.add("Z");
        
        Bag<String> bag3 = new Bag<>();
        bag3.add("B");
        bag3.add("C");
        bag3.add("X");
        bag3.add("Y");
        
        RedBlackBST<String, Bag<String>> rbbst = new RedBlackBST<>();
        rbbst.put("1", bag1);
        rbbst.put("2", bag2);
        rbbst.put("3", bag3);
        
        rbbst = invert(rbbst);
        
        for (String key : rbbst.keys()) {
            System.out.print(key + ": ");
            for (String ele : rbbst.get(key)) {
                System.out.print(ele + " ");
            }
            System.out.println();
        }
    }

    private static RedBlackBST<String, Bag<String>> invert(RedBlackBST<String, Bag<String>> that) {

        RedBlackBST<String, Bag<String>> rbbst = new RedBlackBST<>();

        for (String key : that.keys()) { // 遍历that的所有键
            for (String element : that.get(key)) { // 遍历这个bag中的所有元素
                if (!rbbst.contains(element) ) { // 如果rbbst中不包含该元素
                    rbbst.put(element, new Bag<>()); // 存入键值对
                }
                Bag<String> tmp = rbbst.get(element); // 取出对应的bag
                tmp.add(key); // bag存入key
            }
        }
        return rbbst;
    }
}
```

输入正向索引：

    1: A B C
    2: X Y Z
    3: B C X Y

生成反向索引：

    A: 1 
    B: 3 1 
    C: 3 1 
    X: 3 2 
    Y: 3 2 
    Z: 2 

***
**3.5.15 编写一个程序，从标准输入接受一个字符串并从命令行参数获得一个整数k作为参数，在标准输出中有序打印出在字符串中找到的k元文法（k-gram），以及每个k-gram在字符串中的位置。**

*Answer：*

***
**3.5.16 为SparseVector添加一个sum()方法，接受一个SparseVector对象作为参数并将两者相加的结果返回一个SparseVector对象。**

*Answer：*

```java
// 参见官方实现的SparseVector
/**
 * Returns the sum of this vector and the specified vector.
 *
 * @param that
 *            the vector to add to this vector
 * @return the sum of this vector and that vector
 * @throws IllegalArgumentException
 *             if the dimensions of the two vectors are not equal
 */
public SparseVector plus(SparseVector that) {
    if (this.d != that.d)
        throw new IllegalArgumentException("Vector lengths disagree");
    SparseVector c = new SparseVector(d);
    for (int i : this.st.keys())
        c.put(i, this.get(i)); // c = this
    for (int i : that.st.keys())
        c.put(i, that.get(i) + c.get(i)); // c = c + that
    return c;
}
```

***

#### 提高题

***
**3.5.17 数学集合。你的目标是实现表中MathSET的API来处理（可变的）数学集合。请使用符号表实现它。**

API：

- MathSET<Key>
    + MathSET(Key[] universe)：创建一个集合
    + void add(Key key)：将key加入集合
    + MathSET<Key> complement()：所有在universe中并且不在该集合中的键的集合
    + void union(MathSET<Key> a)：将a中所有不在该集合中的键加入该集合（并集）
    + void intersection(MathSET<Key> a)：将该集合中所有不在a中的键删除（交集）
    + void delete(Key key)：将key从集合中删去
    + boolean contains(Key key)：集合中是否存在键key
    + boolean isEmpty()：集合是否为空
    + int size()：集合中键的总数

*Answer：*

```java
public class MathSET<Key> implements Iterable<Key> {

    private HashSET<Key> hashSET;

    private Key[] universe; // U，全集

    public MathSET(Key[] universe) {
        this.universe = universe;
        hashSET = new HashSET<>();
    }

    public void add(Key key) {
        hashSET.add(key);
    }

    /*
     * 求补集
     */
    public MathSET<Key> complement() {

        MathSET<Key> tmp = new MathSET<>(this.universe);

        for (Key key : universe) {
            if (!contains(key)) {
                tmp.add(key);
            }
        }

        return tmp;
    }

    /*
     * 求并集
     */
    public void union(MathSET<Key> a) {
        if (a == null) {
            throw new IllegalArgumentException("called union() with a null argument");
        }
        for (Key key : a) {
            add(key);
        }
    }

    /*
     * 求交集
     */
    public void intersection(MathSET<Key> a) {
        if (a == null) {
            throw new IllegalArgumentException("called intersects() with a null argument");
        }

        MathSET<Key> tmp = new MathSET<>(universe);

        if (this.size() < a.size()) {
            for (Key x : this) {
                if (a.contains(x)) {
                    tmp.add(x);
                }
            }
        } else {
            for (Key x : a) {
                if (this.contains(x)) {
                    tmp.add(x);
                }
            }
        }
        hashSET = tmp.hashSET;
    }

    public void delete(Key key) {
        hashSET.delete(key);
    }

    public boolean contains(Key key) {
        return hashSET.contains(key);
    }

    public int size() {
        return hashSET.size();
    }

    public boolean isEmpty() {
        return hashSET.isEmpty();
    }
    
    @Override
    public String toString() {
        
        if (size() == 0) {
            return "{}";
        }
        
        StringBuilder sb = new StringBuilder();
        sb.append("{");
        for (Key key : this) {
            sb.append(key + ", ");
        }
        sb.replace(sb.length() - 2, sb.length(), "}");
        return sb.toString();
    }

    @Override
    public Iterator<Key> iterator() {
        return hashSET.iterator();
    }

    public static void main(String[] args) {
        Integer[] universe = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
        MathSET<Integer> setA = new MathSET<>(universe);
        MathSET<Integer> setB = new MathSET<>(universe);
        
        setA.add(4);
        setA.add(6);
        setA.add(8);
        setA.add(10);
        
        setB.add(2);
        setB.add(8);
        setB.add(10);
        
        System.out.println(setA); // {8, 10, 4, 6}
        System.out.println(setB); // {8, 2, 10}
        
        setA.union(setB);
        System.out.println(setA); // {2, 4, 6, 8, 10}
        
        MathSET<Integer> setC = setA.complement();
        System.out.println(setC); // {1, 3, 5, 7, 9}
        
        setA.intersection(setB);
        System.out.println(setA); // {8, 2, 10}
        
        setA.delete(2);
        System.out.println(setA); // {8, 10}
        
        setA.intersection(setC);
        System.out.println(setA); // {}
    }
}
```

***
**3.5.18 多重集合。请参考练习3.5.2、练习3.5.3以及前面的练习，为无序和有序的多重集合（可以含有相同键的集合）给出MultiHashSET和MultiSET的API，并分别用SeparateChainingMultiST和BinarySearchMultiST实现它们。**

*Answer：*

根据下一题中SeparateChainingMultiST和BinarySearchMultiST的实现来实现对应的SET即可。

例如：

SequentialSearchMultiST（可以包含重复键的单链表实现的符号表）---实现--->SeparateChianingMultiST（可含有重复键的散列表）---实现--->MultiHashSET（添加相关集合操作）

总而言之，各种ST和SET之间的实现关系：

- **有序实现的集合，不允许重复键（有序实现的SET）**：
    + 底层使用BST、RedBlackBST或BinarySearchST实现。
    + 可以删除底层关于值的操作相关操作，也可以在SET实现中添加虚拟值DUMMY。
    + 在SET中实现相关集合操作。
- **无序实现的集合，不允许重复键（无序实现的SET）**：
    + 底层使用SeparateChainingHashST（底层为SequentialSearchST）、LinearProbingHashST实现。
    + 可以删除底层关于值的操作相关操作，也可以在SET实现中添加虚拟值DUMMY。
    + 在SET中实现相关集合操作。
- **有序实现的集合，允许重复键（有序实现的MultiSET）**：
    + 底层使用允许重复键的BST、RedBlackBST或BinarySearchST实现。
    + 可以删除底层关于值的操作相关操作，也可以在SET实现中添加虚拟值DUMMY。
    + 在MultiSET中实现相关集合操作。
- **无序实现的集合，允许重复键（无序实现的MultiSET）**：
    + 底层使用允许重复键的SeparateChainingHashST（底层为允许重复键的SequentialSearchST）、LinearProbingHashST实现。
    + 可以删除底层关于值的操作相关操作，也可以在SET实现中添加虚拟值DUMMY。
    + 在MultiSET中实现相关集合操作。

***
**3.5.19 符号表中的等值键。（有序的和无序的）MultiST的API分别和表3.1.2以及表3.1.4中定义的符号表API相同，只是允许存在等值的键。因此，get()方法的行为是返回给定键所关联的任意值。另外，我们还需要添加一个新方法来返回给定键关联的所有值：Iterable<Value> getAll(Key key)。根据我们的SeparaChainingHashST和BinarySearchST的代码来实现SeparateChianingMultiST和BinarySearchMultiST的API。**

*Answer：*

SeparateChianingMultiST：

```java
// 允许出现重复值的无序单链表实现的符号表
class SequentialSearchMultiST<Key, Value> {

    private Node<Key, Value> first; // 链表首结点

    private int N;

    private static class Node<Key, Value> {
        Key key;
        Value val;
        Node<Key, Value> next;

        public Node(Key key, Value val, Node<Key, Value> next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }

    public int size() {
        return N;
    }

    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据键，返回一个随机的对应值
     */
    public Value get(Key key) {

        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }

        List<Value> list = new ArrayList<>();

        for (Node<Key, Value> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                list.add(x.val);
            }
        }

        int i = list.size();

        return i == 0 ? null : list.get(StdRandom.uniform(i));
    }
    
    /*
     * 返回一个键对应的所有值
     */
    public Iterable<Value> getAll(Key key) {
        Queue<Value> queue = new Queue<>(); 
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                queue.enqueue(x.val);
            }
        }
        return queue;
    }
    
    /*
     * 插入键值对，允许重复键
     */
    public void put(Key key, Value val) {

        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        first = new Node<>(key, val, first); // 未命中，新建结点
        N++;
    }

    /*
     * 检测链表是否包含某个key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            if (key.equals(x.key)) {
                return true;
            }
        }
        return false;
    }

    /*
     * 根据键删除键值对
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }
        first = delete(first, key);
    }

    private Node<Key, Value> delete(Node<Key, Value> x, Key key) {
        if (x == null) {
            return null;
        }
        if (key.equals(x.key)) {
            N--;
            return x.next;
        }
        x.next = delete(x.next, key);
        return x;
    }

    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<>();
        for (Node<Key, Value> x = first; x != null; x = x.next) {
            queue.enqueue(x.key);
        }
        return queue;
    }
}

// 提高题3.5.19
// 允许重复值的拉链法散列表
public class SeparateChainingMulitiST<Key, Value> {

    private static final int INIT_CAPACITY = 4; // 初始数组长度

    private int n; // 键值对的数量
    private int m; // 散列表的大小

    private SequentialSearchMultiST<Key, Value>[] st;

    /*
     * 构造函数
     */
    public SeparateChainingMulitiST() {
        this(INIT_CAPACITY);
    }

    @SuppressWarnings("unchecked")
    public SeparateChainingMulitiST(int m) {
        this.m = m;
        st = (SequentialSearchMultiST<Key, Value>[]) new SequentialSearchMultiST[m];
        for (int i = 0; i < m; i++) {
            st[i] = new SequentialSearchMultiST<>();
        }
    }

    /*
     * 返回散列表中键值对的数量
     */
    public int size() {
        return n;
    }

    /*
     * 判断散列表是否是空的
     */
    public boolean isEmpty() {
        return size() == 0;
    }

    /*
     * 根据key返回Value
     */
    public Value get(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to get() is null");
        }
        int i = hash(key);
        return st[i].get(key);
    }

    /*
     * 存入键值对key-Value
     */
    public void put(Key key, Value val) {
        if (key == null) {
            throw new IllegalArgumentException("first argument to put() is null");
        }

        if (val == null) {
            delete(key);
            return;
        }

        // 如果链表的平均长度大于10，对散列表进行调整扩大
        if (n >= 10 * m) {
            resize(2 * m);
        }

        int i = hash(key);
        st[i].put(key, val);
        n++;
    }

    /*
     * 判断散列表是否包含指定的key
     */
    public boolean contains(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to contains() is null");
        }
        int i = hash(key);
        return st[i].contains(key);
    }

    /*
     * 根据给定的键删除键值对，有重复键时删除所有重复键
     */
    public void delete(Key key) {
        if (key == null) {
            throw new IllegalArgumentException("argument to delete() is null");
        }

        int i = hash(key);
        while (st[i].contains(key)) {
            st[i].delete(key);
            n--;
        }

        // 如果链表的平均长度小于2，对散列表进行缩小
        if (m > INIT_CAPACITY && n <= 2 * m) {
            resize(m / 2);
        }
    }
    
    /*
     * 以一个Iterable集合的方式返回所有的key
     */
    public Iterable<Key> keys() {
        Queue<Key> queue = new Queue<Key>();
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                queue.enqueue(key);
            }
        }
        return queue;
    }
    
    /*
     * 返回一个键对应的所有值
     */
    public Iterable<Value> getAll(Key key) {
        int i = hash(key);
        return st[i].getAll(key);
    }
    
    // 将key散列到[0, m-1]
    private int hash(Key key) {
        return (key.hashCode() & 0x7fffffff) % m;
    }
    
    // 对散列表扩大并重新散列所有键值对
    private void resize(int chains) {
        SeparateChainingMulitiST<Key, Value> temp = new SeparateChainingMulitiST<Key, Value>(chains);
        for (int i = 0; i < m; i++) {
            for (Key key : st[i].keys()) {
                temp.put(key, st[i].get(key));
            }
        }
        this.m = temp.m;
        this.n = temp.n;
        this.st = temp.st;
    }
    
    public static void main(String[] args) {
        SeparateChainingMulitiST<String,Integer> scmst = new SeparateChainingMulitiST<>();
        scmst.put("A", 0);
        scmst.put("B", 2);
        scmst.put("C", 3);
        scmst.put("A", 5);
        scmst.put("d", 4);
        
        for (String string : scmst.keys()) { 
            System.out.print(string + " "); // d A A B C 
        }
        System.out.println();
        for (Integer i : scmst.getAll("A")) {
            System.out.print(i + " "); // 5 0 
        }
    }
}
```

BinarySearchMultiST

略。

***
**3.5.20 对照索引。编写一个ST的用例Concordance，为从标准输入得到的字符串构建对照索引并打印出来。**

*Answer：*

```java
// ST的底层实现为TreeMap，SET的底层实现为TreeSet
public class Concordance {

    public static void main(String[] args) {

        int CONTEXT = 5;

        In in = new In(args[0]);
        String[] words = in.readAllStrings();
        ST<String, SET<Integer>> st = new ST<String, SET<Integer>>();

        // build up concordance
        for (int i = 0; i < words.length; i++) {
            String s = words[i];
            if (!st.contains(s)) {
                st.put(s, new SET<Integer>());
            }
            SET<Integer> set = st.get(s);
            set.add(i);
        }
        StdOut.println("Finished building concordance");

        // process queries
        while (!StdIn.isEmpty()) {
            
            String query = StdIn.readString();
            
            SET<Integer> set = st.get(query);
            if (set == null) {
                set = new SET<Integer>();
            }
            for (int k : set) {
                for (int i = Math.max(0, k - CONTEXT + 1); i < k; i++) {
                    StdOut.print(words[i] + " ");
                }
                StdOut.print("*" + words[k] + "* ");
                for (int i = k + 1; i < Math.min(k + CONTEXT, words.length); i++) {
                    StdOut.print(words[i] + " ");
                }
                StdOut.println();
            }
            StdOut.println();
        }
    }
}
```

输入《双城记》，查询king：

    Finished building concordance
    king
    only there were a *king* with a large jaw 
    england there were a *king* with a large jaw 
    same possessions as the *king* the first minister of 
    wife had implored the *king* the queen the court 
    prince our lord the *king* by reason of his 
    assisted lewis the french *king* in his wars against 
    been presented to the *king* himself what do i 
    was presented to the *king* and queen all here 
    yourself excepted saw the *king* take it in his 
    life of the late *king* louis fifteen but how 
    see the fine worldthe *king* the queen and court 
    the carriage of the *king* and queen you work 
    if he needed a *king* and queen to restore 
    for soon the largefaced *king* and the fairfaced queen 
    cried long live the *king* long live the queen 
    fountains green banks more *king* and queen more bulls 
    the fallen and unfortunate *king* of france had been 
    the people of the *king* and the royal family 
    charles darnay that the *king* was in prison and 
    new era began the *king* was tried doomed and 
    his most gracious majesty *king* george the third miss 
    fix god save the *king* mr cruncher in an 

**3.5.21 反向对照索引。编写一个程序InvertedConcordance，从标准输入接受一个对照索引并在标准输出中打印原始的字符串。**

*Answer：*

***
**3.5.22 完全索引的CSV文件。编写一个ST的用例FullLookupCSV，构造一个ST对象的数组（每列一个），以及一个允许使用者指定键和值的列的测试用例。**

```java
// 以CSV中0列为key，1到m-1分别为值构建ST数组
// ST数组分别为0-1，0-2，0-3，...，0-m-1索引
// args[1]：数组的初始大小，需大于等于csv文件中每行字符串分割出的tokens.length - 1
// 没有考虑CSV中的内部逗号问题，例如：0837101306522,,"Elevate - elevate CD, 10 songs www.youthelevate.com"  这个""中的逗号不应该作为分隔符
public class FullLookupCSV {

    @SuppressWarnings("unchecked")
    public static void main(String[] args) {

        In in = new In(args[0]); // 要读取的csv文件

        int size = Integer.parseInt(args[1]); // 数组的大小

        if (size < 1) {
            throw new IllegalArgumentException("索引数组大小不能小于1");
        }

        @SuppressWarnings("rawtypes")
        RedBlackBST[] rbbsts = new RedBlackBST[size];

        int cnt = -1; // 使用的数组角标
        
        while (in.hasNextLine()) {

            String[] tokens = in.readLine().split(",");

            for (int i = 1; i < tokens.length; i++) {
                if (rbbsts[i - 1] == null) {
                    rbbsts[i - 1] = new RedBlackBST<>();
                    cnt++;
                }
                rbbsts[i - 1].put(tokens[0], tokens[i]); // tokens[i]可能是空字符串""
            }
        }
        
        System.out.println("索引构建完毕，可以开始检索....");

        while (!StdIn.isEmpty()) {

            String[] querys = StdIn.readString().split(","); // 查询以“,”分隔参数

            String key = querys[0]; // 要查找的键
            int col = Integer.parseInt(querys[1]); // 要查找的索引列

            if (col > cnt) {
                throw new IllegalArgumentException("无当前列的索引");
            }

            if (rbbsts[col].contains(key)) {
                String result = (String) rbbsts[col].get(key);
                result = result.equals("") ? "当前列中该键的索引为空字符串" : result;
                System.out.println(result);
            } else {
                System.out.println("该值不在索引中");
            }
        }
    }
}
```

***
**3.5.23 稀疏矩阵。为系数二维矩阵设计一组API并将它实现，支持矩阵的加法和乘法操作。包含分别能够指定行和列向量的构造函数。**

*Answer：*

```java
// 官方实现
public class SparseMatrix {
    
    private int n; // n-by-n matrix
    private SparseVector[] rows; // the rows, each row is a sparse vector

    // initialize an n-by-n matrix of all 0s
    public SparseMatrix(int n) {
        this.n = n;
        rows = new SparseVector[n];
        for (int i = 0; i < n; i++)
            rows[i] = new SparseVector(n);
    }

    // put A[i][j] = value
    public void put(int i, int j, double value) {
        if (i < 0 || i >= n)
            throw new IllegalArgumentException("Illegal index");
        if (j < 0 || j >= n)
            throw new IllegalArgumentException("Illegal index");
        rows[i].put(j, value);
    }

    // return A[i][j]
    public double get(int i, int j) {
        if (i < 0 || i >= n)
            throw new IllegalArgumentException("Illegal index");
        if (j < 0 || j >= n)
            throw new IllegalArgumentException("Illegal index");
        return rows[i].get(j);
    }

    // return the number of nonzero entries (not the most efficient implementation)
    public int nnz() {
        int sum = 0;
        for (int i = 0; i < n; i++)
            sum += rows[i].nnz();
        return sum;
    }

    // return the matrix-vector product b = Ax
    public SparseVector times(SparseVector x) {
        if (n != x.dimension())
            throw new IllegalArgumentException("Dimensions disagree");
        SparseVector b = new SparseVector(n);
        for (int i = 0; i < n; i++)
            b.put(i, rows[i].dot(x));
        return b;
    }

    // return this + that
    public SparseMatrix plus(SparseMatrix that) {
        if (this.n != that.n)
            throw new RuntimeException("Dimensions disagree");
        SparseMatrix result = new SparseMatrix(n);
        for (int i = 0; i < n; i++)
            result.rows[i] = this.rows[i].plus(that.rows[i]);
        return result;
    }

    // return a string representation
    public String toString() {
        String s = "n = " + n + ", nonzeros = " + nnz() + "\n";
        for (int i = 0; i < n; i++) {
            s += i + ": " + rows[i] + "\n";
        }
        return s;
    }
}
```

***
**3.5.24 不重叠的区间查找，给定对象的一组互不重叠的区间，编写一个函数接受一个对象作为参数并判断它是否存在与其中一个区间之内。例如，如果对象是整数而区间为1643-2033，5532-7643，8999-10332，5666653-5669321，那么查询9122的结果为第三个区间，而8122的结果是不在任何区间。**

*Answer：*

对象需要实现Comparable，然后依次比较区间的两个边界即可。

***
**3.5.25 登记员的日程安排。东北部某著名大学的注册主任最近做出的安排中有一位老师需要在同一时间为两个不同的班级授课。请用一种方法来键差类似的冲突，帮助这位主任不再犯同样的错误。**

*Answer：*

将一位老师所有要上课的时间间隔实现为Interval1D，遍历所有的Interval1D比较两个对象是否intersects。

***
**3.5.6 LRU缓存。创建一个支持以下操作的数据结构：访问和删除。访问操作会将不存在与数据结构中的元素插入。删除操作会删除并返回最近最少访问的元素。**

*Answer：*

将元素按照访问的先后顺序保存在一条双向链表中，并保存指向开头和结尾元素的指针。将元素和元素在链表中的位置分别作为键和相应的值保存在一张符号表中。

- 当访问一个元素时，将它从链表中删除并重新插入链表的头部。
- 当删除一个元素时，将它从链表尾部和符号表中删除。

***
**3.5.27 列表。实现下表的API：**

- public class List<Item> implements Iterable<Item>
    + List()：创建一个列表
    + void addFront(Item item)：将item添加到列表的头部
    + void addBack(Item item)：将item添加到列表的尾部
    + Item deleteFront()：删除列表头部的元素
    + Item deleteBack()：删除列表尾部的元素
    + void delete(Item item)：从列表中删除item
    + void add(int i, Item item)：将item添加为列表的第i个元素
    + Item delete(int i)：从列表中删除第i个元素
    + boolean contains(Item item)：列表中是否存在元素item
    + boolean isEmpty()：列表是否为空
    + int size()：列表中元素的总数

*Answer：*

根据add方法的表述（将item添加为列表的第i个元素），判断i为元素在列表中的位置，而不是大小次序。

使用一个双向链表和一个符号表进行实现：

- 双向链表和符号表均保存键。
    + addFront(Item item)：链表头部和符号表添加元素
    + addBack(Item item)：链表尾部和符号表添加元素
    + Item deleteFront()：链表头部和符号表删除元素
    + Item deleteBack()：链表尾部和符号表删除元素
    + delete(Item item)：遍历链表删除元素，符号表删除元素
    + add(int i, Item item)：遍历链表到达指定位置，添加元素，符号表添加元素
    + delete(int i)：遍历链表达到指定位置，删除元素，符号表删除元素
    + contains(Item item)：使用符号表的contains()方法确定是否包含元素（符号表的contains()方法更高效）
    + isEmpty()：返回链表的isEmpty()方法
    + size()：返回链表的size()方法

也可以单独使用双向链表或数组、以及数组搭配符号表实现。

***
**3.5.28 uniQueue。创建一个类似于队列的数据类型，但每个元素只能插入队列依次。用一个符号表来记录所有已经被插入的元素并忽略所有将它们重新插入的请求。**

*Answer：*

插入前使用contains()判断，如果已经含有该键，则直接返回。

***
**3.5.29 支持随机访问的符号表。创建一个数据结构，能够向其中插入键值对，查找一个键并返回相应的值以及删除并返回一个随机的键。**

*Answer：*

使用符号表保存键值对，使用一个随机队列保存键。当发生随机删除时，调用随机队列的dequeue()，随机返回一个键，将这个键传入符号表的delete()方法以完成随机删除，最终返回这个键。

***

#### 实验题

***

本章实验题均为对性能的测试，暂略。

## 第四章 图

### 4.1 无向图

***

#### 练习题

***
**4.1.1 一副含有V个顶点且不含平行边的图中至少含有多少条边？一副含有V个顶点的连通图中至少含有多少条边。**

*Answer：*

1. 由于不要求连通，所以V个结点可以是彼此不相连的，所以可以为0条边。
2. $V-1$

***
**4.1.2 按照正文中示意图的样式（请见图4.1.9）画出Graph的构造函数在处理图4.1.25的tinyGex2.txt时构造的邻接表。**

tinyGex2.txt数据如下：

```text
12
16
8 4
2 3
1 11
0 6 
3 6
10 3
7 11
7 8
11 8
2 0
6 2
5 2
5 10
5 0
8 1
4 1
```

生成的图如下：

![tinyGex2.txt](images\chapter04\tinyGex2.txt.png)

*Answer：*

```text
0  --->             5 2 6
1  --->             4 8 1
2  --->           5 6 0 3
3  --->            10 6 2
4  --->               1 8
5  --->            0 10 2
6  --->             2 3 0
7  --->              8 11
8  --->          1 11 7 4
9  --->     
10 --->               5 3
11 --->             8 7 1
```

***
**4.1.3 为Graph添加一个复制构造函数，它接受一幅图G然后创建并初始化这幅图的一个副本。G的用例对它作出任何改动都不应该影响到它的副本。**

*Answer：*

```java
public Graph(Graph G) {

    this(G.V());
    this.E = G.E();

    for (int v = 0; v < G.V(); v++) {
        // reverse so that adjacency list is in same order as original
        Stack<Integer> reverse = new Stack<>();
        for (int w : G.adj[v]) {
            reverse.push(w);
        }
        for (int w : reverse) {
            adj[v].add(w);
        }
    }
}
```

***
**4.1.4 为Graph添加一个方法hasEdge()，他接受两个整型参数v和w，如果图含有边v-w，方法返回true，否则返回false。**

*Answer：*

```java
public boolean hasEdge(int v, int w) {
    validateVertex(v);
    validateVertex(w);

    for (Integer i : adj(v)) {
        if (i == w) {
            return true;
        }
    }
    return false;
}
```

***
**4.1.5 修改Graph，不允许存在平行边和自环。**

*Answer：*

修改addEdge方法：

```java
public void addEdge(int v, int w) {

    // 防止自环
    if (v == w) {
        throw new IllegalArgumentException("Self Ring is not permitted.");
    }
    
    // 防止平行边
    if (hasEdge(v, w)) {
        throw new IllegalArgumentException("Parallel Edge is not permitted.");
    }
    
    E++;
    adj[v].add(w);
    adj[w].add(v);
}
```

***
**4.1.6 有一张含有四个顶点的图，其中边为0-1、1-2、2-3和3-0.给出一种邻接表数组，无论以任何顺序调用addEdge()来添加这些边都无法创建它。**

*Answer：*

```text
0  ---> 3 1
1  ---> 0 2
2  ---> 1 3
3  ---> 2 0
```

上述邻接表中元素的出现顺序互相冲突。
假设上述邻接表可以通过addEdge()构造，那么如果要0号中元素的顺序为3、1，则说明先添加0-1，再添加0-3；如果在添加0-1时要保证1号中元素顺序为0、2，则意味着在添加0-1前应该先添加1-2；以此类推，在添加1-2前应该先添加2-3，而添加2-3之前则要先添加3-0。先添加3-0与开头先添加0-1冲突，所以这个邻接表不能通过addEdge()构造。

***
**4.1.7 为Graph编写一个测试用例，接受用命令行参数指定输入流构造的一幅图，然后用toString()方法将其打印出来。**

*Answer：*

读取tinyCG.txt，构造图：

```java
public static void main(String[] args) {
    Graph G = new Graph(new In(args[0]));
    System.out.println(G);
}
```

```text
6 vertices, 8 edges 
0: 2 1 5 
1: 0 2 
2: 0 1 3 4 
3: 5 4 2 
4: 3 2 
5: 3 0
```

***
**4.1.8 按照正文中的要求，用union-find算法实现4.1.2.3中的搜索的API。**

```java
public class UnionFindSearch {

    private QuickUnionUF uf; // union-find算法实现
    private int count; // 与起点连接的顶点的数量（含起点自己）
    private int s; // 起点
    private int N; // 图G的顶点总数

    public UnionFindSearch(Graph G, int s) {
        
        this.N = G.V();
        validateVertex(s);
        this.s = s;
        
        uf = new QuickUnionUF(N);
        dfs(G, s);
    }

    /*
     * 顶点v是否与起点s连通
     */
    public boolean marked(int v) {
        validateVertex(v);
        return uf.connected(v, s);
    }

    /*
     * 与起点s连通的顶点总数（包含起点s自己）
     */
    public int count() {
        return count;
    }

    // 使用union-find实现dfs算法
    private void dfs(Graph G, int v) {
        count++;
        for (int w : G.adj(v)) {
            if (!uf.connected(v, w)) {
                uf.union(v, w);
                dfs(G, w);
            } 
        }
    }

    private void validateVertex(int v) {
        if (v < 0 || v >= N) {
            throw new IllegalArgumentException("vertex " + v + " is not between 0 and " + (N - 1));
        }
    }
    
    public static void main(String[] args) {
        Graph G = new Graph(5);
        G.addEdge(0, 1);
        G.addEdge(0, 2);
        G.addEdge(0, 3);
        G.addEdge(1, 2);
        G.addEdge(1, 3);
        G.addEdge(3, 4);
        UnionFindSearch ufs = new UnionFindSearch(G, 0);
        System.out.println(ufs.count()); // 5
        System.out.println(ufs.marked(4)); // true
    }
}
```

***
**4.1.9 使用dfs(0)处理由Graph的构造函数从tinyGex2.txt得到的图并按照4.1.3.5节的图4.1.14的样式给出详细的轨迹。同时画出edgeTo[]所表示的树。**

*Answer：*

0所在连通分量的邻接表：

```text
0  --->             5 2 6
2  --->           5 6 0 3
3  --->            10 6 2
5  --->            0 10 2
6  --->             2 3 0
10 --->               5 3
```

由于DepthFirstSearch没有edgeTo[]数组，使用DepthFirstPaths的dfs()方法：

```java
public static void main(String[] args) {
    
    Graph G = new Graph(12);
    G.addEdge(8, 4);
    G.addEdge(2, 3);
    G.addEdge(1, 11);
    G.addEdge(0, 6);
    G.addEdge(3, 6);
    G.addEdge(10, 3);
    G.addEdge(7, 11);
    G.addEdge(7, 8);
    G.addEdge(11, 8);
    G.addEdge(2, 0);
    G.addEdge(6, 2);
    G.addEdge(5, 2);
    G.addEdge(5, 10);
    G.addEdge(5, 0);
    G.addEdge(8, 1);
    G.addEdge(4, 1);
    
    DepthFirstPaths search = new DepthFirstPaths(G, 0);
    
    for (int v = 0; v < G.V(); v++) {
        System.out.print(0 + " to " + v + ": ");
        if (search.hasPathTo(v)) {
            for (int  x : search.pathTo(v)) {
                if (x == 0) {
                    System.out.print(x);
                } else {
                    System.out.print("-" + x);
                }
            }
        }
        System.out.println();
    }
}
```

详细轨迹略。

edgeTo[]数组：
```text
0   2   3   5   6   10
    6   10  0   3   5
```
树的形状为一条直线，图略。

***
**4.1.10 证明在任意一幅连通图中都存在一个顶点，删去它（以及和它相连的所有边）不会影响到图的连通性，编写一个深度优先搜索的方法找出这样一个顶点。**

*Answer：*

***
**4.1.11 使用算法4.2中的bfs(G, 0)处理由Graph的构造函数从tinyGex2.txt得到的图并画出edgeTo[]所表示的树。**

*Answer：*

```java
public static void main(String[] args) {

    Graph G = new Graph(12);
    G.addEdge(8, 4);
    G.addEdge(2, 3);
    G.addEdge(1, 11);
    G.addEdge(0, 6);
    G.addEdge(3, 6);
    G.addEdge(10, 3);
    G.addEdge(7, 11);
    G.addEdge(7, 8);
    G.addEdge(11, 8);
    G.addEdge(2, 0);
    G.addEdge(6, 2);
    G.addEdge(5, 2);
    G.addEdge(5, 10);
    G.addEdge(5, 0);
    G.addEdge(8, 1);
    G.addEdge(4, 1);

    BreadthFirstPaths bfp = new BreadthFirstPaths(G, 0);

    for (int v = 0; v < G.V(); v++) {
        System.out.print(0 + " to " + v + ": ");
        if (bfp.hasPathTo(v)) {
            for (int x : bfp.pathTo(v)) {
                if (x == 0) {
                    System.out.print(x);
                } else {
                    System.out.print("-" + x);
                }
            }
        }
        System.out.println();
    }
}
```

结果：

```text
0 to 0: 0
0 to 1: 
0 to 2: 0-2
0 to 3: 0-2-3
0 to 4: 
0 to 5: 0-5
0 to 6: 0-6
0 to 7: 
0 to 8: 
0 to 9: 
0 to 10: 0-5-10
0 to 11: 

edgeTo[]树：

        0
      / | \
     2  5  6
    /   |
   3    10
```

***
**4.1.12 如果v和w都不是根结点，能够由广度优先搜索得到的树中计算它们之间的距离吗？**

*Answer：*

不能。例如，由tinyGex2.txt构造的图中3和10之间的距离是1，3和6之间的距离是1.但是当以0为起点进行广度优先搜索时，得到的树如下所示：

```text
        0
      / | \
     2  5  6
    /   |
   3    10
```

在这颗树中我们不能得到3和10或3和6之间的距离。

***
**4.1.13 为BreadthFirstPaths的API添加并实现一个方法distTo()，返回从起点到给定的顶点的最短路径的长度，它所需的时间应该为常数。**

*Answer：*

```java
public class BreadthFirstPaths {
    
    private static final int INFINITY = Integer.MAX_VALUE; // initial distance between s and v
    private boolean[] marked; // marked[v] = is there an s-v path
    private int[] edgeTo; // edgeTo[v] = previous edge on shortest s-v path
    private int[] distTo; // distTo[v] = number of edges shortest s-v path

    /*
     * 构造函数：计算起始点s与图G中每个顶点的最短路径
     */
    public BreadthFirstPaths(Graph G, int s) {
        marked = new boolean[G.V()];
        distTo = new int[G.V()];
        edgeTo = new int[G.V()];
        validateVertex(s);
        bfs(G, s);
    }

    /*
     * 判断起始点与v之间是否存在路径
     */
    public boolean hasPathTo(int v) {
        validateVertex(v);
        return marked[v];
    }

    /*
     * 返回起始点与v之间最短路径的边数
     */
    public int distTo(int v) {
        validateVertex(v);
        return distTo[v];
    }

    /*
     * 返回起始点与v之间的最短路径
     */
    public Iterable<Integer> pathTo(int v) {
        validateVertex(v);
        if (!hasPathTo(v)) {
            return null;
        }
        Stack<Integer> path = new Stack<>();
        int x;
        for (x = v; distTo[x] != 0; x = edgeTo[x]) {
            path.push(x);
        }
        path.push(x);
        return path;
    }

    // 广度优先搜索查找图中的路径
    private void bfs(Graph G, int s) {
        Queue<Integer> q = new Queue<>();
        for (int v = 0; v < G.V(); v++) {
            distTo[v] = INFINITY;
        }
        distTo[s] = 0;
        marked[s] = true;
        q.enqueue(s);

        while (!q.isEmpty()) {
            int v = q.dequeue();
            for (int w : G.adj(v)) {
                if (!marked[w]) {
                    edgeTo[w] = v;
                    distTo[w] = distTo[v] + 1;
                    marked[w] = true;
                    q.enqueue(w);
                }
            }
        }
    }

    // throw an IllegalArgumentException unless {@code 0 <= v < V}
    private void validateVertex(int v) {
        int V = marked.length;
        if (v < 0 || v >= V)
            throw new IllegalArgumentException("vertex " + v + " is not between 0 and " + (V - 1));
    }

    public static void main(String[] args) {

        Graph G = new Graph(12);
        G.addEdge(8, 4);
        G.addEdge(2, 3);
        G.addEdge(1, 11);
        G.addEdge(0, 6);
        G.addEdge(3, 6);
        G.addEdge(10, 3);
        G.addEdge(7, 11);
        G.addEdge(7, 8);
        G.addEdge(11, 8);
        G.addEdge(2, 0);
        G.addEdge(6, 2);
        G.addEdge(5, 2);
        G.addEdge(5, 10);
        G.addEdge(5, 0);
        G.addEdge(8, 1);
        G.addEdge(4, 1);

        BreadthFirstPaths bfp = new BreadthFirstPaths(G, 0);

        for (int v = 0; v < G.V(); v++) {
            System.out.print(0 + " to " + v + ": ");
            if (bfp.hasPathTo(v)) {
                for (int x : bfp.pathTo(v)) {
                    if (x == 0) {
                        System.out.print(x);
                    } else {
                        System.out.print("-" + x);
                    }
                }
                System.out.print(" dist " + bfp.distTo(v));
            }
            System.out.println();
        }
    }
}
```

结果：
```text
0 to 0: 0 dist 0
0 to 1: 
0 to 2: 0-2 dist 1
0 to 3: 0-2-3 dist 2
0 to 4: 
0 to 5: 0-5 dist 1
0 to 6: 0-6 dist 1
0 to 7: 
0 to 8: 
0 to 9: 
0 to 10: 0-5-10 dist 2
0 to 11: 
```

***
**4.1.14 如果用栈代替队列来实现广度优先搜索，我们还能得到最短路径吗？**

*Answer：*

>
在广度优先搜索中，我们希望按照与起点的距离的顺序来遍历所有顶点，看起来这种顺序很容易实现：使用（FIFO，先进先出）队列来替代栈（LIFO，后进先出）即可。我们将从有待搜索的通道中选择最早遇到的那条。-- p345

由上述论述可知，如果换回栈，我们将不能得到最短路径。


***
**4.1.15 修改Graph的输入流构造函数，允许从标准输入流读入图的邻接表，如图4.1.26的tinyGadj.txt所示。在顶点和边的总数之后，每一行由一个顶点和 它的所有相邻顶点组成。**

tinyGadj.txt：

```text
13 vertices, 13 edges
0: 6 5 2 1
1: 0
2: 0
3: 5 4
4: 6 5 3
5: 4 3 0
6: 4 0
7: 8
8: 7
9: 12 11 10
10: 9
11: 12 9
12: 11 9
```

构成的图如下：

![tinyGadj.txt](images\tinyGadj.txt.png)

*Answer：*

```java
// isAdjFile只是为了与原构造函数Graph(In in)进行区分。
@SuppressWarnings("unchecked")
public Graph(In in, boolean isAdjFile) {

    if (!isAdjFile) {
        throw new IllegalArgumentException("Argument isAdjFile must be true.");
    }

    try {

        /*
         * 读取第一行：13 vertices, 13 edges 得到V = 13, E = 13
         */
        String[] ve = in.readLine().split(", ");
        this.V = Integer.parseInt(ve[0].substring(0, ve[0].indexOf(" ")));
        this.E = Integer.parseInt(ve[1].substring(0, ve[1].indexOf(" ")));

        // 根据V创建邻接表数组
        adj = (Bag<Integer>[]) new Bag[V];
        for (int v = 0; v < V; v++) {
            adj[v] = new Bag<>();
        }

        // 遍历每一个元素
        for (int i = 0; i < V; i++) {
            
            
            /*
             * 读取行：0: 6 5 2 1
             */
            String[] datas = in.readLine().split(": ");
            if (datas.length == 1) {
                // length = 1，说明该行的元素是孤立的，例如“12: ”，跳过该行
                continue;
            }

            int v = Integer.parseInt(datas[0]); // 获取到元素，0
            validateVertex(v); // 检查顶点v是否合法
            
            String[] adjs = datas[1].split(" "); // 获取其邻接元素，6、5、2、1
            Stack<Integer> reverse = new Stack<>();
            
            // 压入栈
            for (int j = 0; j < adjs.length; j++) {
                int w = Integer.parseInt(adjs[j]);
                validateVertex(w); // 检查顶点w是否合法
                reverse.push(w);
            }
            // 从栈中取出，此时add的顺序为1、2、5、6
            for (Integer w : reverse) {
                // 防止自环
                if (v == w) {
                    throw new IllegalArgumentException("Self Edge is not permitted.");
                }

                // 防止平行边
                if (hasEdge(v, w)) {
                    throw new IllegalArgumentException("Parallel Edge is not permitted.");
                }
                adj[v].add(w);
            }
        }
    } catch (NoSuchElementException e) {
        throw new IllegalArgumentException("invalid input format in Graph constructor", e);
    }
}
```

读取tinyGadj.txt，使用toString()测试：

```java
public static void main(String[] args) {
    Graph G = new Graph(new In(args[0]), true);
    System.out.println(G);
}
```

输出结果：

（实际我们的toString()方法就是输出为tinyGadj.txt这类格式）

```text
13 vertices, 13 edges 
0: 6 5 2 1 
1: 0 
2: 0 
3: 5 4 
4: 6 5 3 
5: 4 3 0 
6: 4 0 
7: 8 
8: 7 
9: 12 11 10 
10: 9 
11: 12 9 
12: 11 9 
```

***
**4.1.16 顶点v的离心率是它和离它最远的顶点的最短距离。图的直径即是所有顶点的最大离心率，半径为所有顶点的最小离心率，中点为离心率和半径相等的点。实现以下API，如表4.1.10所示：**

- public class GraphProperties
    + GraphProperties(Graph G)：构造函数，如果G不是连通的，抛出异常
    + eccentricity(int v)：v的离心率
    + int diameter()：G的直径
    + int radius()：G的半径
    + int center()：G的某个中点

*Answer：*

```java
public class GraphProperties {
    
    private Graph G; // 保存图的引用
    private int[] eccentricities; // 保存每一个顶点的离心率
    private int diameter; // 保存图的直径
    private int radius; // 保存图的半径
    private List<Integer> centers; // 保存图的中点
        
    public GraphProperties(Graph G) {
        // 测试图的连通性
        DepthFirstSearch dfs = new DepthFirstSearch(G, 0); 
        if (dfs.count() != G.V()) {
            // 图不连通，抛出异常
            throw new IllegalArgumentException("Graph G is not connected.");
        }
        this.G = G;
        
        calculateEcc(); // 计算每一点的离心率
        calculateDR(); // 计算直径和半径
        calculateCenter(); // 计算中点
    }

    /*
     * v的离心率
     */
    public int eccentricity(int v) {
        return eccentricities[v];
    }
    
    /*
     * 图的直径
     */
    public int diameter() {
        return diameter;
    }
    
    /*
     * 图的半径
     */
    public int radius() {
        return radius;
    }
    
    /*
     * 随机返回图的一个中点
     */
    public int center() {
        return centers.get(StdRandom.uniform(centers.size()));
    }
    
    /*
     * 返回图G
     */
    public Graph G() {
        return G;
    }
    
    // 计算每个顶点的离心率、图的直径和半径
    private void calculateEcc() {
        
        int V = G.V();
        eccentricities = new int[V]; // 初始化离心率数组
        BreadthFirstPaths bfp = null;
        int ecc = 0; // 离心率
        for (int i = 0; i < V; i++) { 
            // 对每一个顶点进行一次广度优先搜索
            bfp = new BreadthFirstPaths(G, i);
            // 计算起点i与每一个点的距离
            for (int j = 0; j < V; j++) {
                if (i == j) {
                    continue;
                }
                int dist = bfp.distTo(j);
                // 遍历出距离的最大值即为起点i的离心率
                if (dist > ecc) {
                    ecc = dist;
                }
            }
            eccentricities[i] = ecc;
        }
    }


    // 计算图的直径和半径，即数组eccentricities[]的最大值和最小值
    private void calculateDR() {
        diameter = Integer.MIN_VALUE;
        radius = Integer.MAX_VALUE;
        for (int ecc : eccentricities) {
            if (ecc > diameter) {
                diameter = ecc;
            }
            if (ecc < radius) {
                radius = ecc;
            }
        }
    }
    
    // 计算图中点
    private void calculateCenter() {
        centers = new ArrayList<Integer>();
        for (int i = 0; i < eccentricities.length; i++) {
            if (eccentricities[i] == radius) {
                centers.add(i);
            }
        }
    }
    
    public static void main(String[] args) {
        Graph G = new Graph(new In(args[0])); // mediumG.txt
        GraphProperties gp = new GraphProperties(G); 
        
        System.out.println(gp.eccentricity(25)); // 14
        System.out.println(gp.diameter()); // 14
        System.out.println(gp.radius()); // 13
        System.out.println(gp.center()); // 9
    }
}
```

***
**4.1.17 图的周长为图中最短环的长度。如果是无环图，则它的周长为无穷大。为GraphProperties添加一个方法girth，返回图的周长。**

*Answer：*

***
**4.1.18 使用CC找出由Graph的输入流从tinyGex2.txt得到的图中所有连通分量并按照图4.1.21的样式给出详细的轨迹。**

*Answer：*

轨迹略。

```java
public class Ex4118 {
    public static void main(String[] args) {
        Graph G = new Graph(new In(args[0])); // 读取tinyGex2.txt构造图
        
        CC cc = new CC(G);
        int M = cc.count();

        System.out.println(M + " components");

        @SuppressWarnings("unchecked")
        Bag<Integer>[] components = (Bag<Integer>[]) new Bag[M];

        for (int i = 0; i < M; i++) {
            components[i] = new Bag<>();
        }

        for (int v = 0; v < G.V(); v++) {
            components[cc.id(v)].add(v);
        }

        for (int i = 0; i < M; i++) {
            for (int v : components[i]) {
                System.out.print(v + " ");
            }
            System.out.println();
        }
    }
}
```

结果：
```text
3 components
10 6 5 3 2 0 
11 8 7 4 1 
9 
```

***
**4.1.19 使用Cycle在由Graph的输入流从tinyGex2.txt得到的图中找到一个环并按照本节示意图的样式给出详细的轨迹。在最坏情况下，Cycle构造函数的运行时间的增长数量级是多少？**

*Answer：*

轨迹略。

```java
public class Ex4119 {

    public static void main(String[] args) {
        Graph G = new Graph(new In(args[0])); // 读取tinyGex2.txt构造图
        Cycle cycle = new Cycle(G);
        for (int i : cycle.cycle()) {
            System.out.print(i + " ");
        }
    }
}
```

结果：
```text
2 5 10 3 6 2 
```

***
**4.1.20 使用TwoColor给出由Graph的输入流从tinyGex2.txt得到的图的一个着色方案并按照本节示意图的样式给出详细的轨迹。在最坏情况下，TwoColor构造函数的运行时间的增长数量级是多少？**

*Answer：*

轨迹略。

```java
public class Ex4120 {
    public static void main(String[] args) {
        Graph G = new Graph(new In(args[0])); // 读取tinyGex2.txt构造图
        TwoColor tw = new TwoColor(G);
        for (boolean bool : tw.color()) {
            System.out.print(bool + " ");
        }
    }
}
```

结果：
```text
false false true true true true false false false false false true 
```

***
**4.1.24 用SymbolGraph和movie.txt找到今年获得奥斯卡奖提名演员的Kevin Bacon数。**

*Answer：*
```java
// 官方实现
public class BaconHistogram {
    public static void main(String[] args) {
        String filename  = args[0];
        String delimiter = args[1];
        String source    = args[2];

        SymbolGraph sg = new SymbolGraph(filename, delimiter);
        Graph G = sg.graph();
        if (!sg.contains(source)) {
            StdOut.println(source + " not in database.");
            return;
        }

        // run breadth-first search from s
        int s = sg.indexOf(source);
        BreadthFirstPaths bfs = new BreadthFirstPaths(G, s);


        // compute histogram of Kevin Bacon numbers - 100 for infinity
        int MAX_BACON = 100;
        int[] hist = new int[MAX_BACON + 1];
        for (int v = 0; v < G.V(); v++) {
            int bacon = Math.min(MAX_BACON, bfs.distTo(v));
            hist[bacon]++;

            // to print actors and movies with large bacon numbers
            if (bacon/2 >= 7 && bacon < MAX_BACON)
                StdOut.printf("%d %s\n", bacon/2, sg.nameOf(v));
        }

        // print out histogram - even indices are actors
        for (int i = 0; i < MAX_BACON; i += 2) {
            if (hist[i] == 0) break;
            StdOut.printf("%3d %8d\n", i/2, hist[i]);
        }
        StdOut.printf("Inf %8d\n", hist[MAX_BACON]);
    }
}
```

结果：

```text
Done reading datafiles\movies.txt
  0        1
  1     1324
  2    70717
  3    40862
  4     1591
  5      125
Inf      655
```

***
**4.1.22 编写一段程序BaconHistogram，打印一幅Kevin Bacon数的柱状图，显示movie.txt中Kevin Bacon 数为0、1、2、3...的演员分别有多少。将值为无穷大的人（不与Kevin Bacon连通）归为一类。**

*Answer：*

***
**4.1.23 计算由movies.txt得到的图的连通分量的数量和包含的顶点数小于10的连通分量的数量。计算最大连通分量的离心率、直径、半径和中点。Kevin Bacon在最大的连通分量之中吗？**

*Answer：*

当前的CC实现会造成StackOverFlow。

***
**4.1.24 修改DegreesOfSeparation，从命令行接受一个整型参数y，忽略上映年数超过y的电影。**

*Answer：*

```java
public class Ex4124 {
    public static void main(String[] args) {
        
        // 构造符号图
        SymbolGraph sg = new SymbolGraph(args[0], args[1]);

        // 获取符号图中用整数表示的无向图
        Graph G = sg.G();

        // 获取起点
        String source = args[2];
        if (!sg.contains(source)) {
            System.out.println(source + "not in database.");
            return;
        }

        // 获取起点对应无向图的整数值，并使用该值进行广度优先搜索
        int s = sg.index(source);
        BreadthFirstPaths bfs = new BreadthFirstPaths(G, s);

        System.out.println("输入年份：");
        int y = Integer.parseInt(StdIn.readLine()); // 读取一个年份
        
        
        while (!StdIn.isEmpty()) {
            String sink = StdIn.readLine(); // 读取符号图的一个顶点
            if (sg.contains(sink)) {
                int t = sg.index(sink); // 将符号图顶点转为无向图顶点
                if (bfs.hasPathTo(t)) {
                    // 如果有从起点到该顶点的路径
                    for (int v : bfs.pathTo(t)) {

                        String name = sg.name(v);
                        
                        // 以")"结尾的name为电影
                        if (name.endsWith(")")) {
                            
                            // 获取该电影的上映年份
                            int year = Integer.parseInt(name.substring(name.indexOf("(") + 1, name.length() - 1));
                            
                            // 如果年份大于参数y，则跳过该电影
                            if (year > y) {
                                continue;
                            }
                        }

                        // 打印路径
                        System.out.println("   " + name);
                    }
                } else {
                    System.out.println("Not connected");
                }
            } else {
                System.out.println("Not in database.");
            }
        }
    }
}
```

***
**4.1.25 编写一个类似于DegreesOfSeparation的SymbolGraph用例，使用深度优先搜索代替广度优先搜索来查找两个演员之间的路径。**

*Answer：*

将原实现中的广度优先搜索路径替换为深度优先搜索路径即可。（有栈溢出的风险，因为DepthFirstPaths的dfs算法的实现是递归，使用VM参数-Xss32m）

```java
public class DegreesOfSeparationDFS {
    
    public static void main(String[] args) {
        // 构造符号图
        SymbolGraph sg = new SymbolGraph(args[0], args[1]);

        // 获取符号图中用整数表示的无向图
        Graph G = sg.G();

        // 获取起点
        String source = args[2];
        if (!sg.contains(source)) {
            System.out.println(source + "not in database.");
            return;
        }

        // 获取起点对应无向图的整数值，并使用该值进行广度优先搜索
        int s = sg.index(source);
        DepthFirstPaths bfp = new DepthFirstPaths(G, s);

        while (!StdIn.isEmpty()) {
            String sink = StdIn.readLine(); // 读取符号图的一个顶点
            if (sg.contains(sink)) { 
                int t = sg.index(sink); // 将符号图顶点转为无向图顶点
                if (bfp.hasPathTo(t)) { 
                    // 如果有从起点到该顶点的路径
                    for (int v : bfp.pathTo(t)) {
                        // 打印路径
                        System.out.println("   " + sg.name(v));
                    }
                } else {
                    System.out.println("Not connected");
                }
            } else {
                System.out.println("Not in database.");
            }
        }
    }
}
```

***
**4.1.26 使用1.4节中的内存使用模型评估用Graph表示一幅含有V个顶点和E条边的图所需的内存。**

*Answer：*

56 + 40V + 128E

***
**4.1.27 如果重命名一幅图中的顶点就能够使之变得和另一幅图完全相同，这两幅图就是同构的。画出含有2、3、4、5个顶点的所有非同构图。**

*Answer：*

***
**4.1.28 修改Cycle，允许图含有自环和平行边。**

*Answer：*

***

#### 提高题

***
**4.1.29 欧拉环和汉密尔顿环。考虑以下4组边定义的图：**

- 0-1 0-2 0-3 1-3 1-4 2-5 2-9 3-6 4-7 4-8 5-8 5-9 6-7 6-9 7-8
- 0-1 0-2 0-3 1-3 0-3 2-5 5-6 3-6 4-7 4-8 5-8 5-9 6-7 6-9 8-8
- 0-1 1-2 1-3 0-3 0-4 2-5 2-9 3-6 4-7 4-8 5-8 5-9 6-7 6-9 7-8
- 4-1 7-9 6-2 7-3 5-0 0-2 0-8 1-6 3-9 6-3 2-8 1-5 0-8 4-5 4-7

**那几幅图含有欧拉环（恰好包含了所有的边且没有重复的环）？哪几幅图含有汉密尔顿环（恰好包含了所有的顶点且没有重复的环？）**

*Answer：*

构成图如下：

![ex4129](images\chapter04\ex4129.png)

b包含欧拉环，c、d包含汉密尔顿环。

***
**4.1.30 图的枚举。含有V个顶点和E条边（不含平行边）的不同的无向图共有多少种？**

*Answer：*

***
**4.1.31 检测平行边。设计一个线性时间的算法来统计图中平行边的总数。**

*Answer：*

***
**4.1.32 奇环。证明一幅图能够用两种颜色着色（二分图）当且仅当它不含有长度为奇数的环。**

*Answer：*

例如，三元图，二分着色时一定会有两个顶点的颜色相同，所以含有奇数长度的环不能使用两种颜色着色。

***
**4.1.33 符号图。实现一个SymbolGraph（不一定必须使用Graph），秩序遍历一遍图的定义数据。由于需要查找符号表，实现中图的各种操作时耗可能会变为原来的$logV$倍。**

*Answer：*

***
**4.1.34 双向连通性。如果任意一对顶点都能由两条不同（没有重叠的边或顶点）的路径连通则图就是双向连通的。在一幅连通图中，如果一个顶点（以及和它相连的边）被删掉后不再连通，该顶点就被称为关节点。证明没有关节点的图是双向连通的。**

*Answer：*

***
**4.1.35 边的连通性。在一幅连通图中，如果一条边被删除后图会被分为两个独立的连通分量，这条边就被称为桥。没有桥的图称为边连通图。开发一种基于深度优先搜算算法的数据类型，判断一个图是否是边连通图。**

*Answer：*

```java
// 官方实现
public class Bridge {
    private int bridges;      // number of bridges
    private int cnt;          // counter
    private int[] pre;        // pre[v] = order in which dfs examines v
    private int[] low;        // low[v] = lowest preorder of any vertex connected to v

    public Bridge(Graph G) {
        low = new int[G.V()];
        pre = new int[G.V()];
        for (int v = 0; v < G.V(); v++)
            low[v] = -1;
        for (int v = 0; v < G.V(); v++)
            pre[v] = -1;
        
        for (int v = 0; v < G.V(); v++)
            if (pre[v] == -1)
                dfs(G, v, v);
    }

    public int components() { return bridges + 1; }

    private void dfs(Graph G, int u, int v) {
        pre[v] = cnt++;
        low[v] = pre[v];
        for (int w : G.adj(v)) {
            if (pre[w] == -1) {
                dfs(G, v, w);
                low[v] = Math.min(low[v], low[w]);
                if (low[w] == pre[w]) {
                    StdOut.println(v + "-" + w + " is a bridge");
                    bridges++;
                }
            }

            // update low number - ignore reverse of edge leading to v
            else if (w != u)
                low[v] = Math.min(low[v], pre[w]);
        }
    }

    // test client
    public static void main(String[] args) {
        int V = Integer.parseInt(args[0]);
        int E = Integer.parseInt(args[1]);
        Graph G = GraphGenerator.simple(V, E);
        StdOut.println(G);

        Bridge bridge = new Bridge(G);
        StdOut.println("Edge connected components = " + bridge.components());
    }
}
```

***
**4.1.36 欧几里得图。为平面上的图设计并实现一份叫做EuclideanGraph的API，其中图所有顶点均有坐标。实现一个show()方法，用StdDraw将图绘出。**

*Answer：*

***
**4.1.37 图像处理。在一幅图像中将所有相邻的、颜色相同的点相连就可以得到一幅图，为这种隐式定义的图实现填充（flood fill）操作。**

*Answer：*

***

#### 实验题

***
**4.1.38 随机图。编写一个程序ErdosRenyiGraph，从命令行接受整数V和E，随机生成E对0到V-1之间的整数来构造一幅图。注意：生成器可能会产生自环和平行边。**

由于这个实现可能会产生自环和平行边，所以要求Graph的实现允许自环和平行边的存在。

```java
public class ErdosRenyiGraph {
    public static void main(String[] args) {
        int V = Integer.parseInt(args[0]);
        int E = Integer.parseInt(args[1]);
        
        Graph G = new Graph(V);
        
        for (int i = 0; i < E; i++) {
            int v = StdRandom.uniform(V);
            int w = StdRandom.uniform(V);
            G.addEdge(v, w);
        }
    }
}
```

***
**4.1.39 随机简单图。编写一个程序RandomSparseGraph，从命令行接受整数V和E，用均等的几率生成含有V个顶点和E条边的所有可能的简单图。**

*Answer：*

上题中的实现，如果uniform(V)方法是0-(v-1)均等分布的，那么就可以用均等的几率生成所有的图。

***
**4.1.40 随机稀疏图。编写一个程序RandomSparseGraph，根据精心选择的一组V和E的值生成随机的稀疏图，以便用它对由Erdos-Renyi模型得到的图进行有意义的经验性测试。**

*Answer：*

***

（其余实验题暂略）

### 4.2 有向图

***

#### 练习题

***
**4.2.1 一幅含有V个顶点且没有平行边的有向图中最多可能含有多少条边？一幅含有V个顶点且没有孤立顶点的有向图中最少需要多少条边？**

*Answer：*

- 每一个顶点都与其他顶点连接，所以共有：$V(V-1)$条边
- 一个顶点指向其余所有的顶点，所以共有：$V-1$条边

***
**4.2.2 按照正文中示意图的样式（图4.1.9）画出Digraph的构造函数再处理图4.2.19的tinyDGex2.txt时构造的邻接表。**

tinyDGex2.txt：

12
16
8 4
2 3
0 5
0 6
3 6
10 3
7 11
7 8
11 8
2 0
6 2
5 2
5 10
3 10
8 1
4 1

![tinyDGex2.txt](images\tinyDGex2.txt.PNG)

*Answer：*

邻接表：

```text
0  --->       6 5
1  --->       
2  --->       0 3
3  --->      10 6
4  --->         1
5  --->      10 2 
6  --->         2
7  --->      8 11
8  --->       1 4
9  --->
10 --->         3
11 --->         8
```

***
**4.2.3 为Digraph添加一个构造函数，它接受一幅有向图G然后创建并初始化这幅图的一个副本。G的用例对它做出的任何改动都不应该影响到它的副本。**

*Answer：*

```java
public Digraph(Digraph G) {
    this(G.V());
    this.E = G.E();
    for (int v = 0; v < V; v++) {
        this.indegree[v] = G.indegree(v);
    }
    for (int v = 0; v < G.V(); v++) {
        // reverse so that adjacency list is in same order as original
        Stack<Integer> reverse = new Stack<>();
        for (int w : G.adj[v]) {
            reverse.push(w);
        }
        for (int w : reverse) {
            adj[v].add(w);
        }
    }
}
```

***
**4.2.4 为Digraph添加一个方法hasEdge()，它接受两个整形参数v和w，如果图含有边v->w，方法返回true，否则返回false。**

*Answer：*

```java
public boolean hasEdge(int v, int w) {
    validateVertex(v);
    validateVertex(w);
    
    for (int element : adj(v)) {
        if (element == w) {
            return true;
        }
    }
    return false;
}
```

***
**4.2.5 修改Digraph，不允许存在平行边和自环。**

*Answer：*

```java
public void addEdge(int v, int w) {

    // 防止自环
    if (v == w) {
        throw new IllegalArgumentException("Self Edge is not permitted.");
    }

    // 防止平行边
    if (hasEdge(v, w)) {
        throw new IllegalArgumentException("Parallel Edge is not permitted.");
    }
    
    adj[v].add(w);
    indegree[w]++;
    E++;
}
```

***
**4.2.6 为Digraph编写一个测试用例。**

*Answer：*

```java
public class Ex426 {
    public static void main(String[] args) {
        // 读取tinyDGex2.txt
        Digraph G = new Digraph(new In(args[0]));
        
        System.out.println(G.V()); // 12
        System.out.println(G.E()); // 16
        
        System.out.println(G.hasEdge(2, 3)); // true
        System.out.println(G.hasEdge(3, 2)); // false
        
        System.out.println(G.indegree(3)); // 2
        
//      G.addEdge(2, 3); //  java.lang.IllegalArgumentException: Parallel Edge is not permitted.
        
//      G.addEdge(2, 2); // java.lang.IllegalArgumentException: Self Edge is not permitted.
        
        System.out.println(G.outdegree(0)); // 2
        
        
        /*
            12 vertices, 16 edges 
            0: 6 5 
            1: 
            2: 0 3 
            3: 10 6 
            4: 1 
            5: 10 2 
            6: 2 
            7: 8 11 
            8: 1 4 
            9: 
            10: 3 
            11: 8 
         */
        System.out.println(G);
        
        G.addEdge(9, 1);
        
        /*
            12 vertices, 16 edges 
            0: 6 5 
            1: 
            2: 0 3 
            3: 10 6 
            4: 1 
            5: 10 2 
            6: 2 
            7: 8 11 
            8: 1 4 
            9: 1
            10: 3 
            11: 8 
         */
        System.out.println(G);
    }
}
```

***
**4.2.7 顶点的入度为指向该顶点的边的总数。顶点的出度为由该顶点指出的边的总数。从出度为0的顶点是不可能到达任何顶点的，这种顶点叫做终点；入度为0的顶点是不可能从任何顶点达到的，所以叫做起点。一幅允许出现资环且每个顶点的出度均为1的有向图叫做映射（从0到V-1之间的整数到它们自身的函数）。编写一段程序Degress.java，实现下面的API。**

- public class Degress
    + Degress(Digraph G)： 
    + int indegree(int v)：
    + int outdegree(int v)：
    + Iterable<Integer> sources()：
    + Iteravle<Integer> sinks()：
    + boolean isMap()：

*Answer：*

```java
public class Degress {
    
    private Digraph G;
    
    public Degress(Digraph G) {
        this.G = G;
    }
    
    public int indegree(int v) {
        return G.indegree(v);
    }
    
    public int outdegree(int v) {
        return G.outdegree(v);
    }
    
    public Iterable<Integer> sources() {
        Bag<Integer> sources = new Bag<>();
        
        int V = G.V();
        for (int i = 0; i < V; i++) {
            if (G.indegree(i) == 0) {
                sources.add(i);
            }
        }
        
        return sources;
    }
    
    public Iterable<Integer> sinks() {
        Bag<Integer> sinks = new Bag<>();
        
        int V = G.V();
        for (int i = 0; i < V; i++) {
            if (G.outdegree(i) == 0) {
                sinks.add(i);
            }
        }
        
        return sinks;
    }
    
    public boolean isMap() {
        int V = G.V();
        for (int i = 0; i < V; i++) {
            if (G.outdegree(i) != 1) {
                return false;
            }
        }
        return true;
    }
}
```

***
**4.2.8 画出所有含有2、3、4和5个顶点的非同构有向无环图。**

*Answer：*

***
**4.2.9 编写一个方法，检查一幅有向无环图的顶点的给定排列是否就是该图顶点的拓扑排序。**

*Answer：*

```java
public static boolean isTopoOrder(Digraph G, Iterable<Integer> order) {

    // 获取图G的拓扑排序
    Iterable<Integer> topoOrder = new Topological(G).order();

    // 比较topoOrder、order
    Iterator<Integer> topoIter = topoOrder.iterator();
    Iterator<Integer> orderIter = order.iterator();

    while (topoIter.hasNext()) {
        if (!orderIter.hasNext()) {
            return false;
        }
        int v = topoIter.next();
        int w = orderIter.next();
        if (v != w) {
            return false;
        }
    }

    return orderIter.hasNext() ? false : true;
}
```

***
**4.2.10 给定一幅有向无环图，是否存在一种无法用基于深度优先搜索算法得到的顶点的拓扑排序？顶点的相邻关系不限。证明你的结论。**

*Answer：*

***
**4.2.11 描述一组稀疏有向图，其含有的有向环的个数随着顶点增加而呈指数级增长。**

*Answer：*

***
**4.2.12 一幅含有V个顶点和V-1条边且为一条简单路径的有向图的传递闭包中含有多少条边？**

*Answer：*

$(V^2-V)/2$（没有计算自环）

***
**4.2.13 给出这幅图含有10个顶点和以下边的有向图的传递闭包：**

3 -> 7
1 -> 4
7 -> 8
0 -> 5
5 -> 2
3 -> 8
2 -> 9
0 -> 6
4 -> 9
2 -> 6
6 -> 4

*Answer：*

```text
    0   1   2   3   4   5   6   7   8    9
0   T       T       T   T   T            T
1       T           T                    T
2           T       T       T            T
3               T               T   T  
4                   T                    T
5           T       T   T   T            T
6                   T       T            T
7                               T   T   
8                                   T
9                                        T      
```

***
**4.2.14 证明$G$和$G^R$中的强连通分量是相同的。**

*Answer：*

由定义可知，如果两个顶点v和w是强连通的，则存在一条由v到w的路径，也存在一条从w到v的路径。

当对图G进行取反时，强连通的两个顶点间的路径全部反向，此时原来v到w的路径变为w到v的路径，w到v的路径变为v到w的路径，所以这两个顶点依旧强连通。

本身不强连通的两个顶点v和w，在取反后自然也不是强连通的。

所以取反前后强连通分量是相同的。

***
**4.2.15 一幅有向无环图的强连通分量是哪些？**

*Answer：*

V个顶点构成的向无环图中含有V个强连通分量，每一个分量由一个顶点组成。

***
**4.2.16 用Kosaraju算法处理一幅有向无环图的结果是什么？**

*Answer：*

```java
public class Ex4216 {
    @SuppressWarnings("unchecked")
    public static void main(String[] args) {
        
        Digraph G = new Digraph(5);
        G.addEdge(0, 1);
        G.addEdge(0, 2);
        G.addEdge(0, 3);
        G.addEdge(0, 4);
        
        KosarajuSCC scc = new KosarajuSCC(G);

        // number of connected components
        int m = scc.count();
        StdOut.println(m + " strong components");

        // compute list of vertices in each strong component
        Queue<Integer>[] components = (Queue<Integer>[]) new Queue[m];
        for (int i = 0; i < m; i++) {
            components[i] = new Queue<Integer>();
        }
        for (int v = 0; v < G.V(); v++) {
            components[scc.id(v)].enqueue(v);
        }

        // print results
        for (int i = 0; i < m; i++) {
            for (int v : components[i]) {
                StdOut.print(v + " ");
            }
            StdOut.println();
        }
    }
}
```

结果：

```text
5 strong components
4 
3 
2 
1 
0 
```

***
**4.2.17 真假判断：一幅有向图的反向图的顶点的逆后序排列和该有向图的顶点的后序排列相同。**

*Answer：*

假。

```java
public class Ex4217 {
    public static void main(String[] args) {
        
        Digraph G = new Digraph(6);
        G.addEdge(0, 1);
        G.addEdge(0, 2);
        G.addEdge(2, 3);
        G.addEdge(2, 4);
        G.addEdge(4, 5);
        G.addEdge(5, 2);
        
        // G的反向图的逆后序排列
        Iterable<Integer> grrp = new DepthFirstOrder(G.reverse()).reversePost();
        
        // G的后序排列
        Iterable<Integer> gp = new DepthFirstOrder(G).post();
        
        
        for (int i : grrp) {
            System.out.print(i + " ");
        }
        System.out.println();
        for (int i : gp) {
            System.out.print(i + " ");
        }
    }
}
```

结果：

```text
3 2 5 4 1 0 
5 4 3 2 1 0 
```

***
**4.2.18 使用1.4节中的内存使用模型评估含有V个顶点和E条边的Digraph的内存使用情况。**

*Answer：*

***

#### 提高题

***
**4.2.19 拓扑排序与广度优先搜索。解释为何如下算法无法得到一组拓扑排序：运行广度优先搜索并按照所有顶点和起点的距离标记它们。**

*Answer：*

如果按上述思路来做，需要确保进行广度优先搜索时选取的起点入度为0，即在调度问题中优先级最高。而这一点需要在拿到图时检查各个顶点的入度。
此外，在满足上述条件时，与起点距离相同的顶点之间的指向关系是没办法确定的。例如A-->B，B-->C，A-->C，在此情况下，B与C距离起点A的距离都是1，但是B指向C，这时如何排列B和C的顺序就会产生问题。如果按照距离进行标记，则B和C是并列的。但真实的拓扑排序中B要优先于C。
所以不能使用广度优先搜索标记距离法得到拓扑排序。
