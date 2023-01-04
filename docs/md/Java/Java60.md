```java
package com.hundsun.frameworlk.a;
import java.io.Serializable;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 《Mybatis手撸专栏》第3章：实现映射器的注册和使用
 */
@SuppressWarnings("all")
public class A_102 {
	public static void main(String[] args) {

		// 1.注册Mapper
		MapperRegistry mapperRegistry = new MapperRegistry();
		mapperRegistry.addMapper(IUserDao.class);

		// 2.从SqlSession工厂获取Session
		DefaultSqlSessionFactory sqlSessionFactory = new DefaultSqlSessionFactory(mapperRegistry);
		SqlSession sqlSession = sqlSessionFactory.openSession();

		// 3. 获取映射器对象
		IUserDao userDao = sqlSession.getMapper(IUserDao.class);
		System.out.println("userDao.queryUserName = " + userDao.queryUserName("1001"));

	}

	// 映射器注册机
	static class MapperRegistry {

		// 将已添加的映射器代理加入到 HashMap
		private final Map<Class<?>, MapperProxyFactory<?>> mapperProxyFactorys = new HashMap<>();

		// 获取映射器
		public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
			final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) mapperProxyFactorys.get(type);
			if (mapperProxyFactory == null) {
				throw new RuntimeException("Type " + type + " is not known to the MapperRegistry.");
			}
			try {
				return mapperProxyFactory.newInstance(sqlSession);
			} catch (Exception e) {
				throw new RuntimeException("Error getting mapper instance. Cause: " + e, e);
			}
		}

		// 注册映射器
		public <T> void addMapper(Class<T> type) {

			// Mapper 必须是接口才会注册
			if (type.isInterface()) {
				if (hasMapper(type)) {
					// 如果重复添加了，报错
					throw new RuntimeException("Type " + type + " is already known to the MapperRegistry.");
				}
				// 注册映射器代理工厂
				mapperProxyFactorys.put(type, new MapperProxyFactory<>(type));
			}
		}

		private <T> boolean hasMapper(Class<T> type) {
			return mapperProxyFactorys.get(type) != null;
		}
	}

	// SqlSession标准定义
	interface SqlSession {

		/**
		 * 根据指定的SqlID获取一条记录的封装对象，只不过这个方法容许我们可以给sql传递一些参数
		 * 一般在实际使用中，这个参数传递的是pojo，或者Map或者ImmutableMap
		 *
		 * @param <T>
		 * @param statement
		 * @param parameter
		 * @return
		 */
		<T> T selectOne(String statement, Object parameter);

		/**
		 * 得到映射器，这个巧妙的使用了泛型，使得类型安全
		 *
		 * @param <T>
		 * @param type
		 * @return
		 */
		<T> T getMapper(Class<T> type);
	}

	// SqlSession标准实现
	static class DefaultSqlSession implements SqlSession {

		/**
		 * 映射器注册机
		 */
		private MapperRegistry mapperRegistry;

		public DefaultSqlSession(MapperRegistry mapperRegistry) {
			this.mapperRegistry = mapperRegistry;
		}

		@Override
		public <T> T selectOne(String statement, Object parameter) {
			String parameterArrayToString = "";
			if (parameter.getClass().isArray()) {
				parameterArrayToString = Arrays.toString((Object[]) parameter);
			}

			return (T) ("你被代理了！ 方法名称：" + statement + "参数：" + parameterArrayToString);
		}

		@Override
		public <T> T getMapper(Class<T> type) {
			return mapperRegistry.getMapper(type, this);
		}
	}

	// SqlSessionFactory工厂定义
	interface SqlSessionFactory {

		/**
		 * 打开一个 session
		 *
		 * @return
		 */
		SqlSession openSession();
	}

	// SqlSessionFactory工厂实现
	static class DefaultSqlSessionFactory implements SqlSessionFactory {

		private MapperRegistry mapperRegistry;

		public DefaultSqlSessionFactory(MapperRegistry mapperRegistry) {
			this.mapperRegistry = mapperRegistry;
		}

		@Override
		public SqlSession openSession() {
			return new DefaultSqlSession(mapperRegistry);
		}
	}

	// 映射器代理类
	static class MapperProxy<T> implements InvocationHandler, Serializable {

		private static final long serialVersionUID = -6849794470754667710L;
		private SqlSession sqlSession;
		private Class<T> mapperInterface;

		public MapperProxy(SqlSession sqlSession, Class<T> mapperInterface) {
			this.sqlSession = sqlSession;
			this.mapperInterface = mapperInterface;
		}

		@Override
		public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

			// 注意如果是 Object 提供的 toString、hashCode 等方法是不需要代理执行的，所以添加 Object.class.equals(method.getDeclaringClass()) 判断
			if (Object.class.equals(method.getDeclaringClass())) {
				return method.invoke(this, args);
			}

			return sqlSession.selectOne(method.getName(), args);
		}
	}

	// 代理类工厂
	static class MapperProxyFactory<T> {

		private Class<T> mapperInterface;

		public MapperProxyFactory(Class<T> mapperInterface) {
			this.mapperInterface = mapperInterface;
		}

		public T newInstance(SqlSession sqlSession) {
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
userDao.queryUserName = 你被代理了！ 方法名称：queryUserName参数：[1001]
userDao.queryUserAge = 你被代理了！ 方法名称：queryUserAge参数：[1002]
```
