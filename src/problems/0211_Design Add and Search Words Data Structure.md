# 211. Design Add and Search Words Data Structure (2021.05.02)

https://leetcode.com/problems/design-add-and-search-words-data-structure/

```java
/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * boolean param_2 = obj.search(word);
 */
```

## Solution 1: Trie

- $TC:WordDictionary() - O(1), addWord() - O(m), search() - O(m), but O(26^m) in worst case$
- $SC:O(n*m)$
- m表示被加入Trie中的字符串的平均长度，n为被加入Trie中的字符串的个数。
- 本题就是实现一个Trie，不过要注意对通配符的处理，在遇到通配符的情况下，我们要对当前TrieNode的所有children进行递归搜索（backtracking）。

```java
class WordDictionary {
    
    class TrieNode {
        public TrieNode[] children = new TrieNode[26];
        public boolean isWord = false;
    }
    
    private TrieNode root;

    /** Initialize your data structure here. */
    public WordDictionary() {
        this.root = new TrieNode();
    }
    
    public void addWord(String word) {
        TrieNode node = root;
        for (int i = 0; i < word.length(); i++) {
            int idx = word.charAt(i) - 'a';
            if (node.children[idx] == null) node.children[idx] = new TrieNode();
            node = node.children[idx];
        }
        node.isWord = true;
    }
    
    public boolean search(String word) {
        return search(root, word, 0);
    }
    
    private boolean search(TrieNode node, String word, int idx) {
        if (node == null) return false;
        if (idx == word.length()) return node.isWord;
        
        char c = word.charAt(idx);
        if (c != '.') {
            return search(node.children[c - 'a'], word, idx + 1);
        } else {
            for (int i = 0; i < 26; i++) {
                if (search(node.children[i], word, idx + 1)) return true;
            }
        }
        return false;
    }
}
```