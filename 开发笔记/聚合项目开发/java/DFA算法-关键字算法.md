## DFA算法

```java
package com.ljq.common.lib.util;

import lombok.Getter;
import lombok.Setter;
import org.springframework.data.util.Pair;
import org.springframework.util.StringUtils;

import java.util.*;

public class KeywordFinder {
    /**
     * DFA节点
     */
    private static class DFANode<T> {
        /**
         * 是否是最后的语
         */
        @Getter
        @Setter
        private boolean end;

        /**
         * 关键词
         */
        @Getter
        @Setter
        private Map.Entry<String, T> keyword;

        /**
         * 后面跟随的字节点
         */
        @Getter
        private HashMap<Character, DFANode<T>> subNodes;

        DFANode(){
            this.subNodes = new HashMap<>(20);
        }

        /**
         * 根据词进行初始化DFA树
         * @param keyword
         * @param offset
         */
        private void init(Map.Entry<String, T> keyword, int offset){
            String words = keyword.getKey();
            char keyChar = getChar(words, offset);
            DFANode<T> node = this.subNodes.get(keyChar);
            if(node == null){
                //还不存在树节点里则加入
                node = new DFANode<T>();
                this.subNodes.put(keyChar, node);
            }
            offset ++;
            if(offset < words.length()){
                //处理后面的字符
                node.init(keyword, offset);
            }else{
                //标识为最后一个字符
                node.setEnd(true);
                node.setKeyword(keyword);
            }
        }

        /**
         *
         * @param text
         * @param offset
         * @return
         */
        public Map.Entry<String, T> find(String text, int offset){
            if(this.isEnd() && isRightWordBoundary(text, offset)){
                //如果自己本身就已属关键字
                return this.getKeyword();
            }
            HashMap<Character, DFANode<T>> subNodes = this.subNodes;
            for(int i=offset; i<text.length() && !subNodes.isEmpty(); i++){
                char keyChar = getChar(text, i);
                DFANode<T> current = subNodes.get(keyChar);

                //找不到则退出
                if (current == null) break;

                if(current.isEnd() && isRightWordBoundary(text, i)){
                    //有结束标记且属于字符边界则表示已找到一个关键词
                    return current.getKeyword();
                }
                //继续查找
                subNodes = current.subNodes;
            }
            return null;
        }
    }

    /**
     *
     * @param text
     * @param offset
     * @return
     */
    private static char getChar(String text, int offset){
        char c = text.charAt(offset);
        //统一转为小写，以便不区分大小写
        return Character.toLowerCase(c);
    }

    /**
     * 判断某个位置是否属于左字符边界
     * @param text
     * @param offset
     * @return
     */
    private static boolean isLeftWordBoundary(String text, int offset){
        int p = offset - 1;
        if(p < 0) return true;
        char c = text.charAt(p);
        char key = text.charAt(offset);
        if((Character.isLowerCase(key) || Character.isUpperCase(key) || Character.isDigit(key))){
            //当前字符为字母、数字则左边字符不允许为字母、数字
            return !(Character.isLowerCase(c) || Character.isUpperCase(c) || Character.isDigit(c));
        }else{
            return true;
        }
    }

    /**
     * 判断某个位置是否属于右字符边界
     * @param text
     * @param offset
     * @return
     */
    private static boolean isRightWordBoundary(String text, int offset){
        int p = offset + 1;
        if(p >= text.length()) return true;
        char c = text.charAt(p);
        //右边字符不允许为字母、数字
        return !(Character.isLowerCase(c) || Character.isUpperCase(c) || Character.isDigit(c));
    }
    /**
     * 生成DFA结点树
     * @param keywords
     * @return
     */
    private static <T> HashMap<Character, DFANode<T>> buildDFANodes(Map<String, T> keywords){
        HashMap<Character, DFANode<T>> nodes = new HashMap<>();
        for(Map.Entry<String, T> keyword: keywords.entrySet()){
            String text = keyword.getKey();
            if(StringUtils.isEmpty(text)) continue;

            //拿第一个字符
            char keyChar = getChar(text, 0);
            DFANode<T> node = nodes.get(keyChar);
            if(node == null){
                //还不存在树节点里则加入
                node = new DFANode<T>();
                nodes.put(keyChar, node);
            }
            if(text.length() > 1){
                //处理后面的字符
                node.init(keyword, 1);
            }else{
                //标识为最后一个字符
                node.setEnd(true);
                node.setKeyword(keyword);
            }
        }
        return nodes;
    }

    /**
     *
     * @param content
     * @param offset
     * @return
     */
    private static int skipSpace(String content, int offset){
        while (offset < content.length() && content.charAt(offset) == ' '){
            offset ++;
        }
        return offset;
    }

    /**
     * 读取数量 "+1"
     * @param content
     * @param offset
     * @return
     */
    static Pair<Integer, Integer> readQuantity(String content, int offset){
        offset = skipSpace(content, offset);
        if(offset < content.length() && content.charAt(offset) == '+'){
            offset = skipSpace(content, offset + 1);
            int index = offset;
            while (offset < content.length() && Character.isDigit(content.charAt(offset))){
                offset ++;
            }
            if(index != offset){
                return Pair.of(offset, Integer.valueOf(content.substring(index, offset)));
            }
        }
        return null;
    }
    /**
     * 找所有关键字
     * @param keywords 关键字表(key = 关键字, value = 对象)
     * @param content  需要查找的内容文本
     * @return 找到的关键字列表(key = 关键字对象, value = 关键字跟随的数量）
     */
    public static <T> Optional<List<Pair<Map.Entry<String, T>, Integer>>> findAll(Map<String, T> keywords, String content){
        if(StringUtils.isEmpty(content) || keywords == null || keywords.isEmpty()) return Optional.empty();

        HashMap<Character, DFANode<T>> dfaNodes = buildDFANodes(keywords);
        if(dfaNodes.isEmpty()) return Optional.empty();

        List<Pair<Map.Entry<String, T>, Integer>> results = null;
        int offset = 0;
        while (offset < content.length()){
            char keyChar = getChar(content, offset);
            DFANode<T> node = dfaNodes.get(keyChar);
            //命中关键词的第一个字符且属于字符左边界
            if(node != null && isLeftWordBoundary(content, offset)){
                //继续找出关键词
                Map.Entry<String, T> keyword = node.find(content, offset + 1);
                if(keyword != null){
                    //找到了关键字，则处理跟随数量
                    offset += keyword.getKey().length();
                    Pair<Integer, Integer> quantity = readQuantity(content, offset);
                    if(results == null) results = new ArrayList<>();
                    if(quantity != null){
                        results.add(Pair.of(keyword, quantity.getSecond()));
                        //偏移位置
                        offset = quantity.getFirst();
                    }else{
                        results.add(Pair.of(keyword, 1));
                    }
                    //减1以便后面加回
                    offset --;
                }
            }
            offset ++;
        }
        return Optional.ofNullable(results);
    }
}

```
## 使用方法

```java
package com.ljq.common.lib.util;

import lombok.AllArgsConstructor;
import lombok.Data;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.data.util.Pair;

import java.util.*;


public class KeywordFinderTest {
    @Data
    @AllArgsConstructor
    public static class Product {
        private Long id;
    }
    @Test
    public void readQuantity(){
        Pair<Integer, Integer> quantity = KeywordFinder.readQuantity("+1", 0);
        assert quantity !=null;
        assert quantity.getFirst() == 2;
        assert quantity.getSecond() == 1;


        quantity = KeywordFinder.readQuantity("+", 0);
        assert quantity == null;
        quantity = KeywordFinder.readQuantity("+A", 0);
        assert quantity == null;

        quantity = KeywordFinder.readQuantity("   +   2", 0);
        assert quantity !=null;
        assert quantity.getSecond() == 2;

        quantity = KeywordFinder.readQuantity("   +100", 0);
        assert quantity !=null;
        assert quantity.getSecond() == 100;

        quantity = KeywordFinder.readQuantity("+100件", 0);
        assert quantity !=null;
        assert quantity.getSecond() == 100;

        quantity = KeywordFinder.readQuantity("A + 100件", 1);
        assert quantity !=null;
        assert quantity.getSecond() == 100;

        quantity = KeywordFinder.readQuantity("A + 100件", 1);
        assert quantity !=null;
        assert quantity.getSecond() == 100;
    }
    @Test
    public void findAll() {
        Map<String, Integer> products = new HashMap<>();
        products.put("A1", 1);
        products.put("B1", 2);
        products.put("红A", 1);
        products.put("蓝B", 2);
        products.put("A2", 3);
        products.put("B2", 4);
        Optional<List<Pair<Map.Entry<String, Integer>, Integer>>> keywords = KeywordFinder.findAll(products, "红A和A1");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getSecond() == 1;
        assert  keywords.get().get(1).getSecond() == 1;
        assert  keywords.get().get(0).getFirst().getKey().equals("红A");

        keywords = KeywordFinder.findAll(products, "A1 A2");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(1).getFirst().getKey().equals("A2");

        keywords = KeywordFinder.findAll(products, "A1A2");
        assert  !keywords.isPresent();

        keywords = KeywordFinder.findAll(products, "A A1 A2");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(1).getFirst().getKey().equals("A2");

        keywords = KeywordFinder.findAll(products, "A1 B1");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(1).getFirst().getKey().equals("B1");

        keywords = KeywordFinder.findAll(products, "红A蓝B");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("红A");
        assert  keywords.get().get(1).getFirst().getKey().equals("蓝B");

        //
        keywords = KeywordFinder.findAll(products, "我要A1 B1");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");

        //AA1字母相连，不命中
        keywords = KeywordFinder.findAll(products, "我要AA1和A1");
        assert  keywords.isPresent() && keywords.get().size() == 1;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");

        keywords = KeywordFinder.findAll(products, "A1+2");
        assert  keywords.isPresent() && keywords.get().size() == 1;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(0).getSecond().equals(2);

        keywords = KeywordFinder.findAll(products, "A1+2红A");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(0).getSecond().equals(2);
        assert  keywords.get().get(1).getFirst().getKey().equals("红A");

        keywords = KeywordFinder.findAll(products, "A1+2 B1+10");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("A1");
        assert  keywords.get().get(0).getSecond().equals(2);
        assert  keywords.get().get(1).getFirst().getKey().equals("B1");
        assert  keywords.get().get(1).getSecond().equals(10);

        keywords = KeywordFinder.findAll(products, "红A+");
        assert  keywords.isPresent() && keywords.get().size() == 1;
        assert  keywords.get().get(0).getFirst().getKey().equals("红A");
        assert  keywords.get().get(0).getSecond() == 1;

        keywords = KeywordFinder.findAll(products, "红A+A1");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("红A");
        assert  keywords.get().get(0).getSecond() == 1;
        assert  keywords.get().get(1).getFirst().getKey().equals("A1");
        assert  keywords.get().get(1).getSecond() == 1;

        keywords = KeywordFinder.findAll(products, "红A+A1+2");
        assert  keywords.isPresent() && keywords.get().size() == 2;
        assert  keywords.get().get(0).getFirst().getKey().equals("红A");
        assert  keywords.get().get(0).getSecond() == 1;
        assert  keywords.get().get(1).getFirst().getKey().equals("A1");
        assert  keywords.get().get(1).getSecond() == 2;
    }
}

```
