**智能Java代码生成器** - 基于MCP服务架构的数据库驱动代码生成工具

[ZhaoXingPeng/DBJavaGenix: 智能数据库代码生成工具基于MCP架构](https://github.com/ZhaoXingPeng/DBJavaGenix)

### 安装配置（推荐方式）

**通过npm安装**

```
npm install -g dbjavagenix-mcp-server
```

**配置MCP客户端** 在Claude Desktop或您的MCP客户端中添加以下配置：


```
{
  "mcpServers": {
    "dbjavagenix": {
      "timeout": 60,
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "dbjavagenix-mcp-server"]
    }
  }
}
```

**启动服务**


```
npx dbjavagenix-mcp-server
```

### 使用方式

```
请帮我生成Java代码：

**数据库信息：**
- 类型：MySQL
- 主机：localhost:3306
- 数据库：test_db
- 用户名：root
- 密码：password

**生成选项：**
- 表名：user
- 模板分类：MybatisPlus-Mixed
- 包名：com.example.project
- 作者：YourName
- 包含：Swagger + Lombok + MapStruct

请先连接数据库，分析表结构，然后生成完整的Java代码。
```

## bat脚本

```
@echo off
setlocal enabledelayedexpansion
chcp 65001 >nul 2>&1
title 简化版启动器

cls
echo ====================================
echo     简化版 dbjavagenix 启动器
echo ====================================
echo.

echo [信息] 跳过复杂检查，直接尝试运行...
echo.

REM 简单检查 Node.js
where node >nul 2>&1
if !ERRORLEVEL! NEQ 0 (
    echo [错误] 未找到 Node.js，请先安装！
    echo 下载地址: https://nodejs.org/
    pause
    exit /b 1
)

echo [信息] Node.js 已安装
echo.

echo [执行] 正在运行: npx dbjavagenix-mcp-server
echo 按 Ctrl+C 可以随时终止
echo.

REM 直接运行，不做过多检查
npx dbjavagenix-mcp-server

echo.
echo [完成] 程序已退出，退出代码: !ERRORLEVEL!
echo.
pause
endlocal

```
