# Java String DSA Mastery Guide
### Methods + Patterns + Practice Problems

---

## Part 1: Java String / StringBuilder Methods

### The Immutability Trap
`String` is immutable — every "modification" creates a new object. This is why `StringBuilder` exists, and why building strings in a loop with `+=` is an interview red flag (O(n²) due to repeated copying).

```java
String s = "abc";
s.concat("d");        // does NOT change s!
s = s.concat("d");    // now s = "abcd"
```

### Core Inspection Methods

| Method | Use |
|---|---|
| `s.length()` | size — used in almost every loop bound |
| `s.charAt(i)` | access char at index — O(1) |
| `s.isEmpty()` / `s.isBlank()` | edge case checks |
| `s.equals(t)` / `s.equalsIgnoreCase(t)` | content comparison (`==` compares references!) |
| `s.compareTo(t)` | lexicographic comparison — needed for sorting strings |

### Searching

| Method | Use |
|---|---|
| `s.indexOf(ch/str)` | first occurrence, `-1` if absent |
| `s.lastIndexOf(ch/str)` | last occurrence |
| `s.contains(t)` | substring existence check |
| `s.startsWith(prefix)` / `s.endsWith(suffix)` | prefix/suffix problems |

### Slicing & Transforming

| Method | Use |
|---|---|
| `s.substring(i)` / `s.substring(i, j)` | extract substring — j exclusive |
| `s.split(regex)` | tokenizing — e.g. splitting sentences into words |
| `s.trim()` / `s.strip()` | remove leading/trailing whitespace (`strip()` is unicode-aware, prefer it) |
| `s.toLowerCase()` / `s.toUpperCase()` | case normalization — vital in anagram/palindrome checks |
| `s.replace(a, b)` | replace all occurrences of a char/substring |
| `s.toCharArray()` | converts to `char[]` so you can sort it, use two pointers, mutate freely |
| `String.valueOf(x)` / `Character.toString(c)` | build string from other types |
| `String.join(delim, arr)` | join a list/array back into a string |

### StringBuilder (your best friend for building strings)

| Method | Use |
|---|---|
| `new StringBuilder()` | mutable buffer |
| `sb.append(x)` | O(1) amortized — always prefer over `+=` in loops |
| `sb.reverse()` | instant palindrome/reverse checks |
| `sb.deleteCharAt(i)` / `sb.delete(i,j)` | remove chars efficiently |
| `sb.insert(i, x)` | insert at position |
| `sb.charAt(i)` / `sb.setCharAt(i, c)` | mutate in place — String can't do this! |
| `sb.toString()` | convert back when done |

### Character Class Helpers

```java
Character.isDigit(c)
Character.isLetter(c)
Character.isLetterOrDigit(c)
Character.isUpperCase(c) / isLowerCase(c)
Character.toLowerCase(c) / toUpperCase(c)
c - '0'          // digit char to int
(char)('a' + i)  // int to letter
```

---

## Part 2: DSA String Patterns — What, When, Signal Words

| # | Pattern | When to use | Signal words |
|---|---|---|---|
| 1 | Two Pointers | palindrome check, reverse in place, comparing from both ends | "palindrome", "reverse", "is mirror" |
| 2 | Sliding Window | longest/shortest substring satisfying a condition | "longest substring", "minimum window", "at most/exactly K distinct" |
| 3 | HashMap / Frequency Array | anagrams, first non-repeating char, char frequency comparisons | "anagram", "permutation of", "frequency", "unique character" |
| 4 | HashSet | checking duplicates | "distinct", "without repeating", "unique" |
| 5 | StringBuilder Construction | building result strings, encoding/compressing | "build", "construct", "encode/decode", "compress" |
| 6 | Sorting Characters | anagram grouping/checking | "group anagrams", "same characters" |
| 7 | Prefix Sum on Strings | substring queries about char counts over ranges | "range query", "count of char between indices" |
| 8 | Backtracking / Recursion | generating all permutations, subsequences, partitions | "generate all", "permutations", "partition", "combinations" |
| 9 | Dynamic Programming | LCS, edit distance, longest palindromic substring, word break | "longest common", "edit distance", "minimum operations to convert" |
| 10 | Stack | valid parentheses, nested structure, decode strings | "valid parentheses", "nested", "decode string" |
| 11 | Trie (Prefix Tree) | prefix search, autocomplete, dictionary lookup | "prefix", "autocomplete", "word search", "dictionary" |
| 12 | KMP / Rabin-Karp | efficient pattern matching in text | "find substring occurrences", "pattern matching" |

### Quick Decision Checklist
Before coding any string problem, ask:
1. Comparing characters from both ends? → **Two Pointers**
2. Looking for a substring with some property? → **Sliding Window**
3. Care about character counts? → **HashMap / Frequency Array**
4. Generating all possibilities? → **Backtracking**
5. Comparing/transforming two strings optimally? → **DP**
6. Nesting/matching structure involved? → **Stack**
7. Building a new string? → **StringBuilder, never `+=`**

---

## Part 3: Practice Problems by Pattern

### 1. Two Pointers

**Problem: Valid Palindrome**
```java
public boolean isPalindrome(String s) {
    int l = 0, r = s.length() - 1;
    while (l < r) {
        char cl = Character.toLowerCase(s.charAt(l));
        char cr = Character.toLowerCase(s.charAt(r));
        if (!Character.isLetterOrDigit(cl)) { l++; continue; }
        if (!Character.isLetterOrDigit(cr)) { r--; continue; }
        if (cl != cr) return false;
        l++; r--;
    }
    return true;
}
```

**Problem: Reverse Words in a String** (`"  hello   world  "` → `"world hello"`)
```java
public String reverseWords(String s) {
    String[] words = s.trim().split("\\s+");
    StringBuilder sb = new StringBuilder();
    for (int i = words.length - 1; i >= 0; i--) {
        sb.append(words[i]);
        if (i != 0) sb.append(" ");
    }
    return sb.toString();
}
```

---

### 2. Sliding Window

**Problem: Longest Substring Without Repeating Characters**
```java
public int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> lastSeen = new HashMap<>();
    int left = 0, maxLen = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (lastSeen.containsKey(c) && lastSeen.get(c) >= left) {
            left = lastSeen.get(c) + 1;
        }
        lastSeen.put(c, right);
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

**Problem: Minimum Window Substring** (`s="ADOBECODEBANC", t="ABC"` → `"BANC"`)
```java
public String minWindow(String s, String t) {
    if (s.length() < t.length()) return "";
    Map<Character, Integer> need = new HashMap<>();
    for (char c : t.toCharArray()) need.put(c, need.getOrDefault(c, 0) + 1);

    Map<Character, Integer> window = new HashMap<>();
    int left = 0, required = need.size(), formed = 0;
    int bestLen = Integer.MAX_VALUE, bestStart = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);
        if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) formed++;

        while (formed == required) {
            if (right - left + 1 < bestLen) {
                bestLen = right - left + 1;
                bestStart = left;
            }
            char lc = s.charAt(left);
            window.put(lc, window.get(lc) - 1);
            if (need.containsKey(lc) && window.get(lc) < need.get(lc)) formed--;
            left++;
        }
    }
    return bestLen == Integer.MAX_VALUE ? "" : s.substring(bestStart, bestStart + bestLen);
}
```

---

### 3. HashMap / Frequency Array

**Problem: Valid Anagram**
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    for (char c : t.toCharArray()) freq[c - 'a']--;
    for (int f : freq) if (f != 0) return false;
    return true;
}
```

**Problem: First Unique Character in a String**
```java
public int firstUniqChar(String s) {
    int[] freq = new int[26];
    for (char c : s.toCharArray()) freq[c - 'a']++;
    for (int i = 0; i < s.length(); i++) {
        if (freq[s.charAt(i) - 'a'] == 1) return i;
    }
    return -1;
}
```

---

### 4. HashSet

**Problem: Longest Substring with At Most Two Distinct Characters**
```java
public int lengthOfLongestSubstringTwoDistinct(String s) {
    Map<Character, Integer> count = new HashMap<>();
    int left = 0, maxLen = 0;
    for (int right = 0; right < s.length(); right++) {
        count.merge(s.charAt(right), 1, Integer::sum);
        while (count.size() > 2) {
            char lc = s.charAt(left);
            count.put(lc, count.get(lc) - 1);
            if (count.get(lc) == 0) count.remove(lc);
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

### 5. StringBuilder Construction

**Problem: String Compression** (`"aabcccccaaa"` → `"a2b1c5a3"`)
```java
public String compress(String s) {
    StringBuilder sb = new StringBuilder();
    int i = 0;
    while (i < s.length()) {
        char c = s.charAt(i);
        int count = 0;
        while (i < s.length() && s.charAt(i) == c) { count++; i++; }
        sb.append(c).append(count);
    }
    return sb.length() < s.length() ? sb.toString() : s;
}
```

---

### 6. Sorting Characters

**Problem: Group Anagrams**
```java
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> groups = new HashMap<>();
    for (String s : strs) {
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        String key = new String(arr);
        groups.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
    }
    return new ArrayList<>(groups.values());
}
```

---

### 7. Prefix Sum on Strings

**Problem: Range Character Count Queries**
```java
// Precompute prefix count of char 'x' up to each index, answer queries in O(1)
public int[] buildPrefixCount(String s, char x) {
    int[] prefix = new int[s.length() + 1];
    for (int i = 0; i < s.length(); i++) {
        prefix[i + 1] = prefix[i] + (s.charAt(i) == x ? 1 : 0);
    }
    return prefix;
}
// count of x in s[l..r] inclusive = prefix[r+1] - prefix[l]
```

---

### 8. Backtracking

**Problem: Generate All Subsequences**
```java
public List<String> subsequences(String s) {
    List<String> result = new ArrayList<>();
    backtrack(s, 0, new StringBuilder(), result);
    return result;
}
private void backtrack(String s, int i, StringBuilder path, List<String> result) {
    if (i == s.length()) { result.add(path.toString()); return; }
    path.append(s.charAt(i));
    backtrack(s, i + 1, path, result);
    path.deleteCharAt(path.length() - 1);
    backtrack(s, i + 1, path, result);
}
```

**Problem: Palindrome Partitioning**
```java
public List<List<String>> partition(String s) {
    List<List<String>> result = new ArrayList<>();
    backtrackPart(s, 0, new ArrayList<>(), result);
    return result;
}
private void backtrackPart(String s, int start, List<String> path, List<List<String>> result) {
    if (start == s.length()) { result.add(new ArrayList<>(path)); return; }
    for (int end = start + 1; end <= s.length(); end++) {
        String sub = s.substring(start, end);
        if (isPalin(sub)) {
            path.add(sub);
            backtrackPart(s, end, path, result);
            path.remove(path.size() - 1);
        }
    }
}
private boolean isPalin(String s) {
    int l = 0, r = s.length() - 1;
    while (l < r) if (s.charAt(l++) != s.charAt(r--)) return false;
    return true;
}
```

---

### 9. Dynamic Programming on Strings

**Problem: Longest Palindromic Substring**
```java
public String longestPalindrome(String s) {
    int n = s.length();
    boolean[][] dp = new boolean[n][n];
    int start = 0, maxLen = 1;
    for (int i = 0; i < n; i++) dp[i][i] = true;

    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            if (s.charAt(i) == s.charAt(j)) {
                if (len == 2 || dp[i + 1][j - 1]) {
                    dp[i][j] = true;
                    if (len > maxLen) { start = i; maxLen = len; }
                }
            }
        }
    }
    return s.substring(start, start + maxLen);
}
```

**Problem: Edit Distance**
```java
public int minDistance(String word1, String word2) {
    int m = word1.length(), n = word2.length();
    int[][] dp = new int[m + 1][n + 1];
    for (int i = 0; i <= m; i++) dp[i][0] = i;
    for (int j = 0; j <= n; j++) dp[0][j] = j;

    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (word1.charAt(i - 1) == word2.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1];
            } else {
                dp[i][j] = 1 + Math.min(dp[i - 1][j - 1], Math.min(dp[i - 1][j], dp[i][j - 1]));
            }
        }
    }
    return dp[m][n];
}
```

---

### 10. Stack

**Problem: Valid Parentheses**
```java
public boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '{' || c == '[') stack.push(c);
        else {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if (c == ')' && top != '(') return false;
            if (c == '}' && top != '{') return false;
            if (c == ']' && top != '[') return false;
        }
    }
    return stack.isEmpty();
}
```

**Problem: Decode String** (`"3[a2[c]]"` → `"accaccacc"`)
```java
public String decodeString(String s) {
    Deque<Integer> counts = new ArrayDeque<>();
    Deque<StringBuilder> strs = new ArrayDeque<>();
    StringBuilder cur = new StringBuilder();
    int num = 0;

    for (char c : s.toCharArray()) {
        if (Character.isDigit(c)) {
            num = num * 10 + (c - '0');
        } else if (c == '[') {
            counts.push(num);
            strs.push(cur);
            cur = new StringBuilder();
            num = 0;
        } else if (c == ']') {
            StringBuilder temp = cur;
            cur = strs.pop();
            int repeat = counts.pop();
            for (int i = 0; i < repeat; i++) cur.append(temp);
        } else {
            cur.append(c);
        }
    }
    return cur.toString();
}
```

---

### 11. Trie

**Problem: Implement Trie + Search Prefix**
```java
class Trie {
    class Node {
        Node[] children = new Node[26];
        boolean isEnd = false;
    }
    private Node root = new Node();

    public void insert(String word) {
        Node cur = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (cur.children[idx] == null) cur.children[idx] = new Node();
            cur = cur.children[idx];
        }
        cur.isEnd = true;
    }

    public boolean search(String word) {
        Node node = find(word);
        return node != null && node.isEnd;
    }

    public boolean startsWith(String prefix) {
        return find(prefix) != null;
    }

    private Node find(String word) {
        Node cur = root;
        for (char c : word.toCharArray()) {
            int idx = c - 'a';
            if (cur.children[idx] == null) return null;
            cur = cur.children[idx];
        }
        return cur;
    }
}
```

---

### 12. Pattern Matching (KMP)

**Problem: Find first occurrence of pattern in text (implement `indexOf` yourself)**
```java
public int strStr(String text, String pattern) {
    if (pattern.isEmpty()) return 0;
    int[] lps = buildLPS(pattern);
    int i = 0, j = 0;
    while (i < text.length()) {
        if (text.charAt(i) == pattern.charAt(j)) {
            i++; j++;
            if (j == pattern.length()) return i - j;
        } else if (j > 0) {
            j = lps[j - 1];
        } else {
            i++;
        }
    }
    return -1;
}
private int[] buildLPS(String pattern) {
    int[] lps = new int[pattern.length()];
    int len = 0, i = 1;
    while (i < pattern.length()) {
        if (pattern.charAt(i) == pattern.charAt(len)) {
            lps[i++] = ++len;
        } else if (len > 0) {
            len = lps[len - 1];
        } else {
            lps[i++] = 0;
        }
    }
    return lps;
}
```

---

## How to Drill This

Do one pattern a day:
1. Solve the problem cold — no looking at the reference code.
2. Check your solution against the reference.
3. Note exactly where you got stuck (which method you forgot, which edge case you missed).
4. Move to the next pattern only once you can write that pattern's code from memory in under 10 minutes.

Before touching the keyboard on any new string problem, run through the **Quick Decision Checklist** in Part 2 — it will point you to the right pattern before you waste time on a brute-force approach that won't pass in an interview.
