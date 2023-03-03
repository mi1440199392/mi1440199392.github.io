# 前言

<font face="幼圆">

!> 封装`DTO`对象数据单位转换工具类

</font>

# 数据单位转换工具类 : 通过 map 去标识需要转换的类属性字段

```java
package com.alibaba.frame.a;
import lombok.Data;
import org.springframework.util.CollectionUtils;
import java.io.Serializable;
import java.lang.reflect.Field;
import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Objects;
import java.util.function.Supplier;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 数据单位转换工具类 : 通过 map 去标识需要转换的类属性字段
 */
public class A_117 {

	// 初始化数据
	private static final List<ProductOrder> orders = new ArrayList<>(10);

	static {
		final ProductOrder order1 = new ProductOrder();
		order1.setPayTotalAmount(new BigDecimal(11000000));
		order1.setAmountPercentage(BigDecimal.valueOf(0.596));
		order1.setCountPerMilliAge(BigDecimal.valueOf(0.7894));
		order1.setLength(BigDecimal.valueOf(1300.65112));
		order1.setWidth(BigDecimal.valueOf(6522.12344));

		final ProductOrder order2 = new ProductOrder();
		order2.setPayTotalAmount(new BigDecimal(2390000));
		order2.setAmountPercentage(BigDecimal.valueOf(0.885));
		order2.setCountPerMilliAge(BigDecimal.valueOf(0.2394));
		order2.setLength(BigDecimal.valueOf(1700.64003));
		order2.setWidth(BigDecimal.valueOf(7522.12344));

		orders.add(order1);
		orders.add(order2);
	}

	public static void main(String[] args) {

		// 初始化配置，标记需要转换的类属性字段
		AmountConvertUtil.initConvertConfig(() -> {
			final Map<String, AmountConvertEnum> amountConvertEnumMap = new HashMap<>(16);
			amountConvertEnumMap.put("payTotalAmount", AmountConvertEnum.B);
			amountConvertEnumMap.put("amountPercentage", AmountConvertEnum.PERCENTAGE);
			amountConvertEnumMap.put("countPerMilliAge", AmountConvertEnum.PER_MILLI);
			amountConvertEnumMap.put("length", AmountConvertEnum.R);
			amountConvertEnumMap.put("width", AmountConvertEnum.R);
			return amountConvertEnumMap;
		});

		System.out.println("数据单位转换前 = " + orders);
		AmountConvertUtil.amountConvert(orders);
		System.out.println("数据单位转换后 = " + orders);
	}

	// 订单类
	@Data
	static class ProductOrder implements Serializable {
		private static final long serialVersionUID = -1L;
		// 支付总金额
		private BigDecimal payTotalAmount;
		// 金额百分比
		private BigDecimal amountPercentage;
		// 计数千分比
		private BigDecimal countPerMilliAge;
		// 保留 2 位
		private BigDecimal length;
		// 保留 2 位
		private BigDecimal width;
	}

	// 枚举：数据单位转换格式
	enum AmountConvertEnum {

		R, // 精度
		B, // 万元
		PERCENTAGE, // 百分
		PER_MILLI; // 千分
	}

	// 数据单位转换工具类
	static class AmountConvertUtil {
		private static Map<String, AmountConvertEnum> amountConvertEnumMap = new HashMap<>(16);

		public static void initConvertConfig(Supplier<Map<String, AmountConvertEnum>> supplier) {
			amountConvertEnumMap = Collections.synchronizedMap(supplier.get());
		}

		public static <T> void amountConvert(List<T> source) {
			if (CollectionUtils.isEmpty(source)) return;

			for (T obj : source) {
				if (Objects.isNull(obj)) continue;

				final Field[] fields = obj.getClass().getDeclaredFields();
				for (Field field : fields) {
					if (!amountConvertEnumMap.containsKey(field.getName())) continue;
					if ("serialVersionUID".equals(field.getName())) continue;

					try {
						field.setAccessible(true);
						final Object value = field.get(obj);
						final AmountConvertEnum amountConvertEnum = amountConvertEnumMap.get(field.getName());
						switch (amountConvertEnum) {
							// 百分
							case PERCENTAGE:
								final BigDecimal decimal1 = ((BigDecimal) value).multiply(new BigDecimal(100)).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal1);
								break;

							// 千分
							case PER_MILLI:
								final BigDecimal decimal2 = ((BigDecimal) value).multiply(new BigDecimal(1000)).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal2);
								break;

							// 万元
							case B:
								final BigDecimal decimal3 = ((BigDecimal) value).divide(new BigDecimal(10000), 2, RoundingMode.HALF_UP);
								field.set(obj, decimal3);
								break;

							// 精度
							case R:
								final BigDecimal decimal4 = ((BigDecimal) value).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal4);
								break;
							default:
								break;
						}
					} catch (IllegalAccessException e) {
						e.printStackTrace();
					}
				}
			}
		}
	}
}
```

<font face="幼圆">

!> 控制台输出

</font>

```text
数据单位转换前 = [A_117.ProductOrder(payTotalAmount=11000000, amountPercentage=0.596, countPerMilliAge=0.7894, length=1300.65112, width=6522.12344), A_117.ProductOrder(payTotalAmount=2390000, amountPercentage=0.885, countPerMilliAge=0.2394, length=1700.64003, width=7522.12344)]
数据单位转换后 = [A_117.ProductOrder(payTotalAmount=1100.00, amountPercentage=59.60, countPerMilliAge=789.40, length=1300.65, width=6522.12), A_117.ProductOrder(payTotalAmount=239.00, amountPercentage=88.50, countPerMilliAge=239.40, length=1700.64, width=7522.12)]
```

# 数据单位转换工具类 : 通过自定义注解 @interface 标识需要转换的类属性字段

```java
package com.alibaba.frame.a;
import lombok.Data;
import org.springframework.util.CollectionUtils;
import java.io.Serializable;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Field;
import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 数据单位转换工具类 : 通过自定义注解 @interface 标识需要转换的类属性字段
 */
public class A_118 {

	// 初始化数据
	private static final List<ProductOrder> orders = new ArrayList<>(10);

	static {
		final ProductOrder order1 = new ProductOrder();
		order1.setPayTotalAmount(new BigDecimal(11000000));
		order1.setAmountPercentage(BigDecimal.valueOf(0.596));
		order1.setCountPerMilliAge(BigDecimal.valueOf(0.7894));
		order1.setLength(BigDecimal.valueOf(1300.65112));
		order1.setWidth(BigDecimal.valueOf(6522.12344));

		final ProductOrder order2 = new ProductOrder();
		order2.setPayTotalAmount(new BigDecimal(2390000));
		order2.setAmountPercentage(BigDecimal.valueOf(0.885));
		order2.setCountPerMilliAge(BigDecimal.valueOf(0.2394));
		order2.setLength(BigDecimal.valueOf(1700.64003));
		order2.setWidth(BigDecimal.valueOf(7522.12344));

		orders.add(order1);
		orders.add(order2);
	}

	public static void main(String[] args) {

		System.out.println("数据单位转换前 = " + orders);
		AmountConvertUtil.amountConvert(orders);
		System.out.println("数据单位转换后 = " + orders);
	}

	// 订单类
	@Data
	static class ProductOrder implements Serializable {
		private static final long serialVersionUID = -1L;

		// 支付总金额
		@AmountConvert(format = AmountConvertEnum.B)
		private BigDecimal payTotalAmount;

		// 金额百分比
		@AmountConvert(format = AmountConvertEnum.PERCENTAGE)
		private BigDecimal amountPercentage;

		// 计数千分比
		@AmountConvert(format = AmountConvertEnum.PER_MILLI)
		private BigDecimal countPerMilliAge;

		// 保留 2 位
		@AmountConvert(format = AmountConvertEnum.R)
		private BigDecimal length;

		// 保留 2 位
		@AmountConvert(format = AmountConvertEnum.R)
		private BigDecimal width;
	}

	// 枚举：数据单位转换格式
	enum AmountConvertEnum {

		R, // 精度
		B, // 万元
		PERCENTAGE, // 百分
		PER_MILLI; // 千分
	}

	// 注解：标识数据单位转换的类属性字段
	@Target(ElementType.FIELD)
	@Retention(RetentionPolicy.RUNTIME)
	@interface AmountConvert {

		AmountConvertEnum format();
	}


	// 数据单位转换工具类
	static class AmountConvertUtil {

		public static <T> void amountConvert(List<T> source) {
			if (CollectionUtils.isEmpty(source)) return;

			for (T obj : source) {
				if (Objects.isNull(obj)) continue;

				final Field[] fields = obj.getClass().getDeclaredFields();
				for (Field field : fields) {
					final AmountConvert annotation = field.getAnnotation(AmountConvert.class);
					if (Objects.isNull(annotation)) continue;
					final AmountConvertEnum amountConvertEnum = annotation.format();

					try {
						field.setAccessible(true);
						final Object value = field.get(obj);
						switch (amountConvertEnum) {
							// 百分
							case PERCENTAGE:
								final BigDecimal decimal1 = ((BigDecimal) value).multiply(new BigDecimal(100)).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal1);
								break;

							// 千分
							case PER_MILLI:
								final BigDecimal decimal2 = ((BigDecimal) value).multiply(new BigDecimal(1000)).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal2);
								break;

							// 万元
							case B:
								final BigDecimal decimal3 = ((BigDecimal) value).divide(new BigDecimal(10000), 2, RoundingMode.HALF_UP);
								field.set(obj, decimal3);
								break;

							// 精度
							case R:
								final BigDecimal decimal4 = ((BigDecimal) value).setScale(2, RoundingMode.HALF_UP);
								field.set(obj, decimal4);
								break;
							default:
								break;
						}
					} catch (IllegalAccessException e) {
						e.printStackTrace();
					}
				}
			}
		}
	}
}
```

<font face="幼圆">

!> 控制台输出

</font>

```text
数据单位转换前 = [A_118.ProductOrder(payTotalAmount=11000000, amountPercentage=0.596, countPerMilliAge=0.7894, length=1300.65112, width=6522.12344), A_118.ProductOrder(payTotalAmount=2390000, amountPercentage=0.885, countPerMilliAge=0.2394, length=1700.64003, width=7522.12344)]
数据单位转换后 = [A_118.ProductOrder(payTotalAmount=1100.00, amountPercentage=59.60, countPerMilliAge=789.40, length=1300.65, width=6522.12), A_118.ProductOrder(payTotalAmount=239.00, amountPercentage=88.50, countPerMilliAge=239.40, length=1700.64, width=7522.12)]
```

---

<font face="幼圆">

!> 水平有限，继续努力!!!

</font>
