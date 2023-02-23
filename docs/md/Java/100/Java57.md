```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

```java
package com.hundsun.demo.pojo.controller;
import lombok.Data;
import org.hibernate.validator.constraints.Length;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;
import javax.validation.Valid;
import javax.validation.constraints.*;

@RestController
@Validated
public class StudentController {

	/**
	 * 参数校验 { 被注释的元素必须是一个数字，其值必须大于等于指定的最小值；被注释的元素必须是一个数字，其值必须大于等于指定的最大值 }
	 *
	 * @param name
	 */
	@GetMapping("/student1/{name}")
	public void getStudentList1(@Length(min = 2, max = 4) @PathVariable("name") String name) {
		System.out.println("已访问该控制器...，参数 = " + name);
	}

	/**
	 * 参数校验 { 被注释的元素必须是 True }
	 *
	 * @param status
	 */
	@GetMapping("/student2/{status}")
	public void getStudentList2(@AssertTrue @PathVariable("status") boolean status) {
		System.out.println("已访问该控制器...，参数 = " + status);
	}

	/**
	 * 参数校验 { 被注释的元素必须是 False }
	 *
	 * @param flag
	 */
	@GetMapping("/student3/{flag}")
	public void getStudentList3(@AssertFalse @PathVariable("flag") boolean flag) {
		System.out.println("已访问该控制器...，参数 = " + flag);
	}

	/**
	 * 参数校验 { 被注释的元素必须是一个数字，其值必须在可接受的范围内 }
	 *
	 * @param number
	 */
	@GetMapping("/student4/{number}")
	public void getStudentList4(@Digits(integer = 2, fraction = 1) @PathVariable("number") Integer number) {
		System.out.println("已访问该控制器...，参数 = " + number);
	}

	/**
	 * 对象校验
	 *
	 * @param student
	 */
	@PostMapping("/student5")
	public void getStudentList5(@Valid @RequestBody Student student) {
		System.out.println("已访问该控制器...，参数 = " + student);
	}

	@Data
	static class Student {
		@NotNull
		private Integer id;

		@NotEmpty
		@Length(min = 2, max = 4)
		private String name;

		@Min(value = 10)
		@Max(value = 20)
		private Integer age;
	}


	// 全局异常处理类，捕获参数校验的失败异常
	@RestControllerAdvice
	static class GlobalExceptionHandler {

		@ExceptionHandler(value = {Exception.class})
		public String handlerException(Exception e) {
			return "参数校验不通过";
		}
	}
}
```

> 发起请求

```xpath
http://localhost:8080/student5

{
    "id": "",
    "name": "zhangsan",
    "age": "30"
}
```

> 控制台

```shell
参数校验不通过
```

> **SpringBoot** 参数校验使用，更多查看 [https://mp.weixin.qq.com/s/x6_mNdtb6i2XmTiyz4kXrg](https://mp.weixin.qq.com/s/x6_mNdtb6i2XmTiyz4kXrg)