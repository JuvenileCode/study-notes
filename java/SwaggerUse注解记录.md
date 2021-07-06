## Swagger 注解记录

[参考](https://blog.csdn.net/jiangyu1013/article/details/83107255)

### Api

**`@Api`用在类上，说明该类的作用。可以标记一个 Controller 类作为 Swagger 文档资源**

```java
@Api(tags={"用户接口"})
@RestController
public class UserController {
}
```

### ApiModel

**`@ApiModel`用在类上，表示对类进行说明，用于实体类中的参数接收说明。**

```java
@ApiModel(value = "com.biancheng.auth.param.AddUserParam", description = "新增用户参数")
public class AddUserParam {
}
```

### ApiModelProperty

**`@ApiModelProperty`用于字段，表示对 model 属性的说明。**

```java
@Data
@ApiModel(value = "com.biancheng.auth.param.AddUserParam", description = "新增用户参数")
public class AddUserParam {
    @ApiModelProperty(value = "ID")
    private String id;
    @ApiModelProperty(value = "名称")
    private String name;
    @ApiModelProperty(value = "年龄")
    private int age;
}
```

### ApiParam

**`@ApiParam`用于 Controller 中方法的参数说明。**

```java
@PostMapping("/user")
public UserDto addUser(@ApiParam(value = "新增用户参数", required = true) @RequestBody AddUserParam param) {
    System.err.println(param.getName());
    return new UserDto();
}
```

### ApiOperation

**`@ApiOperation`用在 Controller 里的方法上，说明方法的作用，每一个接口的定义。**

```java
@ApiOperation(value="新增用户", notes="详细描述")
public UserDto addUser(@ApiParam(value = "新增用户参数", required = true) @RequestBody AddUserParam param) {
}
```

### ApiImplicitParam 和 ApiImplicitParams

**用于方法上，为单独的请求参数进行说明。**

```java
@ApiImplicitParams({
        @ApiImplicitParam(name = "id", value = "用户ID", dataType = "string", paramType = "query", required = true, defaultValue = "1") })
@ApiResponses({ @ApiResponse(code = 200, message = "OK", response = UserDto.class) })
@GetMapping("/user")
public UserDto getUser(@RequestParam("id") String id) {
    return new UserDto();
}
```

- name：参数名，对应方法中单独的参数名称。
- value：参数中文说明。
- required：是否必填。
- paramType：参数类型，取值为 path、query、body、header、form。
- dataType：参数数据类型。
- defaultValue：默认值。