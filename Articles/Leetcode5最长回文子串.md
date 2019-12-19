## 题目：给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

示例1：

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

示例2：

```
输入: "cbbd"
输出: "bb"
```

### 解法1：中心扩展算法 （C++）

**性能指标数据**：执行用时 8 ms，消耗内存 8.7M。

**复杂度分析：**

* 时间复杂度： O(n^2)，由于围绕中心来扩展回文会耗去 O(n) 的时间，所以总的复杂度为 O(n^2)。
* 空间复杂度：O(1)。


```
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() < 2) return s;
        // 定义两个变量 start 和 maxLen，分别表示最长回文子串的起点和长度。
        int n = s.size(), maxLen = 0, start = 0;
        for (int i = 0; i < n;) {
            //在遍历s字符串时，首先需要判断剩余的字符数是否小于等于 maxLen 的一半，如果是的话，表明即使从当前字符到末尾字符组成的子串是半个回文串，那么整个回文串长度最多也就是 maxLen，既然 maxLen 无法再变长了，计算这些就没有意义，故直接在当前位置 break。否则，向后继续判断。
            if (n - i <= maxLen / 2) break;
            //定义两个变量 left 和 right 分别指向当前位置，然后执行向右遍历跳过重复项。此操作很重要。因为对于 noon，i在第一个o的位置，如果我们以o为最中心往两边扩散，是无法得到长度为4的回文串的，只有先跳过重复，此时left指向第一个o，right指向第二个o，然后再向两边扩散。而对于bob，i在第一个o的位置时，无法向右跳过重复，此时 left 和 right 同时指向o，再向两边扩散也是正确的，故可以同时处理奇数和偶数的情况。
            int left = i, right = i;
            while (right < n - 1 && s[right + 1] == s[right]) ++right;
            i = right + 1;
            // 由中心向两端扩展。
            while (right < n - 1 && left > 0 && s[right + 1] == s[left - 1]) {
                ++right; --left;
            }
            // 更新 maxLen 和 start 
            if (maxLen < right - left + 1) {
                maxLen = right - left + 1;
                start = left;
            }
        }
        return s.substr(start, maxLen);
    }
};
```



### 解法二：动态规划(Dynamic Programming)算法

**性能指标数据**：执行用时 200 ms，消耗内存 13.1 M。

**复杂度分析：**

* 时间复杂度： O(n^2)。
* 空间复杂度：O(n^2)，该方法使用 O(n^2) 的空间来存储表。

**解题思路**：维护一个二维数组 dp，其中 dp[i][j] 表示字符串区间 [i, j] 是否为回文串，当 i = j 时，只有一个字符，肯定是回文串，如果 i = j + 1，说明是相邻字符，此时需要判断 s[i] 是否等于 s[j]，如果i和j不相邻，即 i - j >= 2 时，除了判断 s[i] 和 s[j] 相等之外，dp[j + 1][i - 1] 若为真，就是回文串。



```
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        int n = s.size(), dp[n][n] = {0}, left = 0, len = 1;
        for (int i = 0; i < n; ++i) {
            dp[i][i] = 1;
            for (int j = 0; j < i; ++j) {
                dp[j][i] = (s[i] == s[j] && (i - j < 2 || dp[j + 1][i - 1]));
                // dp[j][i]=1时，即字符串区间 [j, i]是回文字符串时，更新 left 和 len变量。
                if (dp[j][i] && len < i - j + 1) {
                    len = i - j + 1;
                    left = j;
                }
            }
        } 
        return s.substr(left, len);
    }
};
```

### 解法三：Manacher's Algorithm（马拉车算法）

**性能指标数据**：执行用时 16 ms，消耗内存 9.2 M。

**复杂度分析：**

* 时间复杂度： O(n)。
* 空间复杂度：

```
class Solution {
public:
    string longestPalindrome(string s) {
        string t ="$#";
        for (int i = 0; i < s.size(); ++i) {
            t += s[i];
            t += '#';
        }
        int p[t.size()] = {0}, id = 0, mx = 0, resId = 0, resMx = 0;
        for (int i = 1; i < t.size(); ++i) {
            p[i] = mx > i ? min(p[2 * id - i], mx - i) : 1;
            while (t[i + p[i]] == t[i - p[i]]) ++p[i];
            if (mx < i + p[i]) {
                mx = i + p[i];
                id = i;
            }
            if (resMx < p[i]) {
                resMx = p[i];
                resId = i;
            }
        }
        return s.substr((resId - resMx) / 2, resMx - 1);
    }
};
```




