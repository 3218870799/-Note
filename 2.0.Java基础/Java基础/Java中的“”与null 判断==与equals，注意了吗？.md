举两个例子，看懂就懂！！
```
package com.xqc.test;

public class TestNULLAndEquals {
    public static void main(String[] args) {
        //str1不是一个实例化对象
        String str1=null;
        //str2是已经实例化，已经分配了内存，在内存中存在
        //可以使用Object的str2.equals(),str2.toString方法
        String str2="";
         //false,==判断是否指向同一对象
         System.out.println(str1=="");
         //false
         System.out.println(str2==null);

         //报错,str1不是一个实例化对象，不能使用Object的方法
         //System.out.println(str1.equals(""));
         //false，String中的equals进行了重写，比较两个字符串是否相等
         System.out.println(str2.equals(null));
         //false
         System.out.println(str1==str2);
         //报错，str1不是实例化对象，不能使用Object的方法
         System.out.println(str1.equals(str2));
     }
 }
```

 