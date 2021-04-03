---
title: "Java的运算系统"
date: 2020-05-01T16:42:24+08:00
draft: false
---
#### 基本运算
- /


    int b = 5/2;
    -->结果为 '2'（地板除）
- 取余运算



     5/2=2……1
     
      System.out.println(9 % 5);——>2
      System.out.println(-9 % 5);——>-2(余数)
1. 判断奇偶  
  通过取余是否为零完成



       //判断一个整数是不是奇数
         public static boolean isOdd(int number){
             return number % 2 != 0;
       }

2. 运算符
- += ，-= ，*=，/=   
**Eg：a+=2 | a = a+2**
-   +=    
a +=3  ==> a = a + 3
-   -=    
a -=3  ==>a - e
-   %=    a
a %=3  ==>a % 3


- 二元一次开根Eg



         public static double[] calculate(int a, int b, int c) {
           double d = b * b - 4 * a * c;
           if (d > 0){
               double root1 = ((-b + Math.sqrt(d)) / 2 * a);
               double root2 = ((-b - Math.sqrt(d)) / 2 * a);
               return new double[]{root1,root2};
              }else if(d == 0) {
              double root = ((-b + Math.sqrt(d)) / 2 * a);
                return new double[]{root};
         }else if (d < 0){
             return new double[]{};
         }
        return null;

3. 自增自减 i++，i--,++i,--i
- i++ | i=i+1 | i+= 1  

 
        
          i++;  ==> i=i+1/ i+=1
                1、i作为表达式的值 ==>System.out.println(i++);==>0
              
                2、i = i + 1（a+=）
                  i = 1
    
     ++i; ==> 1、b = b + 1
              
              2、把结果作为表达式的值 ==>System.out.println(i++);==>1
---
- i++ vs ++i


      public static void printNumberTo10(int number) {
         while (true) {
             System.out.println(number++);
             if (number > 10) {
                    return;
             }
         }
       } // ——> 包括number本身             
--- 
        i = 0；
        x = 0;
        System.out.print(i++);
        System.out.print(++x);
            result: i++ = 0,i = 1;
                    ++x = 1,x = 1;
4. 比较运算符 
- 结果是一个布尔值
    

         Eg：
         System.out.println(2 == 1);  ==> true
         System.out.println(2 > 1);  ==> false
5. 逻辑运算
- && **与：只要有一个是假的就都是假的**
-  || **或：只要有一个是真的就都是真的**
-  ！非（相反）
- 短路
   
 或：左侧结果是真的，后面的结果就会自动忽略掉（不会影响结果）
    与：左侧结果是假的……


      Boolean config = getconfig()
        if (config != null && config) {
            /*config 的三种可能
              1: true -> true
              2: false -> false
            */3: null -> false
        }
       public static Boolean getconfig(){
        //可能返回ture/false/null
        }
 
**Eg：装拆箱的时候，对象为null的时候拆箱就会出现空指针异常，可以通过先判断是否为null来避免，用&&进行逻辑运算，当为null的时候直接结束运算。**

6. 三元运算符 ?:
- 一元：int a = 1;  a++
- 二元：a = a + 1; 9 % 5
- 三元运算嵌套



         //当 a > 0时，得到">"，否则，得到"<="
        String sign;

        if (a > 0){
            sign = ">";
        }else {
            sign = "<=";
        }

        //三元运算符重写这个过程：

        sign = a > 0 ? ">" : "<=";

        //若 a > b 返回 1
        //若 a < b 返回 -1
        //若 a = b 返回 0
         int result = a > b ? 1 : (a < b ? -1 :0);
         
         return 100 >= score && score >= 90 ? "学生"+student+"的分数是"+score+",评级是A" : (89 >= score && score >= 60 ? "学生"+student+"的分数是"+score+",评级是B" : (59 >= score && score >= 0 ? "学生"+student+"的分数是"+score+",评级是C" :"非法输入" ));


7. 位运算
- ~ 按位取反 把字符的字节里面0变成1，1变成0
- 补码：+1 
- 负数是通过补码表示的

|4|0000|0100|
|:-:|:-:|:-:|
|
取反

|~|1111|1011|
|:-:|:-:|:-:|

+1

---

|-4|0000|1100|
|:-:|:-:|:-:|

- 补码可以完成加法和减法（不进位加法）
**Eg：5-3**


|5|0000|0101|
|:-:|:-:|:-:|

|3|0000|0011|
|:-:|:-:|:-:|

取反补位

---

|-3|1111|1101|
|:-:|:-:|:-:|
---
---
|5+（-3）|10000|0100|
|:-:|:-:|:-:|

去掉第一位

---

|2|0000|0010|
|:-:|:-:|:-:|
- & &= 按位与  ：每个位进行一次与操作（有一个是0，则都是0）【And】

0000 0011   3

0000 0101   5

--- 
0000 0001   1


- | |= 按位或 ：有一个是1，则就是1【or】

0000 0110   6

0000 1000   8

---
0000 1110   14



    Y ^ Y = 0 
    Y ^ 0 = Y 
    Eg:
    [1,2,3,4,5,6]
    [1,2,3,4,2,1]
    //找重复出现的数则用异或
    
    
判断字符串里是否有某个字符


    public static boolean isValidHexCharacter(char ch) {
        byte bch = (byte) ch;
        String str = "0123456789AaBbCcDdEeFf";
        for (int i = 0; i < str.length(); i++) {
            int b = (int) str.charAt(i);
            int d = b ^ bch;
            if (d == 0) {
                return true;
            }
        }
        return false;
    }


位运算代替boolean

    public class Cat {
       private static int CUTE = 0x1;
       private int properties = 0;

    /**
     * 使用位运算设置猫咪萌的属性
     *
     * @param cute true为萌，false为不萌
     */
    public void setCute(boolean cute) {
        properties = cute? (properties | CUTE) : (properties &(~CUTE));//true为1 ，false为0
        
    }

    /**
     * 这只猫萌吗？请在此处使用位运算读取properties，得到猫是否萌的结果
     *
     * @return 萌则返回true，否则返回false
     */
    public boolean isCute() {
        return (properties & CUTE) != 0;
    }

- <<=左移【<<】 

 0000 0101     |5[左移一位]

~~0~~ 0000 101++0++    |  5 * 2 ^ 1 = 10（去掉最前一位，后面补一个0）

  0000 0101   |5[左移2位]

~~00~~  0001 01++00++  5* 2 ^ 2= 20

对任何进制进行左移操作，就是乘以这个进制的基数
左移n为，相当于乘以2的n次方

- `>>` >>=带符号右移：该数对应的二进制码整体右移，左边的用原有标志位（原本是1就用1，是0就用0）补充，右边超出的部分舍弃。

1(表示符号'+') 111 1110

0(表示符号'-') 111 1110



- `>>>` >>>=无符号右移：不管正负标志位为0还是1，将该数的二进制码整体右移，左边部分总是以0填充，右边部分舍弃。

-5用二进制表示1111 1011 *标志*位



-5>>2: *1*111 1011-------------->**11** *1*1 1110。

**11**为标志位

-5>>>2:  *1*111 1011-------------->**00** *1*1 1110。

**00**为补充的0

位运算：
1. 设置属性某一位上面是1，其他都是0。
2. 然后进行按位与（与过有1的都是1），从而把属性都放进int里面，只需要4个字节
boolean： 存放需要32个字节
- 设置`|`  无论原先是0还是1 ，进行按位或1操作最后都是1
- 读取`&`  无论原先是0还是1 ，进行按位与1操作最后都是1
- 不用乘除号进行运算



        // 返回number乘以16的结果
        // 请不要使用乘号
        public static int multiply16(int number) {
            return number << 4;
        }
        // 返回number除以16的结果
        // 请不要使用除号
        public static int divide16(int number) {
             return number >> 4;
         }

 
8. 运算符的优先级
- 乘除高于加减
- 其他全部加括号
9. 字符串加法
- 对象进行字符串加法的时候，就会把相应的toString方法或者原生类型的相应表示
- 字符串加法会自动调用StringBuilder，减轻内存压力。因为对象是不可改变的，进行运算的时候会残生很多新的对象，造成储存空间的浪费。


## 取余
- 只适用于整数
## 异或
<<<<<<< HEAD
两个数的二进制位 相比较 相同为0 不同为 1 ,结果为〇是两个数就是相等
=======
两个数的二进制位 相比较 相同为0 不同为  ,结果为〇是两个数就是相等
>>>>>>> 2e3f18c906f1b2f3387e59ac7ebd57b35882004e

||||||
|--|--|--|--|--|--|
1|1|0|0|1|  
1|1|0|0|1|

|0|0|0|0|0
|--|--|--|--|--|


## 题目
1. 寻找没有出现过的数字
- 位运算中的异或运算 XOR，主要因为异或运算有以下几个特点：
- - 一个数和 0 做 XOR 运算等于本身：a⊕0 = a
- - 一个数和其本身做 XOR 运算等于 0：a⊕a = 0
- - XOR 运算满足交换律和结合律：a⊕b⊕a = (a⊕a)⊕b = 0⊕b = b
- 故而在以上的基础条件上，将所有数字按照顺序做抑或运算，最后剩下的结果即为唯一的数字
- 时间复杂度：O(n)O(n)，空间复杂度：O(1)O(1)
```java
class Solution {
    public int singleNumber(int[] nums) {
        int ans = 0;
        for(int num: nums) {
            ans ^= num;
        }
        return ans;
    }
}
```