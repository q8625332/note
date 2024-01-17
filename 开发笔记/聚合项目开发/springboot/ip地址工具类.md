## java ip地址工具类

```java
package com.ljq.common.lib.util;

import org.springframework.util.StringUtils;

import javax.servlet.http.HttpServletRequest;
import java.util.regex.Pattern;

/**
 * ip地址实用类
 */
public abstract class IPUtils {

    private static Pattern ipV6Pattern;
    private static Pattern ipV4Pattern;

    static {
        // ipv6
        ipV6Pattern = Pattern.compile("^((([0-9A-Fa-f]{1,4}:){7}[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){1,7}:)|(([0-9A-Fa-f]{1,4}:){6}:[0-9A-Fa-f]{1,4})|(([0-9A-Fa-f]{1,4}:){5}(:[0-9A-Fa-f]{1,4}){1,2})|(([0-9A-Fa-f]{1,4}:){4}(:[0-9A-Fa-f]{1,4}){1,3})|(([0-9A-Fa-f]{1,4}:){3}(:[0-9A-Fa-f]{1,4}){1,4})|(([0-9A-Fa-f]{1,4}:){2}(:[0-9A-Fa-f]{1,4}){1,5})|([0-9A-Fa-f]{1,4}:(:[0-9A-Fa-f]{1,4}){1,6})|(:(:[0-9A-Fa-f]{1,4}){1,7})|(([0-9A-Fa-f]{1,4}:){6}(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|(([0-9A-Fa-f]{1,4}:){5}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|(([0-9A-Fa-f]{1,4}:){4}(:[0-9A-Fa-f]{1,4}){0,1}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|(([0-9A-Fa-f]{1,4}:){3}(:[0-9A-Fa-f]{1,4}){0,2}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|(([0-9A-Fa-f]{1,4}:){2}(:[0-9A-Fa-f]{1,4}){0,3}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|([0-9A-Fa-f]{1,4}:(:[0-9A-Fa-f]{1,4}){0,4}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3})|(:(:[0-9A-Fa-f]{1,4}){0,5}:(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3}))$");
        // ipv4
        ipV4Pattern = Pattern.compile("^(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])(\\.(\\d|[1-9]\\d|1\\d{2}|2[0-4]\\d|25[0-5])){3}$");
    }

    /**
     * 获取远程客户端ip地址
     *
     * @param request
     * @return
     */
    public static String getRemoteAddress(HttpServletRequest request) {
        //注意：以下阿里云及Cloudflare的请求头获取方式有可能会被人为伪造
        //如果在一些对IP要求严格的场合，还需要同时验证X-Real-IP请求头，验证IP是否归属于CDN的IP
        String realIp = request.getHeader("Ali-CDN-Real-IP");
        if (isValidIP(realIp) &&
                !StringUtils.isEmpty(request.getHeader("Ali-CDN-Real-Port"))) {
            //阿里云线路
            return realIp;
        }

        realIp = request.getHeader("CF-Connecting-IP");
        if (isValidIP(realIp) &&
                !StringUtils.isEmpty(request.getHeader("CF-RAY"))) {
            //Cloudflare线路
            return realIp;
        }

        realIp = request.getHeader("X-Real-IP");
        if (!isValidIP(realIp)) realIp = "";
        String forwarded = request.getHeader("X-Forwarded-For");
        if (!StringUtils.isEmpty(forwarded)) {
            //注意：此方式获取的IP地址，有可能会被伪造，因为X-Forwarded-For是不可信的数据
            String[] ips = forwarded.split(",");
            String ip1 = ips[0].trim();
            if (isValidIP(ip1)) {
                if (!StringUtils.isEmpty(realIp)) {
                    if (ips.length > 1) {
                        String ip2 = ips[ips.length - 2].trim();
                        //如果倒数第二级代理ip是当前客户端，则真实ip取第一级代理ip
                        // X-Real-IP = 172.69.35.11
                        // X-Forwarded-For = 58.62.167.249, 172.69.35.11,172.31.31.53
                        if (ip2.equals(realIp)) realIp = ip1;
                    }
                } else {
                    realIp = ip1;
                }
            }
        }
        if (!StringUtils.isEmpty(realIp)) {
            return realIp;
        }
        return request.getRemoteAddr();
    }

    /**
     * 是否有效的IP
     *
     * @param ip
     * @return
     */
    public static boolean isValidIP(String ip) {
        return !StringUtils.isEmpty(ip) && (isIPv4(ip) || isIPv6(ip));
    }

    /**
     * 是否是有效的IP v4格式ip
     *
     * @param ip
     * @return
     */
    public static boolean isIPv4(String ip) {
        return !StringUtils.isEmpty(ip) && ipV4Pattern.matcher(ip).matches();
    }

    /**
     * 是否是有效的IP v6格式ip
     *
     * @param ip
     * @return
     */
    public static boolean isIPv6(String ip) {
        return !StringUtils.isEmpty(ip) && ipV6Pattern.matcher(ip).matches();
    }
}

```
