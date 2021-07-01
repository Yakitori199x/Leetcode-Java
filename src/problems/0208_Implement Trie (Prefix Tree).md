# 208. Implement Trie (Prefix Tree) (2021.04.26)

https://leetcode.com/problems/implement-trie-prefix-tree/

```java
/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

## Solution 1

- $TC:Trie() O(1), insert(word) O(m), search(word) O(m), startsWith(prefix) O(m)$
- $SC:Trie() O(1), insert(word) O(m) (如果被插入的word完全没有前缀的重合), search(word) O(1), startsWith(prefix) O(1)$
- 关于前缀树相比于HashMap以及平衡搜索树的优点，可以参考：https://leetcode.com/problems/implement-trie-prefix-tree/solution/

```java
public class TrieNode {
    public TrieNode[] children;
    public boolean isWord;
    
    public TrieNode() {
        this.children = new TrieNode[26];
        this.isWord = false;
    }
}

class Trie {
    private TrieNode root;
    
    /** Initialize your data structure here. */
    public Trie() {
        this.root = new TrieNode();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) {
            if (cur.children[c - 'a'] == null) {
                cur.children[c - 'a'] = new TrieNode();
            }
            cur = cur.children[c - 'a'];
        }
        cur.isWord = true;
    }

    // search和startsWith的唯一区别就在于遍历完待搜索的word之后，是否要检查word在当前的Trie中是否是一个已被记录的word（isWord）
    // 因此，两个method的实现有很大一部分是重合的，我们也可以把这部分共通的逻辑抽取出来，额外写一个method
    // 比如public TrieNode search(String word) -> 返回null则说明word还没遍历完就确定不存在于Trie，否则说明word是Trie中的word或某些word的前缀
    // 之后就可以在search中判断返回的TrieNode是否满足isWord
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        TrieNode cur = root;
        for (char c : word.toCharArray()) {
            if (cur.children[c- 'a'] == null) return false;
            cur = cur.children[c - 'a'];
        }
        return cur.isWord == true;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        TrieNode cur = root;
        for (char c : prefix.toCharArray()) {
            if (cur.children[c- 'a'] == null) return false;
            cur = cur.children[c - 'a'];
        }
        return true;
    }
}
```