```java
/**
 * 返回状态码
 * 
 * @author admin
 */
public class HttpStatus {
   
    // 操作成功
    public static final int SUCCESS = 200;

    // 对象创建成功
    public static final int CREATED = 201;

    // 请求已经被接受
    public static final int ACCEPTED = 202;

    // 操作已经执行成功，但是没有返回数据
    public static final int NO_CONTENT = 204;

    // 资源已被移除
    public static final int MOVED_PERM = 301;

    // 重定向
    public static final int SEE_OTHER = 303;

    // 资源没有被修改
    public static final int NOT_MODIFIED = 304;

    // 参数列表错误（缺少，格式不匹配）
    public static final int BAD_REQUEST = 400;

    // 未授权
    public static final int UNAUTHORIZED = 401;

    // 访问受限，授权过期
    public static final int FORBIDDEN = 403;

    // 资源，服务未找到
    public static final int NOT_FOUND = 404;

}
```

```java
import java.io.Serializable;

/**
 * 响应信息主体
 *
 * @author admin
 */
public class R<T> implements Serializable {
    private static final long serialVersionUID = 1L;

    // 成功
    public static final int SUCCESS = HttpStatus.SUCCESS;

    // 失败
    public static final int FAIL = HttpStatus.ERROR;

    private int code;

    private String message;

    private T data;

    public static <T> R<T> ok() {
        return restResult(null, SUCCESS, "操作成功");
    }

    public static <T> R<T> ok(T data) {
        return restResult(data, SUCCESS, "操作成功");
    }

    public static <T> R<T> ok(T data, String message) {
        return restResult(data, SUCCESS, message);
    }

    public static <T> R<T> fail() {
        return restResult(null, FAIL, "操作失败");
    }

    public static <T> R<T> fail(String message) {
        return restResult(null, FAIL, message);
    }

    public static <T> R<T> fail(T data) {
        return restResult(data, FAIL, "操作失败");
    }

    public static <T> R<T> fail(T data, String message) {
        return restResult(data, FAIL, message);
    }

    public static <T> R<T> fail(int code, String message) {
        return restResult(null, code, message);
    }

    private static <T> R<T> restResult(T data, int code, String message) {
        R<T> apiResult = new R<>();
        apiResult.setCode(code);
        apiResult.setData(data);
        apiResult.setmessage(message);
        return apiResult;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getmessage() {
        return message;
    }

    public void setmessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public static <T> Boolean isError(R<T> ret) {
        return !isSuccess(ret);
    }

    public static <T> Boolean isSuccess(R<T> ret) {
        return R.SUCCESS == ret.getCode();
    }
}
```