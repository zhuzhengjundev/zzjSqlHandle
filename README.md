<html>
        <body>
        <h1>jsp操作数据库很烦？来试试它 ---> jSqlHandle</h1>
        <h2>zhuzhengjundev</h2>
        <img src="https://qlogo1.store.qq.com/qzone/1471888328/1471888328/100?1590622011">
        <h3>简介：</h3>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你只需要引入一个jar包，配置数据库信息之后，然后准备一个实体类传入对应的方法中，即可对数据表进行增，删，改，查，分页查，当然，你也可以写sql语句传入方法中。除此之外，你还可以使用事务，在对所有sql操作成功之后一起提交事务即操作成功，否则回滚掉事务，让之前的sql操作失效。
        </p>
        <h4>一：来看看如何配置吧！</h4>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① 配置的话很简单，你只需要把`jSqlHandle.jar`包放在对应的lib目录下，然后在`WebContent`目录下新建一个文件夹名为：`config`，然后在`config`文件夹里新建一个`dbconfig.properties`文件，切记文件的名字和后缀名要一模一样，否则连接不上数据库。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② 在上述正确的路径下新建文件夹和文件之后，你需要在`dbconfig.properties`文件里编写你的数据库配置内容，需要四个变量，分别为driverClassName(驱动)、db_url(数据库地址)、db_name(数据库连接名)、db_password(数据库连接密码)。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;实例：</p>
        <pre>     
        driverClassName=com.mysql.cj.jdbc.Driver
        db_url=jdbc:mysql://localhost:3306/my_store?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
        db_name=root
        db_password=123456
        </pre>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你可以将以上四行代码复制粘贴到你新建的`dbconfig.properties`文件中，然后修改后面对应你数据库信息的值</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最后看一下配置的截图吧！</p><br>
        <img src="http://a1.qpic.cn/psc?/V100BnzP2hNMuE/qWkkGOdvaHRbO0s2SzmJ0NRgdj09ekusZlZr8Kz*SRJhLXbkGm6MNPTJ0BVt5B*Yal*NqsTMe8FGjIHkptXzxQ!!/c&ek=1&kp=1&pt=0&bo=iwWQAgAAAAADFy4!&tl=1&vuin=1471888328&tm=1594108800&sce=60-2-2&rf=0-0">
        <h4>二：来看看使用说明吧！</h4>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1、使用该jar包你只需要知道两个类，`JDbHandle`和`JResponse`；</h5>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① `JDbHandle`是主要的操作数据表的类，只有它可操作数据表，来说说怎么使用吧，首先去new一个它，这个不用多说吧，不过在new它的同时，你得传入一个参数`Boolean isOpenTran`，看名字和类型应该可以猜出大概吧，是否打开数据库事务。当传入的参数为true，即你开启了一个事务，在你对数据表操作的增删改等事情之后，它其实都没有真正生效，之后当你操作完所有逻辑之后，再调用该类的`commit`方法，即可生效，不过你也可以选择调用`rollback`方法，即把之前的增删改全部撤回，一般只有当同时操作有关联的多张表时才会传`true`，否则传入`false`就好，当传入`false`，即你对数据表操作的任何操作都是即时生效的，你也不需要在操作完逻辑之后调用`commit`或者`rollback`方法。不过最后要说的是，不过传入的是`true`还是`false`，就算你只new了一次它，没有调用里面的方法，你也需要调用一个方法，那就是`stopConn`，即关闭数据库连接，因为你每new一个`JDbHandle`类，即连接数据库一次。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② `JResponse`类，它是`JDbHandle`类里面所有增删改查方法都会返回的一个类，你只需要在调用`JDbHandle`类的方法之后去用一个`JResponse`类型变量接收即可。该类里面有四个成员变量：int state//状态、String message(信息)、JsonArray data(数据)、JPageData pageData(分页查询数据)，当然这里的`data`和`pageData`变量不会同时出现，`data`是普通查询即查询所有数据时用它接收，`pageData`是分页查询时用它接收。当然，`pageData`里面又有四个成员变量：int pageIndex(当前页)、int pageSize(每页数量)、int pageAll(总页数)、JsonArray data(数据)；</p>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2、你还需要知道一件事</h5>
        <p>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                在以上配置配好之后，简单的看一下使用说明后，你还需要做一件事：<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                那就是你需要根据数据表的名称，表内各字段的名称来创建实体类(名称的字母必须一模一样，不区分大小写)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                当然，如果你直接写sql字段串也可以，同样也是调用下面的增删改查方法(直接传入即可)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                做完这些之后你就可以调用增删改查方法了，下面的有对增删改查四种方法进行介绍
        </p>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3、如何插入`insert`</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        //该实体类后面的参数作为插入的字段数据值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)
        Users users = new Users("value2","value3","value4","value5");
        JResponse jResponse = dbHandle.insert(users);
        jResponse.getState() //拿到状态值，大于0即插入成功，等于0即插入失败
        jResponse.getMessage() //拿到中文提示信息
        dbHandle.stopConn();
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4、如何删除`dalete`</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        //该实体类后面的参数作为删除的字段条件值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)
        Users users = new Users("value2","value3","value4","value5");
        JResponse jResponse = dbHandle.dalete(users);
        jResponse.getState() //拿到状态值，大于0即删除成功，等于0即删除失败
        jResponse.getMessage() //拿到中文提示信息
        dbHandle.stopConn();
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5、如何更新`update`</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        //该实体类后面的参数作为更新的字段数据值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)
        Users users = new Users("value2","value3","value4","value5");
        //不过在调用更新方法的时候需要多传入一个参数，即你需要使用哪些字段名称作为更新条件，可多个条件，中间用英文逗号隔开，作为字符串传入即可
        //当然，如果你直接写sql字符串，就不需要传入第二个参数
        JResponse jResponse = dbHandle.update(users,"userName,userPwd");
        jResponse.getState() //拿到状态值，大于0即更新成功，等于0即更新失败
        jResponse.getMessage() //拿到中文提示信息
        dbHandle.stopConn();
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6、如何查询`select`</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        //该实体类后面的参数作为查询的字段条件值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)
        Users users = new Users("value2","value3","value4","value5");
        //第一个参数为查询表的实体类，各个字段代表查询条件，第二个参数表示是否模糊查询
        JResponse jResponse = dbHandle.select(users,false);
        或者
        //第一个参数为查询表的实体类，各个字段代表查询条件，第二个参数表示是否模糊查询,查询排序的字段，以及排序方式（是否正序）
        JResponse jResponse = dbHandle.select(users,false,"key1,key2",true);
        jResponse.getState() //拿到状态值，大于0即查询成功，等于0即查询的数据为空或查询失败
        jResponse.getMessage() //拿到中文提示信息
        jResponse.getData() //拿到查询的数据(该数据为jsonArray格式，你也可以使用gson将它转成List )
        dbHandle.stopConn();
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6、如何分页查询`selectPage`</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        //该实体类后面的参数作为查询的字段条件值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)
        Users users = new Users("value2","value3","value4","value5");
        //不过在调用分页查询方法的时候需要多传入四个参数，即是否模糊查询，查询排序的字段，以及排序方式（是否正序），你要查询的页码，还有每页显示的数据条数
        //当然，如果你直接写sql字符串，只需要传入两个参数，即你要查询的页码，还有每页显示的数据条数，不过这里要注意一点，传入的sql字符串结尾不要加封号
        //这里selectPage方法传参的意思按照每页10条数据且按照 row_id 和  user_name 字段倒叙查询出第一页的数据，
        JResponse jResponse = dbHandle.selectPage(users2,false,"  row_id , user_name ",false,1,10);
        jResponse.getState() //拿到状态值，大于0即查询成功，等于0即查询的数据为空或查询失败
        jResponse.getMessage() //拿到中文提示信息
        jResponse.getPageData() //拿到查询的分页数据对象(该数据为JPageData对象，使用说明中有提到)
        jResponse.getPageData().getPageIndex(); //查询的当前页码
        jResponse.getPageData().getPageSize(); //查询的每页显示的数据条数
        jResponse.getPageData().getPageAll(); //查询的数据的总页数
        jResponse.getPageData().getData(); //查询的当前页码的数据(该数据同样为jsonArray格式，你也可以使用gson将它转成List )
        dbHandle.stopConn();
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7、如果你不想通过实体类操作，或者说实体类满足不了你的需求，你可以自己编写`sql`语句，以字符串的格式传入上面五种方法中传实体类的位置</h5>
        <pre>
        JDbHandle dbHandle = new JDbHandle(false);
        dbHandle.insert("INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)");
        dbHandle.delete("DELETE FROM 表名称 WHERE 列名称 = 值 AND 列名称 = 值");
        //记住更新方法如果使用实体类得传第二个参数，即你需要使用哪些字段名称作为更新条件，可多个条件，中间用逗号隔开，作为字符串传入即可;这里直接写sql不需要传
        dbHandle.update("UPDATE 表名称 SET 更新列名称 = 新值 WHERE 条件列名称 = 条件值");
        dbHandle.select("SELECT 列名称 FROM 表名称 WHERE 条件列名称 = 条件值");
        dbHandle.selectPage("SELECT 列名称 FROM 表名称 WHERE 条件列名称 = 条件值",1,10);//注意这里传参的sql语句结尾不要加封号
        </pre>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8、工具类 `JTool`</h5>
        <pre>
 	//使用它可以直接获取一个uuid的值，返回类型为String。
	JTool.getUuid()
	//使用它可以直接获取当前系统时间，返回类型为String。
	JTool.getDate()
	//使用它可以将`JsonArray`对象解析对应的List，你需要传入 JsonArray 和 Class<T>
	//JsonArray 就是你要解析的json数组，Class<T> 为你要解析的实体类。返回类型为 List<指定的实体类>。
	JTool.arseJsonArrayToList(JsonArray jsonArray,Class<T> classT)
	//使用它可以将`JsonArray`对象解析对应的List，你需要传入 JsonArray 和 Class<T>
	//JsonArray 就是你要解析的json数组，Class<T> 为你要解析的实体类。返回类型为  指定的实体类。
	JTool.parseJsonArrayToObj(JsonArray jsonArray,Class<T> classT) 
        </pre>
        </body>
</html>
