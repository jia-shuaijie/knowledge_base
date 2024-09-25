
静态注入需要使用注解`@PostConstruct`

```java

import lombok.RequiredArgsConstructor;  
import org.springframework.stereotype.Component;  
  
import javax.annotation.PostConstruct;
// 示例注入
@Component  
@RequiredArgsConstructor
public class CacheUtil {
	private static JedisUtil jedisUtil;

	private final JedisUtil jedisUtilArg;

	@PostConstruct  
	public void init() {  
	    jedisUtil = this.jedisUtilArg;  
	}
}
```

这样就可以保证静态注入可以使用了.