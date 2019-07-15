---
layout:     post
title:      "selectByExampl&&eselectByExampleWithBLOBs ! "
subtitle:   "mybatis "
date:       2019-7-15 21:00:00
author:     "zhangliang"
header-img: "img/post-think-try-write.jpg"
tags:
    - just 4 fun
---

## selectByExample VS eselectByExampleWithBLOBs
- 首先在mapper.xml中查看这两个SQL查询语句
  - 发现两段SQL查询返回的resultMap不同，一个是BaseResultMap，另一个是ResultMapWithBLOBs。
- selectByExample
```xml
<select id="selectByExample" parameterType="com.macro.mall.tiny.mbg.model.PmsBrandExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from pms_brand
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
</select>
```
- selectByExampleWithBLOBs
```xml
<select id="selectByExampleWithBLOBs" parameterType="com.macro.mall.tiny.mbg.model.PmsBrandExample" resultMap="ResultMapWithBLOBs">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    ,
    <include refid="Blob_Column_List" />
    from pms_brand
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
```
- 再来看这两个SQL查询语句的resultMap
  - 发现ResultMapWithBLOBs继承了BaseResultMap，不仅有了BaseResultMap中的属性，同时也有了自己的brand_story属性。而brand_story在数据库中存入的是text类型。text属于大字段，
故如需检索的字段中包含大字段类型时，必须用selectByExampleWithBLOBs，不检索大字段时，用selectByExample就足够了。update同样如此。
- BaseResultMap
```xml
<resultMap id="BaseResultMap" type="com.macro.mall.tiny.mbg.model.PmsBrand">
    <id column="id" jdbcType="BIGINT" property="id" />
    <result column="name" jdbcType="VARCHAR" property="name" />
    <result column="first_letter" jdbcType="VARCHAR" property="firstLetter" />
    <result column="sort" jdbcType="INTEGER" property="sort" />
    <result column="factory_status" jdbcType="INTEGER" property="factoryStatus" />
    <result column="show_status" jdbcType="INTEGER" property="showStatus" />
    <result column="product_count" jdbcType="INTEGER" property="productCount" />
    <result column="product_comment_count" jdbcType="INTEGER" property="productCommentCount" />
    <result column="logo" jdbcType="VARCHAR" property="logo" />
    <result column="big_pic" jdbcType="VARCHAR" property="bigPic" />
</resultMap>
```
- ResultMapWithBLOBs
```xml
<resultMap extends="BaseResultMap" id="ResultMapWithBLOBs" type="com.macro.mall.tiny.mbg.model.PmsBrand">
    <result column="brand_story" jdbcType="LONGVARCHAR" property="brandStory" />
</resultMap>
```
