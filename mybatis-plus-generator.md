---
title: mybatis-plus-generator代码生成器配置
---

1. pom.xml引入包


```

<dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.5.3</version>
        </dependency>
				
				<!-- 模板引擎 -->

        <dependency>
			<groupId>org.apache.velocity</groupId>
			<artifactId>velocity-engine-core</artifactId>
			<version>2.0</version>

		</dependency>
				
```

2. 写方法执行生成配置

```

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostgreSQLGeneratorTest {
    //配置 Mapper的xml文件输出路径
    Map<OutputFile, String> outputFileStringMap = Collections.singletonMap(OutputFile.xml, "/Users/yan/Documents/java/workspace/cloud2022/balawo-mybatisp/src/main/resources/mapper");

    /**
     * 数据源配置
     */
    private static final DataSourceConfig DATA_SOURCE_CONFIG = new DataSourceConfig
            .Builder("jdbc:postgresql://xxx.aliyuncs.com:3432/demo", "aranya_staging", "password")
            .build();

    private static final GlobalConfig c = new GlobalConfig.Builder()
            .outputDir("/Users/yan/Documents/java/workspace/cloud2022/balawo-mybatisp/src/main/java")  // 指定输出目录
            .author("yan")
            .enableSwagger()
            .dateType(DateType.TIME_PACK)
            .disableOpenDir()//生成文件后、不要打开目录
            .commentDate("yyyy-MM-dd")
            .build();


    PackageConfig p = new PackageConfig.Builder()
            .parent("com.balawo") // 设置父包名
            .moduleName("mp") // 设置父包模块名
            .entity("entity")//实体类包名
            .service("service")//service包名
            .serviceImpl("service.impl")//impl包名
            .mapper("mapper")//mapper接口包名
            .controller("controller")//controller包名
            .pathInfo(outputFileStringMap) // 设置mapperXml生成路径
            .build();


    //Entity策略配置
    private static final StrategyConfig strategy = new StrategyConfig.Builder()
            //实体类策略配置
            .entityBuilder()
            .superClass("com.balawo.mp.BaseEntity")
            .enableLombok()
            .enableRemoveIsPrefix()//允许去除bollean类型is前缀
//            .enableActiveRecord() //ActiveRecord 模式 crud更简洁
            .logicDeleteColumnName("deleted_at")//逻辑删除字段名(数据库)
            .logicDeletePropertyName("deletedAt")//逻辑删除属性名(实体)
//            .addSuperEntityColumns("id", "created_by", "created_time", "updated_by", "updated_time")
            .addIgnoreColumns("created_at", "updated_at", "deleted_at")//忽略字段
            //addTableFills 添加表字段填充，"create_time"字段自动填充为插入时间，"update_time"字段自动填充为插入修改时间
            .addTableFills(new Column("create_time", FieldFill.INSERT))
            .addTableFills(new Column("update_time", FieldFill.INSERT_UPDATE))
            .idType(IdType.AUTO)
            .naming(NamingStrategy.underline_to_camel)  //数据库表映射到实体的命名策略：下划线转驼峰命
            .columnNaming(NamingStrategy.underline_to_camel)    //数据库表字段映射到实体的命名策略：下划线转驼峰命
            .enableTableFieldAnnotation()       // 开启生成实体时生成字段注解s

            //Controller策略配置
            .controllerBuilder()
            .enableHyphenStyle()
            .enableRestStyle()

            //service策略配置
            .serviceBuilder()
            .formatServiceFileName("%sService") //格式化 service 接口文件名称，%s进行匹配表名，如 UserService（默认的文件名称带有I）
            .formatServiceImplFileName("%sServiceImpl") //格式化 service 实现类文件名称，%s进行匹配表名，如 UserServiceImpl

            .build();



    @Test
    public void testSimple() {
        AutoGenerator g = new AutoGenerator(DATA_SOURCE_CONFIG);
        g.global(c);
        g.packageInfo(p);
        g.strategy(strategy);
        g.execute();
    }

    @Autowired
    private TagsService tagsService;

    @Autowired
    private TagsMapper tagsMapper;

    @Test
    public void saveTest(){
//        Tags tags = new Tags();
//        tags.setName("springboot");
//        tagsService.save(tags);
        System.out.println(111111);
        System.out.println(tagsService.count());


        Tags t = new Tags();
        t.setName("t2");
//        tagsMapper.insert(t);
       tagsService.save(t);

    }
}


```