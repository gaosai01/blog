
# 题目：合唱团

### 题目描述
有 n 个学生站成一排，每个学生有一个能力值，牛牛想从这 n 个学生中按照顺序选取 k 名学生，要求相邻两个学生的位置编号的差不超过 d，使得这 k 个学生的能力值的乘积最大，你能返回最大的乘积吗？
### 输入描述:
每个输入包含 1 个测试用例。每个测试数据的第一行包含一个整数 n (1 <= n <= 50)，表示学生的个数，接下来的一行，包含 n 个整数，按顺序表示每个学生的能力值 ai（-50 <= ai <= 50）。接下来的一行包含两个整数，k 和 d (1 <= k <= 10, 1 <= d <= 50)。
### 输出描述:
输出一行表示最大的乘积。
### 示例1
#### 输入
3
7 4 7
2 50
#### 输出
49

### 代码提交地址

[点此链接](https://www.nowcoder.com/practice/661c49118ca241909add3a11c96408c8?tpId=85&tqId=29830&tPage=1&rp=1&ru=/ta/2017test&qru=/ta/2017test/question-ranking)

```
https://www.nowcoder.com/practice/661c49118ca241909add3a11c96408c8?tpId=85&tqId=29830&tPage=1&rp=1&ru=/ta/2017test&qru=/ta/2017test/question-ranking
```

### 代码

```
import java.util.*;

public class Main {
	public static void main(String[] args) {
		Scanner scan = new Scanner(System.in);
		int n = scan.nextInt();
		int[] a = new int[n];
		for (int i = 0; i < n; ++i) {
			a[i] = scan.nextInt();
		}
		int k = scan.nextInt();
		int d = scan.nextInt();

		long[][] dpmax = new long[k][n];
		for (int i = 0; i < n; ++i) {
			dpmax[0][i] = a[i];
		}
		long[][] dpmin = new long[k][n];
		for (int i = 0; i < n; ++i) {
			dpmin[0][i] = a[i];
		}
		for (int j = 1; j < k; ++j) {
			for (int i = j; i < n; ++i) {
				// 求最大值
				dpmax[j][i] = dpmax[j - 1][i - 1] * a[i];
				for (int x = i - 2; x >= 0 && i - x <= d; --x) {
					dpmax[j][i] = Math.max(dpmax[j][i], dpmax[j - 1][x] * a[i]);
				}
				dpmax[j][i] = Math.max(dpmax[j][i], dpmin[j - 1][i - 1] * a[i]);
				for (int x = i - 2; x >= 0 && i - x <= d; --x) {
					dpmax[j][i] = Math.max(dpmax[j][i], dpmin[j - 1][x] * a[i]);
				}
				// 求最小值
				dpmin[j][i] = dpmax[j - 1][i - 1] * a[i];
				for (int x = i - 2; x >= 0 && i - x <= d; --x) {
					dpmin[j][i] = Math.min(dpmin[j][i], dpmax[j - 1][x] * a[i]);
				}
				dpmin[j][i] = Math.min(dpmin[j][i], dpmin[j - 1][i - 1] * a[i]);
				for (int x = i - 2; x >= 0 && i - x <= d; --x) {
					dpmin[j][i] = Math.min(dpmin[j][i], dpmin[j - 1][x] * a[i]);
				}

			}
		}
		// 找最大值
		long ans = 0;
		for (int i = 0; i < n; ++i) {
			ans = Math.max(ans, dpmax[k - 1][i]);
			ans = Math.max(ans, dpmin[k - 1][i]);
		}
		System.out.println(ans);

	}
}

```