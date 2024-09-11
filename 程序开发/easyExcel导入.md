在使用easyExcel导入excel时会碰到下面一些重复性的问题
1. 表头校验
2. 每行的数据校验
3. 错误数据记录
4. 导入总数/正确导入数/导入异常数...

根据上面的问题,可以考虑抽象一个监听器的实现类,这样重复性的问题就可以解决.
这样我们只需要自己的实现类中关注导入数据的入库操作就好.

## 提取公共工具类

### Bean对象部分属性值的空判断
```java
package cn.demo.util;  
  
import com.alibaba.excel.util.StringUtils;
import java.lang.reflect.Field;  
import java.util.List;  
  
public class ObjectUtil {  
  
    /**  
     * 判断对象中部分字段是否为空  
     *  
     * @param obj       要校验属性的对象  
     * @param fieldList 要校验的属性列表  
     * @return 当列表字段中有一个为空那么本次判断就是 false  
     */    public static boolean fieldListIsEmpty(Object obj, List<String> fieldList) {  
        if (obj == null) {  
            return true;  
        }  
        for (Field f : obj.getClass().getDeclaredFields()) {  
            // 设置访问权限  
            f.setAccessible(true);  
            try {  
                if (fieldList.contains(f.getName()) && f.get(obj) == null && StringUtils.isBlank(f.get(obj).toString())) {  
                    return true;  
                }  
            } catch (IllegalAccessException e) {  
                throw new RuntimeException(e);  
            }  
        }  
        return false;  
    }  
  
}
```

### 监听器抽象类
```java
import cn.demo.util.ObjectUtil;  
import com.alibaba.excel.context.AnalysisContext;  
import com.alibaba.excel.metadata.data.ReadCellData;  
import com.alibaba.excel.read.listener.ReadListener;  
import com.alibaba.excel.util.ListUtils;  
import lombok.Getter;  
  
import java.util.ArrayList;  
import java.util.List;  
import java.util.Map;  
  
  
public abstract class AbstractListener<T> implements ReadListener<T> {  
    private final int BATCH_COUNT;  
    @Getter  
    private int total;  
    @Getter  
    private List<T> dataList;  
    @Getter  
    private final List<T> errorList = new ArrayList<>();  
    private final List<String> headList;  
    private final List<String> fieldList;  
  
    public AbstractListener(int bathCount, List<String> headList, List<String> fieldList) {  
        BATCH_COUNT = bathCount;  
        dataList = ListUtils.newArrayListWithExpectedSize(bathCount);  
        this.headList = headList;  
        this.fieldList = fieldList;  
    }  
  
    @Override  
    public void invokeHead(Map<Integer, ReadCellData<?>> headMap, AnalysisContext context) {  
        // 校验表头  
        for (int item = 0; item < headList.size(); item++) {  
            if (!headList.get(item).equals(headMap.get(item).getStringValue())) {  
                throw new RuntimeException("表头不匹配");  
            }  
        }  
    }  
  
    @Override  
    public void invoke(T t, AnalysisContext analysisContext) {  
        total += 1;  
        if (ObjectUtil.fieldListIsEmpty(t, fieldList)) {  
            errorList.add(t);  
            return;  
        }  
        dataList.add(t);  
        if (dataList.size() >= BATCH_COUNT) {  
            saveData();  
            dataList = ListUtils.newArrayListWithExpectedSize(BATCH_COUNT);  
        }  
    }  
  
    @Override  
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {  
        saveData();  
    }  
  
    protected abstract void saveData();  
}
```

## Demo

### DemoBean
```java
import com.alibaba.excel.annotation.ExcelProperty;  
import lombok.Data;  
  
@Data  
public class UserExcel {  
    @ExcelProperty(value = "姓名")  
    private String name;  
    @ExcelProperty(value = "年龄")  
    private Integer age;  
    @ExcelProperty(value = "性别")  
    private String sex;  
}
```

### demoLinstener

```java
package cn.demo.listenter;  
  
import cn.demo.UserExcel;  
import lombok.extern.slf4j.Slf4j;  
  
import java.util.Arrays;  
  
@Slf4j  
public class UserListener extends AbstractListener<UserExcel> {  
  
    public UserListener() {  
        super(20,  
                Arrays.asList("姓名", "年龄", "性别"),  
                Arrays.asList("name", "age", "sex"));  
    }  
  
    @Override  
    protected void saveData() {  
        System.out.println(getDataList());  
    }  
}
```

### demoTest

```java
import cn.demo.listenter.UserListener;
import com.alibaba.excel.EasyExcel; 
import org.junit.Test;  
  
import java.io.File;  
  
public class UserExcelTest {  
  
    @Test  
    public void test() {  
        UserListener userListener = new UserListener();  
        EasyExcel.read(new File("F:\\jsj\\study\\user.xlsx"), userListener).sheet().doRead();  
    }  
}
```

### demoExcelImage

![easyExcel demo](https://i.jpg.dog/59c85531eb9876fa0cc64c51b1ad0dea.png)