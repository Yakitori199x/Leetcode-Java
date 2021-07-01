# 355. Design Twitter (2021.05.03)

https://leetcode.com/problems/design-twitter/

```java
/**
 * Your Twitter object will be instantiated and called as such:
 * Twitter obj = new Twitter();
 * obj.postTweet(userId,tweetId);
 * List<Integer> param_2 = obj.getNewsFeed(userId);
 * obj.follow(followerId,followeeId);
 * obj.unfollow(followerId,followeeId);
 */
```

## Solution 1: HashMap + LinkedList + Priority Queue

- $TC:postTweet - O(1), getNewsFeed - O(mlogm), follow - O(1), unfollow - O(1)$
- $SC:O(m^2 + m*n)$
- m表示用户数量，n表示每个用户平均发出的推特数量。SC为每个用户均follow了其他m-1个用户，且平均发出了n条推特的情况。

```java
class Twitter {
    // 获取newsFeed的时候需要按照时间来排序，因此需要一个timestamp来标记推特发出的时间
    private static int timestamp = 0;
    // 用于快速记录和查找用户
    private Map<Integer, User> users;
    
    class Tweet {
        public int id;
        public int time;
        // 该实现将用户发送的Tweet组织成一个链表，且每个Tweet instance为其中的一个结点
        public Tweet next;
        
        public Tweet(int id) {
            this.id = id;
            this.time = timestamp++;
            this.next = null;
        }
    }
    
    class User {
        public int id;
        // 防止重复follow同一个人
        public Set<Integer> follows;
        // 用链表来存储User发送的tweet，在getNewsFeed中能很好地应用于pq
        public Tweet tweets;
        
        public User(int id) {
            this.id = id;
            this.follows = new HashSet<>();
            this.tweets = null;
            // newsFeed包含用户自己发的推，所以默认用户follow了自己且不能被unfollow
            follow(id);
        }
        
        public void follow(int followeeId) {
            follows.add(followeeId);
        }
        
        public void unfollow(int followeeId) {
            follows.remove(followeeId);
        }
        
        public void post(int tweetId) {
            // 新发出的推特会被加入链表的头部，确保了tweets本身是按照时间顺序排列的
            Tweet t = new Tweet(tweetId);
            t.next = tweets;
            tweets = t;
        }
    }
    
    /** Initialize your data structure here. */
    public Twitter() {
        this.users = new HashMap<>();
    }
    
    /** Compose a new tweet. */
    public void postTweet(int userId, int tweetId) {
        if (!users.containsKey(userId)) users.put(userId, new User(userId));
        users.get(userId).post(tweetId);
    }
    
    /** Retrieve the 10 most recent tweet ids in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user herself. Tweets must be ordered from most recent to least recent. */
    public List<Integer> getNewsFeed(int userId) {
        List<Integer> newsFeed = new LinkedList<>();
        if (!users.containsKey(userId)) return newsFeed;
        
        // 维护一个最大堆优先队列，按照推特的发送时间排序
        Queue<Tweet> pq = new PriorityQueue<>((a, b) -> b.time - a.time);
        // 将当前user的所有followee的最新的推特加入pq
        for (Integer user : users.get(userId).follows) {
            Tweet tweet = users.get(user).tweets;
            // 如果该用户没有发过推特，则跳过
            if (tweet != null) pq.offer(tweet);
        }
        
        // 因为每个followee的推特肯定是按时间排好序的，所以保证了每次poll出来的tweet必然是当前剩余推特中最新的
        // 也使得我们不需要将所有的推特，可能是m*n条，全部加入到pq（最小堆）
        for (int i = 0; i < 10 && !pq.isEmpty(); i++) {
            Tweet tweet = pq.poll();
            newsFeed.add(tweet.id);
            // 后续还有推特，则加入pq
            if (tweet.next != null) pq.offer(tweet.next);
        }
        return newsFeed;
    }
    
    /** Follower follows a followee. If the operation is invalid, it should be a no-op. */
    public void follow(int followerId, int followeeId) {
        // 因为在getNewsFeed中要获取所有followee发出的推特，所以要确保followee也被创建并记录在users里
        if (!users.containsKey(followerId)) users.put(followerId, new User(followerId));
        if (!users.containsKey(followeeId)) users.put(followeeId, new User(followeeId));
        users.get(followerId).follow(followeeId);
    }
    
    /** Follower unfollows a followee. If the operation is invalid, it should be a no-op. */
    public void unfollow(int followerId, int followeeId) {
        // 当follower不存在或是followerId和followee是同一个人时，操作失效
        if (!users.containsKey(followerId) || followerId == followeeId) return;
        users.get(followerId).unfollow(followeeId);
    }
}
```