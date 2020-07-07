<html>
        <body>
        <h1>jSqlHandle（让初学者不再迷茫）</h1>
        <h2>zhuzhengjundev</h2>
        <img src="https://qlogo1.store.qq.com/qzone/1471888328/1471888328/100?1590622011">
        <h3>简介：</h3>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你只需要引入一个jar包，配置数据库信息之后，然后准备一个实体类传入对应的方法中，即可对数据表进行增，删，改，查，分页查，当然，你也可以写sql语句传入方法中。除此之外，你还可以使用事务，在对所有sql操作成功之后一起提交事务即操作成功，否则回滚掉事务，让之前的sql操作失效。
        </p>
        <h4>一：来看看如何配置吧！</h4>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① 配置的话很简单，你只需要把jSqlHandle.jar包放在对应的lib目录下，然后在WebContent目录下新建一个文件夹名为：config，然后在config文件夹里新建一个dbconfig.properties文件，切记文件的名字和后缀名要一模一样，否则连接不上数据库。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② 在上述正确的路径下新建文件夹和文件之后，你需要在dbconfig.properties文件里编写你的数据库配置内容，需要四个变量，分别为driverClassName(驱动)、db_url(数据库地址)、db_name(数据库连接名)、db_password(数据库连接密码)。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;实例：</p>
        <p>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                driverClassName=com.mysql.cj.jdbc.Driver<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                db_url=jdbc:mysql://localhost:3306/my_store?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                db_name=root<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                db_password=123456<br>
        </p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你可以将以上四行代码复制粘贴到你新建的dbconfig.properties文件中，然后修改后面对应你数据库信息的值</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最后看一下配置的截图吧！</p><br>
        <img src="http://a1.qpic.cn/psc?/V100BnzP2hNMuE/qWkkGOdvaHRbO0s2SzmJ0NRgdj09ekusZlZr8Kz*SRJhLXbkGm6MNPTJ0BVt5B*Yal*NqsTMe8FGjIHkptXzxQ!!/c&ek=1&kp=1&pt=0&bo=iwWQAgAAAAADFy4!&tl=1&vuin=1471888328&tm=1594108800&sce=60-2-2&rf=0-0">
        <h4>二：来看看使用说明吧！</h4>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1、使用该jar包你只需要知道两个类，JDbHandle和JResponse；</h5>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① JDbHandle是主要的操作数据表的类，只有它可操作数据表，来说说怎么使用吧，首先去new一个它，这个不用多说吧，不过在new它的同时，你得传入一个参数-（Boolean isAutoCommit），看名字和类型应该可以猜出大概吧，是否自动提交。当传入的参数为true，即你开启了一个事务，在你对数据表操作的增删改等事情之后，它其实都没有真正生效，之后当你操作完所有逻辑之后，再调用该类的commit方法，即可生效，不过你也可以选择调用rollback方法，即把之前的增删改全部撤回，一般只有当同时操作有关联的多张表时才会传true，否则传入false就好，当传入false，即你对数据表操作的任何操作都是即时生效的，你也不需要在操作完逻辑之后调用commit或者rollback方法。不过最后要说的是，不过传入的是true还是false，就算你只new了一次它，没有调用里面的方法，你也需要调用一个方法，那就是stopConn，即关闭数据库连接，因为你每new一个JDbHandle类，即连接数据库一次。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② JResponse类，它是JDbHandle类里面所有增删改查方法都会返回的一个类，你只需要在调用JDbHandle类的方法之后去用一个JResponse类型变量接收即可。该类里面有四个成员变量：int state//状态、String message(信息)、JsonArray data(数据)、JPageData pageData(分页查询数据)，当然这里的data和pageData变量不会同时出现，data是普通查询即查询所有数据时用它接收，pageData是分页查询时用它接收。当然，pageData里面又有四个成员变量：int pageIndex(当前页)、int pageSize(每页数量)、int pageAll(总页数)、JsonArray data(数据)；</p>
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
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3、如何插入（insert）</h5>
        <p>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JDbHandle dbHandle = new JDbHandle(false);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //该实体类后面的参数作为插入的字段数据值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                Users users = new Users("value2","value3","value4","value5");<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JResponse jResponse = dbHandle.insert(users);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getState() //拿到状态值，大于0即插入成功，等于0即插入失败<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getMessage() //拿到中文提示信息<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                dbHandle.stopConn();
        </p>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4、如何删除（dalete）</h5>
        <p>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JDbHandle dbHandle = new JDbHandle(false);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //该实体类后面的参数作为删除的字段条件值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                Users users = new Users("value2","value3","value4","value5");<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JResponse jResponse = dbHandle.dalete(users);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getState() //拿到状态值，大于0即删除成功，等于0即删除失败<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getMessage() //拿到中文提示信息<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                dbHandle.stopConn();
        </p>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5、如何更新（update）</h5>
        <p>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JDbHandle dbHandle = new JDbHandle(false);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //该实体类后面的参数作为更新的字段数据值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                Users users = new Users("value2","value3","value4","value5");<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //不过在调用更新方法的时候需要多传入一个参数，即你需要使用哪些字段名称作为更新条件，可多个条件，中间用逗号隔开，作为字符串传入即可<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //当然，如果你直接写slq字符串，就不需要传入第二个参数<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JResponse jResponse = dbHandle.update(users,"userName,userPwd");<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getState() //拿到状态值，大于0即更新成功，等于0即更新失败<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getMessage() //拿到中文提示信息<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                dbHandle.stopConn();
        </p>
        <h5>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6、如何查询（select）</h5>
        <p>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JDbHandle dbHandle = new JDbHandle(false);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                //该实体类后面的参数作为查询的字段条件值(注：切记该实体类的类名和成员变量名字要与数据表对应，不能缺少或添加字段，不区分大小写)<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                Users users = new Users("value2","value3","value4","value5");<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                JResponse jResponse = dbHandle.select(users);<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getState() //拿到状态值，大于0即查询成功，等于0即查询的数据为空或查询失败<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getMessage() //拿到中文提示信息<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                jResponse.getData() //拿到查询的数据(该数据为jsonArray格式，你也可以使用gson将它转成List<obj> )<br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                dbHandle.stopConn();
        </p>
        </body>
</html>
