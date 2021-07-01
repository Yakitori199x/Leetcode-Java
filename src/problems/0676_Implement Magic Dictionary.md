# 676. Implement Magic Dictionary (2021.04.23)

https://leetcode.com/problems/implement-magic-dictionary/

```java
/**
 * Your MagicDictionary object will be instantiated and called as such:
 * MagicDictionary obj = new MagicDictionary();
 * obj.buildDict(dictionary);
 * boolean param_2 = obj.search(searchWord);
 */
```

## Solution 1: Use length as Key

- $TC:O(n*k)$
- $SC:O(n*k)$
- n表示dictionary中的单词数，k表示所有单词的平均长度。对于时间复杂度，MagicDictionary()为O(1)，buildDict()为O(n)，search()为O(n*k)。
- 思路为将dictionary中的单词以单词的长度为key保存到map中，在search的时候先判断被搜索的单词长度是否存在map中。
- 若不存在，说明dictionary中不可能有符合条件的单词。若存在，则将searchWord与map中对应长度的单词进行比较，看是否只存在一处不同。

```java
class MagicDictionary {

    private Map<Integer, Set<String>> map;
    
    /** Initialize your data structure here. */
    public MagicDictionary() {
        this.map = new HashMap<>();
    }
    
    public void buildDict(String[] dictionary) {
        for (String word : dictionary) {
            int len = word.length();
            if (!map.containsKey(len)) map.put(len, new HashSet<>());
            map.get(len).add(word);
        }
    }
    
    public boolean search(String searchWord) {
        int len = searchWord.length();
        if (!map.containsKey(len)) return false;

        for (String word : map.get(len)) {
            int count = 0;
            for (int i = 0; i < len; i++) {
                if (word.charAt(i) != searchWord.charAt(i)) count++;
            }
            
            if (count == 1) return true;
        }
        return false;
    }
}
```

## Solution 2: Count value as Key

- $TC:O(n*k^2)$
- $SC:O(n*k^2)$
- n表示dictionary中的单词数，k表示所有单词的平均长度。对于时间复杂度，MagicDictionary()为O(1)，buildDict()为O(n*k^2)，search()为O(k^2)。
- 对于空间复杂度，每一个长度为k的单词可以衍生出k个相同长度的单词作为key，如果所有的单词衍生出的均不相同，那map消耗的空间就是O(n*k^2)。
- 该解法使用了单字符的掩码来为每一个单词派生出所有的改动一个字符后的模式，以apple为例，其结果为*pple, a*ple, ap*le, app*e, appl*。
- 接下来我们就以每一个衍生出的模式为key，记录下每一种模式出现的次数，该次数就说明了dictionary中有多少个单词在修改一位字符后可以对应到该模式。
- 在search的时候，我们同样地去求出searchWord对应衍生的不同模式，并遍历每一种模式，检查map中是否包含该模式。
- 若map中不存在该模式，说明dictionary中的所有单词不可能只修改一个字符转换为searchWord。
- 若map中存在该模式且对应次数大于1，说明存在两个以上单词能够转换成searchWord，返回true。
- 若map中存在该模式且对应次数等于1，我们就需要判断searchWord是不是dictionary中的单词，若不是则返回true，否则返回false，该过程通过一个set来记录和判断。

```java
class MagicDictionary {

    private Map<String, Integer> map;
    private Set<String> set;
    
    /** Initialize your data structure here. */
    public MagicDictionary() {
        this.map = new HashMap<>();
        this.set = new HashSet<>();
    }
    
    public void buildDict(String[] dictionary) {
        for (String word : dictionary) {
            set.add(word);
            for (String mask : maskOneCharacter(word)) {
                map.put(mask, map.getOrDefault(mask, 0) + 1);
            }
        }
    }
    
    public boolean search(String searchWord) {
        for (String mask : maskOneCharacter(searchWord)) {
            if (map.containsKey(mask)) {
                if (map.get(mask) > 1) return true;
                if (map.get(mask) == 1 && !set.contains(searchWord)) return true;
            }
        }
        return false;
    }
    
    private List<String> maskOneCharacter(String word) {
        List<String> masks = new ArrayList<>();
        for (int i = 0; i < word.length(); i++) {
            masks.add(word.substring(0, i) + "*" + word.substring(i + 1));
        }
        return masks;
    }
}
```

## Solution 3: Trie

- $TC:O(n*k)$
- $SC:O(n*k)$
- n表示dictionary中的单词数，k表示所有单词的平均长度。对于时间复杂度，MagicDictionary()为O(1)，buildDict()为O(n*k)，search()为O(k^2)。

```java
class MagicDictionary {
    
    class TrieNode {
        public TrieNode[] children = new TrieNode[26];
        public boolean isWord;
    }
    
    private TrieNode root;

    /** Initialize your data structure here. */
    public MagicDictionary() {
        this.root = new TrieNode();
    }
    
    public void buildDict(String[] dictionary) {
        for (String word : dictionary) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null) node.children[c - 'a'] = new TrieNode();
                node = node.children[c - 'a'];
            }
            node.isWord = true;
        }
    }
    
    public boolean search(String searchWord) {
        char[] chs = searchWord.toCharArray();
        TrieNode node = root;
        for (int i = 0; i < chs.length; i++) {
            for (char c = 'a'; c <= 'z'; c++) {
                // 当替换的字符与当前字符相等，或是替换的字符在Trie中对应位置不存在，则直接跳过该字符
                if (chs[i] == c || node.children[c - 'a'] == null) continue;
                char temp = chs[i];
                chs[i] = c;
                if (isWordInTrie(new String(chs), node, i)) return true;
                chs[i] = temp;
            }
            // 经过上面的循环，已经可以确定searchWord的第i位字符不管怎么变换都不可能使其对应到Trie中的word
            // 因此，相比于直接查找并替换下一位的字符，我们可以先来看看第i位的字符在Trie的对应位置是否存在
            // 如果存在，那说明还有继续搜索下去的可能性，但我们可以排除第i位u，用第i+1作为起始进行搜索
            // 如果不存在，那说明不论怎样都不可能使得searchWord在仅修改一个字符的条件下对应到Trie中的word
            if (node.children[chs[i] - 'a'] == null) return false;
            // 以searchWord的下一位作为起始时，Trie的起始位置也要更新到下一位置
            node = node.children[chs[i] - 'a'];
        }
        return false;
    }
    
    private boolean isWordInTrie(String s, TrieNode root, int index) {
        TrieNode node = root;
        for (int i = index; i < s.length(); i++) {
            char c = s.charAt(i);
            if (node.children[c - 'a'] == null) return false;
            node = node.children[c - 'a'];
        }
        return node.isWord;
    }
}
```