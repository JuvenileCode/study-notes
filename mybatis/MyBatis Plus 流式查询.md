## MyBatis Plus 流式查询

### 参考

[来源一](https://orchidflower.github.io/2020/09/04/Using-MyBatis-ResultHandler-to-export-huge-resultset-avoiding-OOM/)

[来源二](https://segmentfault.com/a/1190000022167975)

### 定义

```java
@Mapper
public interface OrgDataMapper extends BaseMapper<OrgData> {
    @Select("select * from org_data t ${ew.customSqlSegment}")
    @Options(resultSetType = ResultSetType.FORWARD_ONLY, fetchSize = 1000)
    @ResultType(OrgData.class)
    void getOrgWithBigData(@Param(Constants.WRAPPER) QueryWrapper<OrgData> wrapper, ResultHandler<OrgData> handler);
    
    @Select("${sql}")
    @Options(resultSetType = ResultSetType.FORWARD_ONLY, fetchSize = 1000)
    @ResultType(TblMallOrder.class)
    void dynamicSelectLargeData1(@Param("sql") String sql, ResultHandler<TblMallOrder> handler);
	
    @Select("select * from policy_info")
    @Options(resultSetType = ResultSetType.FORWARD_ONLY, fetchSize = Integer.MIN_VALUE)
    @ResultType(PolicyInfo.class)
    void readStreamingData(ResultHandler<PolicyInfo> handler);
}
```

#### 说明：

```bash
1. resultSetConcurrency=ResultSet.CONCUR_READ_ONLY 设置只读结果集

2. resultSetType = ResultSetType.FORWARD_ONLY 设置结果集的游标只能向下滚动

3. fetchSize = Integer.MIN_VALUE 设置fetch size为int的最小值，这里和oracle/db2有区别.
   - Oracle/db2是从服务器一次取出fetch size 条记录放在客户端，客户端处理完成一个批次后再向服务器取下一个批次，直到所有数据处理完成。
   - mysql在执行ResultSet.next()方法时，会通过数据库连接一条一条的返回。MySQL按照自己的节奏不断的把buffer写回网络中。flush buffer的过程是阻塞式的，也就是说如果网络中发生了拥塞，send buffer被填满，会导致buffer一直flush不出去，那MySQL的处理线程会阻塞，从而避免数据把客户端内存撑爆。
   - 之前使用过db2处理流式查询，设置的fetch size为100,没有问题。这次使用mysql刚开始时也设置的100，发现内存溢出了，后来在网上看到mysql流式获取数据的坑，debug进去果然没走到ResultsetRowsStreaming类，设置fetch size 参数为Integer.MIN_VALUE后，才进了ResultsetRowsStreaming类。
```



### 使用

```java
public void getOrgWithBigData() {
    orgDataMapper.getOrgWithBigData(queryWrapper,resultContext -> {

        OrgData orgData = resultContext.getResultObject();
        //这边循环调用就可以实现业务了
    }
}

public void getLargeData1() {
    String sql = "select * from t_mall_order";
    tblMallOrderDao.dynamicSelectLargeData1(sql, new ResultHandler<TblMallOrder>() {
        @Override
        public void handleResult(ResultContext<? extends TblMallOrder> resultContext) {
            TblMallOrder tblMallOrder = resultContext.getResultObject();
            System.out.println(tblMallOrder);
        }
    });
}
                                   
public void dataSynchronizeEs() {
    policyInfoMapper.readStreamingData(resultContext -> {
        PolicyInfo p = resultContext.getResultObject();
        //

    });
}                           
```

