#响应消息数据格式



## Request
	1. request对象和response对象的原理
		1. request和reponse都是由服务器创建的.我们来使用它们
		2. request对象是用来获取请求消息的,response对象是来设置响应消息
	2. request对象继承体系结构:
		ServletRequest --接口
			| 继承
		HttpServletRequest --接口
			|实现
		org.apache.catalina.connector.RequestFacade类(tomcat)
	3. request :获取请求消息

	4. 其他功能:
			1.获取请求参数通用方式:不论get还是post请求方式都可以使用下列方法来获取请求参数
				1. string getParameter(string name ) :根据参数名称获取参数值username=zs&password=123
				2. String[] getParametervalues(String name) :根据参数名称获取参数值的数组hobby=xx&hobby=game
				3. Enumeration<string> getParameterNames() :获取所有请求的参数名称
				4. Map<string , String[]> getParameterMap() :获取所有参数的map集合
			*中文乱码问题:
				* get方式: tomcat 8已经将get方式乱码问题解决了
				post方式:会乱码
				*解决:在获取参数前，设irequest的编码
					request.setCharacterEncoding("utf-8");
			2.请求转发: 一种在服务器内部的资源跳转方式
				1. 步骤:
					1. 通过request对象获取请求转发器对象:  
						RequestDispatcher getRequestDispatcher(string path)
					2. 使用RequestDispatcher对象来进行转发: 
						forward(ServletRequest request, ServletResponse response)
				2. 特点:
					1. 浏览器地址栏路径不发生变化
					2. 只能转发到当前服务器内部资源中。
					3. 转发是一次请求
			3.共享数据: 
				*域对象:一个有作用范围的对象，可以在范围内共享数据
				request域:代表一次请求的范围，一 般用于请 求转发的多个资源中共享数据
				*方法:
					1. void setAttribute(String name ,object obj) :存储数据
					2. object getAttitude(String name ) :通过键获取值
					3. void removeAttribute(String name) :通过键移除键值对
			4. 获取ServletContext :
				  ServletContext getServletContext()
				
##Response
###响应消息:服务器端发送给客户端的数据
###数据格式:	
	1. 响应行
		1. 组成:协议/版本响应状态码状态码描述
		2. 响应状态码:服务器告诉客户端浏览器本次请求和响应的一个状态。
			1. 状态码都是3位数字
			2. 分类:
				1. 1xx :服务器就收客户端消息，但没有接受完成,等待一段时间后,发送1xx多状态码
				2. 2xx:成功。代表:200
				3. 3xx:重定向。代表: 302(重定向) 304(访问缓存)
				4. 4xx 客户端错误 代表:404(请求路径没有对应的资源),405(请求方式没有对应的方法)
				5. 5xx 服务端错误	 代表:500(服务器内部异常)
	2. 响应头
		1. 格式:头名称: 值
		2. 常见的响应头:
			1. Content-type:服务器告诉客户端本次响应体数据格式以及编码格式
			2. Content-disposition :服务器告诉客户端以什么格式打开响应体数据
					*值:
						 in-line:默认值,在当前页面内打开
						attachment;filename=xxxx :以附件形式打开响应体
	3. 响应空行
	4. 响应体:真实传输的数据