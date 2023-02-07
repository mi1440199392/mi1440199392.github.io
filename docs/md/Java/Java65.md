```java 
package com.hundsun.frameworlk.http.config;

import java.util.Map;
import java.util.Objects;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 模拟定期刷新缓存
 */
public class Main {
	private static final Map<String, String> dictMap = new ConcurrentHashMap<>(3);
	private static final AtomicInteger number = new AtomicInteger(0);

	public static void main(String[] args) {

		// 刷新缓存
		final ScheduledExecutorService scheduled = Executors.newScheduledThreadPool(1);
		scheduled.scheduleWithFixedDelay(() -> {
			System.out.println("定期刷新缓存...");
			dictMap.put("city", RedisClient.defaultClient.getValue() + "-" + number.incrementAndGet());
		}, 2, 5, TimeUnit.SECONDS);


		// 获取缓存
		final ScheduledExecutorService scheduled2 = Executors.newScheduledThreadPool(1);
		scheduled2.scheduleWithFixedDelay(() -> System.out.println("获取缓存数据 = " + Main.cache()), 1, 1, TimeUnit.SECONDS);
	}

	public static String cache() {
		String city = dictMap.get("city");
		if (Objects.isNull(city)) {
			city = RedisClient.defaultClient.getValue();
			dictMap.put("city", city);
		}
		return city;
	}

	@FunctionalInterface
	interface RedisClient {
		RedisClient defaultClient = () -> "浙江省-杭州市-滨江区-长河街道-闻涛诚苑";

		String getValue();
	}
}
```

```text
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑
定期刷新缓存...
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-1
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-1
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-1
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-1
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-1
定期刷新缓存...
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-2
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-2
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-2
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-2
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-2
定期刷新缓存...
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-3
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-3
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-3
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-3
获取缓存数据 = 浙江省-杭州市-滨江区-长河街道-闻涛诚苑-3
```
