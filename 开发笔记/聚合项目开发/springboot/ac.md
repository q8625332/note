## 字符串匹配

> 当我们业务上需要字符串匹配的时候，使用正则的效率就会比较差了
> 所以可以使用自动机来匹配
> 场景有使用脏词配匹配，或者敏感词匹配

## 导包

```xml
<dependency>
    <groupId>org.ahocorasick</groupId>
    <artifactId>ahocorasick</artifactId>
    <version>0.3.1</version>
</dependency>
```

## 使用

```java

//例子
Trie trie = Trie.builder().build();

Trie trie = 
list<String> strList = new ArrayList<>();
Trie.TrieBuilder tb = Trie.builder();
//关键字全匹配
tb.onlyWholeWords();
//忽略大小写
tb.ignoreCase();
for(String s : strList){  
    tb.addKeyword(s);  
}  
trie = tb.build();


//
```