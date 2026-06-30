

[agent-database-cli]([https://github.com/sleepinginsummer/agent-database-cli](https://github.com/sleepinginsummer/agent-database-cli))

## 怎么安装

丢给ai


```
安装请阅读 https://github.com/sleepinginsummer/agent-database-cli/blob/main/AI_INSTALL.md，按说明安装 CLI 并添加 `SKILL.md`。
```




```

// 初始化 url  mysql://账号:密码@链接:3306/数据库
{  
  "databases": {  
    "test-app-remote": {  
      "type": "mysql",  
      "url": "mysql://账号@链接:3306/数据库",  
      "readonly": false,  
      "blacklist": [  
        "drop",  
        "truncate",  
        "delete",  
        "update",  
        "insert",  
        "merge",  
        "alter"  
      ],  
      "keepAliveSeconds": 180,  
      "passwordRef": "agentdbcli:test-app-remote:url"  
    }
}
```

| 字段          | 描述                        |
| ----------- | ------------------------- |
| url         | mysql://账号:密码@链接:3306/数据库 |
| readonly    | readonly代表是否可以使用ddl执行     |
| passwordRef | 使用过一次后，密码会存储在passwordRef  |
| blacklist   | 禁止的列表，是不允许ai干这些活          |
