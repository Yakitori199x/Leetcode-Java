# 535. Encode and Decode TinyURL (2021.04.24)

https://leetcode.com/problems/encode-and-decode-tinyurl/

```java
// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.decode(codec.encode(url));
```

## Solution 1: Encode as Integer

- encode时用一个List来维护longUrl，并且返回longUrl对应在List中的index作为shortUrl。
- decode时使用shortUrl的最后一部分index去获取对应的longUrl。
- 这种实现足够简洁，但是同一个longUrl会被重复encode多次，而且encode的结果是一个十进制数，随着url的增加会很快变长。
- 详细分析见：https://leetcode.com/problems/encode-and-decode-tinyurl/discuss/100268/Two-solutions-and-thoughts

```java
public class Codec {
    
    private List<String> urls = new ArrayList<>();
    
    // Encodes a URL to a shortened URL.
    public String encode(String longUrl) {
        this.urls.add(longUrl);
        return "http://tinyurl.com/" + (urls.size() - 1);
    }

    // Decodes a shortened URL to its original URL.
    public String decode(String shortUrl) {
        int idx = Integer.valueOf(shortUrl.substring(shortUrl.lastIndexOf('/') + 1));
        return this.urls.get(idx);
    }
}
```

## Solution 2: Random Hash

- 该解法对每一位使用了数字加英文大小写字符的组合来进行encoding，极大丰富了encoder的表达能力。
- 如此一来，只需要长度为6的key就可以表达多至(10+26*2)^6 = 56,800,235,584的Url。
- 详细分析见：https://leetcode.com/problems/encode-and-decode-tinyurl/discuss/100268/Two-solutions-and-thoughts
- 以及：https://leetcode.com/discuss/interview-question/124658/Design-a-URL-Shortener-(-TinyURL-)-System/

```java
public class Codec {
    
    private Map<String, String> index = new HashMap<>();
    private Map<String, String> reverseIdx = new HashMap<>();
    private String encodeChars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    
    // Encodes a URL to a shortened URL.
    public String encode(String longUrl) {
        while (!index.containsKey(longUrl)) {
            StringBuilder sb = new StringBuilder();
            for (int i = 0; i < 6; i++) {
                sb.append(encodeChars.charAt((int) Math.random() * encodeChars.length()));
            }
            
            String key = sb.toString();
            // 随机生成的key其实和longUrl本身没什么关系，如果产生冲突则需要重复生成
            if (!reverseIdx.containsKey(key)) {
                reverseIdx.put(key, longUrl);
                index.put(longUrl, key);
            }
        }
        
        return "http://tinyurl.com/" + index.get(longUrl);
    }

    // Decodes a shortened URL to its original URL.
    public String decode(String shortUrl) {
        String key = shortUrl.substring(shortUrl.lastIndexOf('/') + 1);
        return reverseIdx.get(key);
    }
}
```

## Solution 3: Combined

- 该解法综合了Solution1和Solution2各自的特点。
- 我们可以用DB来存储longUrl并返回其在DB中被分配到的整数Key，类似Solution1的index。
- 但我们并不直接使用这个Key作为最终encoding的Key，而是不断对其%62然后/62直至0，以获取每一位对应的char。
- 比如我们使用的是Solution2中的encodeChars，那么0->a，2->c，...，62->a，...。其实就是将整数Key转换成一个62进制的数，当然具体取决于每一位有多少个可选字符。
- 在decode的时候把shortUrl的Key转换为10进制数，然后去DB中获取对应的longUrl即可。
- 详见：https://leetcode.com/discuss/interview-question/124658/Design-a-URL-Shortener-(-TinyURL-)-System/

## Appendix
- 对于多机器分布式环境的设计和探讨，见：https://leetcode.com/discuss/interview-question/124658/Design-a-URL-Shortener-(-TinyURL-)-System/