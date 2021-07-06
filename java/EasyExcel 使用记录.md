## EasyExcel 使用记录

> [官方文档地址](https://www.yuque.com/easyexcel/doc/read#8e72a573)

### Maven导入坐标

```xml
<!--easyexcel excel处理-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>2.2.6</version>
</dependency>
```

 

### Excel导出

#### 1、实体定义

```java
@ToString
@Getter
@Setter
@ContentRowHeight(20)
@HeadRowHeight(30)
@ColumnWidth(20)
public class ProjectBasicExcel {

    @ExcelProperty(value = "项目名称", index = 0)
    private String projectName;

    @ExcelProperty(value = "项目代码", index = 1)
    private String projectCode;

    @ColumnWidth(25)
    @ExcelProperty(value = "项目工期", index = 2)
    private String projectDuration;

    @ExcelProperty(value = "责任单位", index = 3)
    private String blameUnitName;

    @ExcelProperty(value = "建设单位", index = 4)
    private String consUnitName;

    @ExcelProperty(value = "项目类型", index = 5)
    private String majorProjectType;

    @ExcelProperty(value = "总投资（亿元）", index = 6)
    private String totalInvestment;

    @ExcelProperty(value = "年度计划投资（万元）", index = 7)
    private String annualPlanInvestment;

    @ExcelProperty(value = "年度累计完成投资（万元）", index = 8)
    private String annualAccumulatedInvestment;

    @ExcelProperty(value = "年度投资完成率", index = 9)
    private String annualRate;

    @ExcelProperty(value = "建设性质", index = 10)
    private String constructionNature;
}
```

#### 2、使用代码

```java
@PostMapping("/importExcel")
@ApiOperation(value = "Excel导出", notes = "Excel导出")
public void importExcel(@RequestBody ProjectBasicQuery query, HttpServletResponse response) throws IOException{
    try {
        response.setContentType("application/vnd.ms-excel");
        response.setCharacterEncoding("utf-8");
        String fileName = DateUtil.today() + "-" + ObjectId.next();
        response.setHeader("Content-disposition", "attachment;filename=" + fileName + ".xlsx");
        List<ProjectBasicExcel> projectBasicExcel = projectBasicInfoService.excelData(query);
        EasyExcel.write(response.getOutputStream(), ProjectBasicExcel.class).autoCloseStream(Boolean.FALSE).sheet("sheet")
            .doWrite(projectBasicExcel);
    } catch (Exception e) {
        log.error("项目基础信息导出Excel异常：{}", e.getMessage());
        e.printStackTrace();
        response.reset();
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().println(R.failed(e.getMessage()));
    }
}
```



### Excel导入

#### 1、实体定义

```java
import com.alibaba.excel.annotation.ExcelProperty;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

/**
 * @auther: Bin.L
 * @date: 2021-05-18 16:06
 * @Description:
 */

@ToString
@Getter
@Setter
public class ReadExcel {

    @ExcelProperty("项目名称")
    private String projectName;

    @ExcelProperty("项目性质")
    private String projectXinZhi;

    @ExcelProperty("大类")
    private String oneType;

    @ExcelProperty("小类")
    private String twoType;

}
```

#### 2、导入数据处理器

```java
import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;
import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.uqian.zdxmgl.dao.ProjectBasicInfoMapper;
import com.uqian.zdxmgl.entity.ProjectBasicInfo;
import lombok.extern.slf4j.Slf4j;

import java.util.Optional;

/**
 * @auther: Bin.L
 * @date: 2021-05-18 16:16
 * @Description:
 */
@Slf4j
public class DemoDataListener extends AnalysisEventListener<ReadExcel> {

    /**
     * 每隔5条存储数据库，实际使用中可以3000条，然后清理list ，方便内存回收
     */
    private static final int BATCH_COUNT = 5;

    private ProjectBasicInfoMapper projectBasicInfoMapper;

    /**
     * 假设这个是一个DAO，当然有业务逻辑这个也可以是一个service。当然如果不用存储这个对象没用。
     */
    private ReadExcel demoDAO;
    public DemoDataListener(ProjectBasicInfoMapper projectBasicInfoMapper) {
        // 这里是demo，所以随便new一个。实际使用如果到了spring,请使用下面的有参构造函数
        demoDAO = new ReadExcel();
        this.projectBasicInfoMapper = projectBasicInfoMapper;
    }
    /**
     * 如果使用了spring,请使用这个构造方法。每次创建Listener的时候需要把spring管理的类传进来
     *
     * @param demoDAO
     */
    public DemoDataListener(ReadExcel demoDAO) {
        this.demoDAO = demoDAO;
    }
    /**
     * 这个每一条数据解析都会来调用
     *
     * @param data
     *            one row value. Is is same as {@link AnalysisContext#readRowHolder()}
     * @param context
     */
    @Override
    public void invoke(ReadExcel data, AnalysisContext context) {
        log.info("解析到一条数据:{}", data);
        // 达到BATCH_COUNT了，需要去存储一次数据库，防止数据几万条数据在内存，容易OOM
        saveData(data);
    }
    /**
     * 所有数据解析完成了 都会来调用
     *
     * @param context
     */
    @Override
    public void doAfterAllAnalysed(AnalysisContext context) {
        // 这里也要保存数据，确保最后遗留的数据也存储到数据库
        log.info("所有数据解析完成！");
    }
    /**
     * 加上存储数据库
     */
    private void saveData(ReadExcel data) {

        ProjectBasicInfo projectBasicInfo = projectBasicInfoMapper.selectOne(new LambdaQueryWrapper<ProjectBasicInfo>()
                .eq(ProjectBasicInfo::getProjectName, data.getProjectName()));

        Optional.ofNullable(projectBasicInfo).map( pro ->{
            ProjectBasicInfo upPro = new ProjectBasicInfo();
            upPro.setId(pro.getId());
            upPro.setConstructionNature(data.getProjectXinZhi());
            upPro.setOneType(data.getOneType());
            upPro.setTwoType(data.getTwoType());
            projectBasicInfoMapper.updateById(upPro);
            return "";
        } ).orElse("FAIL");

        log.info("存储数据库成功！");
    }
}
```

#### 3、使用代码

```java
@Slf4j
@Api(tags = "数据测试")
@RestController
@RequestMapping("/testData")
public class TestController {

    @Autowired
    private ProjectBasicInfoMapper projectBasicInfoMapper;

    @PostMapping("/upload")
    @ResponseBody
    @ApiOperation(value = "读取数据", notes = "读取数据")
    public void create() throws IOException {
        try {
            // Excel文件位置
            String readPath = "/Users/bl/Desktop/有谦软联/project/分类/基础设施大类小类汇总表.xlsx";
            // 这里 需要指定读用哪个class去读，然后读取第一个sheet 文件流会自动关闭
            EasyExcel.read(readPath, ReadExcel.class, new DemoDataListener(projectBasicInfoMapper)).sheet().doRead();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

