<html>
        <body>
        <h1>jSqlHandle（让初学者不再迷茫）</h1>
        <h3>zhuzhengjundev</h3>
        <img style="-webkit-user-select: none;margin: auto;" src="https://qlogo1.store.qq.com/qzone/1471888328/1471888328/100?1590622011">
        <h5>简介：</h5>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你只需要引入一个jar包，配置数据库信息之后，然后准备一个实体类传入对应的方法中，即可对数据表进行增，删，改，查，分页查，当然，你也可以写sql语句传入方法中。除此之外，你还可以使用事务，在对所有sql操作成功之后一起提交事务即操作成功，否则回滚掉事务，让之前的sql操作失效。
        </p>
        <h5>一：来看看如何配置吧！</h5>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1、配置的话很简单，你只需要把jSqlHandle.jar包放在对应的lib目录下，然后在WebContent目录下新建一个文件夹名为：config，然后在config文件夹里新建一个dbconfig.properties文件，切记文件的名字和后缀名要一模一样，否则连接不上数据库。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2、在上述正确的路径下新建文件夹和文件之后，你需要在dbconfig.properties文件里编写你的数据库配置内容，需要四个变量，分别为driverClassName(驱动)、db_url(数据库地址)、db_name(数据库连接名)、db_password(数据库连接密码)。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;实例：</p>
        <p>     &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <h3>driverClassName=com.mysql.cj.jdbc.Driver</h3><br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <h3>db_url=jdbc:mysql://localhost:3306/my_store?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC</h3><br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <h3>db_name=root</h3><br>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <h3>db_password=123456</h3><br>
        </p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;你可以将以上四行代码复制粘贴到你新建的dbconfig.properties文件中，然后修改后面对应你数据库信息的值</p>
        <h5>二：来看看说明吧！</h5>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1、使用该jar包你只需要知道两个类，JDbHandle和JResponse；</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;① JDbHandle是主要的操作数据表的类，只有它可操作数据表，来说说怎么使用吧，首先去new一个它，这个不用多说吧，不过在new它的同时，你得传入一个参数-（Boolean isAutoCommit），看名字和类型应该可以猜出大概吧，是否自动提交。当传入的参数为true，即你开启了一个事务，在你对数据表操作的增删改等事情之后，它其实都没有真正生效，之后当你操作完所有逻辑之后，再调用该类的commit方法，即可生效，不过你也可以选择调用rollback方法，即把之前的增删改全部撤回，一般只有当同时操作有关联的多张表时才会传true，否则传入false就好，当传入false，即你对数据表操作的任何操作都是即时生效的，你也不需要在操作完逻辑之后调用commit或者rollback方法。不过最后要说的是，不过传入的是true还是false，就算你只new了一次它，没有调用里面的方法，你也需要调用一个方法，那就是stopConn，即关闭数据库连接，因为你每new一个JDbHandle类，即连接数据库一次。</p>
        <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;② JResponse类，它是JDbHandle类里面所有增删改查方法都会返回的一个类，你只需要在调用JDbHandle类的方法之后去用一个JResponse类型变量接收即可。该类里面有四个成员变量：int state//状态、String message(信息)、JsonArray data(数据)、JPageData pageData(分页查询数据)，当然这里的data和pageData变量不会同时出现，data是普通查询即查询所有数据时用它接收，pageData是分页查询时用它接收。当然，pageData里面又有四个成员变量：int pageIndex(当前页)、int pageSize(每页数量)、int pageAll(总页数)、JsonArray data(数据)；</p>
        <h6>2、插入（insert）</h6>
        </body>
</html>
