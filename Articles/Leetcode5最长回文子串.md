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

## 解法1：中心扩展算法 （C++）

```
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.size() < 2) return s;
        // 定义两个变量 start 和 maxLen，分别表示最长回文子串的起点和长度。
        int n = s.size(), maxLen = 0, start = 0;
        for (int i = 0; i < n;) {
            //在遍历s字符串时，首先需要判断剩余的字符数是否小于等于 maxLen 的一半，如果是的话，表明即使从当前字符到末尾字符组成的子串是半个回文串，              那么整个回文串长度最多也就是 maxLen，既然 maxLen 无法再变长了，计算这些就没有意义，故直接在当前位置 break。否则，向后继续判断。
            if (n - i <= maxLen / 2) break;
            //定义两个变量 left 和 right 分别指向当前位置，然后执行向右遍历跳过重复项。此操作很重要。因为对于 noon，i在第一个o的位置，如果我们以               o为最中心往两边扩散，是无法得到长度为4的回文串的，只有先跳过重复，此时left指向第一个o，right指向第二个o，然后再向两边扩散。而对于                  bob，i在第一个o的位置时，无法向右跳过重复，此时 left 和 right 同时指向o，再向两边扩散也是正确的，故可以同时处理奇数和偶数的情况。
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

**复杂度分析：**

* 时间复杂度： O(n^2)，由于围绕中心来扩展回文会耗去 O(n) 的时间，所以总的复杂度为 O(n^2)。
* 空间复杂度：O(1)。
