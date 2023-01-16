# Spring依赖注入

# Spring依赖注入Map方式一

```java
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
public interface Person {

	void running();
}
```

```java
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Person;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("xiaoMing")
public class XiaoMing implements Person {

	@Override
	public void running() {
		System.out.println("小明在运动...");
	}
}
```

```java
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Person;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("xiaoGang")
public class XiaoGang implements Person {

	@Override
	public void running() {
		System.out.println("小刚在运行...");
	}
}
```

```java
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Person;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("xiaoQiang")
public class XiaoQiang implements Person {
	@Override
	public void running() {
		System.out.println("小强在运行...");
	}
}
```

```java
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Person;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 人物
 */
@RestController
public class PersonController {

	// Map注入
	@Autowired
	private final Map<String, Person> persons;

	public PersonController(Map<String, Person> persons) {
		this.persons = persons;
	}


	/**
	 * 测试Map注入
	 *
	 * @param <T> t
	 * @return response
	 */
	@GetMapping("/running")
	public <T> Response<T> running() {
		final Person xiaoMing = persons.get("xiaoMing");
		xiaoMing.running();

		return Response.ok(null);
	}
}
```

> 控制台

```text
小明在运动...
```

***

# Spring依赖注入Map方式二

```java
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.Resource;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentTwoController
 */
@RestController
public class StudentFiveController {

	// Map注入
	@Resource
	@Qualifier("liSi")
	private final Map<String, Student> students;

	public StudentFiveController(Map<String, Student> students) {
		this.students = students;
	}


	/**
	 * 测试Map注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/studyFive")
	public <T> Response<T> studyFive() {
		for (Student student : students.values()) {
			student.study();
		}

		return Response.ok(null);
	}
}
```

> 控制台

```text
李四在学习...
```

***

# Spring依赖注入Map方式三

```java 
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.Resource;
import java.util.Map;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentTwoController
 */
@RestController
public class StudentFourController {

	// Map注入
	@Resource
	private final Map<String, Student> students;

	public StudentFourController(Map<String, Student> students) {
		this.students = students;
	}


	/**
	 * 测试Map注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/studyFour")
	public <T> Response<T> studyFour() {
		final Student zhangSan = students.get("zhangSan");
		zhangSan.study();

		return Response.ok(null);
	}
}
```

> 控制台

```text
张三在学习...
```

***

# Spring依赖注入List方式一

```java 
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.Resource;
import java.util.List;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentController
 */
@RestController
public class StudentController {

	// List注入
	@Resource
	private final List<Student> students;

	public StudentController(List<Student> students) {
		this.students = students;
	}


	/**
	 * 测试List注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/study")
	public <T> Response<T> study() {
		for (Student student : students) {
			student.study();
		}

		return Response.ok(null);
	}
}
```

> 控制台

```text
李四在学习...
张三在学习...
```

***

# Spring依赖注入List方式二

```java 
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentThreeController
 */
@RestController
public class StudentThreeController {

	// List注入
	@Autowired
	@Qualifier("liSi")
	private final List<Student> students;

	public StudentThreeController(List<Student> students) {
		this.students = students;
	}

	/**
	 * 测试List注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/studyThree")
	public <T> Response<T> studyThree() {
		for (Student student : students) {
			student.study();
		}

		return Response.ok(null);
	}
}
```

> 控制台

```text
李四在学习...
```

***

# Spring依赖注入List方式三

```java 
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentTwoController
 */
@RestController
public class StudentTwoController {

	// List注入
	@Autowired
	private final List<Student> students;

	public StudentTwoController(List<Student> students) {
		this.students = students;
	}

	/**
	 * 测试List注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/studyTwo")
	public <T> Response<T> studyTwo() {
		for (Student student : students) {
			student.study();
		}

		return Response.ok(null);
	}
}
```

> 控制台

```text
李四在学习...
张三在学习...
```

# Spring依赖注入Array数组方式

```java
package com.hundsun.frameworlk.http.policy;

/**
 * @author hspcadmin
 * @version 1.0
 * @description 学生
 */
public interface Student {

	void study();
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("liSi")
public class LiSi implements Student {

	@Override
	public void study() {
		System.out.println("李四在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.policy.impl;
import com.hundsun.frameworlk.http.policy.Student;
import org.springframework.stereotype.Component;

/**
 * @author hspcadmin
 * @version 1.0
 * @description
 */
@Component("zhangSan")
public class ZhangSan implements Student {

	@Override
	public void study() {
		System.out.println("张三在学习...");
	}
}
```

```java 
package com.hundsun.frameworlk.http.controller;
import com.hundsun.frameworlk.http.policy.Student;
import com.hundsun.frameworlk.token.domain.response.Response;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author hspcadmin
 * @version 1.0
 * @description StudentTwoController
 */
@RestController
public class StudentSixController {

	// 数组注入
	@Autowired
	private final Student[] students;

	public StudentSixController(Student[] students) {
		this.students = students;
	}


	/**
	 * 测试数组注入
	 *
	 * @param <T> t
	 * @return Response
	 */
	@GetMapping("/studySix")
	public <T> Response<T> studySix() {
		for (Student student : students) {
			student.study();
		}

		return Response.ok(null);
	}
}
```

> 控制台

```text
李四在学习...
张三在学习...
```

***
