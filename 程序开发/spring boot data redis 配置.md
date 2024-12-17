创建Redis配置类,当然也可以直接使用StringRedisTemplate.

```java
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.data.redis.connection.RedisConnectionFactory;  
import org.springframework.data.redis.core.RedisTemplate;  
import org.springframework.data.redis.serializer.RedisSerializer;  
import org.springframework.data.redis.serializer.StringRedisSerializer;  
  
@Configuration  
public class RedisConfig {  
    @Bean  
    public RedisTemplate<String, byte[]> redisTemplate(RedisConnectionFactory connectionFactory) {  
        RedisTemplate<String, byte[]> template = new RedisTemplate<>();  
        template.setConnectionFactory(connectionFactory);  
        StringRedisSerializer stringSerializer = new StringRedisSerializer();  
        // 使用StringRedisSerializer来序列化和反序列化redis的key值  
        template.setKeySerializer(stringSerializer);  
        template.setValueSerializer(RedisSerializer.byteArray());  
        // Hash的key也采用StringRedisSerializer的序列化方式  
        template.setHashKeySerializer(stringSerializer);  
        template.setHashValueSerializer(RedisSerializer.byteArray());  
  
        template.afterPropertiesSet();  
        return template;  
    }  
}
```

## RedisUtil
我这里是依赖于Jackon进行序列化和反序列化

```java
package org.study.util;  
  
import com.fasterxml.jackson.core.type.TypeReference;  
import org.springframework.data.redis.core.*;  
import org.springframework.stereotype.Component;  
  
import java.time.Duration;  
import java.util.Objects;  
  
@Component  
public class RedisUtil {  
    public static RedisTemplate<String, byte[]> redisTemplate;  
    public static StringRedisTemplate strRedisTemplate;  
  
    public RedisUtil(RedisTemplate<String, byte[]> redisTemplateArg, StringRedisTemplate strRedisTemplateArg) {  
        redisTemplate = redisTemplateArg;  
        strRedisTemplate = strRedisTemplateArg;  
    }  
  
    // ============ value 操作 ===========    public static void set(String key, Object value) {  
        redisTemplate.opsForValue().set(key, toByte(value));  
    }  
  
    /**  
     * 存储value设置超时时间  
     *  
     * @param timeout 超时时间,单位:秒  
     */  
    public static void set(String key, Object value, Integer timeout) {  
        redisTemplate.opsForValue().set(key, toByte(value), Duration.ofSeconds(timeout));  
    }  
  
    public static String get(String key) {  
        return new String(Objects.requireNonNull(redisTemplate.opsForValue().get(key)));  
    }  
  
    public static <T> T get(String key, TypeReference<T> type) {  
        return toObj(get(key), type);  
    }  
  
  
    // ============== json ===========  
    public static byte[] toByte(Object o) {  
        if (o instanceof String) {  
            return ((String) o).getBytes();  
        }  
        return JacksonUtil.toJson(o).getBytes();  
    }  
  
  
    public static <T> T toObj(String str, TypeReference<T> type) {  
        return JacksonUtil.toObj(str, type);  
    }  
}
```