## lab3:使用UNION获得其他表格数据
**实验目标**：利用 SQL 注入 UNION 攻击，从数据库的其他表（如 users 表）中检索出敏感数据（如管理员账号和密码）。

### 1. 复现过程
1. 经过''NUION SELECT NULL,NULL--'测试，确定该网站查询返回 2 列，且两列都能回显文字。
2. 构造 Payload 查询 users 表中的用户名和密码：
   `Tech gifts' UNION SELECT username, password FROM users--`
3. 页面下方成功爆出了所有的用户账号和密码。
   <img width="700" height="331" alt="image" src="https://github.com/user-attachments/assets/1288312f-2804-4a2f-9528-f3dcdccf83e3" />
4. 登录用户名和密码。
  <img width="782" height="141" alt="image" src="https://github.com/user-attachments/assets/0fa8ee1d-f691-4e54-a68e-5f7b06e5c58f" />



### 2. 原理分析
由于后端没有对输入进行过滤，攻击者可以使用 `UNION SELECT` 语句拼接一个全新的查询。
`username, password` 指定了想要获取的列，`FROM users` 指定了数据来源的表。数据库执行后，会将用户表的数据直接拼接到商品列表中返回给前端。

### 3. 修复建议
* 使用参数化查询，避免输入被当作代码执行。
* 遵循最小权限原则，数据库连接账户不应有查询其他无关表（如 users 表）的权限。
