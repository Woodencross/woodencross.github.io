# Java Cheatsheet

<!--more-->

## 常用方法import

```java
import java.util.*;
import java.lang.*;
```

## 输入

```java
Scanner in = new Scanner(System.in);

in.next(); //String 以空格结束
in.nextLine(); //String 以回车结束
in.nextInt(); //int
in.nextFloat(); //float
in.nextDouble(); //double

in.hasNext(); //boolean
in.hasNextInt(); //boolean
in.hasNextLine(); //boolean
```

## 输出

```java
System.out.print();
System.out.println();
System.out.printf("%s", "hello");

System.out.printf("%c", 'c'); //字符
System.out.printf("%d", 10); //十进制
System.out.printf("%.2f", 3.1415); //保留两位小数
```

## 排序

```java
int[] a = {2, 1, 3};
Arrays.sort(a); //{1, 2, 3}
Arrays.sort(a, 0, 2); //对下标0-2排序
Arrays.sort(a, (a, b) -> {
  return b - a; //返回值大于0交换
}); //{3, 2, 1}
```

```java
ArrayList<Integer> a = new ArrayList<>();
a.add(2);
a.add(1);
a.add(3);
Collections.sort(a, Compatator.naturalOrder()); //升序
Collections.sort(a, Comparator.reverseOrder()); //降序
```

## 集合

### ArrayList

### LinkedList

```java
LinkedList<Integer> ll = new LinkedList<>();
ll.addFirst(1);
ll.addLast(1);
ll.removeFirst(); //int
ll.removeLast();
ll.getFirst();
ll.getLast();
```

### HashSet

```java
HashSet<Integer> hs = new HashSet<>();
hs.add(1);
hs.contains(1); //boolean
hs.remove(1);
hs.clear();
hs.size();
```

### HashMap

```java
HashMap<Integer, String> hm = new HashMap<>();
hm.put(1, "A");
hm.get(1); //A
hm.remove(1);
hm.containsKey(1);
hm.containsValue("A");
for (int i : hm.keySet()) {};
for (String s : hm.values()) {};
```

## String & StringBuilder

```java
String str = "Hello world!";
str.toCharArray(); //char[]
str.charAt(0); //char
str.trim(); //删除头尾空格
str.split("\\s+"); //删除空格
str.compareTo("ABC"); //比较字典序
```
```java
StringBuilder sb = new StringBuilder();
sb.append("Hello world");
sb.delete(0, 1); //左闭右开
sb.deleteCharAt(0);
sb.indexOf("world"); //子串第一次出现的索引
sb.lastIndexOf("world"); // 子串最后一次出现的索引
sb.insert(0, "abc");
sb.replace(0, 3, "abc");
sb.reverse(); //逆序
sb.subString(0, 3); //String
sb.toString(); //String
```

## Math

```java
Math.pow(2, 3); //2^3
Math.sqrt(9); //开方
Math.abs(-2); //绝对值
Math.round(3.14); //四舍五入
```

## 正则

```java
//java中"\\"表示"\"
A       //指定字符
.       //任意字符
\\d     //任意数字，等价[0-9]
\\w     //任意数字大小写字母下划线，等价[0-9a-zA-Z_]
\\s     //空格，tab
\\D     //非\\d
\\W     //非\\w
\\S     //非\\s
A*      //任意个数
A+      //至少一个
A?      //零或一个
A{3}    //指定个数
A{2,3}  //指定范围个数
A{2,}   //最少n个
A{0,3}  //最多n个
^A      //匹配开头
A$      //匹配结尾
[ABC]   //[]内任意字符
[^ABC]  //非[]内任意字符
A|B|C   //或
?       //非贪婪匹配
```
