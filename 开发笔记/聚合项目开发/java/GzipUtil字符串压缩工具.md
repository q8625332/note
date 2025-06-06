> 字符串压缩
> 业务场景，字符串数据比较庞大，一般需要考虑到存储和传输的性能问题，我们都会优先考虑使用gzip的形式进行压缩


```java
package com.ljq;  
  
import org.apache.commons.codec.binary.Base64;  
import org.apache.commons.io.FileUtils;  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
  
import java.io.ByteArrayInputStream;  
import java.io.ByteArrayOutputStream;  
import java.io.File;  
import java.io.IOException;  
import java.nio.charset.StandardCharsets;  
import java.util.zip.GZIPInputStream;  
import java.util.zip.GZIPOutputStream;  
  
public class GzipUtil {  
  
    private static final Logger LOGGER = LoggerFactory.getLogger(GzipUtil.class);  
  
  
    // 压缩  
    public static String compress(String str) throws IOException {  
        if (str == null || str.length() == 0) {  
            return str;  
        }  
        ByteArrayOutputStream out = new ByteArrayOutputStream();  
        GZIPOutputStream gzip = new GZIPOutputStream(out);  
        gzip.write(str.getBytes());  
        gzip.close();  
        return out.toString("ISO-8859-1");  
    }  
  
    // 解压缩  
    public static String uncompress(String str, String charset) throws IOException {  
        if (str == null || str.length() == 0) {  
            return str;  
        }  
        ByteArrayOutputStream out = new ByteArrayOutputStream();  
        ByteArrayInputStream in = new ByteArrayInputStream(str  
                .getBytes("ISO-8859-1"));  
        GZIPInputStream gunzip = new GZIPInputStream(in);  
        byte[] buffer = new byte[256];  
        int n;  
        while ((n = gunzip.read(buffer)) >= 0) {  
            out.write(buffer, 0, n);  
        }  
        // toString()使用平台默认编码，也可以显式的指定如toString(&quot;GBK&quot;)  
        return out.toString(charset);  
    }  
  
    /**  
     * 使用gzip压缩字符串  
     *  
     * @param str 要压缩的字符串  
     * @return 压缩后的字符串  
     */  
    public static String compressV2(String str) {  
        if (str == null || str.length() <= 0) {  
            return str;  
        }  
        ByteArrayOutputStream out = new ByteArrayOutputStream();  
        try (GZIPOutputStream gzip = new GZIPOutputStream(out)) {  
            gzip.write(str.getBytes(StandardCharsets.UTF_8));  
        } catch (IOException e) {  
            LOGGER.error("字符串压缩失败str:{}，错误信息:{}", str, e.getMessage());  
            throw new RuntimeException("字符串压缩失败");  
        }  
        return Base64.encodeBase64String(out.toByteArray());  
    }  
  
    /**  
     * 使用gzip解压缩  
     *  
     * @param compressedStr 压缩字符串  
     * @return 解压后的字符串  
     */  
    public static String uncompressV2(String compressedStr) {  
        if (compressedStr == null || compressedStr.length() <= 0) {  
            return compressedStr;  
        }  
        ByteArrayOutputStream out = new ByteArrayOutputStream();  
        ByteArrayInputStream in;  
        GZIPInputStream gzip = null;  
        byte[] compressed;  
        String decompressed;  
        try {  
            compressed = Base64.decodeBase64(compressedStr);  
            in = new ByteArrayInputStream(compressed);  
            gzip = new GZIPInputStream(in);  
            byte[] buffer = new byte[1024];  
            int offset;  
            while ((offset = gzip.read(buffer)) != -1) {  
                out.write(buffer, 0, offset);  
            }  
            decompressed = out.toString(StandardCharsets.UTF_8.name());  
        } catch (IOException e) {  
            LOGGER.error("字符串解压失败compressedStr:{}，错误信息:{}", compressedStr, e.getMessage());  
            throw new RuntimeException("字符串解压失败");  
        } finally {  
            if (gzip != null) {  
                try {  
                    gzip.close();  
                } catch (IOException ignored) {  
                }            }  
            try {  
                out.close();  
            } catch (IOException ignored) {  
            }        }  
        return decompressed;  
    }  
  
    // 测试方法  
    public static void main(String[] args) throws IOException {  
  
        //测试字符串  
        String str = FileUtils.readFileToString(new File("e:/temp/test.json"), "utf-8");  
        //"%5B%7B%22lastUpdateTime%22%3A%222011-10-28+9%3A39%3A41%22%2C%22smsList%22%3A%5B%7B%22liveState%22%3A%221";  
  
        System.out.println("原长度：" + str.length());  
  
        System.out.println("压缩后：" + compress(str).length());  
  
        System.out.println("解压缩：" + uncompress(compress(str), StandardCharsets.UTF_8.name()).length());  
    }  
}

```
