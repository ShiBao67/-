2025/10/17
#	哈希表
	直接定址法
	求余留数法：把不连续的关键字映射到连续的地址空间，但会有冲突问题
	冲突处理：线性探测法：冲突后依次探测下一个位置，直到遇到空闲位置。采用除留余数加线性探测，表长=元素总数/除留因子
	平方探测法：平方的顺序探测，表长=4k+3的质数
	拉链法：把所有同意词用单链表串起来，每个元素包含一个头指针，相当于一个一个的单链表
	class Solution {
	public:
    // 函数功能：计算字符串中「无重复字符的最长子串」的长度
    // 输入：字符串 s（例如 "abcabcbb"）
    // 输出：最长无重复子串的长度（例如 "abcabcbb" 的结果为 3，对应 "abc" 或 "bca" 等）
    int lengthOfLongestSubstring(string s) {
        // 用哈希集合存储当前窗口中的字符，实现 O(1) 时间复杂度的重复判断
        unordered_set<char> occ;
        // rk：右指针，初始化为 -1 表示「窗口右边界初始在字符串左侧（未包含任何字符）」
        // ans：记录最长无重复子串的长度，初始为 0
        int rk = -1, ans = 0;
        // n：字符串长度，避免多次调用 s.size()
        int n = s.size();
        
        // 左指针 i 从 0 开始遍历，每轮固定左指针，尝试扩展右指针
        for (int i = 0; i < n; ++i) {
            // 左指针右移时（i > 0），需移除窗口中「原左指针位置的字符」
            // 因为窗口范围从 [i-1, rk] 变为 [i, rk]，原左边界的字符已不在窗口内
            if (i != 0) {
                occ.erase(s[i-1]);
            }
            
            // 扩展右指针：只要右指针下一位（rk+1）未越界，且该位置字符不在当前窗口中
            // 就将该字符加入窗口，并右移右指针
            while (rk + 1 < n && !occ.count(s[rk + 1])) {
                occ.insert(s[rk + 1]);  // 将新字符加入窗口
                ++rk;  // 右指针右移，扩大窗口
            }
            
            // 此时窗口 [i, rk] 是「以 i 为左边界的最长无重复子串」
            // 计算当前窗口长度（rk - i + 1），更新最长长度 ans
            ans = max(ans, rk - i + 1);
        }
        
        return ans;
    	}
	};
#	合并数组
	#include <vector>
	#include <iostream>
	using namespace std;
	
	/**
	 * 方法一：归并两个有序数组，然后计算中位数
	 * 原理：将两个有序数组合并为一个大的有序数组，再根据总长度的奇偶性取中间元素
	 */
	double findMedianSortedArraysMerge(vector<int>& nums1, vector<int>& nums2) {
	    int m = nums1.size();  // 第一个数组长度
	    int n = nums2.size();  // 第二个数组长度
	    vector<int> merged;   // 用于存储合并后的有序数组
	    int i = 0, j = 0;     // 两个数组的遍历指针
	
	    // 合并两个有序数组（类似归并排序的合并步骤）
	    while (i < m && j < n) {
	        // 取较小的元素放入合并数组，并移动对应指针
	        if (nums1[i] < nums2[j]) {
	            merged.push_back(nums1[i]);
	            i++;
	        } else {
	            merged.push_back(nums2[j]);
	            j++;
	        }
	    }
	
	    // 处理第一个数组剩余的元素（若有）
	    while (i < m) {
	        merged.push_back(nums1[i]);
	        i++;
	    }
	
	    // 处理第二个数组剩余的元素（若有）
	    while (j < n) {
	        merged.push_back(nums2[j]);
	        j++;
	    }
	
	    // 计算中位数
	    int totalLen = merged.size();
	    if (totalLen % 2 == 1) {
	        // 总长度为奇数：直接返回中间位置的元素
	        return merged[totalLen / 2];
	    } else {
	        // 总长度为偶数：返回中间两个元素的平均值
	        return (merged[totalLen / 2 - 1] + merged[totalLen / 2]) / 2.0;
	    }
		}
#	移动指针
	/**
	 * 方法二：不合并数组，通过双指针移动找到中位数位置
	 * 原理：利用总长度已知的特性，通过指针移动计数，直接定位到中位数对应的元素
	 */
	double findMedianSortedArraysPointer(vector<int>& nums1, vector<int>& nums2) {
	    int m = nums1.size();   // 第一个数组长度
	    int n = nums2.size();   // 第二个数组长度
	    int totalLen = m + n;   // 总长度
	    int k = totalLen / 2;   // 中位数位置（偶数时需同时考虑k-1和k）
	    int i = 0, j = 0;       // 两个数组的遍历指针
	    int prev = 0, curr = 0; // 记录当前和上一个中间元素（用于偶数长度）
	
	    // 循环k+1次，找到第k个元素（0-based），同时记录前一个元素（用于偶数情况）
	    for (int count = 0; count <= k; count++) {
	        prev = curr;  // 更新上一个元素为当前元素
	
	        // 情况1：第一个数组已遍历完，只能取第二个数组的元素
	        if (i >= m) {
	            curr = nums2[j];
	            j++;
	        }
	        // 情况2：第二个数组已遍历完，只能取第一个数组的元素
	        else if (j >= n) {
	            curr = nums1[i];
	            i++;
	        }
	        // 情况3：第一个数组当前元素更小，取第一个数组的元素
	        else if (nums1[i] < nums2[j]) {
	            curr = nums1[i];
	            i++;
	        }
	        // 情况4：第二个数组当前元素更小，取第二个数组的元素
	        else {
	            curr = nums2[j];
	            j++;
	        }
	    }
	
	    // 根据总长度的奇偶性返回结果
	    if (totalLen % 2 == 1) {
	        // 奇数长度：中位数是第k个元素
	        return curr;
	    } else {
	        // 偶数长度：中位数是第k-1和k个元素的平均值
	        return (prev + curr) / 2.0;
	    }
		}
