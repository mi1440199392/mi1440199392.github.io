```java
package com.hundsun.frameworlk.a;
import java.io.Serializable;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.HashMap;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @deprecated 《Mybatis手撸专栏》第2章：创建简单的映射器代理工厂
 */
@SuppressWarnings("all")
public class A_102 {
	public static void main(String[] args) {

		MapperProxyFactory<IUserDao> mapperProxyFactory = new MapperProxyFactory<>(IUserDao.class);
		Map<String, String> sqlSession = new HashMap<>(16);
		sqlSession.put("com.hundsun.frameworlk.a.A_102$IUserDao.queryUserName", "模拟执行 Mapper.xml 中 SQL 语句的操作：查询用户姓名");
		sqlSession.put("com.hundsun.frameworlk.a.A_102$IUserDao.queryUserAge", "模拟执行 Mapper.xml 中 SQL 语句的操作：查询用户年龄");
		IUserDao userDao = mapperProxyFactory.newInstance(sqlSession);

		System.out.println("userDao.queryUserName = " + userDao.queryUserName("1001"));
		System.out.println("userDao.queryUserAge = " + userDao.queryUserAge("1002"));

	}

	// 映射器代理类
	static class MapperProxy<T> implements InvocationHandler, Serializable {

		private static final long serialVersionUID = -6849794470754667710L;
		private Map<String, String> sqlSession;
		private Class<T> mapperInterface;

		public MapperProxy(Map<String, String> sqlSession, Class<T> mapperInterface) {
			this.sqlSession = sqlSession;
			this.mapperInterface = mapperInterface;
		}

		@Override
		public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

			// 注意如果是 Object 提供的 toString、hashCode 等方法是不需要代理执行的，所以添加 Object.class.equals(method.getDeclaringClass()) 判断
			if (Object.class.equals(method.getDeclaringClass())) {
				return method.invoke(this, args);
			}

			return "你被代理了！" + sqlSession.get(mapperInterface.getName() + "." + method.getName());
		}
	}

	// 代理类工厂
	static class MapperProxyFactory<T> {

		private Class<T> mapperInterface;

		public MapperProxyFactory(Class<T> mapperInterface) {
			this.mapperInterface = mapperInterface;
		}

		public T newInstance(Map<String, String> sqlSession) {
			final MapperProxy<T> mapperProxy = new MapperProxy<>(sqlSession, mapperInterface);
			return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[]{mapperInterface}, mapperProxy);
		}
	}

	interface IUserDao {

		String queryUserName(String uId);

		String queryUserAge(String uId);
	}
}
```

> 控制台

```shell
userDao.queryUserName = 你被代理了！模拟执行 Mapper.xml 中 SQL 语句的操作：查询用户姓名
userDao.queryUserAge = 你被代理了！模拟执行 Mapper.xml 中 SQL 语句的操作：查询用户年龄
```
