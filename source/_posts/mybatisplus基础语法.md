---
title: mybatisplus基础语法
date: 2025-02-20 14:37:01
categories: 
  - 数据库
cover: false
---
配置yml
```
# MyBatis配置
mybatis-plus:
  # 搜索指定包别名
  typeAliasesPackage: com.ruoyi.**.domain
  # 配置mapper的扫描，找到所有的mapper.xml映射文件
  mapperLocations: classpath*:mapper/**/*Mapper.xml
  # 加载全局的配置文件
  configLocation: classpath:mybatis/mybatis-config.xml
  #设置逻辑删除字段，is_del这个字段在删除时自动触发逻辑删除，查询时自动拼接where is_del=0 
  global-config:
    db-config:
      logic-delete-field: is_del
      logic-not-delete-value: 0
      logic-delete-value: 1
```

mybatis-config.xml文件，根据上边的路径，放在resources目录mybatis文件夹下
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 全局参数 -->
    <settings>
        <!-- 使全局的映射器启用或禁用缓存 -->
        <setting name="cacheEnabled"             value="true"   />
        <!-- 允许JDBC 支持自动生成主键 -->
        <setting name="useGeneratedKeys"         value="true"   />
        <!-- 配置默认的执行器.SIMPLE就是普通执行器;REUSE执行器会重用预处理语句(prepared statements);BATCH执行器将重用语句并执行批量更新 -->
        <setting name="defaultExecutorType"      value="SIMPLE" />
		<!-- 指定 MyBatis 所用日志的具体实现 -->
        <setting name="logImpl"                  value="SLF4J"  />
        <!-- 使用驼峰命名法转换字段 -->
		<!-- <setting name="mapUnderscoreToCamelCase" value="true"/> -->
	</settings>
	
</configuration>

```

bean类写法
```
@Data
@TableName("t_yysq_spxx")
public class Spxx{
	//设置成主键，并开启自动生成UUID
	@TableId(value = "id", type = IdType.ASSIGN_UUID)
    private String id;

    //表中的字段
    @TableField("update_by")
    private Long updateBy;

    //时间格式字段，对应数据库中timestamp
    @TableField("update_time")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", locale = "zh", timezone = "GMT+8")
    private Date updateTime;

    //非表中字段
    @TableField(exist = false)
    private String sqsjEnd;

    //逻辑删除字段
    @TableLogic
    private Integer isDel;
}
```

Mapper及Service写法分为两种，一种是不使用批量添加语法，一种是使用批量添加

不使用批量添加写法
```
@Mapper
public interface SpxxMapper extends BaseMapper<Spxx> {
}


@Service
public class LyglServiceImpl implements LyglService {
	//查询
	public List<Lygl> getList(Lygl bean) {
        //拼接查询条件
        QueryWrapper<Lygl> queryWrapper = new QueryWrapper();
        //精准查询
        queryWrapper.lambda().eq(StringUtils.isNotBlank(bean.getId()), Lygl::getId, bean.getId());
        //模糊查询
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getWgfzId()), Lygl::getWgfzId, bean.getWgfzId());
        //时间范围查询-起始
        queryWrapper.lambda().ge(StringUtils.isNotBlank(param.getSqsjStart()), Lygl::getSqsj, param.getSqsjStart()+" 00:00:00");
        //时间范围查询-终止
        queryWrapper.lambda().le(StringUtils.isNotBlank(param.getSqsjEnd()), Lygl::getSqsj, param.getSqsjEnd()+" 23:59:59");
        //or语句的写法
        queryWrapper.and(wrapper -> wrapper.eq("s.status","0").or().eq("s.status","1"));
        //按时间倒序查询
        queryWrapper.orderByDesc("create_time");

        return lyglMapper.selectList(queryWrapper);
    }
    //分页查询
    public IPage<LyglSprz> LyglSprz(Page<LyglSprz> page, LyglSprz bean) {
        QueryWrapper<LyglSprz> queryWrapper = new QueryWrapper();
        //拼接查询条件
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getSqr()),LyglSprz::getSqr, bean.getSqr());
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getSqdw()),LyglSprz::getSqdw, bean.getSqdw());
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getFrontServiceName()),LyglSprz::getFrontServiceName, bean.getFrontServiceName());
        //申请时间起
        queryWrapper.lambda().ge(StringUtils.isNotBlank(bean.getSqsjStart()), LyglSprz::getSqsjStart, bean.getSqsjStart()+" 00:00:00");
        //申请时间止
        queryWrapper.lambda().le(StringUtils.isNotBlank(bean.getSqsjEnd()), LyglSprz::getSqsjEnd, bean.getSqsjEnd()+" 23:59:59");
        //or语句的写法
        queryWrapper.and(wrapper -> wrapper.eq("s.status","0").or().eq("s.status","1"));
        //按时间倒序查询
        queryWrapper.orderByDesc("create_time");
        return lyglSprzMapper.selectPage(page, queryWrapper);
    }
    //新增
    public int insertSprz(LyglSprz rz){
        return lyglSprzMapper.insert(rz);
    }

    //新增
    public Integer insertSprz(LyglSprz rz){
        return lyglSprzMapper.insert(rz);
    }
    //编辑
    public Integer edit(Fwsm bean) {
        //记录编辑时间
        bean.setUpdateTime(new Date());
        //记录编辑人
        bean.setUpdateBy(SecurityUtils.getUsername());
        //编辑数据
        return baseMapper.updateById(bean);
    }
    //查询详情
    public Fwsm detail(String id) {
        Fwsm fwsm = baseMapper.selectById(id);
        return fwsm;
    }
    //根据id删除单条
    public Integer del(String id) {
        return baseMapper.deleteById(id);
    }
    //批量删除
    public int delBatch(String pid){
		//创建批量删除规则
        QueryWrapper<FwsmInstance> instanceQueryWrapperwrapper = new QueryWrapper();
        instanceQueryWrapperwrapper.lambda().eq(FwsmInstance::getPid, pid);
        //批量删除ip实例节点信息
        int res = fwsmInstanceMapper.delete(instanceQueryWrapperwrapper);
        return res;
    }
    //查询条数
    public int selectCount(LyglSprz bean){
    	QueryWrapper<LyglSprz> queryWrapper = new QueryWrapper();
        //拼接查询条件
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getSqr()),LyglSprz::getSqr, bean.getSqr());
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getSqdw()),LyglSprz::getSqdw, bean.getSqdw());
        queryWrapper.lambda().like(StringUtils.isNotBlank(bean.getFrontServiceName()),LyglSprz::getFrontServiceName, bean.getFrontServiceName());
        return lyglSprzMapper.selectCount(queryWrapper);
    }
}

```

启用批量添加写法
Mapper写法不变，变化在Service层，Service实现类要继承ServiceImpl类
```
@Service
public class XyzhHttpInstanceServiceImpl extends ServiceImpl<XyzhHttpInstanceMapper, XyzhHttpInstance> implements IXyzhHttpInstanceService {

    @Autowired
    private XyzhHttpInstanceMapper baseMapper;

    /**
     * 批量新增http实例
     * @param list
     * @param pid
     * @return
     */
    @Override
    public boolean add(List<XyzhHttpInstance> list,String pid){
        //拼接批量添加的ip表数据
        for (XyzhHttpInstance instance:list){
            instance.setPid(pid);
        }
        //批量添加
        return this.saveBatch(list);
    }

    /**
     * 根据http服务id批量删除http实例
     * @param pid
     * @return
     */
    @Override
    public Integer del(String pid){
        //创建批量删除规则
        QueryWrapper<XyzhHttpInstance> wrapper = new QueryWrapper();
        wrapper.lambda().eq(XyzhHttpInstance::getPid, pid);
        //批量删除节点信息
        return baseMapper.delete(wrapper);
    }
}
```

在mybatisplus中写mybatis语法
1、普通写法
```
@Mapper
public interface LyglMapper extends BaseMapper<Lygl> {
    IPage<Lygl> getPage(Page<Lygl> page, @Param("param") Lygl lygl);
}

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ruoyi.lygl.mapper.LyglMapper">
    <select id="getPage" resultType="com.ruoyi.lygl.domain.Lygl">
        SELECT w.fzmc fzmc,l.*
        FROM t_lygl l
        LEFT JOIN t_wgfz w ON l.wgfz_id=w.id
        WHERE l.is_del=0
        <if test="param.frontServiceName!=null and param.frontServiceName!=''">
            AND l.front_service_name LIKE CONCAT('%', #{param.frontServiceName}, '%')
        </if>
        <if test="param.frontApiRoute!=null and param.frontApiRoute!=''">
            AND l.front_api_route LIKE CONCAT('%', #{param.frontApiRoute}, '%')
        </if>
        <if test="param.requestMethod!=null and param.requestMethod!=''">
            AND l.request_method=#{param.requestMethod}
        </if>
        <if test="param.accessingFlag!=null">
            AND l.accessing_flag=#{param.accessingFlag}
        </if>
        <if test="param.fzmc!=null and param.fzmc!=''">
            AND w.fzmc LIKE CONCAT('%',#{param.fzmc}, '%')
        </if>
        <if test="param.createBy!=null and param.createBy!=''">
            AND l.create_by = #{param.createBy}
        </if>
        ORDER BY l.create_time DESC
    </select>
</mapper>
```
2、通过QueryWrapper生成查询条件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ruoyi.yysq.mapper.SpxxMapper">
    <select id="selectSpxxList" resultType="Spxx">
        select s.*,l.front_service_name,l.front_service_description,l.front_api_route,l.request_method,l.lymc
                ,w.fzmc,u.nick_name sqr,d.dept_name
            from t_yysq_spxx s
        left join t_lygl l on l.id = s.lyid
        LEFT JOIN t_wgfz w ON l.wgfz_id=w.id
        left join t_user u on s.sqrid = u.user_id
        left join sys_dept d on s.sqrbmid = d.dept_id
            ${ew.getCustomSqlSegment}
    </select>
</mapper>

@Mapper
public interface SpxxMapper extends BaseMapper<Spxx> {
    IPage<Spxx> selectSpxxList(Page page, @Param(Constants.WRAPPER) Wrapper<Spxx> queryWrapper);
}
```
在Service层的调用，这种情况下用XyzhHttpInstance::getPid这种写法会失效，必须直接写字段
```
	@Override
    public IPage<Spxx> getPage(Page<Spxx> page, Spxx spxx) {
        //拼接查询条件
        QueryWrapper<Spxx> queryWrapper = new QueryWrapper();
        //申请时间起
        queryWrapper.ge(StringUtils.isNotBlank(spxx.getSqsjStart()), "s.sqsj", spxx.getSqsjStart()+" 00:00:00");
        //申请时间止
        queryWrapper.le(StringUtils.isNotBlank(spxx.getSqsjEnd()), "s.sqsj", spxx.getSqsjEnd()+" 23:59:59");
        //注销标识
        queryWrapper.eq("s.is_del", 0);
        //数据状态码
        queryWrapper.eq(StringUtils.isNotBlank(spxx.getStatus()),"s.status", spxx.getStatus());
        //or语句的写法
        queryWrapper.and(wrapper -> wrapper.eq("s.status","0").or().eq("s.status","1"));
        //创建时间倒序
        queryWrapper.orderByDesc("s.create_time");
        return spxxMapper.selectSpxxList(page, queryWrapper);
    }
```