# 基于Linux的博客平台搭建

## 目录
- [技术栈](#技术栈)
- [功能改进与添加](#功能改进与添加)
- [更新日志](#更新日志)
- [未来规划](#未来规划)
- [常见问题](#常见问题)
- [参考资料](#参考资料)

## 技术栈

本平台主要使用以下技术栈：

- **编程语言**: C++，用于服务器端开发
- **数据库**: MySQL，用于存储博客数据、用户信息等
- **前端**: HTML, CSS, JavaScript（AJAX）用于页面展示与交互
- **框架与库**: 
  - `editor.md`: 用于Markdown编辑的富文本编辑器
  - `ncurses`: 用于C++中的终端交互（如管理后台）
- **服务器环境**:
  - **操作系统**: Linux（CentOS）
  - **Web服务器**: 自定义C++编写的简易Web服务器
  - **API协议**: RESTful API，用于前后端通信
  - **代理与缓存**: 使用Nginx作为反向代理与负载均衡

## 功能改进与添加

### 2024-11-14（今天遇到的主要是数据库的编码问题）
- 客户端传入的json字符串在插入MySQL数据库时，插入的中文字符串（博客的标题和内容）会变成乱码。经过排查，从客户端发送的json字符串，到后端代码对于字符串的解析，cout出来的内容都没有乱码，因此将问题定位到插入到数据库的那一步。
  - 在遇到中文乱码时如何进行排查：
    - 确保数据库是**uft8mb4**或**utf8**等支持中文的字符集
    - 数据库连接（这个后端里面的代码）是否设置为**uft8mb4**或**utf8**
    - 发送过来的title和content也要确保是UTF-8？？？
- 还有一个是从数据库中取出数据构建json字符串时，因为没有注意特殊字符（回车、换行、空格等），导致json字符串的构建（没有时间json库，自己手工构建的）有问题，客户端那边也因此无法正常解析并渲染。
  - 添加了转义字符处理函数，用于处理具有特殊含义的字符(" \ \n \r \t 等)
- 后面的话要把json字符串的构建和转义字符处理函数封装到其它地方

### 未来计划（这部分暂时留存）
- 增加全文搜索功能，支持博客标题、内容、评论的搜索。
- 添加社交分享功能，支持将文章分享到社交平台。
- 支持多语言，扩展为国际化平台。

## 更新日志

### 版本 1.0.0
- 初始版本发布。
- 包括博客发布(Markdown编辑器的集成)、显示。
- 用户的登录、注册。

### 版本 1.1.0
- 使用cookie+session进行会话管理。
- 添加了留言板功能。

### 版本 1.2.0
- 待定。
- 待定。

## 未来规划

### 可扩展性增强
- **支持多数据库系统**：目前仅支持MySQL，未来可以考虑支持PostgreSQL等。
- **支持图像上传和展示**：允许用户上传图片作为博客内容的一部分。

### 性能优化
- **缓存机制**：实现博客内容的缓存，以提高读取速度。
- **负载均衡**：支持水平扩展，分布式部署服务器，增加请求处理能力。

## 常见问题

### 问：为什么我在浏览器访问时经常遇到页面加载缓慢的情况？
答：可能是由于服务器负载过高或者数据库查询不优化。可以通过增加缓存、优化SQL查询、调整数据库索引来改善。

### 问：如何管理用户的权限？
答：使用基于角色的访问控制（RBAC）来限制不同类型用户（管理员、普通用户）的访问权限。

## 参考文献

- Linux高性能服务器编程，游双著.
- [TinyWebServer](https://github.com/qinguoyi/TinyWebServer)
- [editor.md GitHub](https://github.com/pandao/editor.md) - Markdown编辑器的文档
