---
title: ssm项目中问题
date: 2017-06-04 13:48:38
categories: Java框架
tags: ssm

---



### mybatis 构造SQL语句参数传递

mybatis参数传递通过paramType来限定参数类型，具体包括下面几种

* 基本数据类型
* Java实体类、Map
* 通过注解方式传递多个参数

#### 基本数据类型参数示例

```XML
<select id="selectTeacher" parameterType="int" resultType="com.myapp.domain.Teacher">  
    select * from Teacher where c_id=#{id}  
</select>  
```

```Java
List<Teacher> tList = teacherMapper.selectTeacher(2);    
```

#### Java实体类型参数示例

```XML
<select id="selectTeacher" parameterType="com.myapp.domain.Teacher" resultType="com.myapp.domain.Teacher">  
    select * from Teacher where c_id=#{id}  
</select> 
```

```Java
Teacher queryTeacher=new Teacher();  
queryTeacher.setId(2);  
List<Teacher> tList = teacherMapper.selectTeacher(queryTeacher);   
```

#### Map参数示例

```xml
<select id="selectTeacher" parameterType="map" resultType="com.myapp.domain.Teacher">  
    select * from Teacher where c_id=#{id} and sex=#{sex}  
</select>  
```

```java
Map<String,String> map=new HasMap<String,String>();  
map.put("id","2");  
map.put("sex","男");  
List<Teacher> tList = teacherMapper.selectTeacher(map);
```

#### 通过注解方式传递多个参数

接口方法

```Java
public List<Teacher> selectTeacher(@Param(value="id") String id,@Param(value="sex") String sex);  
```

```XML
<select id="selectTeacher"  resultType="com.myapp.domain.Teacher">  
    select * from Teacher where c_id=#{id} and sex=#{sex}  
</select>  
```

```Java
List<Teacher> tList = teacherMapper.selectTeacher("2","男");    
```


### springMVC中日期参数传递

日期参数在springMVC中无法直接转换，需要添加额外的转换器。

首先controller中添加一个@initBiner方法

```Java
public class GoodsAction extends BaseAction
{
	@InitBinder
	protected void initBinder(WebDataBinder binder)
	{
		SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		dateFormat.setLenient(false);
		binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
	}
	……
}
```

#### mybatis中日期插入

```Java
<insert id="insertItem" parameterType="TimeLimitPurchase">
		insert into
		t_time_limit_purchase
		(goods_id,begin_time,end_time)
		values (#{goods.goodsId,jdbcType=INTEGER},
		#{beginTime,jdbcType=TIMESTAMP},#{endTime,jdbcType=TIMESTAMP})
</insert>
```