## java环境搭建
**linux环境**

 - jdk1.8
 	

	```java
	#编写文件
	vim  /etc/profile
	
	export JAVA_HOME=/usr/local/jdk1.8.0_191
	export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:
	export PATH=$JAVA_HOME/bin:$PATH

	#使配置文件生效
	source /etc/profile
	```
	
 - jdk14
 	

	> win10  jdk14 配置已经不需要 jre 和 class_home了

	```java
	JAVA_HOME    E:\JAVA\jdk-14
	Path   %JAVA_HOME%\bin
	```
	> centos jdk14 环境变量配置
		

	```java
	#编写文件
	vim  /etc/profile
	#使配置文件生效
	source /etc/profile
	```
	
	```java
	JAVA_HOME=/usr/local/java/jdk-14
	export PATH=$JAVA_HOME/bin:$PATH
	```

## TreeSet 对一个实体类进行排序
HashSet 无序唯一，TreeSet 有序唯一

```java
HashSet<NavigationTable> navigationTableHashSet = new HashSet<>();
	....省略（导航栏代码）
TreeSet<NavigationTable> youxu = new TreeSet<>(navigationTableHashSet);

------------------------------
实体类
public class NavigationTable implements Comparable<NavigationTable> {
	.....省略实体
	
@Override           //对实体类的id进行排序
	public int compareTo(NavigationTable o) {
		int a = this.getId() - o.getId();
		return a;
	}
}
```
## Java中Double保留后小数位的几种方法
[Java中Double保留后小数位的几种方法网页地址](https://www.cnblogs.com/dichuan/p/7769098.html)
## java获取当天，前天，明天，本周，本月，本年的开始日期时间和结束日期时间
[java获取当天，前天，明天，本周，本月，本年的开始日期时间和结束日期时间](https://www.cnblogs.com/suruozhong/p/6085380.html?utm_source=itdadao&utm_medium=referral)

## Math

 1. 舍掉小数取整:Math.floor(3.5)=3
 2. 四舍五入取整:Math.rint(3.5)=4
 3. 进位取整:Math.ceil(3.1)=4 
 4. 取绝对值：Math.abs(-3.5)=3.5
 5. 取余数：A%B = 余数


## 迭代器

> .next()在一个循环了不要出现两次，不然会越界。本人已经体验过了。在此做个记录

```java
Iterator it = jsonObject.entrySet().iterator();
List<String> zgBm = new ArrayList<>();
while (it.hasNext()) {
    Map.Entry entry = (Map.Entry) it.next();
    if (entry.getValue().toString().equals("true")) {
        zgBm.add(entry.getKey().toString());
    }
}
```

## local日期常用方法

```java
getYear()    int    获取当前日期的年份
getMonth()    Month    获取当前日期的月份对象
getMonthValue()    int    获取当前日期是第几月
getDayOfWeek()    DayOfWeek    表示该对象表示的日期是星期几
getDayOfMonth()    int    表示该对象表示的日期是这个月第几天
getDayOfYear()    int    表示该对象表示的日期是今年第几天
withYear(int year)    LocalDate    修改当前对象的年份
withMonth(int month)    LocalDate    修改当前对象的月份
withDayOfMonth(int dayOfMonth)    LocalDate    修改当前对象在当月的日期
isLeapYear()    boolean    是否是闰年
lengthOfMonth()    int    这个月有多少天
lengthOfYear()    int    该对象表示的年份有多少天（365或者366）
plusYears(long yearsToAdd)    LocalDate    当前对象增加指定的年份数
plusMonths(long monthsToAdd)    LocalDate    当前对象增加指定的月份数
plusWeeks(long weeksToAdd)    LocalDate    当前对象增加指定的周数
plusDays(long daysToAdd)    LocalDate    当前对象增加指定的天数
minusYears(long yearsToSubtract)    LocalDate    当前对象减去指定的年数
minusMonths(long monthsToSubtract)    LocalDate    当前对象减去注定的月数
minusWeeks(long weeksToSubtract)    LocalDate    当前对象减去指定的周数
minusDays(long daysToSubtract)    LocalDate    当前对象减去指定的天数
compareTo(ChronoLocalDate other)    int    比较当前对象和other对象在时间上的大小，返回值如果为正，则当前对象时间较晚，
isBefore(ChronoLocalDate other)    boolean    比较当前对象日期是否在other对象日期之前
isAfter(ChronoLocalDate other)    boolean    比较当前对象日期是否在other对象日期之后
isEqual(ChronoLocalDate other)    boolean    比较两个日期对象是否相等


// 获取今天的日期
LocalDate now = LocalDate.now();
// 今天是几号
int dayofMonth = now.getDayOfMonth();
// 今天是周几（返回的是个枚举类型，需要再getValue()）
int dayofWeek = now.getDayOfWeek().getValue();
// 今年是哪一年
int dayofYear = now.getDayOfYear();
// 取本月第1天：
LocalDate firstDayOfThisMonth = now.with(TemporalAdjusters.firstDayOfMonth()); 
// 取本月第2天：
LocalDate secondDayOfThisMonth = now.withDayOfMonth(2);
// 取本月最后一天，再也不用计算是28，29，30还是31：
LocalDate lastDayOfMonth= now.with(TemporalAdjusters.lastDayOfMonth()); 
// 取下一天：
LocalDate firstDayOfNextMonth = lastDayOfMonth.plusDays(1);
// 取2019年1月第一个周一：
LocalDate firstMondayOf2017 = LocalDate.parse("2019-01-01").with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY)); 
//本月的第一天
LocalDate monthFirstDay = LocalDate.of(now.getYear(),now.getMonth(),1);
 //本月的最后一天
LocalDate monthLastDay = now.with(TemporalAdjusters.lastDayOfMonth());
//上月的第一天
LocalDate date = now.minusMonths(1).withDayOfMonth(1);
//上月的最后一天
LocalDate date1 = date.with(TemporalAdjusters.lastDayOfMonth());
```

## LocalDateTime

```java
LocalDateTime now = LocalDateTime.now();
System.out.println("计算两个时间的差：");
LocalDateTime end = LocalDateTime.now();
Duration duration = Duration.between(now,end);
long days = duration.toDays(); //相差的天数
long hours = duration.toHours();//相差的小时数
long minutes = duration.toMinutes();//相差的分钟数
long millis = duration.toMillis();//相差毫秒数
long nanos = duration.toNanos();//相差的纳秒数
System.out.println(now);
System.out.println(end);

System.out.println("发送短信耗时【 "+days+"天："+hours+" 小时："+minutes+" 分钟："+millis+" 毫秒："+nanos+" 纳秒】");


//计算两个日期之间是的差额
LocalDate localDate = LocalDate.now();
LocalDate localDate2 = localDate.minusYears(5).minusMonths(10).minusDays(2);
Period next = Period.between(localDate2,localDate);
log.info("---------------年--------------------");
log.info(String.valueOf(next.getYears()));
log.info("---------------月--------------------");
log.info(String.valueOf(next.getMonths()));
log.info("----------------天-------------------");
log.info(String.valueOf(next.getDays()));

//获取当前系统的时区
OffsetDateTime.now().getOffset();  //输出 "+08:00" 不同时区输出不同值

//获取当天开始时间的时间戳
LocalDateTime.of(LocalDate.now(),LocalTime.MIN).toInstant(OffsetDateTime.now().getOffset()).toEpochMilli();
//另外的方法
LocalDateTime min = LocalDateTime.now(ZoneOffset.UTC).with(LocalTime.MIN);

//获取当天结束时间的时间戳
LocalDateTime.of(LocalDate.now(), LocalTime.MAX).toInstant(OffsetDateTime.now().getOffset()).toEpochMilli();
//另外的方法
LocalDateTime max = LocalDateTime.now(ZoneOffset.UTC).with(LocalTime.MAX);

//时间戳转localDateTime （不需要除1000）
Instant.ofEpochMilli(d.getTradeDate()) .atZone(ZoneOffset.ofHours(8)).toLocalDateTime();
//时间戳转localDateTime （需要除1000）
LocalDateTime.ofEpochSecond(d.getTradeDate() / 1000, 0, ZoneOffset.ofHours(8));

//LocalDateTime 转 RFC3339 格式
OffsetDateTime.now(ZoneOffset.ofHours(8)).plus(wechatCustomMiniappDomainProperties.getCcVerifyPayExpireTime().toMillis(), ChronoUnit.MILLIS).toString()
// RFC3339 转 LocalDateTime 格式
OffsetDateTime.parse(wxPayUnifiedOrderV3Request.getTimeExpire()).toLocalDateTime()
```

## 例子-计算时间（可以参考）

```java
/**
     * 计算时间类型
     *
     * @param days       天
     * @param startDateL 用户开始时间
     * @param endDateL   用户结束时间
     * @return {@link List<Object>}
     */
    public static List<Object> toCalculateTypeTime(Integer days, Long startDateL, Long endDateL) {
        List<Object> res = new ArrayList<>();
        int type = 1; //1查小时 2查天
        LocalDateTime nowDateTime = LocalDateTime.now();
        //返回小时
        if ((Objects.nonNull(days) && days.equals(1))
                || (Objects.nonNull(startDateL) && Objects.nonNull(endDateL) && startDateL.equals(endDateL))) {
            LocalDateTime startDateTime = null;
            LocalDateTime endDateTime = null;
            if (Objects.nonNull(days)) {
                startDateTime = nowDateTime.of(LocalDate.now(), LocalTime.MIN).atZone(ZoneOffset.ofHours(8)).toLocalDateTime();
                endDateTime = nowDateTime.of(LocalDate.now(), LocalTime.MAX).atZone(ZoneOffset.ofHours(8)).toLocalDateTime();
            }
            if (Objects.nonNull(startDateL) && Objects.nonNull(endDateL)) {
                LocalDate startDate = LocalDateTime.ofEpochSecond(startDateL, 0, ZoneOffset.ofHours(8)).toLocalDate();
                startDateTime = LocalDateTime
                        .of(startDate,LocalTime.MIN)
                        .atZone(ZoneOffset.ofHours(8))
                        .toLocalDateTime();
                LocalDate endDate = LocalDateTime.ofEpochSecond(endDateL, 0, ZoneOffset.ofHours(8)).toLocalDate();
                endDateTime = LocalDateTime
                        .of(endDate, LocalTime.MAX)
                        .atZone(ZoneOffset.ofHours(8))
                        .toLocalDateTime();
            }
            if (Objects.isNull(startDateTime) || Objects.isNull(endDateTime)) {
                return null;
            }
            res.add(type);
            res.add(startDateTime);
            res.add(endDateTime);
            return res;
        }
        //返回天
        type = 2;
        LocalDate startDate = null;
        LocalDate endDate = null;
        if (Objects.nonNull(days) && (Objects.isNull(startDateL) || Objects.isNull(endDateL))) {
            LocalDateTime minusDays = nowDateTime.minusDays(days);
            startDate = minusDays.atZone(ZoneOffset.ofHours(8)).toLocalDate();
            endDate = nowDateTime.atZone(ZoneOffset.ofHours(8)).toLocalDate();
            res.add(type);
            res.add(startDate);
            res.add(endDate);
            return res;
        } else if (Objects.isNull(days) && Objects.nonNull(startDateL) || Objects.nonNull(endDateL)) {
            startDate = LocalDateTime.ofEpochSecond(startDateL, 0, ZoneOffset.ofHours(8)).toLocalDate();
            endDate = LocalDateTime.ofEpochSecond(endDateL, 0, ZoneOffset.ofHours(8)).toLocalDate();
            res.add(type);
            res.add(startDate);
            res.add(endDate);
            return res;
        }
        type = 0;
        res.add(type);
        return res;
    }
```

## 校验身份证

```java
//校验身份证
    final static Map<Integer, String> zoneNum = new HashMap<>();
    final static int[] PARITYBIT = {'1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2'};
    final static int[] POWER_LIST = {7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10,
            5, 8, 4, 2};
    static {
        zoneNum.put(11, "北京");
        zoneNum.put(12, "天津");
        zoneNum.put(13, "河北");
        zoneNum.put(14, "山西");
        zoneNum.put(15, "内蒙古");
        zoneNum.put(21, "辽宁");
        zoneNum.put(22, "吉林");
        zoneNum.put(23, "黑龙江");
        zoneNum.put(31, "上海");
        zoneNum.put(32, "江苏");
        zoneNum.put(33, "浙江");
        zoneNum.put(34, "安徽");
        zoneNum.put(35, "福建");
        zoneNum.put(36, "江西");
        zoneNum.put(37, "山东");
        zoneNum.put(41, "河南");
        zoneNum.put(42, "湖北");
        zoneNum.put(43, "湖南");
        zoneNum.put(44, "广东");
        zoneNum.put(45, "广西");
        zoneNum.put(46, "海南");
        zoneNum.put(50, "重庆");
        zoneNum.put(51, "四川");
        zoneNum.put(52, "贵州");
        zoneNum.put(53, "云南");
        zoneNum.put(54, "西藏");
        zoneNum.put(61, "陕西");
        zoneNum.put(62, "甘肃");
        zoneNum.put(63, "青海");
        zoneNum.put(64, "新疆");
        zoneNum.put(71, "台湾");
        zoneNum.put(81, "香港");
        zoneNum.put(82, "澳门");
        zoneNum.put(91, "外国");
    }
    /**
     * @Description: 校验身份证是否合法
     * @Param: [certNo]
     * @return: boolean
     * @Author: 刘俊秦
     * @Date: 2019/6/3 0003
     */
    public static boolean isIDCard(String certNo) {
        if (certNo == null || (certNo.length() != 15 && certNo.length() != 18))
            return false;
        final char[] cs = certNo.toUpperCase().toCharArray();
        //校验位数
        int power = 0;
        for (int i = 0; i < cs.length; i++) {
            if (i == cs.length - 1 && cs[i] == 'X')
                break;//最后一位可以 是X或x
            if (cs[i] < '0' || cs[i] > '9')
                return false;
            if (i < cs.length - 1) {
                power += (cs[i] - '0') * POWER_LIST[i];
            }
        }

        //校验区位码
        if (!zoneNum.containsKey(Integer.valueOf(certNo.substring(0, 2)))) {
            return false;
        }

        //校验年份
        String year = certNo.length() == 15 ? getIdcardCalendar() + certNo.substring(6, 8) : certNo.substring(6, 10);

        final int iyear = Integer.parseInt(year);
        if (iyear < 1900 || iyear > Calendar.getInstance().get(Calendar.YEAR))
            return false;//1900年的PASS，超过今年的PASS

        //校验月份
        String month = certNo.length() == 15 ? certNo.substring(8, 10) : certNo.substring(10, 12);
        final int imonth = Integer.parseInt(month);
        if (imonth < 1 || imonth > 12) {
            return false;
        }

        //校验天数
        String day = certNo.length() == 15 ? certNo.substring(10, 12) : certNo.substring(12, 14);
        final int iday = Integer.parseInt(day);
        if (iday < 1 || iday > 31)
            return false;

        //校验"校验码"
        if (certNo.length() == 15)
            return true;
        return cs[cs.length - 1] == PARITYBIT[power % 11];
    }

    private static int getIdcardCalendar() {
        GregorianCalendar curDay = new GregorianCalendar();
        int curYear = curDay.get(Calendar.YEAR);
        int year2bit = Integer.parseInt(String.valueOf(curYear).substring(2));
        return year2bit;
    }
```
## 根据身份证号计算年龄

```java
 /**
     * @Description: 根据身份证号计算年龄
     * @Param: [pensonnelIdCard]
     * @return: java.lang.Integer
     * @Author: 刘俊秦
     * @Date: 2019/6/3 0003
     */
    public static Integer getPersonAgeFromIdCard(String pensonnelIdCard) {

        //截取身份证中出行人出生日期中的年、月、日
        Integer personYear = Integer.parseInt(pensonnelIdCard.substring(6, 10));
        Integer personMonth = Integer.parseInt(pensonnelIdCard.substring(10, 12));
        Integer personDay = Integer.parseInt(pensonnelIdCard.substring(12, 14));

        Calendar cal = Calendar.getInstance();
        // 得到当前时间的年、月、日
        Integer yearNow = cal.get(Calendar.YEAR);
        Integer monthNow = cal.get(Calendar.MONTH) + 1;
        Integer dayNow = cal.get(Calendar.DATE);

        // 用当前年月日减去生日年月日
        Integer yearMinus = yearNow - personYear;
        Integer monthMinus = monthNow - personMonth;
        Integer dayMinus = dayNow - personDay;

        Integer age = yearMinus; //先大致赋值

        if (yearMinus == 0) { //出生年份为当前年份 
            age = 0;
        } else { //出生年份大于当前年份
            if (monthMinus < 0) {//出生月份小于当前月份时，还没满周岁
                age = age - 1;
            }
            if (monthMinus == 0) {//当前月份为出生月份时，判断日期
                if (dayMinus < 0) {//出生日期小于当前月份时，没满周岁
                    age = age - 1;
                }
            }
        }
        return age;
    }
```
## 枚举enum的使用（与在switch中的使用）
**看看枚举类**

```java
/**
 * 操作码类
 * @author kokJuis
 * @version 1.0
 * @date 2017-3-6
 */
public enum Code {
 
    SUCCESS(10000, "操作成功"), 
    FAIL(10001, "操作失败"), 
 
 
    private int code;
    private String msg;
 
    //为了更好的返回代号和说明，必须呀重写构造方法
    private Code(int code, String msg) {
        this.code = code;
        this.msg = msg;
    }
 
    public int getCode() {
        return code;
    }
 
    public void setCode(int code) {
        this.code = code;
    }
 
    public String getMsg() {
        return msg;
    }
 
    public void setMsg(String msg) {
        this.msg = msg;
    }
 
    
    // 根据value返回枚举类型,主要在switch中使用
    public static Code getByValue(int value) {
        for (Code code : values()) {
            if (code.getCode() == value) {
                return code;
            }
        }
        return null;
    }
    
}
```
**使用：**

```java
//获取代码
int code=Code.SUCCESS.getCode();
//获取代码对应的信息
String msg=Code.SUCCESS.getMsg();
 
//在switch中使用通常需要先获取枚举类型才判断，因为case中是常量或者int、byte、short、char，写其他代码编译是不通过的
 
int code=Code.SUCCESS.getCode();
 
switch (Code.getByValue(code)) {
 
    case SUCCESS：
        //......
    break;
 
    case FAIL：
        //......
    break;
 
}
```

## 数组和集合中行列互换的解法
**分析过程**

 - 主对角线是保持不变
 - 行列互换即角标互换：[0][1] => [1][0]
 - 循环次数：外层循环行，内层循环每一行的列

**示意图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200602175001220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)

```java

//数组的情况
int arry[][] = new int[][] {{1,2,3},{4,5,6},{7,8,9}};
//这里arry为什么要减一呢，因为对角线是不用换的，所有去掉最后一次循环
for(int i=0; i< arry.length-1; i++) {
			//列循环从：i+1开始，提高循环效率
	for(int j=i+1; j< arry[i].length; j++) {
			int temp = arry[i][j];
			arry[i][j] = arry[j][i];
			arry[j][i] = temp;
	}
}

//集合的情况（具体数值就比列举了，和数值差不多）
for (var i = 0; i < arr.size() - 1; i++) {
    for (var j = i + 1; j < arr.get(i).size(); j++) {
            int temp = arr.get(i).get(j);
            arr.get(i).set(j, arr.get(j).get(i));
            arr.get(j).set(i, temp);
     }
 }

```
## java 锁
[java 锁](https://www.cnblogs.com/jyroy/p/11365935.html)


## java多线程

 - 带返回值的多线程调用，例子：
 	
	```java
		//线程组
        List<Callable<String>> callables = new ArrayList<>();

        for (int i = 0; i < 10; i++) {
            Callable<String> myCallable = new Callable<String>() {
                @Override
                public String call() throws Exception {
                    int successCount = 0;
                    int errorCount = 0;
                    for (int j = 0; j < 30; j++) {
                    	//本人调用百度的方法（可以忽略，使用自己的逻辑）
                        BdSearchResVo search = BaiduApiUtil.search(image, imageType, groupIdList, null);
                        if (search.getError_code().equals(0)) {
                            successCount++;
                        } else {
                            errorCount++;
                        }

                    }
                    return "总成功：" + successCount + "，总失败：" + errorCount;
                }
            };
            callables.add(myCallable);
        }
        //取到线程的所有信息
        List<Future<String>> futures = executorService.invokeAll(callables);
        for (Future<String> f : futures) {
            LogPrintUtil.logRes(f.get());
        }
        //停止线程添加任务
        executorService.shutdown();
	```

> 线程交替输出

```java
 public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();
        for (int i = 9; i > -1; i--) {
            stack.add(i);
        }

        OutputData outputData = new OutputData(stack);
        new Thread(outputData).start();
        new Thread(outputData).start();
    }

    static class OutputData implements Runnable {

        private Stack<Integer> stack;

        Integer zhi1 = null;
        Integer zhi2 = null;

        public OutputData(Stack<Integer> stack) {
            this.stack = stack;

        }

        @Override
        public void run() {
            while (!stack.isEmpty()) {
                String name = Thread.currentThread().getName();
                synchronized (this) {
                    notify();
                    if (name.equals("Thread-0")) {
                        zhi1 = stack.peek();
                    }else {
                        zhi2 = stack.peek();
                    }
                    System.out.println(name + "---" + stack.pop());
                    try {
                        wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                if (name.equals("Thread-0")) {
                    System.out.println("乘积：" + zhi1 * zhi2);
                }
            }
        }

    }
```
输出效果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210705161432504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE3NjczMTk=,size_16,color_FFFFFF,t_70)
## CompletableFuture 多线程的用法
**例子：**

```java
@SneakyThrows
public List<BatchUploadUrlResp> fileUploadService(BatchUploadUrlReq req) {
    List<CompletableFuture<BatchUploadUrlResp>> batchUploadUrlResponseFutureList = new ArrayList<>();
    IntStream.range(0, req.getUrls().size())
            .forEach(i -> batchUploadUrlResponseFutureList.add(CompletableFuture.supplyAsync(() -> {
                String imageUrl = req.getUrls().get(i);
                BatchUploadUrlResp batchUploadUrlResp = new BatchUploadUrlResp();
                //下载
                URL url = null;
                try {
                    url = new URL(imageUrl);
                    HttpURLConnection conn = (HttpURLConnection) url.openConnection();
                    conn.setRequestMethod("GET");
                    InputStream in = conn.getInputStream();
                    ByteArrayOutputStream out = new ByteArrayOutputStream();
                    byte[] buffer = new byte[1024];
                    int n = 0;
                    while ((n = in.read(buffer)) != -1) {
                        out.write(buffer, 0, n);
                    }
                    in.close();
                    /*
                     * 处理文件上传
                     * */
                    out.toByteArray();
                } catch (MalformedURLException e) {
                    batchUploadUrlResp.setFailReason("下载图片失败");
                }
                batchUploadUrlResp.setIndex(i);
                return batchUploadUrlResp;
            }, executorService)));
    CompletableFuture<Void> allFutures = CompletableFuture.allOf(batchUploadUrlResponseFutureList.toArray(new CompletableFuture[0]));
    allFutures.get();
    return batchUploadUrlResponseFutureList.stream().map(CompletableFuture::join).sorted(Comparator.comparingInt(BatchUploadUrlResp::getIndex)).collect(Collectors.toList());
}


//另外一种写法，解决上面要自己去判断下标取值的问题。
//例子如下：
Map<String, CompletableFuture<?>> futureMap = MapUtil.newHashMap();
CompletableFuture<List<Long>> outboundIdsCompletableFuture = CompletableFuture.supplyAsync(() -> dyeYarnOutbounds.stream().map(DyeYarnOutbound::getTenantId).collect(Collectors.toList()), AsyncConfiguration.myExecutorService);
futureMap.put(UUID.randomUUID().toString(), outboundIdsCompletableFuture);
CompletableFuture<List<DyeYarnOutboundItem>> itemListCompletableFuture = outboundIdsCompletableFuture.thenApplyAsync(outboundIds -> Optional.ofNullable(dyeYarnOutboundItemService.getByOutboundIds(req.getTenantId(), outboundIds)).orElse(null), AsyncConfiguration.myExecutorService);
futureMap.put(UUID.randomUUID().toString(), itemListCompletableFuture);
CompletableFuture<Map<Long, List<DyeYarnOutboundItem>>> itemMapCompletableFuture = itemListCompletableFuture.thenApplyAsync(itemList -> Optional.ofNullable(itemList).filter(CollUtil::isNotEmpty).map(m -> m.stream().collect(Collectors.groupingBy(DyeYarnOutboundItem::getOutboundId))).orElse(Collections.emptyMap()));
futureMap.put("itemMap", itemMapCompletableFuture);

CompletableFuture.allOf(futureMap.values().toArray(new CompletableFuture[0])).get();
Map<Long, List<DyeYarnOutboundItem>> itemListMap = (Map<Long, List<DyeYarnOutboundItem>>) futureMap.get("itemMap").get();
```

## List集合

> 集合的排序
> 注意：集合直接点sort 方法会使集合失去序列化的效果。
> 解决方法可以使用 Collections.sort(); 解决不能序列化的问题

```java
//匿名写法
movieScores.sort((o1, o2) -> -(o1.getRating().intValue() - o2.getRating().intValue()));

//解决序列化问题
Collections.sort(movieScores,(o1, o2) -> -(o1.getRating().intValue() - o2.getRating().intValue()));

//还有一种解决办法 使用 fastjson 重新序列化
userRecs.setMovieScores(
        JSON.parseArray(
                JSON.toJSONString(movieScores),
                MovieScore.class
        )
);

//简洁排序
//Comparator.reverseOrder() 相反的顺序-倒序
List<Integer> newSorts = temporarySorts
                    .stream()
                    .sorted(Comparator.reverseOrder())
                    .collect(Collectors.toList())
```

> 集合截取
> 注意：集合直接点subList 方法会使集合失去序列化的效果。

```java
//集合截取
movieScores = movieScores.subList(0, 5);

//还有一种解决办法 使用 fastjson 重新序列化
userRecs.setMovieScores(
        JSON.parseArray(
                JSON.toJSONString(movieScores),
                MovieScore.class
        )
);
```
## 流式编程（各种使用例子）

> 转map的例子

```java
//单个封装
List<UserProfileResult> userProfileResults = userApiService.findByIds(userIdList);
//封装一下参数
Map<Long, String> userProfileResultMap = userProfileResults
       	.stream()
       	.collect(Collectors.toMap(UserProfile::getUserId, UserProfile::getName))

//Function.identity() 这个实体的所有数据我都要
List<UserProfileResult> userProfileResults = userApiService.findByIds(userIdList);
//封装一下参数
Map<Long, UserProfileResult> userProfileResultMap = userProfileResults
       	.stream()
       	.collect(Collectors.toMap(UserProfile::getUserId, Function.identity()))
//单个分组
Map<Long, List<UserCrystalTradingResp>> collect1 = userCrystalByBusinessTypeInAndDateScope
                        .parallelStream()
                        .collect(Collectors.groupingBy(UserCrystalTradingResp::getUserId));
//分组同时对两个值进行分组
List<ProArea> infoVOS = Lists.newArrayList();
ProArea sd = new ProArea("山东", "济南", "县城1");
ProArea sd1 = new ProArea("山东", "济南", "县城2");
ProArea yt = new ProArea("山东", "烟台", "县城3");
ProArea cs = new ProArea("湖南", "长沙", "县城4");
ProArea xt = new ProArea("湖南", "湘潭", "县城5");
ProArea xt2 = new ProArea("湖南", "湘潭", "县城6");
infoVOS.add(sd);
infoVOS.add(sd1);
infoVOS.add(yt);
infoVOS.add(cs);
infoVOS.add(xt);
infoVOS.add(xt2);
Map<String, Map<String, List<ProArea>>> infoMap = infoVOS.stream()
        .collect(Collectors.groupingBy(ProArea::getPro, Collectors.groupingBy(ProArea::getAre)));
System.out.println(JSON.toJSONString(infoMap));
//求和
BigDecimal res = unionDailyResps
  .parallelStream()
  .map(UnionDailyResp::getCrystal)
  .reduce(BigDecimal.ZERO, BigDecimal::add);
//排序
List<UnionDailyTreeResp> unionDailyTreeResps = new ArrayList<>();
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(1)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(2)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(3)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(4)).build());
unionDailyTreeResps.sort(Comparator.comparing(UnionDailyTreeResp::getTotalCrystal));
System.out.println(JSON.toJSONString(unionDailyTreeResps));
//排序-倒序（添加.reversed()）
List<UnionDailyTreeResp> unionDailyTreeResps = new ArrayList<>();
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(1)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(2)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(3)).build());
unionDailyTreeResps.add(UnionDailyTreeResp.builder().totalCrystal(new BigDecimal(4)).build());
unionDailyTreeResps.sort(Comparator.comparing(UnionDailyTreeResp::getTotalCrystal).reversed());
System.out.println(JSON.toJSONString(unionDailyTreeResps));

//简单的倒序-（单个数组）
hotSearchOrgSortList.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList());

```
## stream groupingBy 保持原顺序

![[Pasted image 20240228121212.png]]

```java
//分组保留原有顺序
Map<String, List<DfsGoodsMappingEntity>> groupMap = entityList.stream().collect(groupingBy(DfsGoodsMappingEntity::getUpc,LinkedHashMap::new,Collectors.toCollection(ArrayList::new)));
```


## 如何将list对象里面的字符串细化拆分成

> 例子：

```java
public static void main(String[] args) {
    List<Ceshi> list = new ArrayList<>();
    Ceshi ceshi = null;
    ceshi = new Ceshi();
    ceshi.setId(1L);
    ceshi.setKeywords("/a/b/c/");
    list.add(ceshi);
    ceshi = new Ceshi();
    ceshi.setId(2L);
    ceshi.setKeywords("/1/2/3/");
    list.add(ceshi);
    Map<String, Long> stringLongMap = list.stream().map(p -> Arrays.stream(p.getKeywords().substring(1,
            p.getKeywords().length() - 1).split(
            "/")).collect(Collectors.toMap(k -> k, k -> p.getId()))).reduce((m1, m2) -> {
        m1.putAll(m2);
        return m1;
    }).orElse(new HashMap<>());
    System.out.println(stringLongMap);
}

@Data
@NoArgsConstructor
public static class Ceshi {
    private Long id;
    private String keywords;
}
```

> 效果图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6c3cdfe2c1747f98581eee08e61dd9c.png)

## 分页排序（业务方面）参考代码如下：

```java
@Data
@ApiModel("测试实体")
public class OrgPositionSortReq {

    @ApiModelProperty(value = "id")
    private Long id;

    @ApiModelProperty(value = "排序数字")
    private Integer sortNumber;

}

public boolean sortOrgPosition(List<OrgPositionSortReq> orgPositionSortReqList) {
    if (CollUtil.isEmpty(orgPositionSortReqList)) {
        return true;
    }
    List<Integer> hotSearchOrgSortList = orgPositionSortReqList.stream().map(OrgPositionSortReq::getSortNumber).collect(Collectors.toList());
    hotSearchOrgSortList = hotSearchOrgSortList.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList());
    AtomicInteger atomicInteger = new AtomicInteger(0);
    List<Integer> finalHotSearchOrgSortList = hotSearchOrgSortList;
    List<CcaOrgPositionPO> ccaOrgPositionPOS = orgPositionSortReqList.stream().map(p -> {
        CcaOrgPositionPO ccaOrgPositionPO = new CcaOrgPositionPO();
        ccaOrgPositionPO.setId(p.getId());
        ccaOrgPositionPO.setSortNumber(finalHotSearchOrgSortList.get(atomicInteger.getAndIncrement()));
        return ccaOrgPositionPO;
    }).collect(Collectors.toList());
    this.updateBatchById(ccaOrgPositionPOS);
    return true;
}
```
## string的相关用法

> 比如业务要使用补0法的话，例子： `%03d`
>  `%03d`的意思呢，是不满三位就用0补位。
>  超过则不会补0

```java
String.format("%03d", 1)

//结果是 001

String.format("%03d", 999)

//结果是 999

String.format("%03d", 1000)

//结果是 1000

```

## 数据去重

>比如对某个对象集合，进行唯一去重（针对某几个字段）
>Tuple 这个对象不是java的，是hutool工具的

```java
//去重 
Map<Tuple, ImportProcessingFactoryCraftDTO> deduplicationValusMap = v.stream()  
        .collect(Collectors.toMap(itemDto ->  
                        new Tuple(itemDto.getStandardProcessing(), itemDto.getProcessingPrice()),  
                Function.identity(),  
                (o1, o2) -> o1));
```

## ConcurrentLinkedDeque

> ConcurrentLinkedDeque 是 Java 并发包 (java.util.concurrent) 中的一个线程安全的双端队列（Deque）实现。它基于链表结构，支持高效的并发操作，适用于多线程环境下的非阻塞式数据处理。
> 可以在头部尾部插入数据。
> 业务上：有队列数据或者有更改头尾的情况可以使用这个数据

## LinkedBlockingQueue

> `LinkedBlockingQueue` 是 Java 并发包 (`java.util.concurrent`) 中的一个线程安全的阻塞队列实现。它基于链表结构，支持先进先出（FIFO）的队列操作，并且提供了阻塞式的插入和移除操作，适用于多线程环境下的生产者-消费者模型。
> 业务上：数据需要累计异步处理的时候，可以使用。