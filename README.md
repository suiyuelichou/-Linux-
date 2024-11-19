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
- **服务器环境**:
  - **操作系统**: Linux（CentOS_7_9）
  - **Web服务器**: 自定义C++编写的Web服务器
  - **部署**：部署于阿里云服务器
  - **API协议**: RESTful API，用于前后端通信
  - **代理与缓存**: 使用Nginx作为反向代理与负载均衡

## 功能改进与添加

### 2024-11-14之前的进度没有进行系统记录

### 2024-11-14（数据库的编码问题和JSON字符串中特殊字符的转换）
1. 客户端传入的json字符串在插入MySQL数据库时，插入的中文字符串（博客的标题和内容）会变成乱码。经过排查，从客户端发送的json字符串，到后端代码对于字符串的解析，cout出来的内容都没有乱码，因此将问题定位到插入到数据库的那一步。
   - 在遇到中文乱码时如何进行排查：
     - 确保数据库是**uft8mb4**或**utf8**等支持中文的字符集
     - 数据库连接（这个后端里面的代码）是否设置为**uft8mb4**或**utf8**
     - 发送过来的title和content也要确保是UTF-8？？？
2. 还有一个是从数据库中取出数据构建json字符串时，因为没有注意特殊字符（回车、换行、空格等），导致json字符串的构建（没有时间json库，自己手工构建的）有问题，客户端那边也因此无法正常解析并渲染。
   - 添加了转义字符处理函数，用于处理具有特殊含义的字符(" \ \n \r \t 等)
3. 后面的话要把json字符串的构建和转义字符处理函数封装到其它地方

### 2024-11-15(打算实现博客的分页查找，但是没搞定)
1. 对每个页面都添加了留言板的跳转路径。
2. 将blog_home.html的js代码分离出来，单独放进js文件夹。
3. 今天的进度没怎么推进(sad..)。

### 2024-11-16(博客的分页查询)
1. 实现了**数据库分页查询**和**获取数据库博客总条数**的接口
2. 前端博客首页添加了翻页按钮，在js中实现了滚动加载的功能
3. 后端添加了**get_blogs**接口,用于解析blog_home.html前端发送的异步Ajax
4. 使用shell脚本插入新博客用于测试，很方便（👍）

### 2024-11-17
1. 在主页面添加了一个可拖动下拉框，用于个人中心、作者信息等的界面跳转(floating_dropdown.html/js)
2. 在将游客主页的分页查询添加到用户主页时，js代码执行异常。经排查，是**script**标签中未使用**defer**属性，导致blog_user_home.html页面未解析完毕就已经执行完js脚本，使js脚本获取到的内容无法插入到主页面。  
注：defer：脚本会在解析页面的同时并行下载，并在页面解析成功后执行
3. 将游客和用户的主页进行相对同步(目前的差别是用户信息的板块多了一个Ajax请求用于获取用户信息)

### 2024-11-18
1. 完成了个人中心的前端页面样式，后续实现前后端交互
2. 在注册的时候出现报错，用户成功注册，但是服务端没有返回任何内容给客户端。  
经过断点排查，**http_conn::HTTP_CODE http_conn::do_request()**函数中存储else if冲突，使用**strstr**进行字符串判断，会导致**blog**和**blog_login**都能被检测。
3. 添加了点击博客详情时，也显示作者的部分信息。  
为实现上述功能，添加了**根据博客id获取用户id**，**根据用户id获取用户信息**，以及添加了用户数据库的内容（头像地址、文章数、注册时间、email）。

### 2024-11-19
1. 登录可以获取用户信息，显示在用户主页上（如用户头像、用户名、用户发布的文章数），现在还没有添加文章数的功能，先自行修改数据库测试

### 2024-11-20

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

### 未来计划（这部分暂时留存）
- 增加全文搜索功能，支持博客标题、内容、评论的搜索。
- 添加社交分享功能，支持将文章分享到社交平台。
- 支持多语言，扩展为国际化平台。

## 常见问题

### 问：为什么我在浏览器访问时经常遇到页面加载缓慢的情况？
答：可能是由于服务器负载过高或者数据库查询不优化。可以通过增加缓存、优化SQL查询、调整数据库索引来改善。

### 问：如何管理用户的权限？
答：使用基于角色的访问控制（RBAC）来限制不同类型用户（管理员、普通用户）的访问权限。

## 参考文献

- Linux高性能服务器编程，游双著.
- [qinguoyi/TinyWebServer](https://github.com/qinguoyi/TinyWebServer)
