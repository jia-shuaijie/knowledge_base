## 枚举示例
```java
import com.fasterxml.jackson.annotation.JsonCreator;  
import com.fasterxml.jackson.annotation.JsonFormat;  
import lombok.Getter;  
  
import java.util.Arrays;  
  
@Getter  
// 让枚举返回的时候携带里面的值 
@JsonFormat(shape = JsonFormat.Shape.OBJECT)
public enum LevelEnum {  
  
    city(1, "市"),  
    county(2, "县"),  
    firm(3, "企业");  
  
    // @JsonValue  
    private final Integer code;  
  
  
    private final String msg;  
  
    LevelEnum(Integer code, String msg) {  
        this.code = code;  
        this.msg = msg;  
    }  
  
    // 自定义反序列函数  
    // JsonCreator.Mode.DELEGATING： 接收单个值，将接收的值整个传入自定义函数
    @JsonCreator(mode = JsonCreator.Mode.DELEGATING)  
    public static LevelEnum get(Integer code) {  
        if (code == null) {  
            return null;  
        }  
        return Arrays.stream(values()).filter(num -> num.code == code.intValue()).findAny().orElse(null);  
    }  
}
```

### 序列化时将整个对象返回
`@JsonFormat(shape = JsonFormat.Shape.OBJECT)` 

返回示例:
```json
{
    "code": 1,
    "msg": "市"
  }
```

### 序列化时仅返回某个值
`@JsonValue` 这样返回时就仅返回注释的值.