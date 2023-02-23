```java
package com.hundsun.demo.pojo.controller;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestController
public class StudentController {

	@GetMapping("getStudentList")
	public void getStudentList() {
		throw new GetRequestMethodException("查询数据为空...");
	}

	@PostMapping("postStudent")
	public void postStudent() {
		throw new PostRequestMethodException("新增数据异常...");
	}

	@PutMapping("putStudent")
	public void putStudent() {
		throw new PutRequestMethodException("更新数据异常...");
	}

	@DeleteMapping("deleteStudent")
	public void deleteStudent() {
		throw new DeleteRequestMethodException("删除数据异常...");
	}


	// 全局异常处理类
	@RestControllerAdvice
	static class GlobalExceptionHandler {

		@ExceptionHandler(value = {GetRequestMethodException.class})
		public String handlerGetException(GetRequestMethodException e) {
			return e.getMessage();
		}

		@ExceptionHandler(value = {PostRequestMethodException.class})
		public String handlerPostException(PostRequestMethodException e) {
			return e.getMessage();
		}

		@ExceptionHandler(value = {PutRequestMethodException.class})
		public String handlerPutException(PutRequestMethodException e) {
			return e.getMessage();
		}

		@ExceptionHandler(value = {DeleteRequestMethodException.class})
		public String handlerDeleteException(DeleteRequestMethodException e) {
			return e.getMessage();
		}
	}


	// 自定义Get请求异常
	static class GetRequestMethodException extends RuntimeException {

		public GetRequestMethodException(String message) {
			super(message);
		}
	}

	// 自定义Post请求异常
	static class PostRequestMethodException extends RuntimeException {

		public PostRequestMethodException(String message) {
			super(message);
		}
	}

	// 自定义Put请求异常
	static class PutRequestMethodException extends RuntimeException {

		public PutRequestMethodException(String message) {
			super(message);
		}
	}

	// 自定义Delete请求异常
	static class DeleteRequestMethodException extends RuntimeException {

		public DeleteRequestMethodException(String message) {
			super(message);
		}
	}
}
```

> 响应

```xpath
http://localhost:8080/getStudentList

查询数据为空...
```

```xpath
http://localhost:8080/postStudent

新增数据异常...
```

```xpath
http://localhost:8080/putStudent

更新数据异常...
```

```xpath
http://localhost:8080/deleteStudent

删除数据异常...
```