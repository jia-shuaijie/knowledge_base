枚举值入库时默认只能使用名称也就是枚举的`enum.name()`进行入库.
但很多情况都是使用int值进行入库.
针对这种情况我们可以使用`@EnumValue` 进行入库转换操作.

```java
import com.baomidou.mybatisplus.annotation.EnumValue;
import com.baomidou.mybatisplus.annotation.EnumValue;  
import com.fasterxml.jackson.annotation.JsonValue;  
import lombok.Getter;  
  
@Getter  
public enum AdminLevelEnum {  
  
    city(1, "市"),  
    county(2, "县"),  
    firm(3, "企业");  
  
    @JsonValue  
    @EnumValue    
    private final int code;  
    private final String name;  

  
    AdminLevelEnum(int code, String name) {  
        this.code = code;  
        this.name = name;  
    }  
  
}
```

这样写好后我们还需要对`mybatis plus` 进行配置
```yml
mybatis-plus:
	configuration:
		default-enum-type-handler: com.baomidou.mybatisplus.extension.handlers.MybatisEnumTypeHandler
```

这样就可以将int值直接入库出库转换为枚举值了.