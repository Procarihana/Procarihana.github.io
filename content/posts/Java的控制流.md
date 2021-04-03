---
title: "Java的控制流"
date: 2020-05-06T21:05:25+08:00
draft: false
---

## 控制流


## 控制流：方法调用
方法调用就是一个方法栈，每当开始一个新的方法调用的时候就会开启一个方法栈的栈针，方法调用之后方法针会被销毁。当有新的方法调用的时候就会开始新一轮的方法栈，循环往复。
## while与 do while 循环
- while（返回boolean语句)  
{
    
    ……  
    
    }
- do {**无论真假就先执行一遍**}   
  while{(返回 boolean语句）;}
## 控制流：for 循环
- fori


   
        for（int i = 0;//任何语句，包括空语句 1️⃣
             i < 100;//返回boolean的语句，包括空语句  2️⃣
             i++){//任何语句，包括空语句） 4️⃣
               System.out.println(i);  //3️⃣
             }
- for each
  
  for（String s :Iterable<String>）{
      ……
  }

对象能够实现Iterabie接口（可迭代）


    List<String> list = Arrays.asList("1","2");
    //遍历列表，把元素拿出来
         for (int i = ;i<list.size(); i++){
             System.out.println(list.get(i));
        }
        
        ==
        
         for(String element : list){
        System.out.println(element);
        } 

     
     for ( int i = 0 ; i< list.size; i++){
         System.out.println(element);
     }
 
 
  统计一个给定的字符串中，大写英文字母（A,B,C,...,Z）出现的次数。    
        
        
        public static int  countUpperCaseLetters(String str) {
        int a = 0;
        for(char ch : str.toCharArray()){
            if(ch >='A' && ch<='Z'){
                ++a;
            }
        }
        return a;
    }
    public static void main(String[] args) {
        countUpperCaseLetters("AaBbCc1234ABC");
    }
}
## 改变循环的流程：break/continue
- braek;立即结束包裹当前braek第一层循环
- 找到结果后，打破当前循环，结束循环。

```java
        public static boolean find(List<Integer> list,int value){
        boolean exist = false;
        for (Integer element :list){
            if(element == value){
                exist = true;
                break;    
                 //break 包裹这 if 的循环 --> 结束的是if循环
            }
        }
        return exist;
        }
        
        public static void main(String[] args) throws IOException {
        List<Integer> list1 = Arrays.asList(1,2,3);
        find(list1,2);
        find(list1,2);

       }
```

- continue;跳过包裹当前continue第一层循环中的其余语句，继续下一次循环
- 如果if里面的语句满足，则跳过for剩下的未执行的语句，直接调回for的循环条件执行循环

```java
      //只打印list重的奇数元素
      public static void printOdd(List<Integer> list){
        for (int i= 0; i< list.size();i++){
             if (list.get(i) % 2 ==0 ){
                 continue;
                 //continue包裹的第一层循环是for --> 执行完第一次循环之后，就进入if
             } System.out.println(list.get(i));
        }
         }
        public static void main(String[] args) throws IOException {
        printOdd(Arrays.asList(1,4,2,56,-1,3,12));
        
        
         public static void printOddNumbersBetween(int start, int end) {


          for (int i = start; i <= end; i++) {
            if (i % 2 != 0) {
                System.out.print(i + ",");
           }
     
        List<Integer> list = new  ArrayList<>();
        List<Integer> list1 = new  ArrayList<>();
        for (int i = start; i <= end; i++) {
            list.add(i);
        }
        for (int i = 0; i <list.size();i++) {
            if (list.get(i) % 2 != 0) {
                list1.add(list.get(i));
                continue;
            }

        }System.out.println(list1);
      }
        public static void main(String[] args) {
        printOddNumbersBetween(1, 5);
        printOddNumbersBetween(-2, 2);
        }
       }

```
- break label

label：标签，在程序的某得地方设置标签，然后跳转到标签处，实现程序控制流程转移
- lable 要和任意语句进行搭配


         //不报错：任意字符（"http"） + "：" ———>lable ； "//"表示开始注释
        http://google.com
        
         public static void main(String[] args)  {
            int i = 100;
            while (i --> 0){//(i--)>0
                System.out.println(i);
        }
        
        
        public static void main(String[] args)throws IOException{
        最外层的循环:
           for (int i=0;i <2;i++){
               for (int j = 0; j< 1;j++){
                   break 最外层的循环;//通过label，break多层循环
               }

           }
           for (int i=0;i <2;i++){
               for (int j = 0; j< 1;j++){
                   break;//-->只能够解决当前的for，不能够解决所有的for
               }
            }
## 控制流：switch
### 可以switch的东西
  
- int / long / char / byte / short(所有的**整形**数，而不是*整数*，不是*浮点数*)
- enum 枚举
只能选取固定的值，例如:Weekday、colour

    
    private enum WeekDay{
    MONDAY,TUESDAY,//……，SUNDAY
    }
    
    
        enum Signal{    
        GREEN, YELLOW, RED  
        }  
         public class TrafficLight {         
         Signal color = Signal.RED;
         public void change() {  
         switch (color) {  
         color = Signal.GREEN;  
         break;  
         color = Signal.RED;  
        case GREEN:
        color = Signal.YELLOW;  
        break;  
          
            }  
        }  
      }

-   String （JDK7+）


        int i = new Random().nextInt(5);
           switch (i){
               case 0:
                   System.out.println(i);break;
               case 1:
                   System.out.println(i);break;
               case 2:
                   System.out.println(i);break;
               case 3:
                   System.out.println(i);break;
               case 4:
                   System.out.println(i);break;
                   default:
                       System.out.println(i);break;
                       //没有添加break的情况下执行完一个语句之后，会紧接着执行下一个case（穿透）
- 声明的作用域在一个{}，不同的{}可以声明相同的变量而互不相冲

    
      //如果i是A/B/C, 打印ABC
           //否则打印DE
           int i = (char)('A' + new Random().nextInt(5));
           switch (i){
               case 'A':
               case 'B':
               case 'C':{
                   String s = "ABC";
                   System.out.println(s);}
                   break;
                   default:
                       String s = "DE" ;
                       System.out.println(s);
                       break;


## 控制流：嵌套
- 所有调用都可以嵌套，但是方法声明不能够嵌套


                       while (true){
                           for (int j = 0; j < 100 ; j++) {
                               List<String> l = null;
                               for (String e : l){
                                   switch (i){

                                   }

                               }

                           }

穿透
      
       public static boolean isLeapYear(int year) {
         if (year % 100 == 0) {
             if (year % 400 == 0) {
                 return true;
             } else {
                 return false;
             }
         } else if (year % 4 == 0) {
                 return true;
         } else {
             return false;
         }
         
        // or
         return year % 100 == 0 ? ( year % 400 == 0 ? true : false ) : (year % 4 == 0 ? true :false );
         
计算质数
         
         
         public static int howManyPrimeNumbers(int n) {
        int num = 0;
        for (int n1 = 2; n1 < n; n1 ++){
                if (isPrimeNumber(n1)){
                    num++;
            }
        }
        return num;
        }
    public static boolean isPrimeNumber(int n1) {
        for (int i = 2; i <= Math.sqrt(n1) + 1; i++) {
            if (n1 % i == 0 && n1 !=i ) {
               return false;
            }
        }
      return true;
    }



 给定一个二维数组，返回其主对角线（从左上到右下）元素之和。

 https://blog.csdn.net/hester_hester/article/details/77925927
     
    
    public static int diagonalSum(int[][] matrix) {
        int N = matrix.length;
        int result = 0;
        for (int i = 0;i < N;i++){
            result += matrix[i][i];
        }return result;

    }