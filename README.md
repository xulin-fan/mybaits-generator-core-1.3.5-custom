# mybaits-generator-core-1.3.5-custom
mybaits-generator-core-1.3.5功能扩展包

* 扩展一
    > 为`javaClientGenerator`节点添加4个属性, 分别为`generic`(boolean，默认`false`)、`genericClass`、`modelType`(boolean, 默认`true`)、`exampleType`(boolean, 默认`true`)

    > `generic`和`genericClass`的作用在于让生成的*Mapper.java类继承指定的`genericClass`，与`rootInterface`类似，但是增加了泛型的功能, 该属性会自动覆盖`rootInterface`, `genericClass`是自己定义的泛型接口，值为泛型接口带包全类名

    ```Java
        package com.mybatis.data.dict.common;

        import org.apache.ibatis.annotations.Param;

        import java.util.List;

        public interface GenericMapper<T, E> {

        long countByExample(E example);

        int deleteByExample(E example);

        int deleteByPrimaryKey(Integer id);

        int insert(T record);

        int insertSelective(T record);

        List<T> selectByExample(E example);

        T selectByPrimaryKey(Integer id);

        int updateByExampleSelective(@Param("record") T record, @Param("example") E example);

        int updateByExample(@Param("record") T record, @Param("example") E example);

        int updateByPrimaryKeySelective(T record);

        int updateByPrimaryKey(T record);
    ```

    > `modelType`和`exampleType`的作用在于是否为泛型接口添加参数类型, `modelType`是指`domainObjectName`，`exampleType`是指`Example`
    
    ```xml
        <javaClientGenerator targetPackage="com.mybatis.data.dict.mapper" targetProject="src/main/java"
                             type="XMLMAPPER" generic="true" genericClass="com.mybatis.data.dict.common.GenericMapper" modelType="true" exampleType="true">
            <property name="enableSubPackages" value="false"></property>
        </javaClientGenerator>
    ```
    > 使用如上配置后，将生成如下代码:  

    ```Java
        package com.mybaits.data.dict.mapper;

        import com.mybaits.data.pojo.po.model.EnvInfo;
        import com.mybaits.data.pojo.po.model.EnvInfoExample;

        public interface EnvInfoMapper extends com.mybaits.data.dict.common.GenericMapper<EnvInfo, EnvInfoExample> {
        }
    ```

    > `解决的问题`:  
    在开发过程中难免遇到多表联查的场景，这种情况下一般的做法是自己定义一个mapper接口和mapper.xml的实现，这样做的问题在于除了多维护一个mapper接口外，mapper.xml的内容有很多冗余，无法继承baseMapper(参考源码:org.apache.ibatis.builder.xml.XMLMapperBuilder), 经过上述的配置，我们只需要维护一个mapper接口，并且mapper.xml实现了继承baseMapper的功能

    > `使用方法`:
        maven: 将该jar包直接替换本地maven仓库的中的jar包, 路径如下:
    ```
        ~/.m2/repository/org/mybatis/generator/mybatis-generator-core/1.3.5
    ```
        jar包运行: 
    ```
        java -jar mybatis-generator-core-1.3.5.jar -configfile mybatis-generator.xml -overwrite
    ```

    > `需要注意以下几点:`  
        1、targetRuntime为"MyBatis3Impl"(默认)  
        2、如果要继承泛型接口, generic要设置为true, genericClass不能为空且要存在  
        3、当继承了泛型接口后，重复使用工具生成*Mapper.java文件时，为避免自定义的方法不被覆盖，不会重复生成该文件，不受全局参数overwrite影响  
        4、使用该仓库内的dtd文件本地地址替换原配置文件中dtd文件的地址
        

    ```xml
            <!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "/Users/xulinfan/projects/core/src/main/java/org/mybatis/generator/config/xml/mybatis-generator-config_1_0.dtd">
    ```
    

* 扩展二
    > 在Example类中添加了`ifAddColumnNameEqualTo`方法, 代码如下:
    ```Java
        public Criteria ifAndIdEqualTo(Integer value) {
            IfAddCriterion("id =", value);
            return (Criteria) this;
        }
    ```

* 扩展三
    > 为`sqlMapGenerator`节点添加`merge`属性，默认是`false`, 可通过此属性控制是否合并*Mapper.xml文件内容, 不受全局参数overwrite影响

    ```xml
        <sqlMapGenerator merge="false" targetPackage="mapper" targetProject="src/main/resources"></sqlMapGenerator>
    ```
