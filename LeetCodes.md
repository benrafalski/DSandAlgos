# Process
1. can you sort to make things easier?
2. can you use an unordered map (hash map)?
3. can you use a set?


# Hashing and Arrays
## Contains Duplicate
Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.
```c++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort(nums.begin(), nums.end());

        for(int i = 0; i < nums.size() - 1; ++i){
            if(nums[i] == nums[i+1]){
                return true;
            }
        }

        return false;
    }
};
```

## Valid Anagram
Given two strings s and t, return true if t is an anagram of s, and false otherwise.
```c++
class Solution {
public:
    bool isAnagram(string s, string t) {
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        return (s==t) ? true : false;
    }
};
```

## Two Sum
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> ans;
        unordered_map<int, int> umap; // <index, value>

        for(int i = 0; i < nums.size(); ++i){
            // get value of number needed to find
            int val = target - nums[i];
            // if(val < 0) continue;
            // check if the value is already found
            unordered_map<int,int>::const_iterator got = umap.find(val);
            if(got != umap.end()){
                ans = {i, got->second};
                break;
            }else{
                umap.insert({nums[i], i});
            }
        }
        return ans;
    }
};
```

## Group Anagrams
Given an array of strings strs, group the anagrams together. You can return the answer in any order.
```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        // save the original strings
        vector<string> original = strs;
        // now all the anagrams can be checked with a compare
        for(auto &s : strs) sort(s.begin(), s.end());
        unordered_map<string, vector<string>> umap;
        for(int i = 0; i < strs.size(); ++i){
            if(umap.find(strs[i]) == umap.end()){
                // if not found
                umap.insert({strs[i], {original[i]}});
            }else{
                // if found
                umap[strs[i]].push_back(original[i]);
            }
        }

        // now we have a hashmap of all the anagrams and their indecies
        vector<vector<string>> ans;
        for (auto &m : umap) ans.push_back(m.second);
        return ans;
    }
};
```

## Top K Frequent Elements
Given an integer array nums and an integer k, return the k most frequent elements. You may return the answer in any order.
```c++
class Solution {
public:
    bool static cmp(pair<int,int> a, pair<int,int> b){
        return a.second < b.second;
    }

    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> umap;
        for(auto &n : nums) {
            if(umap.find(n) == umap.end()){
                // if not found
                umap.insert({n, 1});
            }else{
                umap[n]++;
            }
        }
        // we now have a hashmap of the values and their frequencies
        vector<pair<int, int>> freqs(umap.begin(), umap.end());
        sort(freqs.begin(), freqs.end(), cmp);

        // now have a vector if sorted frequencies
        vector<int> ans;
        
        while(k > 0){
            ans.push_back(freqs.back().first);
            freqs.erase(freqs.end());
            k--;
        }

        return ans;
    }
};
```

## Product of Array Except Self
Given an integer array nums, return an array answer such that answer[i] is equal to the product of all the elements of nums except nums[i].
```c++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n);

        int before = 1;
        for(int i = 0; i < n; ++i){
            ans[i] = before;
            before *= nums[i];
        }

        int after = 1;
        for(int i = n-1; i >= 0; --i){
            ans[i] *= after;
            after *= nums[i];
        }
        return ans;
    }
};
```

## Valid Sudoku
Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be validated according to the following rules:
1. Each row must contain the digits 1-9 without repetition.
2. Each column must contain the digits 1-9 without repetition.
3. Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without repetition.
```c++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        vector<vector<char>> rs;
        vector<vector<char>> cs;
        vector<vector<vector<char>>> boxes;

        // check rows
        vector<unordered_map<char, int>> vmap(board.size());
        // go through board and get num freq for each row
        for(int i = 0; i < board.size(); ++i){
            for(int j = 0; j < board[i].size(); ++j){
                if(vmap[i].find(board[i][j]) == vmap[i].end()){
                    vmap[i].insert({board[i][j], 1});
                } else{
                    vmap[i][board[i][j]]++;
                }
            }
        }

        for(auto m : vmap){
            for(auto v : m){
                if(v.first != '.' && v.second > 1){
                    return false;
                }
            }
        }

        // check colums
        // transpose board
        vector<vector<char>> tboard(board.size());
        for(auto b : board){
            for(int i = 0; i < b.size(); ++i){
                tboard[i].push_back(b[i]);
            }
        }

        vector<unordered_map<char, int>> tmap(board.size());
        // go through board and get num freq for each row
        for(int i = 0; i < tboard.size(); ++i){
            for(int j = 0; j < tboard[i].size(); ++j){
                if(tmap[i].find(tboard[i][j]) == tmap[i].end()){
                    tmap[i].insert({tboard[i][j], 1});
                } else{
                    tmap[i][tboard[i][j]]++;
                }
            }
        }

        for(auto m : tmap){
            for(auto v : m){
                if(v.first != '.' && v.second > 1){
                    return false;
                }
            }
        }


        // check boxes
        vector<vector<char>> bboard(board.size());
        for(int i = 0; i < 3; ++i){
            for(int j = 0; j < 9; ++j){
                if(j < 3){
                    bboard[0].push_back(board[i][j]);
                }else if(j > 2 && j < 6){
                    bboard[1].push_back(board[i][j]);
                }else{
                    bboard[2].push_back(board[i][j]);
                }
            }
        }

        for(int i = 3; i < 6; ++i){
            for(int j = 0; j < 9; ++j){
                if(j < 3){
                    bboard[3].push_back(board[i][j]);
                }else if(j > 2 && j < 6){
                    bboard[4].push_back(board[i][j]);
                }else{
                    bboard[5].push_back(board[i][j]);
                }
            }  
        }

        for(int i = 6; i < 9; ++i){
            for(int j = 0; j < 9; ++j){
                if(j < 3){
                    bboard[6].push_back(board[i][j]);
                }else if(j > 2 && j < 6){
                    bboard[7].push_back(board[i][j]);
                }else{
                    bboard[8].push_back(board[i][j]);
                }
            }  
        }


        vector<unordered_map<char, int>> bmap(board.size());
        // go through board and get num freq for each row
        for(int i = 0; i < bboard.size(); ++i){
            for(int j = 0; j < bboard[i].size(); ++j){
                if(bmap[i].find(bboard[i][j]) == bmap[i].end()){
                    bmap[i].insert({bboard[i][j], 1});
                } else{
                    bmap[i][bboard[i][j]]++;
                }
            }
        }

        for(auto m : bmap){
            for(auto v : m){
                if(v.first != '.' && v.second > 1){
                    return false;
                }
            }
        }
        return true;
    }
};
```

## Encode and Decode Strings


## Longest Consecutive Sequence
Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.
```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        set<int> s;
        for(auto &n : nums) s.insert(n);

        int ans = 0;

        for(auto n : nums){
            if(s.find(n-1) == s.end()){
                // if start of a sequence
                int length = 0;
                while(s.find(n+length) != s.end()){
                    length++;
                }
                if(length > ans){
                    ans = length;
                }
            }
        }

        return ans;
    }
};
```

# Two Pointers
## Valid Palindrome
Given a string s, return true if it is a palindrome, or false otherwise.
```c++
class Solution {
public:
    bool isPalindrome(string s) {
        s.erase(std::remove_if(s.begin(), s.end(), []( auto const& c ) -> bool { return !std::isalnum(c); } ), s.end());
        for(auto &c : s) c = tolower(c);
        int i = 0, j = s.length()-1;
        while(i <= j){
            if(s[i] != s[j]) return false;
            i++;
            j--;
        }

        return true;
    }
};
```

## Two Sum II - Input Array Is Sorted

Given a 1-indexed array of integers numbers that is already sorted in non-decreasing order, find two numbers such that they add up to a specific target number. Let these two numbers be numbers[index1] and numbers[index2] where 1 <= index1 < index2 < numbers.length.

Return the indices of the two numbers, index1 and index2, added by one as an integer array [index1, index2] of length 2.

The tests are generated such that there is exactly one solution. You may not use the same element twice.

Your solution must use only constant extra space.

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int i=0;
        int j=numbers.size()-1;

        while(numbers[i]+numbers[j] != target){
            if(numbers[i] + numbers[j] > target) j--;
            else i++;
        }
        return {(i+1), (j+1)};
    }
};
```


## pass


## Container With Most Water
You are given an integer array height of length n. There are n vertical lines drawn such that the two endpoints of the ith line are (i, 0) and (i, height[i]).

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return the maximum amount of water a container can store.

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int max = -1;
        int l = 0;
        int r = height.size()-1;
        while(l<r){
            int check = min(height[l],height[r])*(r-l);
            if(check>max) max=check;
            if(height[l]>=height[r]) r--;
            else l++;
        }

        return max;
    }
};
```

## 42. Trapping Rain Water
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.
![image](./Images/img1.png)
```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int i=0;
        int j=i+1;

        int ans=0;

        while(j<height.size()){
            if(height[i]<=height[j]){
                if(j-i==1){
                    i=j;
                    j=i+1;
                }else{
                    // find area
                    int h = min(height[i], height[j]);
                    int w = j - i - 1;
                    int area = h*w;
                    int k = i+1;
                    while(k<j){
                        area -= height[k];
                        k++;
                    }
                    ans+=area;
                    i=j;
                    j=i+1;
                }
            }else{
                j++;
            }
        }

        reverse(height.begin(), height.end());
        i=0;        
        j=i+1;

        while(j<height.size()){
            if(height[i]<height[j]){
                if(j-i==1){
                    i=j;
                    j=i+1;
                }else{
                    // find area
                    int h = min(height[i], height[j]);
                    int w = j - i - 1;
                    int area = h*w;
                    int k = i+1;
                    while(k<j){
                        area -= height[k];
                        k++;
                    }
                    ans+=area;
                    i=j;
                    j=i+1;
                }
            }else{
                j++;
            }
        }
        return ans;
    }
};
```

# Stack

## Valid Parentheses
Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.
```c++
class Solution {
public:
    bool isValid(string s) {
        stack<char> stk;
        for(auto c : s){
            if(c == '{' || c == '(' || c == '['){
                stk.push(c);
            }else{
                if(stk.empty()) return false;
                char pop = stk.top();
                stk.pop();
                if(c == '}' && pop != '{') return false; 
                if(c == ']' && pop != '[') return false; 
                if(c == ')' && pop != '(') return false;           
            }
        }
        return (!stk.empty()) ? false : true;
    }
};
```


## Min Stack
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

Implement the MinStack class:

MinStack() initializes the stack object.
- void push(int val) pushes the element val onto the stack.
- void pop() removes the element on the top of the stack.
- int top() gets the top element of the stack.
- int getMin() retrieves the minimum element in the stack.

You must implement a solution with O(1) time complexity for each function.
```c++
class MinStack {
public:
    stack<int> stk;
    stack<int> minstack;
    MinStack() {

    }
    
    void push(int val) {
        if(stk.empty()) minstack.push(min(INT_MAX, val));
        else minstack.push(min(minstack.top(), val));
        stk.push(val); 
    }
    
    void pop() {
        minstack.pop();
        stk.pop();
    }
    
    int top() {
       return stk.top(); 
    }
    
    int getMin() {
        return minstack.top();
    }

};
```

## Evaluate Reverse Polish Notation
You are given an array of strings tokens that represents an arithmetic expression in a Reverse Polish Notation.

Evaluate the expression. Return an integer that represents the value of the expression.

Note that:

- The valid operators are '+', '-', '*', and '/'.
- Each operand may be an integer or another expression.
- The division between two integers always truncates toward zero.
- There will not be any division by zero.
- The input represents a valid arithmetic expression in a reverse polish notation.
- The answer and all the intermediate calculations can be represented in a 32-bit integer.

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> nums;
        int i = 0;
        for(string t : tokens){
            if(isdigit(t[0]) || t.size() > 1){
                nums.push(stoi(t));
            }else{
                int a = nums.top();
                nums.pop();
                int b = nums.top();
                nums.pop();
                if(t[0] == '+') nums.push(a+b); 
                if(t[0] == '-') nums.push(b-a);
                if(t[0] == '*') nums.push(a*b);
                if(t[0] == '/') nums.push(b/a);
            }
        }
        return nums.top();
    }
};
```


## Generate Parentheses
Given n pairs of parentheses, write a function to generate all combinations of well-formed parentheses.
```c++
class Solution {
public:
    stack<char> stk;
    vector<string> ans;

    vector<string> generateParenthesis(int n) {
        paren(0,0,n);
        return ans;
    }   

    void paren(int open, int closed, int n){
        if(open == closed && open == n){
            string a = "";
            stack<char> stk2 = stk;
            while(!stk2.empty()) {
                a += stk2.top();
                stk2.pop();
            }
            ans.push_back(a);
            return;
        }
        if(open < n){
            stk.push(')');
            paren(open+1, closed, n);
            stk.pop();
        }
        if(closed < open){
            stk.push('(');
            paren(open, closed+1, n);
            stk.pop();
        }
    }
};
```


# Binary Search

## Binary Search 
Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.

You must write an algorithm with O(log n) runtime complexity.
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0;
        int r = nums.size()-1;

        while(l <= r){
            int m = l + (r - l) / 2;
            if(nums[m] == target) return m;
            if(nums[m] < target) l = m + 1;
            else r = m - 1; 
        }

        return -1;
    }
};
```

