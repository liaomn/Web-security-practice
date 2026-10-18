## Lab 4: 在单一列中检索多个值

**靶场平台**：PortSwigger Web Security Academy
**漏洞类型**：UNION 注入 (字符串拼接)

### 1. 实验目标
当查询结果只有一列能够回显文本时，利用字符串拼接技术，将用户名和密码组合在一起，从 users 表中提取管理员账号密码。

### 2. 复现过程
1. 先确定查询返回的列数。使用 `' UNION SELECT NULL, 'abc'--` 测试，发现页面正常显示 `abc`，证明原查询返回 **2 列**，且第 2 列为文本回显列。
2. 使用字符串拼接符（本环境支持 `||`），将用户名和密码用波浪号 `~` 连接起来，塞入第 2 列。
3. 构造 Payload：
   `' UNION SELECT NULL, username || '~' || password FROM users-- `
4. 页面成功返回了拼接后的数据（如 `administrator~i8nfyntg97f92cmxse3y`）。

<img width="633" height="254" alt="image" src="https://github.com/user-attachments/assets/c93993b9-57a9-458d-bec1-1ce23da3d79f" />

5.登录用户名和密码

<img width="655" height="226" alt="image" src="https://github.com/user-attachments/assets/acbc2cca-15f3-454c-9dc2-f13d94f4f51d" />



### 3. 原理分析
由于某些数据库（如 Oracle、PostgreSQL 或特定配置的 MySQL）支持使用 `||` 作为字符串连接符，攻击者可以在只有一列回显点的情况下，将多个字段拼接成一个长字符串输出。如果数据库不支持 `||`，也可以使用 `CONCAT(username, '~', password)` 函数达到相同目的。

### 4. 修复建议
* 严格使用预编译（参数化查询）。
* 对用户输入进行合法性校验，拦截含有 SQL 关键字（如 `UNION`、`SELECT`、`||`）的请求。
