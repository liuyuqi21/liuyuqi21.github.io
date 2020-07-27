# Tomcat
## 配置Tomcat的虚拟目录
- 可以把磁盘上任意位置的文件夹当作一个应用给Tomcat管理
- 方式一：直接修改sever.xml文件（需要重新启动Tomcat，不推荐）
- 方式二：在Tomcat\conf\引擎名称\主机名称目录中，建立一个xml配置文件，文件名就是应用的访问虚拟目录
    ```xml
    <Context docBase="文件路径"/>
    ```
## 配置虚拟主机 
- 修改Tomcat\conf\server.xml
    ```xml
    <Host name="主机名" appBase="存放应用的目录" />
    ```
## 配置默认端口 
- 修改Tomcat\conf\server.xml
    ```xml
    <Connector port="8080" />
    ```
## 配置默认应用
- 方式一：将文件名改为Root
- 方式二：在Tomcat\conf\引擎名称\主机名称目录中，建立一个ROOT.xml文件
    ```xml
    <Context docBase="文件路径"/>
    ```
## 配置默认主页
- 修改web.xml
    ```xml
    <welcome-file-list>
        <welcome-file>文件名</welcome-file>
    </welcome-file-list>
    ```
# HTTP
- HTTP是超文本传输协议，描述客户端和服务端的数据标准，该协议由W3C维护和管理
- HTTP1.0：每次发出请求都需要建立网络连接
- HTTP1.1：在一次网络连接上发出多次请求和得到多次响应。多了一些头
- ```<link> <script src=""></script> <img src=""/>```浏览器会自动发出请求
- URL：统一资源标识符
- URL：协议+主机:端口+URI 统一资源定位符
## 请求消息头
- 消息头相当于服务器和浏览器之间的一些暗号指令，向服务端传递附加信息
- 每个消息头包含一个头字段名称，然后依次是冒号，空格，只，回车和换行符
- 消息头名称不区分大小写
- 请求头字段都允许客户端在值部分制定多个可接受的选项，多个选项之间以逗号分隔
- 有些字段可以出现多次
### 常用的请求头
- Accept:浏览器接受的数据类型（MIME类型：大类型/小类型 如txt-->text/html）
- Accept-Encoding：浏览器能够进行解码的数据编码方式
- Accept-Language：浏览器所希望的语言种类
- Referer：包含一个URL，用户从该URL代表的的页面发出请求访问当前请求的页面（作用：统计广告投放效果，防盗链）
- Content-Type：请求正文的MIME类型
    - 默认类型：application/x-www-form-urlencided 表单类型 enctype属性的默认取值
    - 其他类型：multipart/foem-data 文件上传
- If-Modidied-Since：利用这个头与服务器的文件进行对比，如果一致，则从缓存中直接读取文件
- User-Agent：浏览器类型
- Content-Length：请求消息正文长度
- Connenction：表示是否需要持久连接
- Cookie：会话管理
## 响应消息头
- 服务端向客户端传递的附加信息
- Location：新的资源的地址，和302/307实现请求重定向
- Content-Encoding：响应正文使用的压缩编码
- Content-Length：响应正文的长度
- Content-Language：服务器发送的文本语言
- Content-Type：响应正文的MIME类型
- Last-Modified：文件最后修改时间
- Refresh：指定客户端刷新频率，单位是秒
- Content-Disposition：attachment;filename= 指定客户端下载文件
- Set-Cookie：会话管理
- Expires:-1 Cache-Control:no-cache Pragma:no-cache 三个头一起用，告知浏览器不要缓存

# Servlet
## ResponseRequest
- 解决浏览器乱码：
    - 方案一：向客户端输出一个```<meta>```标签，模拟响应消息头```<meta http-equiv='Content-type' context='text/html;charset=UTF-8'>```
    - 方案二：向客户端输出响应消息头 response.setHeader("Content-Type","text/html;charset=UTF-8");
    - 方案三：response.setContentType("text/html;charset=UTF-8");
## URL地址的写法
- 
    ```java
    //ServletContext写法。必须以"/"，开头，代表当前应用（应用开头的绝对路径）
    RequestDispacher rd=getServletContext().getRequestDispacher("/demo1");
    //request可以写绝对路径也可以写相对路径
    RequestDispacher rd=request.getRequestDispacher("/demo1");

    ```
# Struct2
## 动作方法
- 访问修饰符都是public
- 返回值一般都是String（但是可以是void）
- 方法都没有参数
## 配置文件
- default.properties
- structs-default.xml
- structs-plugin.xml
- structs.xml
- structs.properties
- web.xml

# hibernate
## 实体类的编写规范
- 应该遵循JavaBean的编写规范
- 类都是public
- 一般实现序列化接口
- 类成员（字段）都是私有的
- 私有类成员都有共有get/set方法
- 类都有默认无参构造函数
- 用包装类
## hibernate的OID
- hibernate中的对象标识符：OID
- 在同一个session中，不允许出现两个相同类型的IOD一致
- OID就是映射文件，对应数据库主键的属性
## 自然主键和代理主键的区别
- 代理主键：用于区分数据库中的记录，不参与程序的业务逻辑
- 自然主键： 不仅用于区分数据库中的记录，还会参与程序的业务逻辑
## 快照机制
- 一级缓存和快照中国的内容不一致会更新数据库
- 一级缓存和快照都存在session中，session销毁，缓存的快照消失
## 对象的状态
- 瞬时状态：没有OID，和session没有关系
- 持久状态：有OID，和session有关系，有一级缓存
- 脱管状态：没有OID，和session没关系
## session对象使用原则
- 一个线程只能用一个session
- 把session和线程池绑定

    ```xml
     <property   name="hibernate.current_session_context_class ">thread</property>
    ```
    ```java
    //从当前线程上获取Session对象
    public static Session getCurrentSession(){
        return factory.getCurrentSession();//只有   配置了把session和线程绑定之后才可以使用此方    法，否则返回null
    }
    ```
## hibernate的常用对象
- Configuration
- SessionFactory：一个应用只有一个SessionFactory，在应用加载时创建，应用卸载时销毁
- Session：一个线程一直有个Session对象
- Transaction
## get和load的区别
- 查询的时机不一样 get立即加载 load延迟加载
- 返回的结果不一样 get返回实体类类型 load返回代理对象
## hibernate中的查询方式
- OID查询
- SQL查询 SQLQuery 和 session的doWork方法
- HQL查询 HQL：hibernate query language
    - 分页查询 query.setFirstResult:设置查询的开始记录索引 setMaxResults 设置每次查询的条数
    - uniqueResult()当返回结果唯一，可以用此方法
    - 投影查询：查询语句需要时使用new关键字，在实体类中添加对那个参数列表的构造函数
- QBC查询
    - Query by Criteria
    - 基本查询
        ```java
        Session s=HibernateUtil.getCurrentSession();
        Transaction tx=s.beginTransactino();
        Criteria c=s.createCtiteria(Customer.class);
        List list = c.list();
        for(Object o : list){
            System.out.println(o);
        }
        tx.commit();
        ```
    - 条件查询 c.add(Restrictions.XXX("名称","值"));
    - 排序 c.addOrder(Order.desc(""));
    - 分页 与HQL一样
    - 使用聚合函数 c.setProjection(Projections.XXX)
    - 离线查询 DetachedCriteria对象，该对象的获取不需要session，使用此对象的查询，称之为离线查询
- 对象导航查询

# Mybatis
## 原生jdbc
- 数据库连接
- 预编译Statement,使用预编译的PreparedStatement可以提高数据库性能
- 创建结果集
- 数据库连接，使用时创建，不使用立即释放,对数据库进行频繁连接开启和关闭,影响数据库性能
- 将sql语句硬编码到java代码中,如果sql语句修改,需要重新编译java代码，不利于系统维护
## 框架原理
- mybatis是一个持久层的框架
## mybatis的结构
- 映射文件 mapper.xml 写CURD等SQL语句 对象之间的关系
- 核心配置文件 sqlMapConfig.xml(名称不固定43) 配置数据源事务等运行环境
- SqlSessionFactory 会话工厂 -> 创建SqlSession ->内部创建Excutor操作数据库
## mapper.xml 映射文件
- pramaterType 指定参数的类型
- resultType 指定输出结果的类型
- id:表示映射文件中的sql
- #{}表示一个占位符号
- #{id}其中id表示接收输入的参数，参数名称就是id，如果输入参数是简单类型，#{}中的参数名可以任意，可以使用value或其他名称
- 当输入参数类型是pojo时,#{}中指定pojo的属性名
- ${}表示拼接sql串，将接收到的参数的内容不加修饰拼接在sql中,会引起sql注入
- ${value}：接收参数的内容，如果传入类型是简单类型，${}中只能使用value
## sqlMapConfig
- 别名
    ```xml
    <typeAliases>
        单个别名定义
        <typeAlias type="类型的路径" alias="别名" />
        指定包名
        <package name=""/>
    </typeAliases>
    ```
- mapper接口加载，将mapper.java和mapper.xml放在一个目录且同名
- 批量加载多个mapper ```<package name="" />```
## mapper代理开发
- mapper.xml中namespace等于mapper接口地址
- mapper.java接口中的方法名和mapper.xml中statement的id一致
- 输入参数类型和返回值类型也一致
## 输出映射
- resultType 只有查询出来的列名和属性名一致才可以映射成功
- resultMap 可以解决的列名和属性名不一致
    ```xml
    <!-- resultMap是自定义的结果集，是resultType的本质property是属性名必须注意大小写,colunm无所谓 -->
    <resultMap type="User" id="userResqltMap">
	    <result property="userName" column="userName"/>
	    <result property="roleName" column="rolename"/>
    </resultMap>
    ```
## 通过RowBounds实现分页
- mapper.xml不用改动
- RowBounds rowBounds=new RowBounds('index','offset');
