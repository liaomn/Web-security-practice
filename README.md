# 网络安全实践
Web Security Lab and Vulnerbility Reports
## Lab1:SQL injection vulnerability in WHERE clause
** 靶场平台**:portswigger web security academy
** 漏洞类型**：SQL注入
###  1. 漏洞描述
应用程序未对用户输入的 `category` 参数进行过滤，直接将其拼接到 SQL 查询语句中。这导致攻击者可以通过构造恶意 SQL 语句来绕过原有的查询限制（如 `released = 1`），从而查看隐藏的、未发布的数据。
### 2. 复现过程
1.进入靶场首页，gifts分类，发现网址变成`....filter?category=Gifts`
2.我尝试修改网址，在后面加''OR 1=1--'，然后回车
  <img width="699" height="56" alt="image" src="https://github.com/user-attachments/assets/7d63e91b-10cf-4faa-a6ce-d93c692d2b63" />

3.回车页面刷新，成功爆出所有隐藏商品，右上角显示Solved
  <img width="865" height="136" alt="image" src="https://github.com/user-attachments/assets/1167b178-d1c8-406e-9dc4-0106bfbb8d7a" />

### 3. 原理分析
* `'` (单引号) 闭合了原有的查询参数。
* `OR 1=1` 构造了一个永远为真的条件，绕过了分类限制。
* `--` (注释符) 将原查询后方的限制条件 (例如 `AND released = 1`) 注释掉，使其失效。

### 4. 修复建议
* 使用**参数化查询（预编译语句）**，分离代码与数据。
* 对用户输入进行严格的过滤和转义。
* 部署 Web 应用防火墙（WAF）拦截恶意特征。
  


