# 前言

<font face="幼圆">

!> springBoot-web内置统一请求类和响应类

</font>

# springBoot-web内置统一请求类 和 响应类

```java
package com.alibaba.frame.http.controller;
import lombok.Data;
import org.springframework.http.RequestEntity;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.Optional;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@RestController
public class EntityController {

	/**
	 * springBoot-web内置统一请求类 和 响应类
	 *
	 * @param request RequestEntity
	 * @return ResponseEntity
	 */
	@PostMapping("/entity")
	public ResponseEntity<StudentEntity> entity(RequestEntity<StudentEntity> request) {
		final StudentEntity studentEntity = Optional.ofNullable(request.getBody()).orElse(new StudentEntity());
		System.out.println(studentEntity.getUserId());
		System.out.println(studentEntity.getUserName());

		return ResponseEntity.ok(StudentEntity.build());
	}

	@Data
	static class StudentEntity {
		private int userId;
		private String userName;

		public static StudentEntity build() {
			final StudentEntity studentEntity = new StudentEntity();
			studentEntity.setUserId(1001);
			studentEntity.setUserName("张三");
			return studentEntity;
		}
	}
}
```

<font face="幼圆">

!> postman请求

</font>

```json
{
  "userId":"100",
  "userName":"李四"
}
```

<font face="幼圆">

!> 控制台打印

</font>

```text
100
李四
```

<font face="幼圆">

!> postman响应

</font>

```text
{
    "userId": 1001,
    "userName": "张三"
}
```





