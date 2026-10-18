## Lab 2: SQL注入 UNION 攻击与确定列数

**靶场平台**：PortSwigger Web Security Academy
**漏洞类型**：UNION 注入 (UNION SQLi)



### 1. 实验目标
利用 SQL 注入 UNION 攻击，确定原查询返回的列数，并寻找可以将数据回显到页面上的列。

<img width="551" height="174" alt="image" src="https://github.com/user-attachments/assets/eb05065b-15ec-48aa-b501-19dc9a25d9fb" />


### 2. 复现过程

**第一步：利用 ORDER BY 确定列数**
在网址参数后加入 `' ORDER BY 1--`、`' ORDER BY 2--` 等。
当测试到 `' ORDER BY 4--` 时，页面返回 `Internal Server Error`（服务器内部错误）。
说明原查询只有 3 列。
<img width="550" height="350" alt="image" src="https://github.com/user-attachments/assets/96748388-bc1f-4554-bbf7-04b524df4d8d" />


**第二步：利用 UNION SELECT 验证列数**
输入 Payload：`' UNION SELECT NULL, NULL, NULL--`
页面正常显示，证明 3 列完全匹配。
<img width="750" height="320" alt="image" src="https://github.com/user-attachments/assets/2df4a11e-4714-46a5-ba45-c6ec2c33438f" />


**第三步：寻找回显点和数据类型**
输入 Payload：`' UNION SELECT 1, 'a', 15.2--`
页面下方的商品列表中，成功出现了名字为 `a`、价格为 `$0.15` 的假商品。
<img width="722" height="317" alt="image" src="https://github.com/user-attachments/assets/f5e66934-5259-47a6-9dcf-e8140961c368" />

这说明第 1 列是数字，第 2 列是文字（可以作为回显点提取账号密码），第 3 列是数字/浮点型。

### 3. 原理分析
UNION 操作符用于合并两个查询的结果，但必须保证两个查询的**列数相同**且**对应列的数据类型兼容**。
`NULL` 是万能占位符，适用于所有数据类型，常用来探测列数；而字符串（如 `'a'`）或数字（如 `15.2`）可以用来测试哪一列会把数据输出到前端页面上。

### 4. 修复建议
* 同样使用参数化查询，避免 SQL 注入。
* 对用户的输入进行严格的类型检查和过滤。
