
**类型表格**


| java       | es                                                                                                                                                                                                       |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| String     | keyword                                                                                                                                                                                                  |
| Double     | double                                                                                                                                                                                                   |
| BigDecimal | scaled_float  类型<br>scaling_factor   10000  代表小数位<br><br>例子：<br><br>"hotScore": {<br>  "type": "scaled_float",<br>  "scaling_factor": 10000<br>}<br><br>为什么不能用double，因为转换的时候会出现丢精度的情况，所以需要使用scaled_float |
| Long       | long                                                                                                                                                                                                     |
| Integer    | integer                                                                                                                                                                                                  |
