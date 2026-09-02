# 算法常用API

## 1. `LocalDate`

在算法的日期模拟中，核心 `API `是 **`java.time.LocalDate`**。它内部封装了完整的公历历法规则（包括大小月和闰年），规避了手动维护数组或编写判断逻辑的风险

在 Java 8 的时间 API 中，`LocalDate` 类的构造方法是私有的（`private`），因此**不能使用 `new` 来实例化对象**。



<font color = "blue"> 实例化</font>

```java
LocalDate.of(int year, int month, int dayOfMonth);		// 根据指定的年月日创建日期
LocalDate.parse(CharSequence text);		// 将标准格式的字符串（如：2026-03-30）解析为日期对象。
```

<font color = "blue"> 状态推演</font>

`LocalDate`是不可变对象，所有加减操作都会返回一个新的对象，因此必须重新赋值给原变量。

```java
public LocalDate plusDays(long days);		// 向后推演指定的天数（模拟中最常用的操作）
public LocalDate plusMonths(long months);	// 向后推演指定的月数
public LocalDate plusYears(long years);		// 向后推演指定的年数
public LocalDate minusDays(long days);		// 向前回退指定的天数
public LocalDate minusMonths(long months);	// 向前回退指定的月数
public LocalDate minusYears(long years);	// 向前回退指定的年数
```

<font color = "blue"> 属性提取</font>

```java
public int getYear();			// 获取整型年份
public int getMonthValue();		// 获取整型月份
public int getDayOfMonth();		// 获取整型日期
public int getDayOfWeek().getValue();		//getDayOfWeek() 会返回一个 DayOfWeek 枚举对象，调用其 getValue() 方法最终返回 int（1 代表周一，7 代表周日）
public int getDayOfYear();		// 获取该日期是当前月份的第几天
public boolean isLeapYear();	// 判断当前年份是否为闰年
```

<font color = "blue"> 边界对比</font>

```java
public boolean isBefore(LocalDate other);		// 如果当前日期早于目标日期，返回true
public boolean isAfter(LocalDate other);		// 如果当前日期晚于目标日期，返回true
public boolean isEqual(LocalDate other);		// 如果两个日期相同返回true
```



<font color = "blue"> 模板 </font>

```java
// 1. 设置闭区间边界
LocalDate current = LocalDate.of(起始年, 起始月, 起始日);
LocalDate end = LocalDate.of(结束年, 结束月, 结束日);

// 2. 循环推演，!isAfter 确保包含 end 当天
while (!current.isAfter(end)) {
    
    // 步骤 A: 提取属性
    int y = current.getYear();
    int m = current.getMonthValue();
    int d = current.getDayOfMonth();
    
    // 步骤 B: 执行校验逻辑（如判断数码特征、星期特征等）
    // ...
    
    // 步骤 C: 状态推演（前进 1 天）
    current = current.plusDays(1);
}
```



<font color = "blue"> 例题一 </font>

[洛谷：日期统计](https://www.luogu.com.cn/problem/P15963?contestId=314564)

239 年 9 月 9 日到 9876 年 1 月 1 日中有多少个可爱的日子 ？一个日期是可爱的，当且仅当其年、月、日中所有出现过的数码出现次数相同（不含前导 0）

`一周目`

```java

public class Main {
	public static void main(String[] args) {
//		int year = 2239;
//		int month = 9;
//		int day = 9;
		int count = 0;
		for (int year = 2239; year <= 9876; year++) {
			boolean run = isRun(year);
			for (int month = 1; month <= 12; month++) {
				if (year == 9876 && month > 1) continue;
				if (year == 2239 && month < 9) continue;
				
				int end;
				if (run && month == 2) end = 29;
				else if (month == 2) end = 28;
				else if (month == 1 || month == 3 || month == 5 || month == 7 || month == 8 || month == 10 || month == 12)
					end = 31;
				else end = 30;
				for (int day = 1; day <= end; day++) {
					if (year == 9876 && month == 1 && day > 1) continue;
					if (year == 2239 && month <= 9 && day < 9) continue;
					
					if (isCute(year, month, day)) {
						count++;
					}
				}
			}
		}
		System.out.println(count);
	}
	
	public static boolean isRun(int year) {
		if (year % 400 == 0 || (year % 4 == 0 && year % 100 != 0) ) {
				return true;
		}
		return false;
	}
	
	public static boolean isCute(int year, int month, int day) {
		int[] num = new int[10];
		while (year > 0) {
			num[year % 10]++;
			year /= 10;
		}
		while (month > 0) {
			num[month % 10]++;
			month /= 10;
		} 
		while (day > 0) {
			num[day % 10]++;
			day /= 10;
		}
		
		int count = 0;
		for (int i = 0; i < num.length; i++) {
			if (num[i] == 0) continue;
			if (count == 0) count = num[i];
			if (count != 0 && count != num[i]) {
				return false;
			}
		}
		return true;
	}
}

```

`LocalDateAPI`

```java
import java.time.LocalDate;

public class Main {
	public static void main(String[] args) {
		LocalDate current = LocalDate.of(2239, 9, 9);
		LocalDate end = LocalDate.of(9876, 1, 1);
		
		int count = 0;
		while (!current.isAfter(end)) {

			if (isCute(current)) {
				count++;
//				System.out.println(count);
			}
			
			current = current.plusDays(1);
		}
		
		System.out.println(count);
	}
	
	public static boolean isCute(LocalDate time) {
		int year = time.getYear();
		int month = time.getMonthValue();
		int day = time.getDayOfMonth();
		
		String num = "" + year + month + day;
		int[] nums = new int[10];
		
		for (int i = 0; i < num.length(); i++) {
			char c = num.charAt(i);
			int index = c - '0';
			nums[index]++;
		}
		
		int count = 0;
		for (int i : nums) {
			if (i == 0) continue;
			if (count == 0) count = i;
			else {
				if (count != i) return false;
			}
		}
		
		return true;
	}
}

```



## 2. `StreamTokenizer`  和 `PrintWriter`

在算法竞赛中，当输入输出数据量达到 10^5 级别时，`Scanner` 和 `System.out.println` 会因为底层复杂的正则解析和频繁的同步 I/O 阻塞导致严重超时（TLE）或内存超限（MLE）。

| **操作目标**       | **Scanner 原写法**                     | **快读快写替代写法**                                         |
| ------------------ | -------------------------------------- | ------------------------------------------------------------ |
| **初始化输入**     | `Scanner sc = new Scanner(System.in);` | `StreamTokenizer in = new StreamTokenizer(new BufferedReader(new InputStreamReader(System.in)));` |
| **初始化输出**     | 无（直接调 `System.out`）              | `PrintWriter out = new PrintWriter(System.out);`             |
| **读取一个整数**   | `int n = sc.nextInt();`                | `in.nextToken(); int n = (int) in.nval;`                     |
| **读取一个浮点数** | `double d = sc.nextDouble();`          | `in.nextToken(); double d = in.nval;`                        |
| **读取一个字符串** | `String s = sc.next();`                | `in.nextToken(); String s = in.sval;`                        |
| **打印输出**       | `System.out.println(ans);`             | `out.println(ans);`                                          |
| **程序结束收尾**   | `sc.close();` (可选)                   | `out.flush();` **（必写，否则无输出）**                      |

