```java
import com.fasterxml.jackson.annotation.JsonInclude;  
import com.fasterxml.jackson.core.JsonProcessingException;  
import com.fasterxml.jackson.core.type.TypeReference;  
import com.fasterxml.jackson.databind.DeserializationFeature;  
import com.fasterxml.jackson.databind.JsonNode;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import com.fasterxml.jackson.databind.SerializationFeature;  
import lombok.extern.slf4j.Slf4j;  
  
import java.text.SimpleDateFormat;  
  
/**  
 * jackson json格式化工具类  
 *  
 * @author 黑色的小火苗  
 */  
@Slf4j  
public class JacksonUtil {  
    public static final ObjectMapper OM = new ObjectMapper();  
    // 日期格式化  
    private static final String STANDARD_FORMAT = "yyyy-MM-dd HH:mm:ss";  
  
    static {  
        //对象的所有字段全部列入  
        OM.setSerializationInclusion(JsonInclude.Include.ALWAYS);  
        //取消默认转换timestamps形式  
        OM.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);  
        //忽略空Bean转json的错误  
        OM.configure(SerializationFeature.FAIL_ON_EMPTY_BEANS, false);  
        //所有的日期格式都统一为以下的样式，即yyyy-MM-dd HH:mm:ss  
        OM.setDateFormat(new SimpleDateFormat(STANDARD_FORMAT));  
        //忽略 在json字符串中存在，但是在java对象中不存在对应属性的情况。防止错误  
        OM.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);  
    }  
  
    /**  
     * 将java对象转换为json字符串输出  
     *  
     * @param obj java对象  
     * @return String json字符串  
     */  
    public static String toJson(Object obj) {  
        try {  
            return OM.writeValueAsString(obj);  
        } catch (JsonProcessingException e) {  
            // 对外不暴露具体错误信息  
            throw new RuntimeException("[ jackson ] java对象转json字符串时出现异常");  
        }  
    }  
  
    /**  
     * json字符串转java对象  
     *  
     * @param str json字符串  
     * @param cls java对象class  
     * @param <T> java对象类型  
     * @return java对象  
     */  
    public static <T> T toObj(String str, Class<T> cls) {  
        try {  
            return OM.readValue(str, cls);  
        } catch (Exception e) {  
            // 对外不暴露具体错误信息  
            throw new RuntimeException("[ jackson ] json字符串转java对象时出现异常");  
        }  
    }  
  
    public static <T> T toObj(String str, TypeReference<T> typeReference) {  
        try {  
            return OM.readValue(str, typeReference);  
        } catch (JsonProcessingException e) {  
            // 对外不暴露具体错误信息  
            throw new RuntimeException("[ jackson ] json字符串转javaList对象时出现异常");  
        }  
    }  
  
  
    /**  
     * 直接读取json字符串转JsonNode  
     * * @param str json字符串  
     *  
     * @return JsonNode     
     */    
	public static JsonNode toNode(String str) {  
        try {  
            return OM.readTree(str);  
        } catch (JsonProcessingException e) {  
            // 对外不暴露具体错误信息    
			throw new RuntimeException("[ jackson ] 读取json字符串转JsonNode时出现异常");
        }  
    }  
}
```