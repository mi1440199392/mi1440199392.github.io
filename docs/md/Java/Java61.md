```java
package com.hundsun.frameworlk.a;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 解析注解给对象属性设值
 */
@SuppressWarnings("all")
public class A_103 {

	public static void main(String[] args) throws Exception {

		Class<ObjectCopy> clazz = ObjectCopy.class;
		ObjectCopy instance = clazz.newInstance();
		Field[] fields = clazz.getDeclaredFields();

		for (Field field : fields) {
			FieldFormatBuilder.builder(field, instance).format();
		}

		System.out.println("instance = " + instance);

	}

	@AllArgsConstructor
	@NoArgsConstructor
	static enum ClassType {

		INT(int.class),
		STRING(String.class);

		private Class type;

		public Class getType() {
			return type;
		}

		public void setType(Class type) {
			this.type = type;
		}
	}

	// 属性格式化类构造者器
	@AllArgsConstructor
	static class FieldFormatBuilder {

		private static List<FieldFormat> fieldFormats = new ArrayList<>(10);

		public static FieldFormatBuilder builder(Field field, Object tagertObject) {
			fieldFormats.add(new NumberFormat(field, tagertObject));
			fieldFormats.add(new NameFormat(field, tagertObject));
			return new FieldFormatBuilder();
		}

		public void format() throws IllegalAccessException {
			for (FieldFormat fieldFormat : fieldFormats) {
				fieldFormat.format();
			}
		}
	}


	interface FieldFormat {

		boolean isType();

		void format() throws IllegalAccessException;
	}

	@AllArgsConstructor
	static abstract class AbstractFieldFormat implements FieldFormat {

		protected Field field;
		protected Object tagertObject;
	}


	static class NumberFormat extends AbstractFieldFormat {

		public NumberFormat(Field field, Object tagertObject) {
			super(field, tagertObject);
		}

		@Override
		public boolean isType() {
			return field.getType().equals(ClassType.INT.getType());
		}

		@Override
		public void format() throws IllegalAccessException {
			if (isType()) {
				field.setAccessible(true);
				Numbers numbers = field.getAnnotation(Numbers.class);
				if (numbers != null) {
					field.set(tagertObject, numbers.value());
				}
			}
		}
	}

	static class NameFormat extends AbstractFieldFormat {

		public NameFormat(Field field, Object tagertObject) {
			super(field, tagertObject);
		}

		@Override
		public boolean isType() {
			return field.getType().equals(ClassType.STRING.getType());
		}

		@Override
		public void format() throws IllegalAccessException {
			if (isType()) {
				field.setAccessible(true);
				Names names = field.getAnnotation(Names.class);
				if (names != null) {
					field.set(tagertObject, names.value());
				}
			}
		}
	}


	// 自定义枚举Numbers
	@Retention(value = RetentionPolicy.RUNTIME)
	@Target(value = {ElementType.FIELD})
	@interface Numbers {

		int value() default 0;
	}


	// 自定义枚举Names
	@Retention(value = RetentionPolicy.RUNTIME)
	@Target(value = {ElementType.FIELD})
	@interface Names {

		String value() default "张三";
	}

	@Data
	@ToString
	@NoArgsConstructor
	static class ObjectCopy {

		@Numbers(value = 1001)
		private int id;

		@Names(value = "李四")
		private String name;
	}
}
```

> 控制台

```shell
instance = A_103.ObjectCopy(id=1001, name=李四)
```
