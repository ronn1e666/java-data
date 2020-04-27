##内容
	1. redis
	2. 下载安装
	3. 命令操作
		1. 数据结构  
	4. 持久化操作
	5. 使用java客户端操作redis
		
##redis
	Redis是用c语言开发的一个开源的高性能键值对(key-value)数据库.
	目前为止Redis支持
	1) 字符串类型string
	2) 哈希类型hash
	3) 列表类型list
	4) 集合类型set
	5)有序集合类型sortedset
###redis的应用场景
	缓存(数据查询、短连接、新闻内容、商品内容等等)
	聊天室的在线好友列表
	任务队列。(秒杀、 抢购、12306等等)
	应用排行榜
	网站访问统计
	数据过期处理(可以精确到毫秒)
	分布式集群架构中的session分离

###下载安装
	1. 官网: 
	2. 中文网
	3. 解压直接使用
		1. redis.windows.conf :配置文件
		2. redis-cli.exe :redis的客户端
		3. redis-server.exe :redis服务器

###命令操作
	1. redis的数据结构:
		* redis存储的是: key,value格式的数据， 其中key都是字符串，value有5种不同的数据结构
			* value的数据结构:
			1)字符串类型 string
			2)哈希类型 hash :map格式
			3)列表类型 list: linkedlist格式。 支持重复元素
			4)集合类型 set :不允许重复元素
			5)有序集合类型 sortedset :不允许重复元素，且元素有顺序
	2. 字符串类型 string
		1. 存储(覆盖): set key value
		2. 获取: get key 
		3. 删除: del key

	3. 哈希类型 hash
		1. 存储:hset key field value
		2. 获取:
		   1. 获取指定的filed对应的值 hget key fiel 
		   2. 获取所有的: hgetall key
		3. 删除:hdel key field
	
	4. 列表类型 list :可以添加一个元素到列表的头部(左边)或者尾部(右边)
		1. 添加:
			1. lpush key value 将元素加入列表头部
			2. rpush key value 将元素加入列表尾部
		2. 获取: 
			1. lrange key start end :范围获取   
				larange mylist 0 -1 查看所有  
		3. 删除:
			1. lpop key: 删除列表头部的元素,并将元素返回
			2. rpop key: 删除列表尾部的元素,并将元素返回
	
	5. 集合类型set :不允许重复元素
		1. 存储: sadd key value
		2. 获取: smembers key: 获取set集合中所有元素
		3. 删除: srem key value:删除set集合中的某个元素
	
	6. 有序集合类型 sortedset :不允许重复元素，且元素有顺序
		1. 存储: zadd key score value 
		2. 获取: zrange key start end
				例: zrange mysort 0 -1 withscores
		3. 删除: zrem key value
### 通用操作
	1. keys  * :查询所有的键
	2. type key :获取键对应的value的类型
	3. del key :删除指定的key value
###持久化
	1. redis是一个内存数据库，当redi s服务器重后，获取电脑重后，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。
	2. redis持久化机制:
		1. RDB :默认方式，不需要进行配置，默认就使用这种机制
			*在一定的间隔时间中，检测key的变化情况，然后持久化数据
			1. 编辑redis . wi ndwos . conf文件
				after 900 sec (15 min) if at least 1 key changed
					save 900 1
				after 300 sec (5 min) if at least 10 keys changed
					save 300 10
				after 60 sec if at least 10000 keys changed
					save 60 10000
			2. 重新启动redis服务器，并指定配置文件名称
				D: redis-2.8.9> redis-server.exe  redis.windows.conf
		2. AOF :日志记录的方式,可以记录每-条命令的操作。可以每一次命令操作后，持玖化数据
			1. 编辑redis. windwos. conf文件
				appendonly no (关闭aof) --> appendonly yes (开启aof)
					# appendfsync always :每一次操作都进行持久化
					 appendfsync everysec :每隔一秒进行一次持久化
					# appendfsync no : 不进行持久化
 
###java客户端jedis
	*  Jedis: -款java操作redis数据库的工具.
		*使用步骤:
		1. 下载jedis的jar包
		2. 使用
			//1.获取连接
			Jedis jedis = new Jedis("localhost" ,6379);
			//2.操作
			jedis. set("username" ,”zhangsan");


		 Jedis操作各种redis中的数据结构
			1)字符串类型string
				set
				get
				//1.获取连接
					Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
				//2.操作
					//存储
					jedis.set("username" , "zhangsan");
					//获取
					string username = jedis.get("username");
					System.out.println(username);
					//可以使用setex( )方法存储可以指定过期时间的key value
					jedis.setex(" activecode" , 20, "hehe");//将activecode:hehe键值对存入redis,并且20秒后自动删除该键值对
				//3.关闭连接
					jedis. close();
			2)哈希类型hash : map格式
				hset
				hget
				//1.获取连接
					Jedis jedis = new Jedis();//如果使用空参构造,默值"localhost", 6379端口
				//2.操作
				//存储hash
					jedis.hset(  "user", field: "name",   value: "lisi");
					jedis.hset(  "user", field: "age",    value: "23");
					jedis.hset(  "user", field: "gender", value: "fema1e");
				//获取hash
					String name = jedis.hget( "user", field: "name");
					System.out.println(name);
				//获取hash的所有map中的数据
					Map<String，String> user = jedis.hgetA1l(key:"user") ;
				//3.关闭连接
					jedis. close();
			3)列表类型list : linkedlist格式。 支持重复元素
				lpush / rpush
				lpop / rpop

				//1.获取连接
					Jedis jedis = new Jedis();

				//2.操作
				// list存储
					jedis.lpush( "mylist",  "a", "b", "c");//从左边存
					jedis. rpush( "mylist", "a", "b","c");//从右边存
				// list范围获取
					List<String> mylist = jedis.lrange( "mylist",
					start: 0，end: -1);
					System.out.println(mylist);
				// list弹出
					String element1 = jedis.lpop(  "mylist");//c
					System.out.println(element1); 
				//3.关闭连接
					jedis. close();
			
			4)set类型:无序的
				//1.获取连接
					Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost", 6379端口
				//2.操作
				//set存储
					jedis.sadd( "myset",  "java" , "php", "C++");
				// set获取|
					Set<String> myset = jedis.smembers( key: "myset") ;
					System.out.println(myset);
				//3.关闭连接
					jedis.close();

			4)sortedset类型:
				//1.获取连接
					Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost", 6379端口
				//2.操作
				//set存储
					jedis. zadd( "mysortedset", score: 3, member: "亚瑟");
					jedis. zadd( "mysortedset", score: 30，member: "后商");
					jedis. zadd( "mysortedset", score: 25, member: "孙悟空"); 
				// sortedset获取
					Set<String> mysortedset = jedis.zrange("mysortedset"，start: 0，end: -1);
					System. out . println(mysortedset);
				//3.关闭连接
					jedis.close();


###jedis连接池:JedisPool
	调用方法getResource( )方法获取Jedis连接
	//1.创建Jedi s连接池对象
		JedisPoolConfig config = ncw JedisPoolConfig();
		config.setMaxTotal(50);
		config.setMaxIdle(10);
		JedisPool jedisPool = new JedisPool(config, "localhost", 6379);
	//2.获取连接
		Jedis.jedis = jedisPool.getResource();
	//3.使用
		jedis.set("hehe" , "haha");
	//4.关闭归还到连接池中
		jedis.close();

	