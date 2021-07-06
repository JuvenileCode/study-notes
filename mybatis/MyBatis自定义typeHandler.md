### MyBatis自定义typeHandler

>[mybatis默认typeHandler](http://mybatis.javaboy.org/2019/1110/typehandlers)
>
>`typeHandler`解决数据数据和java数据类型转换问题。

#### 简单示例

**转换List<Object>的typeHandler**

##### 1、JavaBean

```java
@Data
public class ConverDO {

    private int id;

    private List<DiffData> diffDateList;

    private Date createTime;

    private Date lastModify;
}

@Data
public class DiffData {
    private String name;

    private String original;

    private String update;
}
```

`实验过程中将DiffData定义ConverDO中,发现mapper.xml文件读取不到内部类`

##### 2、自定义实现BaseTypeHandler

>自定义typeHandler我们有两种方式，一种是实现`TypeHandler`接口，还有一种简化的写法就是继承自`BaseTypeHandler`类

```java
public class TypeHandler extends BaseTypeHandler<List<DiffData>> {

    @Override
    public void setNonNullParameter(PreparedStatement p, int i, List<DiffData> diffData, JdbcType jdbcType) throws SQLException {
        p.setString(i, JSONObject.toJSONString(diffData));
    }

    @Override
    public List<DiffData> getNullableResult(ResultSet resultSet, String s) throws SQLException {
        return conver(resultSet.getString(s));
    }

    @Override
    public List<DiffData> getNullableResult(ResultSet resultSet, int i) throws SQLException {
        return conver(resultSet.getString(i));
    }

    @Override
    public List<DiffData> getNullableResult(CallableStatement callableStatement, int i) throws SQLException {
        return conver(callableStatement.getString(i));
    }

    private List<DiffData> conver(String jsonStr) {
        if (StringUtils.isEmpty(jsonStr)) {
            return Collections.emptyList();
        }
        List<DiffData> diffData = JSONObject.parseArray(jsonStr, DiffData.class);
        return diffData;
    }
}

```

##### 3、mapper修改

**注意：** 在查询的过程中系统会启用我们自定义的typeHandler，会将秒数转为List<DiffData>对象，但是在插入的时候却不会启用我们自定义的typeHandler，想要在插入的时候启用自定义的typeHandler，需要我们在insert节点中简单配置一下(注意语法，测试多打分号一直报错)。

```xml
<resultMap id="ConverMap" type="next.fire.msg.center.dal.entity.domain.ConverDO" >
    <id column="id" property="id" jdbcType="INTEGER" />
    <result column="diff" property="diffDateList"
            javaType="next.fire.msg.center.dal.entity.domain.DiffData" typeHandler="next.fire.msg.center.service.base.TypeHandler"/>
    <result column="create_time" property="createTime" jdbcType="TIMESTAMP" />
    <result column="last_modify" property="lastModify" jdbcType="TIMESTAMP" />
</resultMap>

<insert id="saveConver" parameterType="next.fire.msg.center.dal.entity.domain.ConverDO">
    INSERT INTO t_handler (diff) VALUES (#{diffDateList,jdbcType=VARCHAR,javaType=next.fire.msg.center.dal.entity.domain.DiffData ,typeHandler=next.fire.msg.center.service.base.TypeHandler})
</insert>

<select id="getConverId" resultMap="ConverMap">
    SELECT * FROM t_handler WHERE id = #{id}
</select>
```



#### 通用对象转换

```java
import com.alibaba.fastjson.JSON;
import com.google.common.base.Strings;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class JSONObjectHandler<T> extends BaseTypeHandler<T> {

    private Class<T> clazz;

    public JSONObjectHandler(Class<T> clazz) {
        if (clazz == null) {
            throw new IllegalArgumentException("JSONObjectHandler type argument cannot be null");
        }
        this.clazz = clazz;
    }

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, JSON.toJSONString(parameter));
    }

    @Override
    public T getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String raw = rs.getString(columnName);
        return parse(raw);
    }

    @Override
    public T getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String raw = rs.getString(columnIndex);
        return parse(raw);
    }

    @Override
    public T getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String raw = cs.getString(columnIndex);
        return parse(raw);
    }

    private T parse(String raw) {
        if (Strings.isNullOrEmpty(raw)) {
            return null;
        }
        return JSON.parseObject(raw, clazz);
    }

}
```



#### 通用List<T>转换

```java
import com.alibaba.fastjson.JSON;
import com.google.common.base.Strings;
import org.apache.ibatis.type.BaseTypeHandler;
import org.apache.ibatis.type.JdbcType;

import java.sql.CallableStatement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

public class JSONArrayHandler<T> extends BaseTypeHandler<List<T>> {

    private Class<T> clazz;

    public JSONArrayHandler(Class<T> clazz) {
        if (clazz == null) {
            throw new IllegalArgumentException("JSONArrayHandler type argument cannot be null");
        }
        this.clazz = clazz;
    }

    @Override
    public void setNonNullParameter(PreparedStatement ps, int i, List<T> parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, JSON.toJSONString(parameter));
    }

    @Override
    public List<T> getNullableResult(ResultSet rs, String columnName) throws SQLException {
        String raw = rs.getString(columnName);
        return parse(raw);
    }

    @Override
    public List<T> getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
        String raw = rs.getString(columnIndex);
        return parse(raw);
    }

    @Override
    public List<T> getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
        String raw = cs.getString(columnIndex);
        return parse(raw);
    }

    private List<T> parse(String raw) {
        if (Strings.isNullOrEmpty(raw)) {
            return null;
        }
        return JSON.parseArray(raw, clazz);
    }

}
```





